## TESTING - REDIS SERVICE CATALOG
### What is Redis?
Redis (short for Remote Dictionary Server) is an open-source, in-memory data store that is primarily used as a:
Database
Cache
Message broker

## Objective
To deploy Redis on a Docker container inside an AWS EC2 Ubuntu instance and test its functionality using three monitoring tools: Memory Usage Monitor, Latency Checker, and Ping Check. The goal is to ensure the Redis server is operating efficiently, detect any performance or connectivity issues early, and document the complete setup with clear, step-by-step instructions and visuals.

##  Prerequisites
AWS account with an EC2 instance (Ubuntu)
Docker installed on the EC2 instance
Security group configured to allow port 6379 (default Redis port)
Public IP address available to access Redis remotely
Access to a monitoring dashboard or custom monitoring scripts

## Implementation Steps 
## Step 1: Launch EC2 Instance
### Description:
Create a new EC2 instance using the AWS Console with the following settings:
AMI: Ubuntu Server (20.04 or 22.04)
Instance Type: t2.micro (for testing)
Key Pair: created a redis.pem key pair
Security Group: Allow port 6379 and SSH (port 22)

![Screenshot from 2025-06-22 12-00-04](https://github.com/user-attachments/assets/b551a5aa-4ae4-4d3c-b1bb-d77421d90a8e)

## Step 2: Connect to EC2 Instance via SSH
### Description:
Once your EC2 instance is running, connect to it from your local machine using SSH and the downloaded key.
![Redis instance connect](https://github.com/user-attachments/assets/65d054a8-c722-4cd7-95c5-0927b843c0e6)

## Step 1: Install Redis
Update and install Redis server:
Updates the local package index so Ubuntu knows the latest available versions of software. It’s always a good practice to run this before installing any new software.
```
 sudo apt update
```
Installs Docker from Ubuntu’s official repositories. The -y flag automatically confirms “yes” to all prompts during installation
```
sudo apt install redis-server -y
```
## Step 2: Configure Redis to allow public access and authentication
Open the Redis configuration file:
```
sudo nano /etc/redis/redis.conf
```
Make the following changes:

Bind to all interfaces:
Replace:
bind 127.0.0.1 ::1
With:
bind 0.0.0.0

![ redis bind](https://github.com/user-attachments/assets/2d0a4192-46e0-4475-8b57-b688ac2e98e7)


2.Disable protected mode:
Change:
protected-mode yes
To:
protected-mode no

![redis protected mode](https://github.com/user-attachments/assets/0b75bfa6-09b7-4db5-85c5-309e441f7965)


3.Set a password:
Find and uncomment this line:
requirepass foobared
Replace with:
requirepass 12345

![ redis requirepass](https://github.com/user-attachments/assets/f0b6c0bc-170a-4a7e-bda7-2fbb8434ffb7)


## Step 3: Restart Redis to apply changes
```
sudo systemctl restart redis-server
```

## Step 4: Set maxmemory and eviction policy (required for monitoring)
Run these commands from your client machine or same server:
Set memory limit (256 MB):
```
redis-cli -h <your-public-ip> -a 12345 CONFIG SET maxmemory 268435456
```
Set eviction policy:
```
redis-cli -h <your-public-ip> -a 12345 CONFIG SET maxmemory-policy allkeys-lru
```

## Step 5: Verify configuration
```
redis-cli -h 54.208.56.134 -a 12345 INFO memory | grep -E 'maxmemory|maxmemory_policy'
```

Step 6: (Optional) Make memory config persistent
Re-open the redis.conf:
Find and set the following parameters:
```
maxmemory 516MB
maxmemory-policy allkeys-lru
```
Then save and restart:
```
sudo systemctl restart redis-server
```

## Step 7: Test Redis
Basic ping test:
```
redis-cli -h 54.208.56.134 -a 12345 ping
```
Expected response:
PONG

![redis pong](https://github.com/user-attachments/assets/d377f2b6-b1bf-4ca7-8218-26490c9a3338)


## Redis Service Catalog: Monitoring Overview
The Redis service catalog features four essential monitoring tools aimed at maintaining the health, responsiveness, and efficiency of the Redis server. Each tool runs Python scripts at scheduled intervals to automatically assess critical operational metrics and behaviors.

These continuous checks allow:
Early issue detection
Fast troubleshooting
Reduced downtime
Improved service reliability


## Monitoring Implemented

## 1.Memory Usage Monitor

This subtask involves creating and configuring a Python-based monitoring script that connects to the Redis server and checks its memory usage to ensure it remains within acceptable limits.

## Monitoring Details:

Monitor Name: Memory Usage Monitor

Type: Python Script

Execution Frequency: 60 seconds

Execution Timeout: 40 seconds

REDIS_HOST -  “54.208.56.134"

REDIS_PORT - “6379"

REDIS_PASSWORD  - “12345”

Exit Code Logic:

0 → Memory usage is within defined threshold.

1 → Memory usage has exceeded the set threshold or Redis is unreachable.

Current Status:
🟢 Running successfully with no detected issues. Redis memory usage is within the expected range.

![redis memory usage monitor](https://github.com/user-attachments/assets/83955c84-90eb-476c-a368-b10ffbf175ba)

## Latency Checker Monitor
This subtask involves creating and configuring a Python-based monitoring script that measures the average latency of Redis server responses by executing multiple PING commands and comparing the result against a defined threshold.

## Monitoring Details:

Monitor Name: Latency Checker

Type: Python Script

Execution Frequency: 60 seconds

Execution Timeout: 30 seconds

REDIS_HOST -  “54.208.56.134"

REDIS_PORT - “6379"

REDIS_PASSWORD  - “12345”

LATENCY_THRESHOLD_MS “10" milliseconds

Exit Code Logic:

0 → Latency is below the defined threshold and Redis responded correctly.

1 → Latency exceeded the threshold or Redis did not respond within timeout.

Current Status:
🟢 Running successfully with no detected issues. Redis is responding within the expected latency limits.

![redis latency check](https://github.com/user-attachments/assets/6830e999-5922-47a6-929a-51b405681da0)

## Ping check Monitor

This subtask involves creating and configuring a Python-based monitoring script that verifies basic connectivity and responsiveness of the Redis server by sending a single PING command and expecting a valid response.

##Monitoring Details:

Monitor Name: Ping Check

Type: Python Script

Execution Frequency: 60 seconds 

Execution Timeout: 50 seconds

REDIS_HOST -  “3.85.160.232"

REDIS_PORT - “6379"

REDIS_PASSWORD  - “12345”

Exit Code Logic:

0 → Redis server responded with PING, indicating it's reachable and functioning.

1 → Redis server did not respond or returned an unexpected response, indicating potential connectivity issues.

Current Status:
🟢 Running successfully with no detected issues. Redis is reachable and responding to ping checks.

![redis ping check](https://github.com/user-attachments/assets/70afdbe7-eb15-4115-ba76-8b27e43defd5)


##Conclusion
 
In this setup, Redis was successfully deployed on an AWS EC2 Ubuntu instance using Docker. With the help of monitoring tools—Memory Usage Monitor, Latency Checker, and Ping Check—the Redis server’s health, responsiveness, and resource usage can be actively tracked. This ensures that Redis is not only running but also performing efficiently. Proper deployment and monitoring help in early detection of issues, maintain system stability, and support high-performance application environments that rely on fast, in-memory data storage.












