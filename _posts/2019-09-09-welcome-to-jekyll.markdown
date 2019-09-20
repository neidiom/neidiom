---
layout: post
title:  "Ansible: Get a Slack notification when your SSL certificate is about to expire"
date:   2019-09-20 17:09:20 +0200
categories: ansible ssl ruby rvm
---
# Get a Slack notification when your SSL certificate is about to expire.

I was tasked to finish a simple script to check the expiration of SSL certificates and post the notifications to our Slack channel. I wrote an Ansible playbook just to spice up everyhing. I had fun doing this so I will share the code hoping someone else might find it useful.

## Configure the Ansible playbook

You must define `slack_webhook` and `domains` as both variables are required.

Optionally configurable variables
* ssl_port - standard is 443,
* ssl_expiry_days_check - the script starts warning if certificate is expiring in less than this period,
* cron_period_check - when the cron job shuld be run.

Example ``ansible_ssl_check.yml`` playbook .

````
---
- hosts: cache-1-lupon-media
  roles:
    - user_group_directories
    - rvm
    - whenever
  vars:
    slack_webhook: "https://hooks.slack.com/services/xxxxxxx/xxxxxxx/xxxxxxxx"
    domains:
      - github.com
      - gitlab.com
````
If you want to test things out then change `ssl_expiry_days_check` to something high like **300**.

## Slack notifications

You will get notifications like this one. 
![](https://github.com/neidiom/my_tutorials/blob/master/ssl_certificate_expiration/slack.png)
