---
layout: wiki
title:  Getting Started
author: Glen Vanderhel
updated_by: Glen Vanderhel
updated_at: 2015-11-03
tags: [myitcrm2]
---

## Wiki.

### Create Our Deploy User


One of the first things we want to make sure we get done is that we have a deploy user. This going to be the user that deploys the website and the same user that the website is run as.

<pre>
sudo adduser deploy
</pre>

### Update and install our dependencies

This downloads the latest list of available software versions. We’ll install our build dependencies for later.

<pre>
sudo apt-get -y update
sudo apt-get -y install build-essential zlib1g-dev libssl-dev libreadline-dev libyaml-dev libcurl4-openssl-dev curl git-core python-software-properties
</pre>

### Install Ruby 1.9.3

We’re going to install Ruby 1.9.3 from source. Note that -p194 may not be the latest version of Ruby. Check out ruby-lang.org to get the latest version and replace it in the following commands:

<pre>
wget ftp://ftp.ruby-lang.org/pub/ruby/1.9/ruby-1.9.3-p194.tar.gz
tar -xvzf ruby-1.9.3-p194.tar.gz
cd ruby-1.9.3-p194/
./configure
make
sudo make install
echo "gem: --no-ri --no-rdoc" >> ~/.gemrc
sudo gem install bundler
</pre>

### Install Nginx with Passenger

Since we’ll be using Nginx for serving our application, we’re going to install it using the latest package from a user repository:

<pre>
sudo gem install passenger
sudo passenger-install-nginx-module
# Choose "download, compile, and install Nginx for me"
# Accept defaults for any other questions it asks you
</pre>

### Next we want to setup a script to allow us to control Nginx. We’re going to grab this from Linode:

<pre>
wget -O init-deb.sh http://library.linode.com/assets/660-init-deb.sh
sudo mv init-deb.sh /etc/init.d/nginx
sudo chmod +x /etc/init.d/nginx
sudo /usr/sbin/update-rc.d -f nginx defaults
</pre>

You can now control Nginx with this script. To start and stop the server manually, you run:

<pre>
sudo /etc/init.d/nginx stop
sudo /etc/init.d/nginx start
</pre>

We can verify nginx is running by opening up Firefox and going to http://localhost



After installation, you’ll get some tips on how to configure an Nginx server to listen on a domain and enable passenger for it. You’ll want to save this for later when you setup your deployment scripts. The root path there will be the public directory where you setup your Rails app folder.


One of the first things you want to do is edit the @nginx.conf@ so that you can tell it to run as the deploy user. This file will be @/opt/nginx/conf/nginx.conf@ and you can add this as the first line:

<pre>
user deploy staff;
</pre>

### Nginx

Install just plain Nginx with the following commands:

<pre>
sudo add-apt-repository ppa:nginx/stable
sudo apt-get update
sudo apt-get install nginx
sudo service nginx start
</pre>

After this, I recommend checking out Deploying To A VPS by Ryan Bates for setting up Unicorn.

Setup your database

Our next step is installing our database server. I’d recommend using PostgreSQL but many of you may prefer MySQL. Take your pick:

### MySQL

<pre>
# MySQL (instead of PostgreSQL)
apt-get -y install mysql-server mysql-client libmysqlclient-dev
mysql -u root -p
# create database blog_production;
# grant all on blog_production.* to blog@localhost identified by 'secret';
# exit
</pre>

OR

### PostgreSQL 9.1.3

<pre>
sudo apt-get -y install postgresql libpq-dev
</pre>

### Node.js for the Rails asset pipeline

One of the other things you’ll want is Node.js. This will help us do the compiling of assets on deployments. It’s a pretty quick installation to get the latest version:

<pre>
sudo apt-add-repository ppa:chris-lea/node.js
sudo apt-get -y update
sudo apt-get -y install nodejs
</pre>

### Configure Your Rails App

You’ll need to get a copy of your rails application on the webserver. The best place to do this is to store it in the home directory of the deploy user. I recommend using Capistrano to set this up. Afterwards, you can modify your @/opt/nginx/conf/nginx.conf@ file to contain a new passenger server like so:

<pre>

server {
	listen 80;
	server_name example.com;
	root /home/deploy/myapplication/public;   # <--- be sure to point to 'public'!
	passenger_enabled on;
}
</pre>

Just change the application folder name and the server name and then restart the nginx service.

### Create a folder for the myitcrm installation and get the latest git repo

<pre>
cd ~
mkdir myitcrm
cd myitcrm
git clone git://github.com/MyITCRM/myitcrm2.git
</pre>

### Create database.yml under config folder 


### Before you start using the app, run the below command first to pre-compile assets (images, js files, ext)

<pre>
bundle exec rake assets:precompile
</pre>

Restart nginx with @sudo /etc/init.d/nginx restart@
