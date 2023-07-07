---
layout: post
title: My Airflow Journey
subtitle: Three don'ts I learned in two years of using Apache Airflow
description: Starting from scratch is impractical, Google Cloud Platform's Composer is expensive, and debugging Airflow on Windows is challenging
date: 2023-07-05 12:00:00 +0200
author: paulo
image: '/images/2023-07-05-my-airflow-journey-img01.jpg'
image_caption: 'Outcome when generating an image using AI and the words "airflow" and "journey"'
tags: [airflow]
feature: 
---

 
It’s been about two years since I started using Apache Airflow both for personal and professional purposes. Though my work is mostly data warehouse development nowadays, I keep tinkering with Airflow on a daily basis. Throughout this journey, I've encountered challenges, found solutions, and improved my workflow. Here are three don'ts I would like to share.


### Create from Stratch

My first interaction with Airflow came via AWS. With the help of YouTube tutorials, I managed to install all components of the orchestrator into one virtual machine: meta database, DAGs, scheduler, web interface, etc.  

I managed to get my first jobs running, and learned a lot in the setup process. Despite the learning experience of setting up all the components, I soon faced limitations due to memory constraints and dependency management. While starting from scratch was beneficial for learning, it proved very impractical in the end. 


### Use GCP Composer

Soon after learning how to use Airflow, I started working on a freelance project using Google Cloud Platform's Composer. GCP Composer is an instance of Airflow, but fully managed. You as the user get to focus on developing your code while letting Google take care of the boring maintenance stuff. It is great!

The only issue is that it is expensive. When using it for simple tasks for personal use, I racked up close to €350/month with Composer services alone. I tried to reduced costs by automatically turning Composer on and off in idle times, but this was not possible. So Composer no more. This lead me to keep searching for alternative solutions.


### Debug on Windows

Once upon a time, me and other data developers took part in a workshop on how to debug code on Airflow. Soon after the workshop started, I came to the realization that everyone else taking part had unix-like systems. I was the only one using a windows machine.


The truth is that I faced multiple challenges debugging Airflow on my Windows machine. It was cumbersome compared to my colleagues using Unix-like systems. To overcome this, I explored options like WSL, Oracle VM VirtualBox Manager, and cloud-based Linux virtual machines with GUI. Although I managed get the job done, the extra effort felt unnecessary.


To enhance my Airflow experience, I made significant changes. First, I switched back to Ubuntu as my main operating system. This allowed me to develop and test using PyCharm on the console. For Windows-specific tasks, I leverage a Windows virtual machine with a synced OneDrive folder. Most importantly, I adopted Docker to run Airflow on a virtual machine, benefiting from containerization and simplifying my workflow. This also allowed me to shut the VM off during idle times and save big time at the end of the month.
