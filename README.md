AWS CI/CD Pipeline for a PHP Application

An automated end-to-end Continuous Integration and Continuous Deployment (CI/CD) pipeline for a PHP web application built using AWS CodeCommit, AWS CodeBuild, AWS CodeDeploy, and AWS CodePipeline. Deployments to Amazon EC2 are executed using CodeDeploy In-Place Deployment with the CodeDeployDefault.AllAtOnce strategy.

Project Overview

This repository demonstrates how to automate the build, packaging, and deployment of a PHP web application on AWS. Whenever changes are pushed to the main branch, AWS CodePipeline automatically triggers AWS CodeBuild to package the application using buildspec.yml and hands off the build artifact to AWS CodeDeploy to perform an In-Place (AllAtOnce) deployment onto an Amazon EC2 instance running Apache and PHP.

Pipeline Architecture

Developer Push (main)
         │
         ▼
┌───────────────────┐
│  AWS CodeCommit   │  Source Stage — hosts PHP source code, buildspec.yml & appspec.yml
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│   AWS CodeBuild   │  Build Stage — runs buildspec.yml & packages artifacts
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│     Amazon S3     │  Artifact Store — stores zipped build output
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│  AWS CodeDeploy   │  Deploy Stage — In-Place Deployment (AllAtOnce) via appspec.yml
└─────────┬─────────┘
          │
          ▼
┌───────────────────┐
│    Amazon EC2     │  Target Host — Apache + PHP serving index.php on HTTP (Port 80)
└───────────────────┘


Repository Structure

aws-cicd-php-pipeline/
├── index.php        # PHP web application entry point
├── buildspec.yml    # Build instructions and artifact packaging for AWS CodeBuild
├── appspec.yml      # Deployment mappings and lifecycle hooks for AWS CodeDeploy
├── .gitattributes   # Git line-ending and file attributes configuration
├── screenshots/     # Screenshots of successful Build and Deployment phases
└── README.md        # Project documentation


AWS Services & Configuration

Component

Configuration Details

Source Control

AWS CodeCommit (main branch)

Build Service

AWS CodeBuild (Managed Amazon Linux environment using buildspec.yml)

Deploy Service

AWS CodeDeploy (Compute Platform: EC2/On-premises)

Deployment Type

In-Place Deployment

Deployment Strategy

CodeDeployDefault.AllAtOnce (updates all targeted instances simultaneously)

Pipeline Orchestration

AWS CodePipeline (Source → Build → Deploy)

Target Compute

Amazon EC2 (Linux instance with Apache httpd, PHP, and CodeDeploy Agent)

Artifact Storage

Amazon S3 (managed by CodePipeline)

Identity & Access

IAM roles for EC2 (AmazonEC2RoleforAWSCodeDeploy), CodeDeploy (AWSCodeDeployRole), CodeBuild, and CodePipeline

Key Files Explained

index.php — The PHP application deployed to the Apache web root (/var/www/html) and served over HTTP.

buildspec.yml — Defines the build phases executed by AWS CodeBuild and declares the artifacts (index.php, appspec.yml, and any deployment scripts) packaged for CodeDeploy.

appspec.yml — Defines file source-to-destination mappings and lifecycle hooks used by the CodeDeploy agent during the In-Place deployment on the EC2 instance.

.gitattributes — Ensures consistent line endings across local machines, AWS CloudShell, and Linux build/deploy environments.

Setup & Deployment Workflow

Prerequisites

An AWS account with permissions to configure IAM, EC2, S3, CodeCommit, CodeBuild, CodeDeploy, and CodePipeline.

An Amazon EC2 Linux instance with:

Apache (httpd), PHP, and the AWS CodeDeploy Agent installed and running.

An attached IAM instance profile containing AmazonEC2RoleforAWSCodeDeploy.

A Security Group allowing inbound HTTP (Port 80) traffic.

An instance tag (e.g., Name=PHP-WebServer) matching the CodeDeploy deployment group.

Implementation Steps

IAM Roles: Created the EC2 instance role (AmazonEC2RoleforAWSCodeDeploy) and the CodeDeploy service role (AWSCodeDeployRole).

EC2 Target Server: Launched the EC2 instance with Apache, PHP, and the CodeDeploy agent running, and verified codedeploy-agent status.

CodeCommit Repository: Pushed index.php, buildspec.yml, appspec.yml, and .gitattributes to the main branch.

CodeBuild Project: Created a managed build project linked to the CodeCommit repository using buildspec.yml.

CodeDeploy Application & Deployment Group: Configured an EC2/On-premises application with an In-Place deployment group using the CodeDeployDefault.AllAtOnce deployment configuration targeting the tagged EC2 instance.

CodePipeline: Connected the Source (CodeCommit), Build (CodeBuild), and Deploy (CodeDeploy) stages and triggered an end-to-end execution.

Triggering Updates

git clone https://github.com/arunsthomas11/aws-cicd-php-pipeline.git
cd aws-cicd-php-pipeline
# Edit index.php
git add index.php
git commit -m "Update PHP application"
git push origin main


Once the pipeline completes all three stages, verify the live application in a browser:

http://<EC2-PUBLIC-IPV4>/


Verification Screenshots

1. AWS CodeBuild — Successful Build Phase

Confirming all CodeBuild phases (DOWNLOAD_SOURCE, INSTALL, PRE_BUILD, BUILD, POST_BUILD, UPLOAD_ARTIFACTS, COMPLETED) completed successfully.

2. AWS CodeDeploy — Successful Deployment Phase (In-Place / AllAtOnce)

Confirming the In-Place deployment completed with CodeDeployDefault.AllAtOnce and all lifecycle events succeeded on the target EC2 instance.

Author

Arun S Thomas

GitHub: @arunsthomas11
