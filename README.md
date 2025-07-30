# Project 5: Creating a Dynamic Website for the Cafe

## Overview

In this project, we will create a dynamic website for a cafe using an EC2 instance and AWS Cloud9 as our IDE. We'll set up a LAMP stack (Linux, Apache, MySQL, PHP), configure a basic website, and deploy a pre-built cafe application.

---
![Architecture](https://github.com/fadykaram88/Creating-a-Dynamic-website-for-the-cafe/raw/main/m5ch-arch.png)



## Task 1: Create EC2 Instance

### Step 1: Launch an EC2 Instance

- Search for **EC2 Instance** in the AWS Console.
- Click **Launch Instance**.
- Configure as follows:
  - **Name**: `aws-cloud9-CafeWebServer3`
  - **Key Pair**: `Vockey`
  - **VPC**: `Lab VPC`
  - **Subnet**: `Public Subnet`
  - **Auto-assign Public IP**: Enable
  - **Security Group**: Select `aws-cloud9-CafeWebServer-3` *(create this SG if not existing, refer to Project 4)*

Click **Launch Instance**.

---

## Task 2: Connect to AWS Cloud9 (IDE)

### Step 2: Setup AWS Cloud9

- In the console, search for **Cloud9**.
- Create an environment and ensure:
  - OS: Ubuntu or Amazon Linux 2
  - SSH permissions are accessible via key pair
  - Security group allows Cloud9 to reach EC2
  - EC2 has SSM agent installed

> You'll get a terminal screen upon launching Cloud9.

---

## Task 3: Analyze LAMP Stack & Web Server

### Step 3: Check OS and Services

In the Cloud9 terminal:

```bash
cat /proc/version
sudo httpd -v
service httpd status
php --version
```

### Step 4: Enable Web and DB Services

```bash
sudo chkconfig httpd on
sudo service httpd start
sudo service httpd status
sudo mariadb --version
sudo systemctl enable mariadb
sudo chkconfig mariadb on
sudo service mariadb start
sudo service mariadb status
```

---

## Task 4: Configure Web Server Access

### Step 5: Allow File Editing in Cloud9

```bash
ln -s /var/www/ /home/ec2-user/environment
sudo chown ec2-user:ec2-user /var/www/html
```

### Step 6: Create a Test Web Page

- In Cloud9, go to:
  - `CafeWebServer > www > html`
  - Create new file `index.html` with:

```html
<html>Hello from the café web server!</html>
```

Save the file.

---

## Task 5: Make Website Public

### Step 7: Allow HTTP Access

- Go to EC2 > Instances > Your Instance
- Open **Security Tab** > Security Groups > Edit Inbound Rules
- Add Rule:
  - **Type**: HTTP
  - **Port**: 80
  - **CIDR**: 0.0.0.0/0

---

## Task 6: Install Cafe Web App

### Step 8: Download & Setup App in Cloud9

```bash
cd ~/environment
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/03-lab-mod5-challenge-EC2/s3/setup.zip
unzip setup.zip
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/03-lab-mod5-challenge-EC2/s3/db.zip
unzip db.zip
wget https://aws-tc-largeobjects.s3.us-west-2.amazonaws.com/CUR-TF-200-ACACAD-3-113230/03-lab-mod5-challenge-EC2/s3/cafe.zip
unzip cafe.zip -d /var/www/html/
cd /var/www/html/cafe/
wget https://docs.aws.amazon.com/aws-sdk-php/v3/download/aws.zip
wget https://docs.aws.amazon.com/aws-sdk-php/v3/download/aws.phar
unzip aws -d /var/www/html/cafe/
chmod -R +r /var/www/html/cafe/
```

Configure app parameters:

```bash
cd ~/environment/setup/
./set-app-parameters.sh
```

---

## Task 7: Configure Secrets

### Step 9: Retrieve Database Secret

- Go to **Secrets Manager** > **Secrets** > `/cafe/dbpassword`
- Click **Retrieve Secret Value**
- Copy the password value to clipboard.

---

## Task 8: Configure and Verify MySQL

### Step 10: Setup Database

```bash
cd ~/environment/db/
./set-root-password.sh
./create-db.sh
```

### Step 11: Connect to MySQL

```bash
mysql -u admin -p
# enter password retrieved earlier
```

```sql
show databases;
use cafe_db;
show tables;
select * from product;
exit;
```

---

## Task 9: Final Configurations

### Step 12: Set PHP Timezone and Restart

```bash
sudo sed -i "2i date.timezone = \"America/New_York\" " /etc/php.ini
sudo service httpd restart
```

---

## Task 10: View Website

- Visit: `http://<public-ip>/cafe`
  - Replace `<public-ip>` with your EC2 instance’s public IPv4

If the site doesn’t load, check file and folder permissions.

---

## Notes

- Learn about Cloud9, Security Groups, and Secrets Manager for full understanding.
- Practice helps solidify the concepts.

---

## Final Words

I hope you enjoyed the project and found it insightful.

**Goodbye and see you in another project!**
