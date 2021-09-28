---
title: 'Setting up Grav on AWS EC2'
published: true
date: '2017-03-04'
publish_date: '2017-03-04 17:01'
categories:
    - Blog
tags:
    - web
    - aws
    - ec2
header_image: '0'
slug: setting-up-grav-on-aws
---

I've been operating my site on various flavors of shared web hosting for years. Shared hosting has a lot of benefits: it's cheap, easy to set up, and hides almost all of the nuts and bolts. I finally decided to take the plunge and remove the training wheels by hosting my site on AWS.

<!--more-->

My site is built using [Grav](https://getgrav.org/), a lightweight file-based CMS. It's similar to Wordpress but without the requirement of a database. You might be familiar with the concept of a LAMP stack. This is a server running Linux, the Apache web server, the MySQL database, and PHP. To run Grav, I set up a "LAP" stack - same thing but without a database. Here's how to do it:

#### Setting up an AWS EC2 Instance

Amazon Web Services Elastic Compute Cloud, or AWS EC2, is a service that provides virtual servers in the cloud. For a basic Grav site we can get away with setting up a single <code>t2.nano</code> instance which is the smallest EC2 instance size currently available. If you're a new customer, AWS offers a <code>t2.micro</code> instance free for a year, so it's worth taking advantage of that.

Setting up an EC2 instance has a lot of steps, but they're pretty well documented. Instead of me copying them here, I'll link to the official documentation. Complete the two guides below then jump back here for the rest.
1. [Setting Up with Amazon EC2](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/get-set-up-for-amazon-ec2.html)
2. [Getting Started with Amazon EC2 Linux Instances](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/EC2_GetStarted.html)

After everything is set up you should have the following in your EC2 management console:

![EC2 instance running](/blog-static/setting-up-grav-on-aws/ec2_instance.png)

Make a note of the public IP, you'll need it later in this guide.

#### Install Required Software

Now that you have a shiny new virtual server, it's time to install the required software.

First, update the server's software:<br>
<code>sudo yum -y update</code>

Second, install Apache, PHP, and the PHP modules required for Grav to perform best:<br>
<code>sudo yum -y install httpd24 php56-devel php56-pear php56-mbstring php56-cli php56-imap php56-gd php56-xml php56-soap php56-pecl-apc</code>

#### Configure Web User Accounts

Create a <code>www</code> group and add the default <code>ec2-user</code> and <code>apache</code> users to it:<br>
<code>sudo groupadd www</code><br>
<code>sudo usermod -a -G www ec2-user</code><br>
<code>sudo usermod -a -G www apache</code>

#### Start and Test the Web Server and PHP

Start the web server and configure it to run upon server restart:<br>
<code>sudo service httpd start</code><br>
<code>sudo chkconfig httpd on</code>

Create a file to display the PHP configuration:<br>
<code>sudo echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php</code>

Now verify that Apache and PHP are working. Open your favorite web browser and navigate to <code>[YOUR_PUBLIC_IP]/phpinfo.php</code>. If everything is set up correctly, you should see a page that starts something like this:

![PHP info](/blog-static/setting-up-grav-on-aws/phpinfo.PNG)

#### Download and Install Grav

Download Grav Core + Admin Plugin (note: you may want to grab a newer version, I last updated this link in January 2019):<br>
<code>wget https://getgrav.org/download/core/grav-admin/1.5.6 -O grav-admin-1.5.6.zip</code>

When the download is complete, you need to extract it into the webroot. By default this is configured at the path <code>/var/www/html</code>. Run the following commands to back up your default webroot and extract Grav into the correct place:<br>
<code>cd ~</code><br>
<code>unzip grav-admin-1.5.6.zip</code><br>
<code>mv /var/www/html /var/www/html.old</code><br>
<code>mv grav-admin /var/www/html</code>

#### Configure Permissions

The final step is to configure the permissions. I ran the following commands based on Grav's [official documentation](https://learn.getgrav.org/troubleshooting/permissions):<br>
<code>cd /var/www</code><br>
<code>chown -R apache:www .</code><br>
<code>find . -type f | xargs chmod 664</code><br>
<code>find ./bin -type f | xargs chmod 775</code><br>
<code>find . -type d | xargs chmod 775</code><br>
<code>find . -type d | xargs chmod +s</code><br>
<code>umask 0002</code>

#### Test your Site

At this stage, everything should be set up! Navigate to  <code>[YOUR_PUBLIC_IP]/admin</code> and Grav should prompt you to set up an administrator account. After that's done, you should be looking at Grav's built in dashboard:

![Grav dashboard](/blog-static/setting-up-grav-on-aws/grav_dash.PNG)

If you see this, congratulations! You have a Grav instance up and running on AWS. I'd recommend using Grav's built in update function to make sure you have the newest version before adding content.

#### A Note About Domains

Now that you have Grav set up, you probably want to point a domain name to it. You can register your domain either through AWS or another registrar (I personally recommend [Namecheap](https://www.namecheap.com)). The process for signing up for a domain is different depending on the registrar. Usually for me it starts with searching for a domain I like, finding out it's unavailable, and then repeating three or four (or 20 or 30) times. After you finally find a domain you like and put all the information required to sign up, you have to edit the domain records to point to the IP address of your EC2 instance. Editing the domain records is usually hidden under an advanced menu somewhere on your domain registrar's control panel.

You want to add two <code>A</code> (for address) records to your domain name, one for <code>yourdomain.com</code> and another for <code>www.yourdomain.com</code>. My configured <code>A</code> records look like this:

![A records](/blog-static/setting-up-grav-on-aws/a_records.PNG)

#### Set up SSL

Now that your site is up and running, you should [follow this guide to setting up a free SSL certificate using LetsEncrypt]({{< ref "2017-03-11-ssl-with-letsencrypt-on-aws-ec2" >}}).