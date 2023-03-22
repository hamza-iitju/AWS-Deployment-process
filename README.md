
# AWS Deployment Process for Laravel Project

### AWS Resources:

>**AWS EC2 Instance:**

1. **Region:** Singapore
2. **Region Code:** ap-southeast-1
3. **Type & Size:** T3 Medium
4. **OS Platform:** Ubuntu
5. **OS Version:** 20.04 LTS
6. **Storage:** 30 GB
7. **Web Server:** Apache 2
8. **PHP Version:** 8.0
9. **Laravel Version:** 8

>**RDS (Postgres)**

 1. **DBMS:** PostgreSQL
 2. **Instance Type:** T3 Medium
 3. **Storage:** 20 GB
 4. **Core:** 2
 5. **RAM:** 4 GB
 6. **Engine Version:** 14.6

### AWS deployment step:

 1. AWS EC2 instance creation
 2. AWS VPC Create & Configuration
 3. Install apache2 & PHP 8.0 process in EC2 instance
 4. Configure free SSL
 5. Create .conf file into the EC2 instance.
 6. Create RDS (postgres)
 7. Create S3 bucket
 8. Create Subdomain in Route 53
 9. Create .pem file from EC2 instance
 10. Pull code from gitlab using deploy token into the EC2 instance
 11. Connect postgres database into laravel & migrate

    
**Details process of deployment:**

**1. AWS EC2 instance creation process:** A general outline of the steps to create an EC2 instance on AWS:

 - Log in to the AWS Management Console and navigate to the EC2    
   service.
  - Click on the “Launch Instance” button to begin the process.
  - Choose an Amazon Machine Image (AMI) for the instance. This is the operating system and software that will be installed on the    
   instance.
  - Select the instance type, which determines the hardware resources that will be allocated to the instance (CPU, memory,  storage,
   etc.).
   - Configure the instance details, including the number of instances to launch, network settings, and IAM role.
   - Add storage to the instance, such as an Elastic Block Store (EBS) volume.
   - Configure security groups to control inbound and outbound traffic to the instance.
   - Review the instance settings and launch the instance.
   - Once the instance is launched, connect to it using SSH or Remote Desktop, depending on the operating system.
   - Configure any additional software or settings on the instance as needed.
   
That's a general overview of the process.

  

**2. AWS VPC Create & Configuration:** AWS VPC creation & configuration process:

- Click on the “Create VPC” button to begin the process.
- Configure the VPC details, such as the name, IPv4 CIDR block, and any optional tags.
- Create one or more subnets within the VPC. Each subnet will have its own CIDR block within the overall VPC CIDR block.
- Create an internet gateway and attach it to the VPC. This allows resources within the VPC to communicate with the internet.
- Configure a routing table for the VPC. This will specify how traffic should be routed between the VPC and the internet or other VPCs.
- Configure security groups to control inbound and outbound traffic to resources within the VPC.
- Optionally, configure a network ACL to provide additional network-level security for the VPC.
- If needed, create a Virtual Private Gateway and configure a VPN connection to connect the VPC to an on-premises data center.
- Finally, launch instances or other resources within the VPC and configure their network settings to use the VPC and its associated subnets.

These are the general steps for creating and configuring an AWS VPC.

  

**3. Install apache2 & PHP 8.0 process in EC2 instance:** The general steps to install Apache2 and PHP 8.0 on an EC2 instance running Ubuntu:

- SSH into the EC2 instance.
- Update the package list using the following command:
	> sudo apt-get update
	
- Install Apache2 using the following command:
	>sudo apt-get install apache2
	
- Verify that Apache2 is running by navigating to your instance's public IP address in a web browser. You should see the default Apache2 page.
- Install PHP 8.0 and its dependencies using the following command:
	> sudo apt-get install php8.0 libapache2-mod-php8.0 php8.0-mysql php8.0-curl php8.0-gd php8.0-json php8.0-mbstring php8.0-intl php8.0-xml php8.0-zip

- Verify that PHP is installed correctly by creating a PHP info page in Apache2. Create a file named info.php in the Apache2 document root directory (/var/www/html) with the following content:
	><?php phpinfo(); ?>

- Save the file and navigate to http://example.com/info.php in a web browser. You should see a page with detailed information about your PHP installation.

That's it! You have successfully installed Apache2 and PHP 8.0 on your EC2 instance.

  

**4. Configure free SSL:** The general steps to configure a free SSL certificate using:
- Install Certbot on your EC2 instance by following the official Certbot documentation for Amazon Linux.
- Run the following command to obtain an SSL certificate for your domain:
	>sudo certbot certonly --webroot --webroot-path /var/www/html -d example.com

