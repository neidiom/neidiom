---
layout: post
title:  "Terraform: Azure Metric Alerts with webhook receiver in multiple subscriptions"
date:   2019-11-14 17:09:20 +0200
categories: azure terraform
---

I needed to define multiple metric alerts and send them to a webhook but explicitly state at which subscription each alert and action group should be created.

## First we define the subscriptions

Create a file named **provider.tf**

````
variable my_first_subscription_id" {
  default = "xxxxxx-xxxxx-xxxxx-xxxx-xxxxx"
}

variable "my_second_subscription_id" {
  default = "xxxx-xxxxx-xxxx-xxxxx-xxxxx"
}

provider "azurerm" {
  subscription_id = var.my_first_subscription_id
}

provider "azurerm" {
  alias  = "second"
  subscription_id = var.my_second_subscription_id
}

````

## Create action group and metric alerts in first subscription

**action_group_1.tf**

````
resource "azurerm_monitor_action_group" "example-ubuntu-dev" {
  name                = "example_actiongroup_1"
  resource_group_name = "example_resource_name_1"
  short_name          = "my_short_name"
  enabled             = true


  webhook_receiver {
    name        = "my_webhook_name"
    service_uri = "http://xx-xx-xx-xx.cloudapp.net/"
  }
}
````

**metric_alert**

````

variable "my_first_vm_id" {
  default = "/subscriptions/xxxx-xxx-xxx-xxx-xxxxx/resourceGroups/my-xx-rg/providers/Microsoft.Compute/virtualMachines/my-machine-name"
  }

resource "azurerm_monitor_metric_alert" "my-first-ubuntu-dev" {
  name                = "example-metricalert"
  resource_group_name = "my_first_rg_name"
  scopes              = ["${var.my_first_vm_id}"]
  description         = "Action will be triggered when Transactions count is greater than 50."
  frequency           = "PT1M"
  enabled             = true


  criteria {
    metric_namespace = "Microsoft.Compute/virtualMachines"
    metric_name      = "Percentage CPU"
    aggregation      = "Average"
    operator         = "LessThan"
    threshold        = 10

    }

  action {
    action_group_id = "${azurerm_monitor_action_group.main.id}"
  }
}
````

## Create action_group and metric alert in second subscription

**action_group_sub_2.tf**

````
resource "azurerm_resource_group" "reporting_action_group_main" {
  provider = "azurerm.reporting"
  name     = "second_action_group_main"
  location = "West Europe"

  tags = {
    environment = "second_actiongroup"
  }
}

# ACTION GROUP

resource "azurerm_monitor_action_group" "reporting_action_group_main" {
  provider = "azurerm.second"
  name                = "reaper_actiongroup"
  resource_group_name = "reporting_action_group_main"
  short_name          = "reaper"
  enabled             = true


  webhook_receiver {
    name        = "my_webhook"
    service_uri = "http://xxxx-xxx-xxxx-xxx-xxxx.cloudapp.net/"
  }
}

````


metric alerts

````

variable "my_second_vm_id" {
  default = "/subscriptions/xxxx-xxx-xxx-xxx-xxxxx/resourceGroups/my-xx-rg/providers/Microsoft.Compute/virtualMachines/my-machine-name"
  }


resource "azurerm_monitor_metric_alert" "my_second_metric_alert" {
  provider = "azurerm.second"
  name                = "CPU percentage metric alert"
  resource_group_name = "my_second_rg_name"
  scopes              = ["${var.my_second_vm_id}"]
  description         = "Action will be triggered when Transactions count is greater than 50."
  frequency           = "PT5M"
  severity            = "4"
  enabled             = true


  criteria {
    metric_namespace = "Microsoft.Compute/virtualMachines"
    metric_name      = "Percentage CPU"
    aggregation      = "Average"
    operator         = "LessThan"
    threshold        = 10

    }

  action {
    action_group_id = "${azurerm_monitor_action_group.reporting_action_group_main.id}"
  }
}
````
