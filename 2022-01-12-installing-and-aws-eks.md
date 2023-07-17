---
title: AWS - Setting up my first EKS
slug: installing-and-aws-eks
date_published: 2022-01-12T18:01:36.000Z
date_updated: 2022-01-20T12:14:43.000Z
tags: #Import 2023-04-07 15:04
draft: true
---

We'll see these steps to creating a new environment on hands on way:

Pros:

AWS manages all the Control Plane of Kubernetes and I feel more comfortable with this;

Cons:

Actually running without High Availability;
I'm not using Terraform(Hashicorp) tool to provision all the Infrastructure of EKS according to the IAC mode;

Topology:
1 EKS Control Plane + 3 m5.large for workers 

1.Create new EC2 to use as a Bootstrap
2.Update AWS CLI to version 2(and execute aws configure using your credentials)
3.Install EKSCTL; 
4.Create Cluster using EKSCTL;
5.Configuring Flux CI/CD;
6.Creating Prometheus Monitoring with Grafana;