- Verify that the SSL certificate has been successfully obtained by running the following command:
	>sudo certbot certificates  

- You should see the details of the SSL certificate that was obtained in the previous step.
- Configure Apache to use the SSL certificate by editing the Apache configuration file located at /etc/httpd/conf/httpd.conf. Add the following lines to the file:
```xml
<VirtualHost *:443>
	ServerName your-domain.com 
	DocumentRoot /var/www/html 
	SSLEngine on 
	SSLCertificateFile /etc/letsencrypt/live/example.com/cert.pem
	SSLCertificateKeyFile /etc/letsencrypt/live/example.com/privkey.pem
	SSLCertificateChainFile /etc/letsencrypt/live/example.com/chain.pem
</VirtualHost>  
```
- Restart Apache to apply the configuration changes by running the following command:
	>sudo systemctl restart httpd

That's it! You have successfully configured a free SSL certificate.

  
**5. Create .conf file into the EC2 instance process:** The general steps to create a new .conf file on an EC2 instance running Apache2:

- SSH into the EC2 instance.
- Navigate to the Apache2 configuration directory using the following command:
	>cd /etc/apache2/sites-available/

- Create a new .conf file with a descriptive name for your website using the following command:
	>sudo nano ocare.conf

- In the editor, add the following basic configuration details to the file:
```xml
<VirtualHost *:80>
	ServerName example.com
	ServerAlias example.com
	DocumentRoot /var/www/html/path_folder/src/public
	ErrorLog ${APACHE_LOG_DIR}/error.log
	CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```
- Save the file by pressing “Ctrl + X”, then “Y” to confirm and “Enter” to exit the editor.
- Enable the new configuration file using the following command:
	>sudo a2ensite example.com.conf

- Restart the Apache2 service to apply the changes using the following command:
	>sudo systemctl restart apache2

That's it! You have successfully created a new .conf file on your EC2 instance running Apache2.

  

**6. Create RDS (postgres) installation process:** To create a new Amazon RDS (PostgreSQL) instance:

- Log in to the AWS Management Console and navigate to the RDS service.
- Click on the “Create database” button to begin the process.
- Choose the PostgreSQL engine and select the version you want to use.
- Choose the “Standard Create” option for creating a new DB instance.
- Configure the following settings:
	
	>**DB instance class:** Choose the appropriate instance type based on your workload needs.
	>**Multi-AZ deployment:** Choose "Yes" to create a standby replica of your primary instance in a different Availability Zone for increased availability and durability.
	>**Storage:** Choose the amount of storage you need for your database.
	>**DB instance identifier:** Choose a unique name for your DB instance.
	>**Master username and password:** Choose a username and strong password for the master user account that will have full access to the database.
	>**Virtual Private Cloud (VPC) and subnet group:** Choose the VPC and subnet group where you want to deploy your DB instance.

- Configure the database settings, such as the database name, character set, and collation.
- Configure the connectivity settings, such as the security group and VPC subnet group.
- Optionally, configure additional settings, such as backups, maintenance windows, and monitoring.
- Review the configuration details and click on the “Create database” button to create the instance.
- Wait for the instance creation process to complete, which may take several minutes.

That's it! You have successfully created a new Amazon RDS (PostgreSQL) instance.

  

**7. Create S3 bucket process:** The general steps to create a new Amazon S3 bucket:

- Log in to the AWS Management Console and navigate to the S3 service.
- Click on the “Create bucket” button to begin the process.
- Choose a unique name for your bucket. Note that bucket names must be globally unique across all AWS accounts, so you may need to choose a different name if your preferred name is already in use.
- Choose the region where you want to create the bucket. Note that the region cannot be changed after the bucket is created.
- Configure the following settings:
    
	>**Set the desired permissions for the bucket.** By default, new buckets are private and accessible only to the AWS account that created them, but you can modify the permissions to allow public access or access from specific IAM users or roles.
	>**Choose whether to enable versioning for the bucket.** Versioning allows you to store multiple versions of objects in the bucket and retrieve previous versions as needed.
	>**Choose whether to enable server access logging for the bucket.** Server access logging allows you to track requests made to the bucket and analyze access patterns.
	>**Choose whether to enable default encryption for objects stored in the bucket.** Default encryption ensures that all objects stored in the bucket are encrypted by default.

- Click on the "Create bucket" button to create the bucket.

