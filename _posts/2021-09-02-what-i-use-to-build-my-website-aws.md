---
title: How I set up this website on AWS(until nov/2021)
slug: what-i-use-to-build-my-website-aws
date_published: 2021-09-02T12:26:23.000Z
date_updated: 2023-04-07T15:52:43.000Z
tags: #Import 2023-04-07 15:04
---

Start from scratch, I'm using these tools to learn and improve my knowledge, that are:

Terraform;
AWS Elastic Compute Cloud(EC2);
Docker and Docker Compose;

I use this project to create my first EC2 on AWS: [https://github.com/joelcpinheiro/ec2_terraform.git](https://github.com/joelcpinheiro/ec2_terraform.git)

I've create my portfolio website project using Ghost CMS([https://ghost.org](https://ghost.org)) and do backup all files present on EC2 to a S3 Storage Service, the mysql data I use mysqldump and this project code can be view on this link [https://github.com/joelcpinheiro/pfoghostcms.git](https://github.com/joelcpinheiro/pfoghostcms.git), unfortunately the source code this website is private, because has some sensitive data like information about connection with database. 

I've created two docker containers, one with Nginx with Let's Encrypt through this docker-compose file and adjusting what that I need([https://github.com/wmnnd/nginx-certbot.git](https://github.com/wmnnd/nginx-certbot.git))
I'm using Ghost CMS, and I'm having two containers. I've use another docker-compose file that has on the same network with the Nginx container and Let's Encrypt too.

The simple topology was development how I describe bellow:
![](__GHOST_URL__/content/images/2023/04/ec2-cloud-aws.png)
I'll create another Environment using AWS ECS(Amazon Elastic Container Service) and migrate or send to Digital Ocean(more cheap than AWS, rs).
