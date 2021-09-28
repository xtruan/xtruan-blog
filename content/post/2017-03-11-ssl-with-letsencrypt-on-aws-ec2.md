---
title: 'Configuring SSL with Let''s Encrypt on AWS EC2'
author: 'Struan Clark'
published: true
date: '2017-03-11'
publish_date: '2017-03-12 19:54'
categories:
    - Blog
tags:
    - web
    - security
    - aws
    - ec2
    - ssl
    - letsencrypt
header_image: '0'
slug: ssl-with-letsencrypt-on-aws-ec2
---

SSL, or Secure Sockets Layer, is one of the technologies essential to security on the web. It allows a web browser and server to form an encrypted connection. Encryption is an essential part of web security. If you're browsing the web in a coffee shop or airport without encryption, it's trivial for anyone else to see exactly what you're looking at.

<!--more-->

Luckily, SSL is becoming more and more prevalent thanks to efforts by internet companies and a better understanding of why security is important among users. Most browsers have also started advertising that sites without SSL are insecure. The images below show what websites that are using SSL look like on Google Chrome vs. those that are not using SSL:

![Using SSL](/blog-static/ssl-with-letsencrypt-on-aws-ec2/https_bar.PNG)
<p style="text-align: center">vs.</p>

![Not using SSL](/blog-static/ssl-with-letsencrypt-on-aws-ec2/http_bar.PNG)

SSL works by using a certificate issued by a trusted certificate authority (CA) being installed on the web server. Traditionally these certificates were not free and were complicated to configure. That changed with the introduction of the [Let's Encrypt CA](https://letsencrypt.org/). Let's Encrypt provides free SSL certificates along with an associated software package to automatically install and update them - very cool.

### Prerequisites

This guide assumes that you have a domain name pointed at an AWS EC2 instance running Amazon Linux. This instance should be set up with the Apache web server and you should have access via SSH. If you don't have this setup, the following steps will not work.

Also note that this will require restarting the web server, so it's worth doing it at an off-peak time if you have a high-traffic site.

### Install Required Software

The first step is to install git and bc (a math library):<br>
<code>sudo yum -y install git bc</code>

In addition, make sure that the Apache module for SSL is installed:<br>
<code>sudo -y install mod24_ssl</code>

Next, clone the Let's Encrypt git repository to the <code>/opt/letsencrypt</code> directory:<br>
<code>sudo git clone https://github.com/letsencrypt/letsencrypt /opt/letsencrypt</code>

### Configure Apache

It's important to have a properly configured web server before continuing. Apache's main configuration file is located at <code>/etc/httpd/conf/httpd.conf</code>.

In my case, I was able to run with most of the default settings. I did update the <code>ServerName</code> and <code>ServerAdmin</code> fields. Somewhere in your <code>httpd.conf</code> file should be the following lines:<br>
<code>ServerName www.yourdomain.com</code><br>
<code>ServerAdmin you@yourdomain.com</code>

### Run Let's Encrypt

The command below is the big one. It runs Let's Encrypt and has it generate and install certificates. One thing to note - Let's Encrypt does not support the concept of a wildcard certificate. Therefore all subdomains must be explicitly specified as arguments. At a minimum, the <code>www</code> subdomain should be specified. Here's the command:<br>
<code>sudo /opt/letsencrypt/letsencrypt-auto --debug -d www.yourdomain.com -d yourdomain.com</code>

##### A note about RAM

The first time I ran this command on a <code>t2.nano</code> instance it failed because that instance did not have enough RAM (it only has 512 MB). The workaround I used was adding 1 GB of swap space. Swap space is essentially hard disk space masquerading as additional RAM. It's significantly slower than RAM, but that's not a huge deal for our purposes. If you run into the same thing, you can run these commands then re-run the big one above:<br>
<code>sudo /bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024</code><br>
<code>sudo /sbin/mkswap /var/swap.1</code><br>
<code>sudo chmod 600 /var/swap.1</code><br>
<code>sudo /sbin/swapon /var/swap.1</code>

### Configure Let's Encrypt Updates

Let's Encrypt certificates only last for 90 days. Therefore a script must be run to update the certificates on a regular interval. 

First, create a configuration file for the updates:<br>
<code>sudo echo "rsa-key-size = 4096" >> /etc/letsencrypt/config.ini</code><br>
<code>sudo echo "email = you@yourdomain.com" >> /etc/letsencrypt/config.ini</code>

Then, configure the Let's Encrypt updater to run via a <code>cron</code> job under the <code>root</code> user:<br>
<code>sudo su</code><br>
<code>crontab -e</code>

You are now editing the crontab file. Add the following line to the file:<br>
<code>30 2 \* \* 1 /opt/letsencrypt/letsencrypt-auto renew --config /etc/letsencrypt/config.ini --agree-tos && apachectl graceful</code>

At this point, SSL should be up and running. Restart the web server (<code>sudo service httpd restart</code>) and navigate to your website. If all's gone well, you should have an <code>https://</code> in your navigation bar!

### Final Touches

Security is a moving target, so it's important to check your site's SSL rating from time to time. The defacto tool for this is the excellent [SSL Server Test](https://www.ssllabs.com/ssltest/) developed by Qualsys.

The setup steps above got me a B rating on the SSL server test. To improve them, a few more updates to the SSL configuration are required. An excellent source of good configurations is [Cipherli.st](https://cipherli.st/).

Take the configurations from the Apache box on Cipherli.st and add them to <code>/etc/httpd/conf.d/ssl.conf</code> then restart the web server again. This should bump the rating up to an A+.