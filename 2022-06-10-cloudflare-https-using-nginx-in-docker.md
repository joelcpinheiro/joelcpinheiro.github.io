---
title: CloudFlare HTTPS using Nginx in Docker
slug: cloudflare-https-using-nginx-in-docker
date_published: 2022-06-10T18:21:20.000Z
date_updated: 2023-04-07T15:49:43.000Z
tags: #Import 2023-04-07 15:04
---

Hello guys, my intention is to show you how is simple to create a Nginx docker container and use SSL out of your environment, with CloudFlare maintaining the SSL Certificate.‌‌‌‌

Firstly, you need to have these things before:‌
‌
1. Your domain hosted on CloudFlare DNS‌ and entry of A type with proxy status enabled to validate the SSL Cert produced by CloudFlare;
2. AWS Instance with [Docker ](https://docs.docker.com/get-docker/)and [Docker-Compose ](https://docs.docker.com/compose/install/)installed(I decided to use AWS in this example).‌‌‌‌

Let's go to the steps to configure your container using docker-compose, I suggest that you use this simple code:

    version: '3'
    
    services:
      webserver:
        image: nginx:latest
        ports:
          - 80:80
        restart: always
        volumes:
          - ./nginx/conf/:/etc/nginx/conf.d/:ro

Create this directory and the nginx domain configuration with this command:

    mkdir -p nginx/conf && vim nginx/conf/demo.conf

Use this simple configuration to resolve the URL:

    server {
       listen 80;
       server_name demo.joelpinheiro.com;
       server_tokens off;
       location / {
         root /usr/share/nginx/html;
        }
    }

After creating the file above, execute this command to up and running the nginx container:

    sudo docker-compose up -d

And the status of docker container:

    [centos@ip-PrivateIP nginx]$ sudo docker-compose ps
          Name                     Command               State         Ports       
    -------------------------------------------------------------------------------
    nginx_webserver_1   /docker-entrypoint.sh ngin ...   Up      0.0.0.0:80->80/tcp
    

Now, you can access the URL https://demo.joelpinheiro.com to see the nginx default page.

Note: I terminated my demo instance, because my free tier is over, sorry guys, if you have any doubt, feel free to reach me out.
