---
layout: post
title: Matching First Names and Gender using gender-guesser  
description: gender-guesser is a python package that allows you to match a first names and name origin to a gender.
tags: python
---

![](/asset/screenshot/2022-02-28-python-gender-guesser-img01.jpg) 


Recently, I had the task to assign genders to a group of customers for whom we did not have such information. For the task, I was provided with a list of unique first names and their residence ISO country codes. To match gender with each name-country combination, I used the [gender-guesser](https://pypi.org/project/gender-guesser/) python package. 


    {%raw%} 

    import pandas as pd
    import pycountry
    import pyodbc
    import gender_guesser.detector as gender

    '''
    import data
    '''

    server = 'servername'
    database = 'databasename'
    username = 'username'
    password = 'password'
    conn = pyodbc.connect('DRIVER={ODBC Driver 17 for SQL Server};'
                        'SERVER='+server+
                        ';DATABASE='+database+
                        ';UID='+username+
                        ';PWD='+ password)

    my_query = '''
    SELECT 
        first_name,
        country,
        freq
    FROM 
        customer.first_name_table
    '''

    df = pd.read_sql(my_query, conn)

    print(df)
    print(type(df))

    {%endraw%}

I began by transforming country ISO codes into country names with the help of `pycountry`. I also replaced blank spaces by underscores, as required by `gender-guesser`.  

    {%raw%} 

    '''
    find country names
    '''

    df['country_name'] = [pycountry.countries.get(alpha_2=ii).name.lower().replace(' ', '_') for ii in df['country']]

    {%endraw%}

The package only works for certain [countries](https://github.com/lead-ratings/gender-guesser/blob/master/gender_guesser/detector.py), otherwise it raises an error. Hence, I used first-name-country-name combinations when the country is found; else, only the first name.


    {%raw%} 

    '''
    user gender detector based on first name and country
    '''

    d = gender.Detector(case_sensitive=False)

    gender_found = []

    for ii,jj in zip(df['first_name'], df['country_name']):
        try:
            gender_result = d.get_gender(ii, jj)
        except:
            gender_result = d.get_gender(ii)
        print(gender_result)
        gender_found.append(gender_result)

    df['gender_v1'] = gender_found

    {%endraw%}


As per documentation, `gender-guesser` will return either "*unknown* (name not found), *andy* (androgynous), *male*, *female*, *mostly_male*, or *mostly_female*. The difference between andy and unknown is that the former is found to have the same probability to be male than to be female, while the later means that the name wasn't found in the database."

For most names, the results are consistent. However, some results can be counter intuitive. For instance, `d.get_gender('Zoë', 'denmark')` results in *andy* while `d.get_gender('Zoë')` results in *female*. Zoë is a common [female name](https://en.wikipedia.org/wiki/Zoe_(name)).

Thus, I created a second version of the gender results using the customers’ first name only. 

    {%raw%} 

    '''
    user gender detector based on first name only
    '''

    df['gender_ v2'] = [d.get_gender(ii) for ii in df['first_name']]                

    {%endraw%}

For simplicity, I considered all *mostly_female* and *mostly_male* names to be considered *female* and *male* names respectively. 

    {%raw%} 

    '''
    turn partial results into either male or female
    '''

    df = df.replace({'mostly_male': 'male'}, regex=True)

    df = df.replace({'mostly_female': 'female'}, regex=True)

    {%endraw%}

In the end, I created a final version of gender classification: take whichever gender result is specific, with priority given to gender version 1.

    {%raw%} 

    '''
    take whichever gender version is more specific, with     priority on gender v1
    '''

    df['gender_final'] = df.apply(lambda x: x['gender_alternative'] 
                                    if x['gender_found'] not in ['male', 'female'] 
                                    else x['gender_found'], 
                                    axis = 1)


    '''
    export list
    '''

    df.to_csv('gender_list.csv', sep=';', index=False, encoding='utf-8-sig')

    {%endraw%}

Over 80% of names in my list found a corresponding gender match. As expected, some names were unisex. For instance, Daniele is a female name in France and a male name in Italy. Other unisex names found were Conny, Janne, Joan, Kim, and Vesa.








