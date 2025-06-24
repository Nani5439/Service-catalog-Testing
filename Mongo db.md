## Testing - Mongodb Service catalog
### What is MongoDB?
MongoDB is a powerful, open-source, NoSQL database system designed to store and manage large volumes of unstructured or semi-structured data. Unlike traditional relational databases, MongoDB stores data in flexible JSON-like documents, making it ideal for modern applications that require scalability, high performance, and dynamic schemas. It is commonly used in web, mobile, and big data applications.

## Objective:
To deploy MongoDB using a Docker container inside an AWS EC2 Ubuntu instance and verify its health using four key monitoring tools:

WiredTiger Cache Usage Monitor

Oplog Replication Lag Monitor

Operation Execution Time Monitor

Connection Check

The goal is to ensure MongoDB is running optimally, detect unhealthy performance indicators, and provide complete documentation with visuals and step-by-step commands.

## Prerequisites:
Active AWS account with an Ubuntu EC2 instance

Docker installed and configured on the instance

Security Groups open for MongoDB (port 27017)

MongoDB Docker image pulled and running

## Implementation Steps 
### Step 1: Launch EC2 Instance
### Description:
Create a new EC2 instance using the AWS Console with the following settings:

AMI: linux Server (20.04 or 22.04)


Instance Type: t2.micro (for testing)


Key Pair: created a mysql.pem key pair


Security Group: Allow 27017 and SSH (port 22)

