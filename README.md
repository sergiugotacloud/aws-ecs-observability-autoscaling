# Containerized Application Deployment with Observability & Auto Scaling on AWS
### Docker · ECR · ECS Fargate · CloudWatch · SNS · Auto Scaling

[![AWS](https://img.shields.io/badge/AWS-Container-orange?logo=amazon-aws)](https://aws.amazon.com/)
[![Docker](https://img.shields.io/badge/Docker-Containerized-2496ED?logo=docker)](https://www.docker.com/)
[![ECR](https://img.shields.io/badge/Amazon-ECR-FF9900?logo=amazon-aws)](https://aws.amazon.com/ecr/)
[![ECS](https://img.shields.io/badge/Amazon-ECS%20Fargate-yellow?logo=amazon-aws)](https://aws.amazon.com/ecs/)
[![CloudWatch](https://img.shields.io/badge/Amazon-CloudWatch-FF4F8B?logo=amazon-aws)](https://aws.amazon.com/cloudwatch/)
[![SNS](https://img.shields.io/badge/Amazon-SNS-FF4F8B?logo=amazon-aws)](https://aws.amazon.com/sns/)

---

## Overview

This project demonstrates how to deploy a containerised web application on AWS using Amazon ECS Fargate while implementing a full observability and auto scaling stack around it.

A simple web application is packaged into a Docker container, pushed to Amazon ECR, and deployed to ECS Fargate behind an Application Load Balancer. The system includes monitoring through CloudWatch dashboards and alarms, alerting via Amazon SNS email notifications, automatic scaling based on CPU utilisation, and ALB access log archival to S3. AWS resources are deployed in the **eu-central-1 region**.

This project simulates a production-style container deployment architecture used by modern cloud engineering teams.

---

## Architecture

![Architecture Diagram](architecture/14-architecture-diagram.png)

```
Developer (Local Machine)
      │
      │  Docker Build + Push
      ▼
Amazon ECR  ── Private container registry stores the application image
      │
      ▼
Amazon ECS Cluster  ── Task Definition references the ECR image
      │
      ▼
ECS Service (Fargate)  ── Launches and manages container tasks
      │
      ├── Auto Scaling Policy  ── Scales task count based on CPU utilisation
      │
      ▼
Target Group  ── Health checks and routes traffic to healthy tasks
      │
      ▼
Application Load Balancer  ── Exposes application publicly, distributes traffic
      │                  └── Access Logs ──▶ Amazon S3
      ▼
End Users (Browser)

ECS Service ──▶ CloudWatch Metrics ──▶ CloudWatch Dashboard
                                   └──▶ CloudWatch Alarms ──▶ Amazon SNS ──▶ Email Notifications
```

---

## Services Used

| Service | Role |
|---|---|
| **Docker** | Packages the web application into a portable container image |
| **Amazon ECR** | Private container registry for storing and versioning images |
| **Amazon ECS Fargate** | Serverless container orchestration — runs tasks without managing servers |
| **Application Load Balancer** | Distributes inbound HTTP traffic across healthy Fargate tasks |
| **Amazon CloudWatch** | Dashboards, metrics, and alarms for full system observability |
| **Amazon SNS** | Delivers email notifications when CloudWatch alarms trigger |
| **ECS Service Auto Scaling** | Scales task count up and down based on CPU utilisation thresholds |
| **Amazon S3** | Archives ALB access logs for auditing and traffic analysis |
| **AWS IAM** | Grants each service least-privilege access to the resources it needs |

---

## Application Flow

1. The application is containerised using Docker and the image is pushed to Amazon ECR
2. An ECS Task Definition references the ECR image URI along with port mappings and resource limits
3. An ECS Service launches the desired number of tasks on Fargate using the Task Definition
4. Each task registers itself with the ALB Target Group and begins serving traffic once health checks pass
5. The Application Load Balancer routes inbound HTTP requests across all healthy tasks
6. ECS publishes CPU and memory metrics to CloudWatch continuously
7. CloudWatch dashboards visualise request count, response time, and resource utilisation in real time
8. CloudWatch alarms monitor defined thresholds and publish to the SNS topic when breached
9. SNS delivers email notifications to all confirmed subscribers when an alarm fires
10. The Auto Scaling policy adjusts the running task count in response to CPU utilisation changes
11. ALB access logs are written to an S3 bucket for auditing and post-hoc traffic analysis

---

## Project Structure

```
aws-ecs-observability-autoscaling/
│
├── architecture/
│   └── 14-architecture-diagram.png              # End-to-end architecture overview
│
├── app/
│   ├── Dockerfile                               # Container image build instructions
│   └── index.html                               # Static web application source
│
├── screenshots/
│   ├── 1-docker-image-built.png                 # Docker image built locally
│   ├── 2-ecr-repository-image.png               # Image pushed to ECR repository
│   ├── 3-ecs-cluster-overview.png               # ECS cluster configuration
│   ├── 4-ecs-task-definition.png                # Task Definition with image URI and port mapping
│   ├── 5-ecs-service-running.png                # ECS Service with tasks in running state
│   ├── 6-alb-overview.png                       # Application Load Balancer overview
│   ├── 7-target-group-healthy.png               # Target Group with all targets healthy
│   ├── 8-application-running-browser.png        # Live application via ALB endpoint
│   ├── 9-cloudwatch-dashboard.png               # CloudWatch monitoring dashboard
│   ├── 10-cloudwatch-alarm.png                  # CloudWatch alarm configuration
│   ├── 11-sns-topic.png                         # SNS topic for alarm notifications
│   ├── 12-ecs-auto-scaling-policy.png           # ECS Service Auto Scaling policy
│   └── 13-s3-alb-access-logs.png               # ALB access logs stored in S3
│
├── README.md
└── LICENSE
```

---

## Screenshots

### 1. Docker Image Built
*Docker image successfully built locally from the application Dockerfile, ready for ECR.*

![Docker](screenshots/1-docker-image-built.png)

---

### 2. Docker Image in Amazon ECR
*Container image pushed to the ECR private repository and available for ECS to pull during deployment.*

![ECR](screenshots/2-ecr-repository-image.png)

---

### 3. ECS Cluster
*ECS cluster provisioned in eu-central-1 as the deployment environment for Fargate tasks.*

![Cluster](screenshots/3-ecs-cluster-overview.png)

---

### 4. ECS Task Definition
*Task Definition configured with the ECR image URI, container port mapping, and Fargate launch type.*

![Task Definition](screenshots/4-ecs-task-definition.png)

---

### 5. ECS Service Running
*ECS Service showing the desired task count active with all tasks reporting a running state.*

![Service](screenshots/5-ecs-service-running.png)

---

### 6. Application Load Balancer
*ALB provisioned and active, configured to forward inbound HTTP traffic to the ECS Target Group.*

![ALB](screenshots/6-alb-overview.png)

---

### 7. Target Group Healthy
*All registered Fargate tasks passing health checks and showing as healthy in the Target Group.*

![Target](screenshots/7-target-group-healthy.png)

---

### 8. Live Application via ALB
*Web application accessible through the ALB public DNS endpoint — end-to-end deployment confirmed.*

![Application](screenshots/8-application-running-browser.png)

---

### 9. CloudWatch Monitoring Dashboard
*Dashboard visualising ECS CPU utilisation, ALB request count, and target response time in real time.*

![Dashboard](screenshots/9-cloudwatch-dashboard.png)

---

### 10. CloudWatch Alarm
*CloudWatch alarm configured to trigger when CPU utilisation exceeds the defined threshold.*

![Alarm](screenshots/10-cloudwatch-alarm.png)

---

### 11. SNS Topic for Notifications
*SNS topic with a confirmed email subscription receiving alarm notifications from CloudWatch.*

![SNS](screenshots/11-sns-topic.png)

---

### 12. ECS Auto Scaling Policy
*Service Auto Scaling policy configured to scale task count based on CPU utilisation.*

![AutoScaling](screenshots/12-ecs-auto-scaling-policy.png)

---

### 13. ALB Access Logs in S3
*ALB access logs successfully archived to S3 for auditing and traffic analysis.*

![Logs](screenshots/13-s3-alb-access-logs.png)

---

## Troubleshooting

### 1. ECS Tasks Failing to Start Due to Incorrect ECR Image URI

After creating the ECS Task Definition and deploying the service, every task was failing immediately on launch. The ECS console reported a task stopped event with a container error, and no application traffic was being served. The ECR repository and image were both present and the push had completed without errors.

The root cause was a malformed image URI in the Task Definition. The ECR URI follows the format `<account-id>.dkr.ecr.<region>.amazonaws.com/<repository>:<tag>` — a single incorrect character in the account ID, or using the wrong region, causes ECS to silently fail to pull the image. In this case the region in the URI didn't match the region the repository was deployed in, so ECS was attempting to pull from a non-existent endpoint.

**Fix:** Copied the image URI directly from the ECR console rather than typing it manually, and created a new Task Definition revision referencing the corrected URI. Redeployed the ECS Service to use the new revision and confirmed tasks moved to a running state.

**Lesson:** Always copy ECR image URIs directly from the AWS console — never type them by hand. A single character error produces the same symptom as a missing image or an IAM permissions failure, which makes the root cause harder to isolate. When tasks fail to start, checking the stopped task's status reason in the ECS console gives the exact pull error and saves significant debugging time.

---

### 2. Target Group Health Checks Failing After Deployment

With tasks launching successfully, the Target Group was still reporting all targets as unhealthy. The ECS Service was cycling tasks continuously, each one launching, failing the health check, and being replaced. The ALB was returning 502 errors to the browser.

The issue was a port mismatch between three configuration points that all need to agree: the container port in the Task Definition, the target group port, and the port the application was actually listening on inside the container. The Task Definition had the container mapped to port 8080, but the NGINX configuration inside the image was binding to port 80. The health check was probing port 8080, receiving no response, and marking every target unhealthy.

**Fix:** Updated the Task Definition container port mapping to port 80 to match what NGINX was binding to, and confirmed the Target Group health check port was set to `traffic port` so it would inherit the correct value automatically. Redeployed the service and confirmed targets moved to healthy within two check intervals.

**Lesson:** Container port mapping, Target Group port, and the application's actual bind port must all be consistent. When health checks fail immediately after deployment, verify all three before investigating anything else — it is the most common cause of this symptom and takes seconds to check.

---

### 3. CloudWatch Dashboard Showing No Metrics After Configuration

After setting up the CloudWatch dashboard and adding widgets for ECS CPU utilisation, ALB request count, and target response time, all widgets were empty. No data points appeared even after waiting several minutes, and the alarm was sitting in an insufficient data state.

The root cause was simply a lack of traffic. CloudWatch only generates ALB metrics like `RequestCount` and `TargetResponseTime` when requests are actually being processed. With no traffic hitting the load balancer after deployment, there were no data points to plot, and the dashboard appeared broken even though the configuration was correct.

**Fix:** Generated traffic manually by repeatedly refreshing the ALB endpoint in the browser, then used a simple shell loop to send a burst of requests to produce a visible data set. Metrics appeared in the dashboard within the first CloudWatch reporting period, and the alarm moved out of insufficient data state once enough data points had been collected.

**Lesson:** An empty CloudWatch dashboard is not the same as a misconfigured one. Always send traffic to the application before concluding that metrics are missing. For alarm testing, temporarily lower the threshold to a value that normal background traffic will exceed, confirm the alarm fires and the SNS notification is delivered, then restore the production threshold.

---

### 4. Auto Scaling Not Triggering and SNS Notifications Not Arriving

Two separate issues presented together during end-to-end testing of the observability stack. The CloudWatch alarm was configured and the SNS topic was set up, but under simulated load the ECS Service was not scaling out, and no email notifications were arriving even when the alarm appeared to be in an alarm state.

The auto scaling issue stemmed from the CPU threshold being set too high relative to what the test load could actually generate. The scaling policy was configured to trigger at 70% CPU utilisation, but the lightweight static application was consuming under 5% even under sustained request volume, so the threshold was never crossed and the policy never activated.

The notification issue was a separate and unrelated problem: the SNS email subscription was in a `PendingConfirmation` state. AWS requires the recipient to click a confirmation link in the initial subscription email before any messages are delivered. The subscription had been created but the confirmation email had not been acted on, so every alarm notification was being published to the topic and silently discarded.

**Fix:** Reduced the auto scaling CPU threshold temporarily to 10% and reran the load test, confirming the policy triggered correctly and tasks scaled out as expected. Then restored the threshold to a production-appropriate value. For SNS, located the original confirmation email and clicked the confirmation link, after which the next alarm notification arrived within seconds.

**Lesson:** Test auto scaling and alerting configurations at thresholds your test environment can actually reach — a policy that is correctly configured but never triggered looks identical to one that is broken. For SNS, always confirm the subscription immediately after creating it, before testing the full alerting chain. Both of these issues produce no errors and no logs; the only signal is the absence of the expected outcome.

---

## What I Learned

This project demonstrated that deploying a container is only half the work — the other half is building the operational layer that makes the deployment trustworthy in production.

**Observability is a design decision, not an afterthought.** CloudWatch dashboards and alarms don't configure themselves. Deciding which metrics matter, what thresholds make sense, and who gets notified when something goes wrong requires thinking about how the system will fail before it does. Setting up the monitoring stack before generating any real traffic meant that the first anomaly would have been caught immediately, rather than discovered through user reports.

**Auto scaling requires realistic threshold calibration.** It is easy to configure a scaling policy and assume it works. Validating it requires generating load that actually crosses the threshold, observing the scaling event in the ECS console, and confirming that tasks are added and removed as expected. A policy that is never triggered during testing gives false confidence. The discipline of testing at realistic thresholds — and temporarily lowering them when the test environment can't match production load — is what separates a configured policy from a validated one.

**Multi-layer architectures multiply the number of places a permission can be missing.** Between ECR, ECS, the ALB, CloudWatch, SNS, S3, and IAM, every service boundary requires at least one explicit permission grant. The ECS task execution role needs ECR pull rights; the ALB needs an S3 bucket policy to write access logs; SNS needs confirmed subscriptions to deliver notifications. Building the habit of checking resource policies and execution roles at each layer — rather than assuming permissions flow through automatically — makes debugging significantly faster.

**Structured logging and access log archival pay dividends immediately.** Storing ALB access logs in S3 from the start meant that every request — including the ones that returned 502s during the health check debugging — was permanently recorded. Being able to query the raw access logs to see exactly which paths were being probed, what status codes were returned, and at what timestamps made the health check failure straightforward to diagnose. Observability infrastructure that is in place before the first failure is worth far more than infrastructure added in response to one.

---

## Author

**Sergiu Gota**
AWS Certified Solutions Architect – Associate · AWS Cloud Practitioner

[![GitHub](https://img.shields.io/badge/GitHub-sergiugotacloud-181717?logo=github)](https://github.com/sergiugotacloud)
[![LinkedIn](https://img.shields.io/badge/LinkedIn-sergiu--gota--cloud-0A66C2?logo=linkedin)](https://linkedin.com/in/sergiu-gota-cloud)

> Built as part of a cloud portfolio to demonstrate production-style container deployment with full observability on AWS.
> Feel free to fork, adapt, or reach out with questions.
