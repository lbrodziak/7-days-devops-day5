# Deploy a Web App with CodeDeploy

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-devops-codedeploy-updated)

**Author:** Łukasz Brodziak  
**Email:** lukasz.brodziak@gmail.com

---

![Image](http://learn.nextwork.org/surprised_maroon_fierce_chinese_gooseberry/uploads/aws-devops-codedeploy-updated_val-27)

---

## Introducing Today's Project!

In this project I will create the deployment part of the CI/CD pipeline. To achieve this I will use AWS CodeDeploy. Also I will create an EC2 instance that will be used to host the web app. To create the EC2 instance I will use CloudFormation which is AWS Infrastructure as Code (IaC) service.

### Key tools and concepts

I used AWS CodeDeploy, EC2, S3, IAM, and CodePipeline. I learned how to automate
deployments, manage revision locations, configure appspec.yml, and verify web apps
through public IPs.

### Project reflection

This project took me approximately 2 hours.

This project is part five of a series of DevOps projects where I'm building a CI/CD pipeline!

---

## Deployment Environment

To set up for CodeDeploy, I launched an EC2 instance and VPC to create a live production environment with secure, isolated network settings needed for app deployment.

Instead of launching resources manually, I used AWS CloudFormation to deploy my EC2 instance and VPC. This allows version-controlled, repeatable infrastructure
creation and easy cleanup.

Other resources created in this template are a VPC, subnet, route table, internet
gateway, security group, and EC2 instance to build a complete and secure network for
the web app deployment.

![Image](http://learn.nextwork.org/surprised_maroon_fierce_chinese_gooseberry/uploads/aws-devops-codedeploy-updated_val-5)

---

## Deployment Scripts

Scripts are shell files used to automate tasks such as installing dependencies (Tomcat,
Apache), starting/stopping services, and setting up configurations. To set up
CodeDeploy, I also wrote scripts to help deploy the application correctly.

The install_dependencies.sh script installs Tomcat and Apache, sets up reverse proxy
configuration so Apache forwards traffic to Tomcat, and prepares the EC2 instance to
host the web application. It ensures the server is ready for deployment.

The start_server.sh will start both Tomcat (our Java application server) & Apache (our
web server), and make sure they are set to automatically restart if EC2 instance
reboots. This ensures our web application remains available even after restart.

The stop_server.sh will stop both the Tomcat (Java app.server) & Apache (web server)
services. This is useful for shutting down the application safely before
deployments/maintenance, ensuring that no requests are being served during those
operations.

---

## appspec.yml

The appspec.yml is a configuration file used by AWS CodeDeploy to tell it exactly how
to deploy an application. It defines: Which files to copy and where to place them on
the EC2 instance. Which scripts to run during each deployment phase.

I updated buildspec.yml because AWS CodeBuild needs to know which files to include
in the final deployment package. I added the appspec.yml and scripts/ directory under
the artifacts section, so CodeDeploy will know how to run the deployment & manage

![Image](http://learn.nextwork.org/surprised_maroon_fierce_chinese_gooseberry/uploads/aws-devops-codedeploy-updated_val-12)

---

## Setting Up CodeDeploy

A CodeDeploy application defines what to deploy (the app and its files). A deployment
group defines how and where to deploy it, including target instances, settings, and
triggers for that application.

To set up a deployment group, you also need to create an IAM role to so it has the
necessary permissions to interact with other AWS services during the deployment
process.This role ensures CodeDeploy can perform only the actions it needs.

Tags help CodeDeploy identify the right EC2 instances for deployment. I used the tag
role=webserver to automatically target the correct instance, making the deployment
process easier, automated, and more accurate.

![Image](http://learn.nextwork.org/surprised_maroon_fierce_chinese_gooseberry/uploads/aws-devops-codedeploy-updated_val-18)

---

## Deployment configurations

Another key setting is the deployment configuration, which affects how updates are
rolled out. I used CodeDeployDefault.AllAtOnce, so all instances are updated at the
same time. Other options include OneAtATime and HalfAtATime for safer rollouts.

In order to connect and deploy apps to EC2, a CodeDeploy Agent is also set up on the
instance. It listens for deployment instructions from CodeDeploy and runs the scripts
defined in appspec.yml to install, start, or stop the application.

![Image](http://learn.nextwork.org/surprised_maroon_fierce_chinese_gooseberry/uploads/aws-devops-codedeploy-updated_val-20)

---

## Success!

A CodeDeploy deployment is a single update to an app with a unique ID. It defines
what to deploy (revision), where (deployment group), and how (settings). It differs
from a group, which defines the target instances and config for deployment.

I had to configure a revision location, which means CodeDeploy knows where to find
the app files. My revision location was an S3 bucket with a .zip file containing the build
artifact to deploy to EC2 instances.

To check that the deployment was a success, I visited the EC2 instance's public IP
using HTTP in my browser. I saw the "Hello enter user!" message, which confirmed that
my web application deployed and ran correctly.

![Image](http://learn.nextwork.org/surprised_maroon_fierce_chinese_gooseberry/uploads/aws-devops-codedeploy-updated_val-27)

---

## Disaster Recovery

In a project extension, I decided to add an error to the app. The intentional error I created was an incorrect call in stop_server.sh script. This will cause the deployment to fail because it will result in command not found error.

I also enabled rollbacks with this deployment, which means that if deployment of new version fails the previous version is restored. This minimises the downtime of the application.

---
## Here is link to day 6 where I deploy infrastructure using CloudFormation 
[7 days DevOps challenge day 6](https://github.com/lbrodziak/7-days-devops-day6)