![Mdb ec2 launched](https://github.com/user-attachments/assets/fc3358a8-6a68-49ea-a89d-59fd78fae656)

### Step 2: Connect to EC2 Instance via SSH
### Description:
Once your EC2 instance is running, connect to it from your local machine using SSH and the downloaded key.

![mdb ssh connected](https://github.com/user-attachments/assets/6ca62385-4947-4911-9ec5-729c689a2731)

### Step 3: Install Docker
### Commands:
```
sudo yum update -y
```
Updates the system’s package database and installs the latest available versions of packages.

```
sudo yum install docker -y
```
Installs Docker engine on your system.

```
systemctl start docker
```
Installs Docker engine on your system.

```
systemctl enable docker
```
Docker service to start automatically at boot.

```
docker pull mongo
```
This command downloads the latest version of the official MongoDB Docker image to your local system.

```
docker images
```
This command shows a table of locally available Docker images.

```
mkdir mongodb
```
reates a new folder named mongodb in the current working directory

```
docker run -d -p 27017:27017 -v mongodb:/data/db --name mongodb mongo
```
docker run: Launches a new Docker container.

-d: Runs the container in detached mode (in the background).

-p 27017:27017: Maps:

Port 27017 on the host (your EC2 instance)

to Port 27017 inside the MongoDB container
This allows you to connect to MongoDB from outside the container.

-v mongodb:/data/db: Creates and mounts a named volume called mongodb to persist MongoDB data:

mongodb = name of the volume (Docker auto-creates it if it doesn’t exist)

/data/db = default directory inside the MongoDB container where data is stored

--name mongodb: Assigns a custom name (mongodb) to the container so you can easily reference it later.

mongo: The official MongoDB image used to create the container.

```
docker ps
```

![mdb docker ps](https://github.com/user-attachments/assets/b0d48de0-de6a-4ef0-a044-f7d099881d21)

```
docker exec -it mongodb mongosh
```
This command opens an interactive MongoDB shell session inside the running mongodb Docker container.

![mdb connected to container](https://github.com/user-attachments/assets/877b5470-6db2-494e-bfc1-93fd2814ab22)

### step 4: Access MONGODB via Public IP in Browser

![mdb access ip](https://github.com/user-attachments/assets/edb9dcdd-5534-4aa4-9b41-99f5e72f05d1)


## MongoDB Service Catalog: Monitoring Overview
The MongoDB service catalog includes four essential monitoring tools designed to ensure the database remains healthy, performant, and stable. These tools are implemented as Python scripts that run at regular intervals, automatically checking MongoDB’s internal metrics and behavior.

These continuous monitoring checks enable:

Early issue detection

Fast troubleshooting

Reduced downtime

Improved database reliability

### Monitoring Implemented
### 1.WiredTiger cache usage Monitor

This script monitors the percentage of WiredTiger cache used in MongoDB. It ensures the cache doesn’t exceed a defined threshold, which could lead to degraded database performance or memory pressure.

### Monitoring Details:

Script Name: wiredtiger_cache_monitor.py

Metric Monitored: WiredTiger cache usage (%)

Threshold: CACHE_USAGE_THRESHOLD (default: 80%)

Environment: MongoDB at mongodb://54.210.50.175:27017

Frequency: Every 30 minutes

Timeout: < 5 seconds

Exit Codes:

0 – Success (Cache usage within limit)

1 – Failure (Exceeded threshold or error)

 

Current Status:
✅ Running successfully. Cache usage remains below threshold and MongoDB is responding to server status checks.

![mdb wried](https://github.com/user-attachments/assets/f7857dd8-bcf3-4624-a062-443909dfc26d)

### 2.Oplog Replication lag Monitor
The MongoDB Oplog Replication Lag Monitor checks the time difference between the primary and secondary replica set members to ensure replication is happening without significant delay. This helps detect replication bottlenecks that could lead to data inconsistency or failover issues.

 

Monitoring Details
Script Name: mongodb_oplog_lag_monitor.py

Purpose: Measure time lag in oplog replication across MongoDB replica set members

Metric: Replication lag (in seconds)

Threshold: REPLICATION_LAG_THRESHOLD (default: 30 seconds)

Environment Variable:
MONGO_URI = mongodb://54.210.50.175:27017

Frequency:
Every 5 minutes

Expected Execution Time: Under 5 seconds

Exit Codes:

0 – Success (Replication lag within threshold)

1 – Failure (Lag exceeded, no primary, or error occurred)

Current Status : UNHEALTHY

Causes :
exit status 1; error details: 2025-06-15 18:21:22,461 - ERROR - Failed to check oplog replication lag: not running with --replSet, full error: {'ok': 0.0, 'errmsg': 'not running with --replSet', 'code': 76, 'codeName': 'NoReplicationEnabled'} 2025-06-15 18:21:22,463 - WARNING - Oplog replication lag exceeds threshold (None > 30)

![mdb opligation](https://github.com/user-attachments/assets/20ea6dc1-370f-4040-a0ab-20cd5c589385)

### 3.Operation Execution Time Monitor
The MongoDB Operation Execution Time Monitor tracks how long it takes for MongoDB to respond to a basic ping operation. It serves as a proxy to detect delays in query execution and responsiveness. Elevated latency may indicate performance degradation, network slowness, or resource bottlenecks in the MongoDB server.

Monitoring Details

Script Name: mongodb_operation_execution_time.py

Purpose: Monitor MongoDB’s operation response time (latency)

Metric: Query latency (measured via ping)

Threshold: LATENCY_THRESHOLD (default: 130 ms)

Environment Variable:

MONGO_URI = mongodb://44.204.149.86:27017

Frequency: Every 5 minutes

Expected Execution Time: Under 2 seconds

Exit Codes:

0 – Success (latency within threshold)

1 – Failure (latency exceeded or connection failed)

Current Status:
✅ Running successfully. 

![mdb operation ](https://github.com/user-attachments/assets/62604146-7eaa-40ed-8b33-c8c1f4eca644)


### 4. Connectivity Check

he MongoDB Connectivity Check verifies whether the MongoDB server is reachable and responding by issuing a simple ping command. It ensures the service is live and accessible over the network.

 

Monitoring Details
Script Name: mongodb_connectivity_check.py

Purpose: Check MongoDB connection status

Command Used: ping via MongoDB admin interface

Environment Variable:

MONGO_URI = mongodb://54.210.50.175:27017

Frequency: Every 1–5 minutes

Expected Execution Time: Under 2 seconds

Exit Codes:

0 – Success (connection established)

1 – Failure (connection failed)

Current Status  :

🟢 MongoDB connectivity is healthy.

![mdb connectivity](https://github.com/user-attachments/assets/1bad090a-8b71-4fbe-a53a-da1f69607e10)


## Conclusion
You have successfully deployed MongoDB using Docker on an Amazon Linux EC2 instance and implemented a reliable monitoring system














