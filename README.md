# Deploy WordPress Container on EC2 with RDS MySQL

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
 
![](https://github.com/Shivraj0199/Deploy-WordPress-on-EC2-with-RDS-MySQL-/blob/main/Img/Screenshot%202025-08-20%20171248.png)
---
### Step: 2 Connect to EC2 Instance  
```
ssh -i your-key.pem ubuntu@<EC2-Public-IP>
```
----
### Step 3: Install Docker and other dependencies on EC2
* ```sudo apt update -y```
* ```sudo apt install docker.io -y```
* ```sudo systemctl start docker```
* ```sudo systemctl enable docker```
* ```sudo apt install nginx``` (For reverse proxy)
* ```sudo systemctl start nginx``` 
* ```sudo apt install mysql-client -y```
* ```mysql -h <RDS-ENDPOINT> -u admin -p```
* ```SHOW DATABASES;```
* ```CREATE DATABASE wordpressdb;```

![](https://github.com/Shivraj0199/Deploy-WordPress-on-EC2-with-RDS-MySQL-/blob/main/Img/Screenshot%202025-08-20%20185803.png)
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

![](https://github.com/Shivraj0199/Deploy-WordPress-on-EC2-with-RDS-MySQL-/blob/main/Img/Screenshot%202025-08-20%20171332.png)
---
### Step 5: Run WordPress Docker Container
**On EC2, run WordPress container:**

```docker run -d --name wordpress -p 80:80 -e WORDPRESS_DB_HOST=<your-rds-endpoint> -e WORDPRESS_DB_USER=admin -e WORDPRESS_DB_PASSWORD=yourpassword -e WORDPRESS_DB_NAME=<wordpressdb> wordpress```

---
### Step 6: Access WordPress
* Open browser → ```http://EC2-PUBLIC-IP```
* You’ll see WordPress setup page
* Complete installation → site title, username, password.

![](https://github.com/Shivraj0199/Deploy-WordPress-on-EC2-with-RDS-MySQL-/blob/main/Img/Screenshot%202025-08-20%20191011.png)
---

### Step 7: Configure Nginx Reverse Proxy
* sudo nano /etc/nginx/sites-available/wordpress
* Paste this code to the configuration file
```
server {
    listen 80;
    server_name tech-connect.cloud www.tech-connect.cloud;

    location / {
        proxy_pass http://127.0.0.1:80;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```
* sudo ln -s /etc/nginx/sites-available/wordpress /etc/nginx/sites-enabled/
* sudo nginx -t
* sudo systemctl restart nginx
---
### Step 8: Point Hostinger Domain → EC2
* Login to Hostinger
* Find DNS setting

| Record Type |  Host   | Name                   | Value                                      | Routing Policy |
|-------------|---------|------------------------|--------------------------------------------|----------------|
| A           |   @     | tech-connect.cloud     | EC2 Public IP                              | Simple         |
| A           |   www   | www.tech-connect.cloud | EC2 Public IP                              | Simple         |

* Save → DNS propagation takes 10–30 min.
---
### Step 9: Secure with Let’s Encrypt (Certbot)
* sudo apt install certbot python3-certbot-nginx -y
* sudo certbot --nginx -d tech-connect.cloud -d www.tech-connet.cloud
* sudo certbot renew --dry -run
---
### Step 10: Access WordPress
 ```https://tech-connect.cloud```

 



