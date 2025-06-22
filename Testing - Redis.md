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

2.Disable protected mode:
Change:
protected-mode yes
To:
protected-mode no

3.Set a password:
Find and uncomment this line:
requirepass foobared
Replace with:
requirepass 12345

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