That's it! You have successfully created a new Amazon S3 bucket.

  

**8. Create subdomain in AWS:** The general steps to create a subdomain in AWS:

- Log in to the AWS Management Console and navigate to the Route 53 service.
- Click on the “Hosted zones” link in the left-hand menu to view a list of your hosted zones.
- Click on the name of the hosted zone where you want to create the subdomain.
- Click on the "Create Record Set" button to begin creating a new record set.
- In the "Name" field, enter the name of the subdomain you want to create. For example, if you want to create a subdomain called "admin.example.com", enter "admin" in the "Name" field.
- In the "Type" field, select the type of record you want to create. For a subdomain, you typically want to create an "A" record that maps the subdomain to an IP address.
- In the "Value" field, enter the IP address of the server or load balancer that will handle requests for the subdomain.
- Optionally, configure additional settings, such as the time-to-live (TTL) value, health checks, and routing policies.
- Click on the "Create" button to create the record set.
    
That's it! You have successfully created a subdomain in AWS using Route 53. Keep in mind that it may take some time for the DNS changes to propagate, so your subdomain may not be immediately accessible from all locations.

  

**9. Create .pem file from EC2 instance process:** To create a .pem file from an EC2 instance, you can follow these steps:

- Connect to your EC2 instance using SSH client.
- Once connected to your instance, run the following command to generate a new .pem file:
	>openssl rsa -in ~/.ssh/id_rsa -outform pem > example.pem  

	This will create a new .pem file called example.pem in your current directory.
- Set the correct permissions on the new .pem file using the following command:
	>chmod 400 oCare-Prod.pem  

	This ensures that only the owner of the file has permission to read and write to it.
- Copy the new .pem file from your instance to your local machine using the scp command.
	>scp -i oCare-Prod.pem ubuntu@your-instance-ip-address:oCare-Prod.pem ~/Downloads  

	This will copy the new .pem file to your local machine and save it in your Downloads folder. You should now have a new .pem file that you can use to connect to your EC2 instance.

**10. Pull code from gitlab using deploy token into the EC2 instance:** The general steps to pull code from GitLab using a deploy token into your EC2 instance and connect to the EC2 instance with a .pem file:

- Log in to your GitLab account and navigate to the repository that contains the code you want to pull.
- Go to "Settings" > "Repository" > "Deploy Tokens" and click on the "Create deploy token" button.
- Enter a name and expiration date for the deploy token, and select the desired repository access level. Note the token value, as you will need it later.
- Download the .pem file for your EC2 instance from the AWS Management Console.
- Set the permissions for the .pem file to 400 by running the following command:
	>chmod 400 oCare-Prod.pem

- Connect to your EC2 instance using SSH with the following command:
    >ssh -i oCare-Prod.pem ubuntu@your-instance-public-dns-name
    
- Install Git on your EC2 instance if it is not already installed. You can do this by running the following command:
	>sudo apt-get install git

- Create a new directory on your EC2 instance where you want to store the code. You can do this by running the following command:
	>mkdir path_folder

- Navigate to the new directory by running the following command:
	>cd path_folder

- Initialize a new Git repository by running the following command:
	> git init

- Add a new remote repository that points to your GitLab repository by running the following command:
	>git remote add origin https://gitlab.com/username/repo.git

- Configure Git to use the deploy token to authenticate with the remote repository by running the following command:
	>git config --global credential.helper store

- Pull the code from the GitLab repository by running the following command:
	>git pull origin master

That's it! You have successfully pulled code from GitLab using a deploy token into your EC2.

**11. Connect postgres database into laravel & migrate:** The general steps to connect a PostgreSQL database to a Laravel application and run database migrations:

- Install the required PostgreSQL PHP extension on your EC2 instance by running the following command:
	>sudo yum install php-pgsql

- Install Laravel on your EC2 instance by following the official Laravel documentation.
- Configure the database connection in the Laravel .env file by setting the DB_CONNECTION, DB_HOST, DB_PORT, DB_DATABASE, DB_USERNAME, and DB_PASSWORD variables. For example:
	```
	DB_CONNECTION=pgsql
	DB_HOST=your-rds-endpoint-here
	DB_PORT=5432
	DB_DATABASE=your-database-name-here 
	DB_USERNAME=your-database-username-here 
	DB_PASSWORD=your-database-password-here
	```

- Create a migration by running the following command:
	>php artisan migrate –seed

That's it! You have successfully connected your PostgreSQL database to your Laravel application and run database migrations. You can now use the database in your Laravel application.
