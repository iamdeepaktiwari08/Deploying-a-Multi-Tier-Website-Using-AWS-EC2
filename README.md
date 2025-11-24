# Deploying-a-Multi-Tier-Website-Using-AWS-EC2
# Project 1 – Deploying a Multi-Tier Website Using AWS EC2

This project demonstrates how to deploy a **highly available PHP website** on AWS using:

- **EC2 Auto Scaling** for the Web Tier
- **Amazon RDS (MySQL)** for the Database Tier

The setup is based on my hands-on implementation captured in the *Multi Tier Deployment – AWS* document, including screenshots of EC2 instances, Auto Scaling Group, PHP info page, RDS instance, and successful DB connection from the website.  

---

## 📌 Project Goal

Deploy a **PHP Website (Frontend)** + **MySQL RDS (Backend)** with **Auto Scaling (minimum 2 instances)** to achieve high availability and scalability.  [oai_citation:1‡Multi Tier Deployment.pdf](sediment://file_000000009d14720793574849fe48c1e4)  

---
Components
	•	Amazon EC2
	•	Runs Apache + PHP
	•	Part of an Auto Scaling Group with at least 2 instances
	•	User accesses website using the EC2 public IP / DNS
	•	Amazon RDS (MySQL)
	•	Managed MySQL database instance
	•	Stores application data
	•	Accessible only from EC2 instances via Security Group
	•	Security Groups
	•	Web SG
	•	Inbound: HTTP (80) + SSH (22) from your IP (for admin)
	•	DB SG
	•	Inbound: MySQL (3306) from Web SG

⸻

✅ What I Implemented
	1.	Launched an EC2 Instance with Apache + PHP and deployed a sample PHP site.  ￼
	2.	Created an Auto Scaling Group (ASG) with minimum 2 EC2 instances for high availability.  ￼
	3.	Created an Amazon RDS MySQL instance, configured networking and security groups.  ￼
	4.	Connected the PHP application to RDS, verified DB connection, and displayed “Welcome to My Website” on the browser.  ￼

⸻

🧱 Technologies Used
	•	AWS EC2
	•	AWS Auto Scaling Group
	•	Amazon RDS (MySQL)
	•	Amazon VPC & Security Groups
	•	Apache HTTP Server
	•	PHP 8.x
	•	MySQL Client / PHP MySQLi Extension

⸻

🔧 Prerequisites
	•	AWS Account
	•	IAM user with permissions for EC2, RDS, Auto Scaling, VPC
	•	Key pair for SSH access to EC2
	•	Basic knowledge of Linux commands and PHP

⸻

🚀 Step-by-Step Deployment

1. Launch EC2 Instance (Web Server)
	1.	Go to EC2 Console → Instances → Launch instance.
	2.	Choose an AMI (e.g., Ubuntu 22.04 or Amazon Linux 2).
	3.	Choose instance type (e.g., t2.micro – free tier eligible).
	4.	Configure:
	•	Network: default VPC (or custom VPC)
	•	Subnet: public subnet
	•	Auto-assign public IP: Enable
	5.	Add security group rules:
	•	HTTP (80) – from 0.0.0.0/0
	•	SSH (22) – from your IP
	6.	Launch instance with your key pair.

2. Install Apache & PHP on EC2

SSH into the instance:
sudo apt update -y
sudo apt install -y apache2 php php-mysqli
Set up the web root:
sudo rm -f /var/www/html/index.html
sudo nano /var/www/html/index.php
Paste the content from src/index.php￼ in this repo (update DB endpoint, username and password).

Restart Apache:
sudo systemctl restart apache2
sudo systemctl enable apache2
Hit http://<EC2-Public-IP> in your browser to verify PHP is working.

3. Create Amazon RDS MySQL Instance
	1.	Go to RDS Console → Databases → Create database.
	2.	Engine: MySQL.
	3.	Template: Free tier (if eligible).
	4.	Set:
	•	DB instance identifier (e.g., database-1)
	•	Master username & password.
	5.	Connectivity:
	•	VPC: same as EC2
	•	Public access: No (recommended)
	•	Security group: create or select DB SG allowing port 3306 from the Web SG.
	6.	Create the database and wait until status is Available.

Copy the RDS endpoint (e.g., database-1.abcxyz.us-east-1.rds.amazonaws.com).

⸻

4. Configure PHP App to Connect RDS

Update /var/www/html/index.php with RDS endpoint & DB credentials.
Example:
$host = "your-rds-endpoint.rds.amazonaws.com";
$user = "admin";
$pass = "yourpassword";
$dbname = "testdb";
Create the database/schema if needed using MySQL client or a migration script.

When you refresh the browser, you should see:

Connected to RDS successfully!
Welcome to My Website

5. Create Launch Template & Auto Scaling Group
	1.	In EC2 → Launch templates, create a new template using the working web server instance configuration (including user data if you automated the setup).
	2.	In EC2 → Auto Scaling Groups → Create Auto Scaling group:
	•	Select the launch template.
	•	Choose subnets across at least 2 Availability Zones.
	•	Set:
	•	Desired capacity: 2
	•	Minimum capacity: 2
	•	Maximum capacity: 4 (example)
	3.	Optionally, attach a load balancer or use instance public IPs for testing.

Now your website will always run on at least two EC2 instances.

⸻

🧪 Testing
	•	Open the public IP (or ALB DNS, if used) in browser:
	•	Verify PHP info or app home page loads.
	•	Confirm RDS connection message appears.
	•	Stop one EC2 instance:
	•	Auto Scaling should create a new one to maintain desired capacity.

⸻

🧹 Clean Up (To Avoid Charges)

	1.	Delete Auto Scaling Group and Launch Template.
	2.	Terminate any remaining EC2 instances.
	3.	Delete the RDS database (snapshot optional).
	4.	Delete any custom Security Groups if not used elsewhere.
	5.	Remove unused Elastic IPs (if allocated).
  📄 Project PDF: [Download Here](documents/Multi-Tier-Deployment.pdf)
  
