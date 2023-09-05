---
layout: post
title: 'Understanding DAGs in dbt: Threads, Errors and Failing Fast'
subtitle: 'A Comparison of Results across Multi-Thread Environments using the BigQuery adapter'
description: 'Three scenarios show us the implications of running a Directed Acyclic Graph (DAG) in multi-threaded dbt environments, shedding light on how errors and the "fail fast" argument impact model execution'
date: 2023-09-05 18:42:00 +0200
author: paulo
image: '/images/2023-09-05-dbt-dag-thread-img00.jpg'
image_caption: 'Photo by [amirali mirhashemian](https://unsplash.com/@amir_v_ali?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/photos/hTeYcjviZ-s?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)'
tags: [dbt, dwh, big-query]
feature:
---    

<!---
Photo by <a href="https://unsplash.com/@amir_v_ali?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">amirali mirhashemian</a> on <a href="https://unsplash.com/photos/hTeYcjviZ-s?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText">Unsplash</a>
--->

In May 2023, I took the dbt Analytics Engineering Certification Exam. The [exam](https://www.getdbt.com/certifications/analytics-engineer-certification-exam/) is designed to test one's ability to "build, test, and maintain models to make data accessible to others" and "use dbt to apply engineering principles to analytics infrastructure." One of the questions that puzzled me at the time was related to the topic of Directed Acyclic Graph (DAG) execution within multi-thread environments.

### The Scenarios

The premise of the question went along the lines of: Picture a DAG with a root node named `model_alpha` and two directed edges leading to two nodes, `model_beta_a` and `model_beta_b.` Each of these nodes connects to its own set of leaf nodes, `model_gamma_a` and `model_gamma_b.`



![2023-09-05-dbt-dag-thread-img01](/images/2023-09-05-dbt-dag-thread-img01.jpg){:loading="lazy"}
<font size="-1"><center><span> Line graph of the described DAG </span></center></font>
<br>



The question posed was: In a scenario where this DAG is run using two threads, with the *fail fast* argument enabled, what would happen if `model_beta_a` encounters an error?

To tackle this question, we'll explore three scenarios using the hypothetical DAG, aiming to observe the differences between running it with a single thread and running it with two threads. The three scenarios are:

1. **Smooth Run**: Executing the DAG without any errors.
2. **Error Run**: Running the DAG where `model_beta_a` encounters a division-by-zero error.
3. **Fail-Fast Error Run**: Executing the DAG with `model_beta_a` encountering a division-by-zero error and the *fail-fast* argument enabled.

All three scenarios are conducted using `dbt core 1.6.1` with the `bigquery plugin 1.6.4`. For simplicity, all models use materialization of type table.


### Some Background

In the context of dbt, a [*thread*](https://docs.getdbt.com/docs/running-a-dbt-project/using-threads) represents a path of ordered commands. The number of threads determines the maximum number of paths dbt will concurrently process when executing a DAG. You can specify the number of threads in the *profiles.yml* file or directly through the dbt command using the `--threads` argument.

Once the order and the number of paths are determined, dbt proceeds with the compilation and execution of each node. Compilation involves assembling queries and syntax error checking, followed by execution of the compiled query on the target database.

Results and metadata from each dbt run are display in the command-line interface (CLI) and also stored in the *target/run_results.json* file. This file is updated with each successful run.

### Smooth Run

In this scenario, we initiate the DAG run with the command `dbt run --threads 1`.



<script src="https://gist.github.com/moralescastillo/e46d0272c3e591c57c40e3bfae51ca55.js"></script>
<font size="-1"><center><span> CLI results from <code>dbt run --threads 1</code> within the smooth run scenario </span></center></font>
<br>

<!---
https://gist.github.com/e46d0272c3e591c57c40e3bfae51ca55.git
-->



The execution proceeds as expected, with all models being ordered, compiled, and executed in succession. The CLI provides a summary of time results in seconds, while the target folder contains more detailed timing information.



![2023-09-05-dbt-dag-thread-img02](/images/2023-09-05-dbt-dag-thread-img02.jpg){:loading="lazy"}
<font size="-1"><center><span> Model execution order, threads and status according to results from the smooth run scenario with one threads </span></center></font>
<br>


  
One observation to be made is that model compilation doesn't begin until the preceding model's execution is completed. 



![2023-09-05-dbt-dag-thread-img04](/images/2023-09-05-dbt-dag-thread-img04.jpg){:loading="lazy"}
<font size="-1"><center><span> Timeline of compilation and execution for beta models from the smooth run scenario with one thread </span></center></font>
<br>



Now, let's compare this to the two-thread run with `dbt run --threads 2.`



<script src="https://gist.github.com/moralescastillo/60da308981ac21bc772da11ecd30e70b.js"></script>
<font size="-1"><center><span> CLI results from <code>dbt run --threads 2</code> within the smooth run scenario </span></center></font>
<br>

<!---
https://gist.github.com/60da308981ac21bc772da11ecd30e70b.git
-->




As expected, running the DAG with two threads results in a slightly shorter execution time compared to a single-thread run. 



![2023-09-05-dbt-dag-thread-img03](/images/2023-09-05-dbt-dag-thread-img03.jpg){:loading="lazy"}
<font size="-1"><center><span> Model execution order, threads and status according to results from the smooth run scenario with two threads </span></center></font>
<br>

  

Under the two-thread case, two observations are worth mentioning. Firstly, despite using separate threads and identical queries, both beta models start and end at different times. 



![2023-09-05-dbt-dag-thread-img05](/images/2023-09-05-dbt-dag-thread-img05.jpg){:loading="lazy"}
<font size="-1"><center><span> Timeline of compilation and execution for beta models from the smooth run scenario with two threads </span></center></font>
<br>

  

Secondly, unlike the single-thread case, compilation and execution in the two-thread case occur concurrently for the beta models.

### Error Run

In this scenario, we intentionally introduce a division-by-zero error in `model_beta_a` to force an error during the DAG run. We then execute the command `dbt run --threads 1`.



<script src="https://gist.github.com/moralescastillo/a8f073593d073695c7b06cd8bd576327.js"></script>
<font size="-1"><center><span> CLI results from <code>dbt run --threads 1</code> within the error run scenario </span></center></font>
<br>

<!---
https://gist.github.com/a8f073593d073695c7b06cd8bd576327.git
-->




As expected, `model_beta_a` is compiled and executed but fails to materialize due to the error. As a result of its dependency, `model_gamma_a` is skipped, and materialization doesn't occur. Consistent with single-thread execution, compilation of `model_beta_b` starts after `model_beta_a`'s execution ends.



![2023-09-05-dbt-dag-thread-img06](/images/2023-09-05-dbt-dag-thread-img06.jpg){:loading="lazy"}
<font size="-1"><center><span> Model execution order, threads and status according to target results from the error run scenario with one thread </span></center></font>
<br>



Now, let's run the same DAG with two threads with the command `dbt run --threads 2`. 



<script src="https://gist.github.com/moralescastillo/ceb82855fb2e1a3aaec0f89ffe1d17ec.js"></script>
<font size="-1"><center><span> CLI results from <code>dbt run --threads 2</code> within the error run scenario </span></center></font>
<br>

<!---
https://gist.github.com/ceb82855fb2e1a3aaec0f89ffe1d17ec.git
-->



Similar to the single-thread case, `model_beta_a` and `model_gamma_a` do not materialize in the two-thread case. Also, compilation and execution of the beta models occur concurrently, just as in the smooth run.


![2023-09-05-dbt-dag-thread-img09](/images/2023-09-05-dbt-dag-thread-img09.jpg){:loading="lazy"}
<font size="-1"><center><span> Model execution order, threads and status according to target results from the error run scenario with two threads </span></center></font>
<br>



### Fail-Fast Error Run

We now introduce the *fail-fast* argument into our DAG run using the command `dbt run --threads 1 --fail-fast`. 

Our aim is to analyze results from both the CLI and the target folder, specifically focusing on what happens to the remaining models after `model_beta_a` encounters an error.



<script src="https://gist.github.com/moralescastillo/a8a2a6c0b2f940b556be98baf92a200e.js"></script>
<font size="-1"><center><span> CLI results from <code>dbt run --threads 1 --fail-fast</code> within the fail-fast error run scenario </span></center></font>
<br>

<!---
https://gist.github.com/a8a2a6c0b2f940b556be98baf92a200e.git
-->



At this point the results become interesting. In the single-thread case, there are discrepancies between the CLI and target results. 

As seen in the CLI results, `model_beta_a` encounters an error and fails to materialize. Surprisingly, despite the single-thread setup, and the fact that models are compiled and executed successively, `model_beta_b` is given a green light to run.

Then, `model_beta_b` cannot be stopped, as stated in the CLI: "The bigquery adapter does not support query cancellation." In the end, the model is materialized. 

This story contrasts with the target results. A quick inspection indicates that `model_beta_b` is skipped "due to fail fast." Naturally, since the model was skipped, there is no record of compilation and execution times.



![2023-09-05-dbt-dag-thread-img07](/images/2023-09-05-dbt-dag-thread-img07.jpg){:loading="lazy"}
<font size="-1"><center><span> Model execution order, threads and status according to target results from the fail-fast error run scenario with one thread </span></center></font>
<br>



Lastly, we run `dbt run --threads 2 --fail-fast`. 



<script src="https://gist.github.com/moralescastillo/ef013fc6fb64b4f6304a13b319668b57.js"></script>
<font size="-1"><center><span> CLI results from <code>dbt run --threads 2 --fail-fast</code> within the fail-fast error run scenario </span></center></font>
<br>

<!---
https://gist.github.com/ef013fc6fb64b4f6304a13b319668b57.git
-->



Similar to the single-thread case, the CLI reports that `model_beta_b` is triggered and materialized. Yet, once again, the target results suggest this model was skipped.



![2023-09-05-dbt-dag-thread-img08](/images/2023-09-05-dbt-dag-thread-img08.jpg){:loading="lazy"}
<font size="-1"><center><span> Model execution order, threads and status according to target results from the fail-fast error run scenario with two threads </span></center></font>
<br>



  
### Conclusion

In summary, we see that the execution of a DAG respects dependencies. If an upstream model fails, downstream models are skipped, regardless of the number of threads. However, the execution of a DAG does not consistently respect the *fail fast* argument.

So, what is the answer to the original question? The answer is, it depends.

In theory, models running in parallel in a two-thread environment should be canceled and marked as skipped. 

Yet, in practice, whether a model running in parallel is run and materialized depends on the dbt adapter's support for query cancelation. In the case of the BigQuery adapter, it does not seem to support query cancelation. Thus models running in parallel could still be materialized.
