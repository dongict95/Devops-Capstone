# Click here >>> [Capstone-project-website](http://ab77f44a9a292446c834b4a85dd664a6-1888173711.us-east-1.elb.amazonaws.com:8000/)

# Udacity Cloud DevOps Capstone Project

This is final project of Udacity Cloud DevOps Engineer Nanodegree Program.

## Project Overview

In this project I have implemented all the knowledge that I have learnt from the Udacity Cloud DevOps Engineer Nanodegree program. In this project I have

-   Created Jenkins server using the AWS CloudFormation.
-   Installed all the needed tools in Jenkins master server using the Launch Configuration.
-   Created a infrastructure pipeline using Jenkins that creates one EKS cluster in AWS.
-   Created one application deployment pipeline that deploys the application docker container in the kubernetes cluster.
-   Used blue/green deployment strategy to deploy the application.

## Project Files

#### infrastructure

This folder all the files related to infrastructure deployment.

-   `jenkins-server-parameters.json`: Parameters file for cloud formation stack.
-   `jenkins-server.yml`: CloudFormation template for creating jenkins server.
-   `Jenkinsfile`: Jenkinsfile for creating EKS cluster and configuring kubectl.

#### kubernetes-resources

This folder contains all template files for Kubernetes resources.

-   `blue-replication-controller.yml`: A replication controller template that creates pods with label as `app=blue`.
-   `blue-service.yml`: A service template that selects all the pods with label as `app=blue`.
-   `green-replication-controller.yml`: A replication controller template that creates pods with label as `app=green`.
-   `green-service.yml`: A service template that selects all the pods with label as `app=green`.

#### screenshots

This folder contains all screenshots taken during creation of this project.

#### scripts

This folder contains script to create, delete and update CloudFormation stack.

#### Dockerfile

This is Dockerfile of application.

#### index.html

This is main html file of application.

#### Jenkinsfile

This file contains the steps of CICD pipeline of application.

## Project Setup

-   Create Jenkins server using cloud formation template.

```
$ sh ./create.sh jenkins-stack infrastructure/jenkins-server.yml infrastructure/jenkins-server-parameters.json
```

-   Following resources are created after executing above command:
    -   Security Group
    -   Launch Configuration
    -   Auto Scaling Group
-   Following tools are automatically installed in the server using Launch Configuration:
    -   Jenkins
    -   Docker
    -   AWS CLI
    -   eksctl CLI
    -   kubectl CLI
    -   Tidy
-   Once the stack creation is complete, access the Jenkins UI using `http://<EC2_PUBLIC_IP>:8080`
-   Login using admin account and complete the initial setup of Jenkins.
-   Install the following plugins in Jenkins:
    -   [CloudBees AWS Credentials](https://plugins.jenkins.io/aws-credentials/)
    -   [Pipeline: AWS Steps](https://plugins.jenkins.io/pipeline-aws/)
    -   [Blue Ocean](https://plugins.jenkins.io/blueocean/)
-   Add AWS credentials in Jenkins.
-   Create new item in Jenkins of type `Pipeline` of name let's say `infra-eks`
-   In the configuration page of `infra-eks`, provide the GitHub repository as `https://github.com/dongict95/Devops-Capstone` and script path as `infrastructure/Jenkinsfile`
-   Apply and save the pipeline.
-   Click on `Build Now` to trigger the pipeline.
-   `infra-eks` does the following steps:
    -   Creates a EKS cluster
    -   Configures kubectl so that we can connect to EKS cluster
    -   Note: This pipeline will take around 15-20 minutes to complete.
-   Add Docker Hub credentials in Jenkins so that we can push docker image to Docker Hub.
-   Create new item in Jenkins of type `Pipeline` of name let's say `deploy-eks`
-   In the configuration page of `deploy-eks`, provide the GitHub repository as `https://github.com/dongict95/Devops-Capstone` and script path as `Jenkinsfile`
-   Apply and save the pipeline.
-   Click on `Build Now` to trigger the pipeline
-   Once the pipeline passes with stage name `Create Service Pointing to Blue Replication Controller`, go to Load Balancer page in AWS console and look for DNS name
-   DNS name will be something like `ab77f44a9a292446c834b4a85dd664a6-1888173711.us-east-1.elb.amazonaws.com`
-   In the browser, open a new tab and hit link as `http://<DNS_NAME>:8000/`. It will show the capstone project website.
-   Approve the pipeline to proceed to next stage
-   Once the pipeline is complete, service will be pointing to pods that has label as `app=green`
