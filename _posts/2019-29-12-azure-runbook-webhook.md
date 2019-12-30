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

## Hello World PowerShell Runbook

````
param(
    $firstname,
    $lastname
)

Write-Output "Hello $firstname $lastname"
````

### Send parameters to webhook with CURL

{% highlight bash %}
#!/bin/bash
ENDPOINT="https://s2events.azure-automation.net/webhooks?token=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
curl $ENDPOINT -X POST -H "content-type: application/json" -d "{ "firstname": "Nedim", "lastname": "Hadzimahmutovic" }"
{% endhighlight %}

Executing the curl script will show you the JOB ID, like shown below

{% highlight bash %}
nedim@T580:~$ ./curl.sh 
{"JobIds":["00782a90-ee28-4db5-a31d-c346cc26f443"]}
{% endhighlight %}

## Sample

````
param (
    [Parameter (Mandatory = $false)]
    [object] $WebHookData
)
if ($WebHookData){
    Write-Output "The Webhook Header"
    Write-Output $WebHookData.RequestHeader.Message
    # Header message passed as a hashtable 
    Write-Output 'The Webhook Name'
    Write-Output $WebHookData.WebhookName
    # This is the name of the webhook in Azure Automation
    Write-Output 'The Request Body'
    Write-Output $WebHookData.RequestBody
    # Body of the message
}
else {
    Write-Output 'No data received'
}

````

## Links

* https://docs.microsoft.com/en-us/azure/automation/automation-webhooks
* https://www.ciraltos.com/webhooks-and-azure-automation-runbooks/
* https://www.spinnaker.io/guides/user/pipeline/triggers-with-artifactsrewrite/webhooks/
