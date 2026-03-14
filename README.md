# Containerized Application Deployment with Monitoring & Auto Scaling on AWS

This project demonstrates how to deploy a containerized application on AWS using ECS Fargate with monitoring, alerting and auto scaling.

## Architecture

![Architecture](architecture/architecture-diagram.png)

## Application Flow

User  
→ Application Load Balancer  
→ Target Group  
→ ECS Service  
→ Fargate Task  
→ Docker Container  

## Monitoring Flow

ECS Service  
→ CloudWatch Metrics  
→ CloudWatch Alarms  
→ SNS  
→ Email  

## Logging Flow

Application Load Balancer  
→ S3 Bucket (ALB Access Logs)

## Services Used

Amazon ECS (Fargate)  
Amazon ECR  
Application Load Balancer  
Amazon CloudWatch  
Amazon SNS  
Amazon S3  
Docker

## Screenshots

Docker Image Build

![Docker](screenshots/1-docker-image-built.png)

ECR Repository

![ECR](screenshots/2-ecr-repository-image.png)

ECS Cluster

![ECS](screenshots/3-ecs-cluster-overview.png)

ECS Task Definition

![Task](screenshots/4-ecs-task-definition.png)

Running ECS Service

![Service](screenshots/5-ecs-service-running.png)

Application Load Balancer

![ALB](screenshots/6-alb-overview.png)

Target Group Healthy

![Target](screenshots/7-target-group-healthy.png)

Application Running

![App](screenshots/8-application-running-browser.png)

CloudWatch Dashboard

![Dashboard](screenshots/9-cloudwatch-dashboard.png)

CloudWatch Alarm

![Alarm](screenshots/10-cloudwatch-alarm.png)

SNS Topic

![SNS](screenshots/11-sns-topic.png)

Auto Scaling Policy

![AutoScaling](screenshots/12-ecs-auto-scaling-policy.png)

ALB Logs in S3

![Logs](screenshots/13-s3-alb-access-logs.png)

## Things I learned while building this

Containerizing applications using Docker  
Running containers with ECS Fargate  
Implementing load balancing using ALB  
Monitoring infrastructure using CloudWatch  
Sending alerts using SNS  
Configuring ECS Auto Scaling  
Storing ALB logs in Amazon S3
