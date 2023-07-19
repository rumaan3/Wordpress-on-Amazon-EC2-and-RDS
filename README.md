# Wordpress-on-Amazon-EC2-and-RDS
# Deploying WordPress Website on EC2 Instance with Amazon RDS

This guide provides step-by-step instructions on how to deploy a WordPress website on an Amazon EC2 instance using Amazon RDS as the database. By following these steps, you can set up a scalable and reliable WordPress site in the AWS cloud environment.

## Prerequisites

Before starting the deployment process, make sure you have the following prerequisites:

- An AWS account with appropriate permissions to create and manage EC2 instances and Amazon RDS.
- Basic knowledge of AWS services such as EC2, RDS, and security groups.
- Familiarity with SSH and command line interface (CLI) tools.
- A domain name registered with a DNS provider (optional but recommended for a production environment).

## Step 1: Launch an EC2 Instance

1. Log in to the AWS Management Console.
2. Open the EC2 service and click on "Launch Instance".
3. ***Select an Amazon Machine Image (AMI) , which is Amazon Linux 2*** , if you selected any other AMI the installation commands to setup the server are different.
4. Choose an instance type based on your website's expected traffic and resource needs.
5. Configure the instance details, such as the number of instances, network settings, and storage options.
6. Set up security groups to control inbound and outbound traffic to the EC2 instance. Allow HTTP (port 80) and SSH (port 22) access, at a minimum.
7. Review your configuration and launch the instance.
8. Create or select an existing key pair for SSH access to the EC2 instance.

## Step 2: Set up Amazon RDS Database

1. Open the Amazon RDS service from the AWS Management Console.
2. Click on "Create database" and select the database engine of your choice (e.g., MySQL, MariaDB).
3. Choose a database deployment option, such as "Standard Create" or "Easy Create" based on your needs.
4. Configure the database settings, including instance specifications, storage, and connectivity options.
	1. In connectivity options , make sure the ***same VPC id*** has been mentioned for the RDS instance as well the EC2 instance you want to connect it to.
	2. Assign the ***same security group*** created for the EC2 instance which allows SSH,HTTPS and HTTP access to the instance , to the VPC of the RDS instance also
	3. This is done while creating and configuring the database
	4. Click on "connect to EC2" and select the EC2 instance to connect it to, to create a connection between them , which creates the appropriate security groups for RDS and EC2 on port 3306.

5. Set up the database authentication credentials and choose an appropriate database name.
6. Configure additional options as needed, such as backups, monitoring, and maintenance settings.
7. Review your configuration and create the database instance.

## Step 3: Connect to the EC2 Instance

1. Once the EC2 instance is launched, note down its public IP address or public DNS name.
2. Open a terminal or SSH client and connect to the EC2 instance using the SSH key pair you specified during instance launch.
   ```
   ssh -i path/to/key.pem ec2-user@<instance-public-ip>
   ```

## Step 4: Install and Configure WordPress

These below commands are to install LAMP stack on EC2 instance for ***AMAZON LINUX 2 AMI ONLY***

1. Update the system packages:

`sudo yum update -y`

2. Install Apache web server:

`sudo yum install httpd -y`

3. Start the Apache web server:

`sudo systemctl start httpd`

4. Enable Apache to start on system boot:

`sudo systemctl enable httpd`

5. Install MySQL server and client:

`sudo yum install mariadb-server -y`

6. Start the MySQL server:

`sudo systemctl start mariadb`

7. Secure the MySQL installation:

`sudo mysql_secure_installation`

Follow the prompts to set a root password, remove anonymous users, disallow remote root login, remove the test database, and reload privilege tables.

8. Install PHP and required modules:

`sudo amazon-linux-extras install php7.4 -y sudo yum install php-mysqlnd -y`

9. Restart Apache to apply the changes:

`sudo systemctl restart httpd`

### Install the WP CLI

I just copied the commands to download the CLI from [https://wp-cli.org/de/#installing](https://wp-cli.org/de/#installing):

$ curl -O [https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar](https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar)

$ php wp-cli.phar — info

$ chmod +x wp-cli.phar

$ sudo mv wp-cli.phar /usr/local/bin/wp

Now, check if the cli is available:

$ wp — info

### WordPress Installation

Navigate to _/var/www/html_ and run

$ cd /var/www/html

$ wp core download.

If you’re now typing your IP address of the instance in your browser, you should see the WordPress installation guide.

## Step 4: Access and Configure RDS

To check if the connection can be made to the RDS database created, follow these steps:

1. **Verify RDS Database Endpoint**: Ensure that you have the correct endpoint (DNS name) of your RDS database. You can find this information in the Amazon RDS dashboard under "Connectivity & security" for your specific database instance.
    
2. **Use MySQL Client**: You can use the MySQL command-line client on your EC2 instance (where you have installed the LAMP stack and WordPress) to test the connection to the RDS database.
    
    Open a terminal or SSH into your EC2 instance and enter the following command:
    
    `mysql -u your_database_username -p -h your_rds_endpoint`
    
    Replace `your_database_username` with the username you created for the RDS database and `your_rds_endpoint` with the endpoint you obtained from the RDS dashboard.
    
3. **Enter Password**: After running the command, you will be prompted to enter the password for the database user. Enter the password and press Enter.
    
    If the connection is successful, you will be logged into the MySQL prompt, and you should see something like this:
    
    `Welcome to the MySQL monitor.  Commands end with ; or \g. Your MySQL connection id is XXXXXXXX Server version: X.X.X RDS XXXXXXXX:XXXXXXXXX ... mysql>`
    
    If the connection fails, you may see an error message indicating the reason for the failure

4. if the connection is successful run the following command to Create a database in RDS

   ```
   CREATE DATABASE your_database_name;
   ```

In Place of "your_database_name" add a name for your database , dont forget the Colon at the end.

## Step 5: Access and Configure WordPress

1. Open a web browser and enter the public IP address or DNS name of your EC2 instance.
   ```
   http://<instance-public-ip>/wp-admin/install.php
   ```

![[Pasted image 20230719182515.png]]

1. For your **database name** , mentioned the name of the database you have just created in RDS above.
2. Mention the database Username and password setup at the beginning of the RDS setup
3. For the Database host , enter the RDS Instance Endpoint which looks something like this (wordpress.c2isuhOIEmks.ap-south-1.rds.amazonaws.com)



### Congratulations! You have successfully deployed WordPress on Amazon EC2 with Amazon RDS as the database. Your website should now be accessible through the provided IP or domain name.
