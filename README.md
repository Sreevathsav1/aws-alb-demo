# AWS ALB Demo — EC2 + Application Load Balancer

## Overview
Two EC2 instances running Apache web servers, load balanced by an AWS Application Load Balancer (ALB). Includes a failover test showing the ALB reroutes traffic when one instance goes down.

## Architecture
- 2x EC2 instances (Amazon Linux 2023, t3.micro)
- 1x Application Load Balancer (Internet-facing)
- 1x Target Group with health checks
- Security groups isolating EC2 from public traffic

## Setup Steps

### 1. Launch EC2 Instances
- AMI: Amazon Linux 2023
- Type: t3.micro
- Region: us-east-1
- Server-1 in us-east-1a, Server-2 in us-east-1b
- Used User Data scripts (see /scripts folder) to auto-install Apache

### 2. Configure Security Groups
- ec2-web-sg: allows SSH from My IP, HTTP only from ALB security group
- alb-sg: allows HTTP from 0.0.0.0/0 (public internet)

### 3. Create Target Group
- Name: alb-demo-tg
- Health check path: /
- Both instances registered on port 80

### 4. Create ALB
- Name: alb-demo
- Internet-facing, spanning us-east-1a and us-east-1b
- Listener: HTTP 80 → alb-demo-tg

## ALB DNS URL
http://alb-demo-1133985074.us-east-1.elb.amazonaws.com

## Reliability Test
Stopped Server-1 → ALB detected unhealthy after ~60 seconds → All traffic routed to Server-2 automatically.

## Scripts
- scripts/server1-userdata.sh — Bootstrap script for Server 1
- scripts/server2-userdata.sh — Bootstrap script for Server 2
