---
layout: post
title:  "Azure: runbook webhook"
date:   2019-12-29 12:50:20 +0200
categories: azure webhhok runbook
---

````
When a client starts a runbook using a webhook, it can't override the parameter values defined in the webhook. 
To receive data from the client, the runbook can accept a single parameter called $WebhookData. 
This parameter is of a type [object] that contains data that the client includes in the POST request.
````


* https://docs.microsoft.com/en-us/azure/automation/automation-webhooks
