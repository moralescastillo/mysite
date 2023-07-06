---
layout: post
title: Preventing Unexpected Billing in GCP 
description: A few tips on how to avoid surprises at the end of the month
date: 2021-04-17 12:00:00 +0200
author: paulo
image: '/images/2022-04-17-gcp-billing-tips-img01.jpg'
image_caption: Budgets & alerts overview on GCP
tags: [gcp]
featured: 
---


One of the intimidating parts about getting started with cloud services is billing. Google Cloud Platform (GCP) gives you a Free Tier ($300 in credit) to get started. Yet, GCP will still ask you for your credit card details. And whenever the credit runs out, google will start billing your card.

These following are some tips to prevent unexpected bills at the end of the month:

### Budgets

A budget sends you an email alert every time your GCP project(s) consumes more than a predetermined money threshold. 

In GCP, go to **Navigation Menu** > **Budgets & alerts** > **+ CREATE BUDGET**. Assign the project(s) to be monitored, specify the budget amount, and determine your alert threshold and actions. 

Beware that, "Setting a budget does not cap resource or API consumption." In other words, a budget just sends you an email. If you take no action, the bill will keep running. So do not ignore those emails!

### Webhooks

You can connect your budget alerts to Webhooks. In this way, you could trigger messages to other platforms like Slack. 

Another alternative is to create a Cloud Function that is triggered by the budget alert. This function could perform various actions via Cloud Engine API, such as shutting down VMs on your behalf. In this way, your resource consumption will be limited automatically. 

### Quotas

GCP gives out resource quotas automatically. Quotas prevent malicious attacks and other unexpected behavior from overconsuming your project’s resources. Google also implements quotas so that all GCP users can have enough resources to work with.

To check and edit your current quotas, go to **Navigation Menu** > **IAM & Admin** > **Quotas**. By editing quotas, you can set an explicit cap to your resource consumption, and thus limit your billing at the end of the month.