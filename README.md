# TESTING - ELASTICSEARCH SERVICE CATALOG
## What is Elasticsearch
Elasticsearch is an open-source, distributed search and analytics engine built on top of Apache Lucene. It's designed for fast, scalable searching of large volumes of data.
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


Instance Type: t3.medium (for testing)


Key Pair: created a elasticsearch.pem key pair


Security Group: Allow 9200 and SSH(22).

![Elastic search ec2 instance created](https://github.com/user-attachments/assets/ac6e1967-44b2-42ec-82ce-592befbc5b5a)

## Step 2: Connect to EC2 Instance via SSH
### Description:
Once your EC2 instance is running, connect to it from your local machine using SSH and the downloaded key.
![elastic search instance connected](https://github.com/user-attachments/assets/daea0865-fe67-4fa5-a701-98a8f331e042)

## Step 3: Install Docker
Update Package List : Updates the package index to the latest versions available.
```
sudo apt update
```
Installs Docker from Ubuntu's official package repositories. The -y flag auto-confirms prompts.
```
sudo apt install docker.io -y
```
Ensures Docker is running and will start on boot.
```
sudo systemctl start docker
sudo systemctl enable docker
```
## step 4:Pull the Official Elasticsearch Docker Image
Downloads the latest stable version (replace 8.13.4 with the version you want).
```
docker pull docker.elastic.co/elasticsearch/elasticsearch:8.13.4
```
## step 5: Create docker network
Docker network is used to communicate with containers.
```
docker network create elastic
```
## step 6: Run Elasticsearch Container
```
docker run --name es01 --net elastic -p 9200:9200 -it  docker.elastic.co/elasticsearch/elasticsearch:8.13.4
```
## step 7: login password for Elasticsearch
![ES password](https://github.com/user-attachments/assets/b5c650cf-19a4-477e-b40b-0b4a8ba2fb0c)

## step 8: Login to Elasticsearch
When you run Elasticsearch Docker container, security is enabled by default. The first-time password for the built-in elastic user is generated automatically.

![ES login page](https://github.com/user-attachments/assets/529bdef8-6568-4fc8-89b5-72761f11eb27)

## Step 9: Verify Access to Elasticsearch
Expected output:
![ES dashboard](https://github.com/user-attachments/assets/cc592e37-9a80-4ee7-81d5-91666893f809)

## Monitoring Elasticsearch
Monitoring ensures your Elasticsearch service is available, performant, and healthy. Below are 4 key monitoring tools you can implement using Python scripts.

## 1.Indexing Rate Monitor
Tracks the rate at which documents are indexed into Elasticsearch. A sudden drop in indexing rate may indicate ingestion pipeline issues or overloaded nodes.

Script Name: elasticsearch_indexing_rate.py

Elasticsearch URL: http://34.226.143.141:9200

Username: elastic

Password: mCCEycn1Xe+n5FN5TW1R

Frequency: Every 900 seconds (15 minutes)

Timeout: 120 seconds

Threshold: Monitoring-only (alerts if indexing stalls or remains unchanged over time)

Exit Codes:

0 – Success (indexing active)

1 – Failure (indexing not increasing, or request error)

Current Status:
✅ Running successfully. Indexing activity is healthy, and document counts are progressing as expected over each check interval.

![ES index rate](https://github.com/user-attachments/assets/57c187e4-7353-4012-b2d9-0cf4f9862697)

## 2. Cluster Health Monitor
Checks the overall health of the Elasticsearch cluster (green, yellow, or red). A yellow or red status could point to unassigned shards, node failures, or replication issues.

Script Name: elasticsearch_cluster_health.py

Elasticsearch URL: http://34.226.143.141:9200

Frequency: 900 seconds

Timeout: 120 seconds

Username: elastic

Password: mCCEycn1Xe+n5FN5TW1R

Exit Codes:

0 – Success (cluster is green)

1 – Failure (cluster is yellow, red, or inaccessible)

Current Status:
✅ Running successfully. Cluster is in green state with all shards active and healthy.

![ES cluster](https://github.com/user-attachments/assets/ba466590-bb63-4aa6-94f2-895b6202acd6)

## 3.Query Latency Monitor
Measures the time taken for search queries to execute. Elevated query latency may suggest inefficient queries, overloaded nodes, or data distribution imbalance.

Script Name: elasticsearch_latency_monitor.py

Elasticsearch URL: http://34.226.143.141:9200

Frequency: 900 seconds

Timeout: 120 seconds

Username: elastic

Password: mCCEycn1Xe+n5FN5TW1R

Exit Codes:

0 – Success (response within latency threshold)

1 – Failure (latency too high or no response)

Current Status:
✅ Running successfully. Latency remains within defined threshold limits.

![ES query](https://github.com/user-attachments/assets/b8878f2e-ab44-4369-8c3d-e8565bc778d1)

## 4.Connectivity Check
Confirms that the Elasticsearch service is reachable and responsive to HTTP requests. This check ensures the API is accessible and the service is not down or unresponsive.

Script Name: elasticsearch_connectivity_check.py

Elasticsearch URL: http://34.226.143.141:9200

Frequency: 900 seconds

Timeout: 120 seconds

Username: elastic

Password: mCCEycn1Xe+n5FN5TW1R

Exit Codes:

0 – Success (reachable)

1 – Failure (not reachable or error)

Current Status:
✅ Running successfully. Elasticsearch is reachable and responding with HTTP 200.

![ES connectivity](https://github.com/user-attachments/assets/66df7dd8-0ac2-4c1a-aca0-00f3f70b7c7a)

## conclusion
lasticsearch at  is now fully monitored using four automated scripts checking connectivity, latency, cluster health, and indexing rate. Each runs every 15 minutes with a 120-second timeout using secure credentials. This setup ensures early issue detection, maintains performance, and supports a reliable production environment.













