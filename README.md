# DevOps-Project
A Cloud Project by Raihan and Will

## Contents
1. Brief
2. Links
3. Planning
4. Risk Assessment
5. AWS Setup
6. Docker
7. Docker Compose
8. Docker Swarm
9. Jenkins
10. Reflections
11. Future Goals

## 1. Brief
We were given a simple Flask application consisting of a frontend, backend and database. Our task was to deploy the application to the cloud so that it could be accessed by an end user via the internet. 
The main requirements of the task were:
* The application was deployed to a virtual machine
* We made use of a managed database service
* The application must be fully tested

## 2. Links
**Jira board: ** 
**GitHub repo: **
**Presentation: **

## 3. Planning
We created a Jira board to track the project and populated it with some initial tasks.

We created a GitHub repo and uploaded the application files we were given for the project.

We created a series of diagrams to help us visualise the application deployment, these also helped us plan how to structure our AWS environment to run the application.

## 4. Risk Assessment
We created a risk assessment to outline some of the risks involved with the project and assess their potential impacts. We first brainstormed and created an initial draft of the risk assessment.

During the project we held a meeting to revise the risk assessment and added some new risks to the assessment which we hadn't originally thought of.


## 5. AWS Setup
We created a custom VPC on AWS to form the core of our project before creating custom subnets, a custom route table and a custom internet gateway.

We created an RDS instance to handle our database. We created a custom security group for the database to allow access within our VPC on port 3306

We created two EC2 instances to run our application with the intention being to run them as a Docker Swarm. We created another custom security group for our EC2 instances to allow access via SSH and HTTP and to allow traffic through ports 2237, 4789, 7946 and 8080.

We installed Docker, MySQL and Python onto our main EC2 instance and Docker onto our secondary instance.

## 6. Docker
We created Dockerfiles for both the frontend and backend parts of the application before building our docker images for our frontend, backend and database. We created an nginx.conf file and another image for to create an nginx instance which would allow us to access into our application via the internet using a reverse-proxy.

We created containers for our Docker images and tested that we could access our application via the web.

## 7. Docker Compose
We installed Docker compose onto our main EC2 instance before writing a "docker-compose.yaml" file to allow us to build our images and deploy our containers all from one command.

In an effort to add some security to this file we made sure to use environmental variables to pass any sensitive or changeable information into this file such as passwords, this meant that this information wasn't stored in a file on our EC2 instance.

## 8. Docker Swarm
We initialised our first EC2 instance as our Docker Swarm manager and added our second instance as a worker node before deploying our application in a stack with multiple replicas of each container or both instances.

The two nodes would allow our system to handle larger amounts of traffic without issue and having multiple replicas gave us some redundancy in case we encountered any problems as well as allowing us to keep the application up even during updates.

## 9. Jenkins
We installed Jenkins onto our main EC2 instance and created a pipeline job which would automate the deployment our application. The job would build the images we had designed earlier, run unit tests of the python running our application, push our up-to-date images to DockerHub and finally deploy our stack to get the application up and running.

The instructions for this Jenkins job are stored in a Jenkinsfile which we created and added to our GitHub repo. The Jenkins job has also been setup to trigger a redeployment whenever the main branch of our GitHub repo is updated. As mentioned earlier the use of replicas in our swarm allows Jenkins to update the replicas one by one so that the application is not taken fully offline.

## 10. Reflections
We initially setup our EC2 instances and RDS instance using AWS' default VPC in order to test the functionality of our application from a known-good starting point. Once we had got our application running and were at a good point to make the change we setup a custom VPC (and subnets, etc) and recreated our EC2 and RDS instances before reinstalling  our application and making sure everything still worked.

Due to us using the default VPC we were actually able to avoid a common issue that other groups encountered where their VPC CIDR block overlapped with the CIDR block Docker Swarm wanted to use, preventing Docker Swarm from being used.

## 11. Future Goals
We could have implemented autoscaling rules into our AWS environment that could spin-up other swarm workers as traffic demanded.

We could have added another manager node to increase the reliability of our system.

We could have used Terraform to setup our infrastructure using rather than manually setting up new instances.
