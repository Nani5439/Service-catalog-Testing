# TESTING - ELASTICSEARCH SERVICE CATALOG
## What is Elasticsearch
lasticsearch is an open-source, distributed search and analytics engine built on top of Apache Lucene. It's designed for fast, scalable searching of large volumes of data.
## Objective
The objective is to deploy Elasticsearch in a Docker container on an AWS EC2 Ubuntu instance and monitor its performance using four tools: Connectivity Check, Query Latency Monitor, Cluster Health Monitor, and Indexing Rate Monitor. This helps ensure the service is running smoothly and issues are detected early.
##  Prerequisites
AWS EC2 instance – Ubuntu-based instance to host Elasticsearch.

Docker installed – Docker engine must be available to run Elasticsearch in a container.

Port 9200 open – Security group should allow inbound TCP traffic on port 9200 for HTTP access to Elasticsearch.

Public IP access – Required to access the Elasticsearch service from outside the EC2 instance.

Monitoring dashboard – Tools like Grafana or custom scripts to visualize and monitor Elasticsearch metrics.

# Implementation Steps 
## Step 1: Launch EC2 Instance
### Description :
Create a new EC2 instance using the AWS Console with the following settings:
AMI: Ubuntu Server (20.04 or 22.04)


Instance Type: t2.micro (for testing)


Key Pair: created a elasticsearch.pem key pair


Security Group: Allow 9200 and SSH(22).

![Elastic search ec2 instance created](https://github.com/user-attachments/assets/ac6e1967-44b2-42ec-82ce-592befbc5b5a)

## Step 2: Connect to EC2 Instance via SSH
### Description:
Once your EC2 instance is running, connect to it from your local machine using SSH and the downloaded key.
![elastic search instance connected](https://github.com/user-attachments/assets/daea0865-fe67-4fa5-a701-98a8f331e042)
## Step 3: Install Docker
### Commands:
### 1.sudo apt update:
```
1.apt update -y
```
Updates the local package index so Ubuntu knows the latest available versions of software. It’s always a good practice to run this before installing any new software.

### 2.sudo apt install docker.io -y
Installs Docker from Ubuntu’s official repositories. The -y flag automatically confirms “yes” to all prompts during installation.

### Verify Docker Installation:
### 3.docker --version
Confirms that Docker is successfully installed and shows the version number.
![ES docker version](https://github.com/user-attachments/assets/912cc6c5-66e9-44bd-bbb9-36858d50e496)

## Step 4: Create a Docker Network 
A Docker network is a virtual network that allows containers to communicate with each other and, optionally, with the outside world.
### 4.docker network create elastic
You can verify the network was created using:
### 5.docker network ls
## Step 5: Run Elasticsearch in Docker
### 6.docker run --name es01 --net elastic -p 9200:9200 -it docker.elastic.co/elasticsearch/elasticsearch:8.13.0
docker run : Starts a new Docker container.

--name es01 : Assigns the name es01 to the container for easy management and reference.

--net elastic : Connects the container to a custom Docker network named elastic, allowing communication with other containers in the same network.

-p 9200:9200 : Maps port 9200 on the host to port 9200 inside the container. This is Elasticsearch’s default HTTP port used for queries.

-it : Runs the container in interactive mode with a terminal. Useful for viewing logs or interacting with the container.

docker.elastic.co/elasticsearch/elasticsearch:8.13.0 : Specifies the official Elasticsearch Docker image and version 8.13.0 from Elastic's Docker registry.

## Verify Container is Running:
### 7.sudo docker ps
You should see the elasticsearch container listed with ports 9200.






