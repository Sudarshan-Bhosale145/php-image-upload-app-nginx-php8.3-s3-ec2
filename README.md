PHP-IMAGE-UPLOAD-USING-NGINX-PHP8.3-S3-AND-EC2

Author: Sudarshan Dattatray Bhosale

Project Type: AWS Cloud | Web Application | Image Upload System

Version: 1.0

License: MIT

🌐 Project Overview
👤 About the Author

Name: Sudarshan Dattatray Bhosale

Education: Bachelor of Computer Applications (BCA), Shivaji University

Project Experience: Online Bus Ticket Booking System (PHP & MySQL)

Skills: AWS, EC2, S3, NGINX, PHP, MySQL, Linux, Cloud & DevOps

Career Goal: Cloud / DevOps Engineer

This repository demonstrates how to create a PHP 8.3 Image Upload Project using:

✔ EC2 (App Server)
✔ EC2 (DB Server – optional)
✔ NGINX
✔ Amazon S3 for image storage
✔ AWS PHP SDK

🔹 Goal: Upload images from PHP and store them in S3
🔹 Tools Used: EC2, S3, PHP 8.3, NGINX, AWS SDK

##🧩 Architecture

---
+------------------------+         +--------------------------+
|      EC2 App Server    |  --->   |     Amazon S3 Bucket     |
| NGINX + PHP 8.3 + SDK  |         |   Image Storage System   |
+------------------------+         +--------------------------+
               |
               | (Optional)
               v
+-------------------------+
|      EC2 DB Server      |
|   MySQL / MariaDB       |
+-------------------------+
---

⚙️ Tech Stack
Component	Description
☁️ AWS EC2	App server running PHP & NGINX
🔐 AWS S3	Image storage bucket
🌐 NGINX	Web server for PHP
🐘 PHP 8.3	Backend scripting
🗄️ MySQL (Optional)	Store metadata (filename, link)
🚀 Step-by-Step Setup
📌 Step 1 — Launch EC2 App Server & Install PHP 8.3 + NGINX

Update system:

sudo apt update -y


Install NGINX:

sudo apt install nginx -y


Install PHP 8.3:

sudo apt install php8.3 php8.3-fpm php8.3-cli php8.3-curl php8.3-mbstring unzip curl -y


Start services:

sudo systemctl start nginx
sudo systemctl enable nginx

📌 Step 2 — Configure NGINX for PHP

Edit config:

sudo nano /etc/nginx/sites-available/default


Paste:

server {
    listen 80;
    server_name _;

    root /var/www/html;
    index index.php index.html;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.3-fpm.sock;
    }
}


Restart NGINX:

sudo systemctl restart nginx

📌 Step 3 — Install AWS SDK for PHP
cd /var/www/html
composer require aws/aws-sdk-php

📌 Step 4 — Configure Amazon S3 Bucket

✔ Create S3 bucket
✔ Enable public read (optional)
✔ Create IAM User
✔ Assign: AmazonS3FullAccess
✔ Collect Access Key + Secret Key

📌 Step 5 — Create PHP Upload Code
upload.php
<?php
require 'vendor/autoload.php';
use Aws\S3\S3Client;
use Aws\Exception\AwsException;

$bucketName = 'your-bucket-name';
$awsRegion = 'us-east-1';

$s3Client = new S3Client([
    'version' => 'latest',
    'region'  => $awsRegion,
    'credentials' => [
        'key'    => 'YOUR_AWS_ACCESS_KEY',
        'secret' => 'YOUR_AWS_SECRET_KEY'
    ]
]);

if(isset($_FILES['image'])){
    $file = $_FILES['image'];
    $fileName = basename($file['name']);
    $fileTmpPath = $file['tmp_name'];

    try {
        $result = $s3Client->putObject([
            'Bucket' => $bucketName,
            'Key'    => $fileName,
            'SourceFile' => $fileTmpPath,
            'ACL'    => 'public-read'
        ]);
        echo "Image uploaded successfully. <a href='{$result['ObjectURL']}'>View Image</a>";
    } catch (AwsException $e) {
        echo "Error uploading image: " . $e->getMessage();
    }
}
?>

index.html
<!DOCTYPE html>
<html>
<head>
    <title>Image Upload</title>
</head>
<body>
    <h2>Upload Image to S3</h2>
    <form action="upload.php" method="POST" enctype="multipart/form-data">
        <input type="file" name="image" required>
        <button type="submit">Upload</button>
    </form>
</body>
</html>

📌 Step 6 — (Optional) Setup EC2 Database Server

Install MySQL:

sudo apt update
sudo apt install mariadb-server -y


Create DB + Table:

CREATE DATABASE image_upload;
USE image_upload;

CREATE TABLE files (
    id INT AUTO_INCREMENT PRIMARY KEY,
    filename VARCHAR(255),
    url TEXT
);

📁 Folder Structure
PHP-IMAGE-UPLOAD-USING-NGINX-PHP8.3-S3-AND-EC2/
│
├── index.html
├── upload.php
├── vendor/              # AWS SDK
├── composer.json
├── README.md
└── images/              # Screenshots (add manually)

📸 Recommended Screenshots

✔ NGINX running
✔ S3 bucket configuration
✔ EC2 instance details
✔ Image successfully uploaded
✔ Output of AWS S3 URL

🧠 Common Issues & Fixes
Issue	Reason	Fix
❌ 403 Access Denied	Wrong IAM policy	Add AmazonS3FullAccess
❌ Upload fails	Wrong bucket region	Use same region in SDK
❌ PHP not executing	NGINX config missing	Check fastcgi path
❌ File empty	Missing enctype	Add multipart/form-data
🧾 Summary

✔ Installed PHP 8.3 + NGINX
✔ Created S3 bucket
✔ Added AWS SDK
✔ Developed upload form
✔ Stored images in S3
✔ (Optional) Stored metadata in DB

🌐 Connect with Me

👨‍💻 Sudarshan Dattatray Bhosale

💼 Cloud & DevOps Enthusiast

🎓 BCA Graduate — Shivaji University

🔗 LinkedIn: https://www.linkedin.com/in/sudarshan-bhosale-174477374

🔗 GitHub: https://github.com/Sudarshan-Bhosale145
