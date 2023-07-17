---
title: AWS - How to set up securely your access to instances via VPN Client
slug: aws-how-to-setup-access-to-the-cloud
date_published: 1970-01-01T00:00:00.000Z
date_updated: 2023-02-17T11:29:18.000Z
tags: #Import 2023-04-07 15:04
draft: true
---

First, I'll try to talk about my experience using AWS Services, how I though is so important, that have a VM called bastion to help with the access.
The important thing to known is, you need to have an account with programmatic access to use this terraform code...

terraform code...

Steps:
1. Create a new certificate on ACM(AWS Certificate Manager);
Create a Root CA;
RSA 2048;

2. Associate the VPN to one VPC that could be the management VPC;
3. Download the VPN client configuration;
4.  
