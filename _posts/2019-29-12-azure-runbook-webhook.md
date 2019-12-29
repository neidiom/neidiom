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
