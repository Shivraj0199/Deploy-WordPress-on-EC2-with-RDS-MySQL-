# Deploy-WordPress-on-EC2-with-RDS-MySQL-

Deployed WordPress using Docker on EC2, with RDS (MySQL) as a managed backend database. Configured security groups, networking, and storage to ensure scalability and data persistence. Secured the website with HTTPS using Certbot

## Architecture  
- **Frontend**: WordPress running on EC2 (Ubuntu)  
- **Backend**: MySQL database hosted in Amazon RDS  
- **Storage**: EBS for EC2, RDS storage for database  
- **Security**: Security Groups for EC2 and RDS  
---
### Step: 1 Launch an EC2 Instance  
- Go to **AWS EC2 Console** → Launch an **Ubuntu Server** (t2.micro for free-tier)  
- Add **Security Group Rules**:  
  - `HTTP (80)` → 0.0.0.0/0  
  - `HTTPS (443)` → 0.0.0.0/0  
  - `SSH (22)` → Your IP
  - `MYSQL/Aurora (3306)` → 0.0.0.0/0
---
### Step: 2 Connect to EC2 Instance  
```
ssh -i your-key.pem ubuntu@<EC2-Public-IP>
```
----
### Step 3: Install Docker on EC2
* ```sudo apt update -y```
* ```sudo apt install docker.io -y```
* ```sudo systemctl start docker```
* ```sudo systemctl enable docker```
* ```sudo apt install mysql-client -y```
* ```mysql -h <RDS-ENDPOINT> -u admin -p```
* ```SHOW DATABASES;```
* ```CREATE DATABASE wordpressdb;```  
---
### Step 4: Create RDS (MySQL)
1. ```Go to RDS →``` Create Database.
2. ```Engine →``` MySQL.
3. ```DB instance →``` Free Tier (db.t3.micro).
4. ```Set:```
    * ```DB Name →``` wordpressdb
    * ```Master username →``` admin
    * ```Master password →``` yourpassword
5. ```Connectivity →``` Public Access: Yes.
6. ```Security group →``` allow MySQL (3306) inbound from your EC2 Security Group.
7. ```Launch DB.```
8. ```Copy Endpoint``` (looks like: wordpressdb.abc123xyz.us-east-1.rds.amazonaws.com)
---
### Step 5: Run WordPress Docker Container
On EC2, run WordPress container:

```docker run -d --name wordpress -p 80:80 -e WORDPRESS_DB_HOST=<your-rds-endpoint> -e WORDPRESS_DB_USER=admin -e WORDPRESS_DB_PASSWORD=yourpassword -e WORDPRESS_DB_NAME=<wordpressdb> wordpress```

---
### Step 6: Access WordPress
* Open browser → ```http://EC2-PUBLIC-IP```
* You’ll see WordPress setup page
* Complete installation → site title, username, password.
---
