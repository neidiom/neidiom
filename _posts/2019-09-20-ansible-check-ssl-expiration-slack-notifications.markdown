---
layout: post
title:  "Ansible: Get a Slack notification when your SSL certificate is about to expire"
date:   2019-09-20 17:09:20 +0200
categories: ansible slack
---
# Get a Slack notification when your SSL certificate is about to expire.

I was tasked to finish a simple script to check the expiration of SSL certificates and post the notifications to our Slack channel. I wrote an Ansible playbook just to spice up everything. I had fun doing this so I will share the code hoping someone else might find it useful.

## Configure the Ansible playbook

You must define `slack_webhook` and `domains` as both variables are required.

Optionally configurable variables
* **ssl_port** - standard is 443,
* **ssl_expiry_days_check** - the script starts warning if certificate is expiring in less than this period,
* **cron_period_check** - when the cron job shuld be run.

Example ``ansible_ssl_check.yml`` playbook .

{% highlight yaml %}
---
- hosts: server_name
  roles:
    - user_group_directories
    - rvm
    - whenever
  vars:
    slack_webhook: "https://hooks.slack.com/services/xxxxxxx/xxxxxxx/xxxxxxxx"
    domains:
      - github.com
      - gitlab.com
{% endhighlight %}

## Testing

If you want to test things out then change
* `ssl_expiry_days_check` to something high like `'300'`,
* `cron_period_check` to `'hourly'`.

## Slack notifications

Your notifications will look like this.

![slack](/img/slack.png)


## Code repository

You can find the Gitub repo [here](https://github.com/neidiom/ansible_ssl_expiry_check).
