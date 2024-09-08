# My name is Jubril

Welcome to JJADAMS WORLD

This is a comprehensive guide to setting up a WordPress website using the LAMP (Linux, Apache, MySQL, PHP) stack. Here's a summarized version of the guide:

Project Overview

- Set up a WordPress website using LAMP stack
- Learn to deploy and manage a WordPress site on a LAMP stack
- Understand the core technologies behind WordPress

Key Features

- Step-by-step instructions for installing and configuring LAMP components
- Secure database creation for WordPress
- User-friendly guide for completing WordPress installation

Benefits

- Gain independence by managing your own WordPress website
- Understand core technologies behind WordPress
- Customize website to exact needs
- Embrace flexibility and power of WordPress

Key Concepts Covered

- AWS (EC2 and Route 53)
- Linux (Ubuntu)
- Apache
- MySQL
- PHP
- WordPress
- DNS
- SSL (certbot)
- OpenSSL command

Task Checklist

1. Deploy an Ubuntu Server
2. Set up LAMP stack on the server
3. Configure WordPress application
4. Map IP address to DNS A record
5. Validate WordPress website setup by accessing web address

Documentation Reference

- Refer to Project 1 for guidance on spinning up an Ubuntu server
- Set inbound rule for MYSQL in security group

This guide provides a detailed walkthrough of setting up a WordPress website using LAMP stack, covering key concepts, benefits, and tasks.


![0](picture/img%20-%20Copy/1.png)

- Click on Edit inbound rules.


![alt text](image-1.png)

- Click on Add rule.

![alt text](image-2.png)

- Click on Custom TCP① and select MySQL/Aurora②.

![alt text](image-3.png)

- Enter the IP address① you want to allow access and click Save rules②.

![alt text](image-4.png)


Note

- You can choose 0.0.0.0/0 to permit access from anywhere, but for added security, restrict access to MySQL exclusively to your Web Server’s IP address. In the Inbound Rule configuration, specify the source as /32

- Open your terminal and connect to your Ubuntu server via SSH.

![alt text](image-5.png)

# Install Apache


- To install Apache, run the following commands in your terminal.

sudo apt update


![alt text](image-6.png)


sudo apt install apache2


![alt text](image-7.png)


- To enable Apache to start on boot, execute sudo systemctl enable apache2①, and then verify its status with the sudo systemctl status apache2② command.


![alt text](image-8.png)


- Now, let's check if our server is running and accessible both locally and from the Internet by executing the following command: curl http://localhost:80


![alt text](image-9.png)


Now, it's time to test how our Apache HTTP server responds to requests from the Internet.

Copy your public IPv4 address from your EC2 dashboard.

![alt text](image-10.png)


- Open a web browser of your choice and try accessing the following URL: http://<Public-IP-Address>:80


![alt text](image-11.png)


- If the installation was successful, you should see this page.


![alt text](image-12.png)



# Install MYSQL



To install this software using 'apt', run the command sudo apt install mysql-server. When prompted, confirm the installation by typing 'Y' and then pressing ENTER.


![alt text](image-13.png)



After the installation is complete, log in to the MySQL console by typing: sudo mysql.


- Note

- It's recommended that you run a security script included with MySQL to enhance security. Before running the script, set a password for the root user using the default authentication method mysql_native_password. For this project, we'll define the password as "pass", but you can choose any password you prefer.

- Run the following command to set the password for the root user with the MySQL native password authentication method: ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'pass';. Exit the MySQL shell when you're done by typing exit.


![alt text](image-14.png)


- Start the interactive script by running: sudo mysql_secure_installation①. Answer y② for yes, or any other key to continue without enabling specific options.

![alt text](image-15.png)


- Set your password validation policy level.


![alt text](image-16.png)


Note

I set my password validation policy level to 0 because I don't require much security, as I will be terminating all resources immediately after this project. However, on the job, it's advised to use the strongest level, which is 2.

- Enable MySQL to start on boot by executing sudo systemctl enable mysql①, and then confirm its status with the sudo systemctl status mysql② command.


![alt text](image-17.png)


# Install PHP
- Install PHP along with required extensions by running the following script: sudo apt install php-curl php-gd php-mbstring php-xml php-xmlrpc php-soap php-intl php-zip.![alt text](image-18.png)


sudo apt install php libapache2-mod-php php-mysql

![alt text](image-19.png)

- Confirm the downloaded PHP version by running php -v.


![alt text](image-20.png)


# Creating A Virtual Host For Your Website Using Apache

- Create the directory for Projectlamp using the 'mkdir' command as follows: sudo mkdir /var/www/projectlamp① and assign ownership of the directory to our current system user using: sudo chown -R $USER:$USER /var/www/projectlamp②.


![alt text](image-21.png)


- Create and open a new configuration file in Apache's sites-available directory using your preferred command-line editor: sudo vi /etc/apache2/sites-available/projectlamp.conf.

- Creating this will produce a new blank file. Paste the configuration text provided below into it:

<VirtualHost *:80>

ServerName projectlamp

ServerAlias www.projectlamp

ServerAdmin webmaster@localhost

DocumentRoot /var/www/projectlamp

ErrorLog ${APACHE_LOG_DIR}/error.log

CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
1![alt text](image-22.png)

- Save your changes by pressing the Esc key, then type :wq and press Enter.

![alt text](image-23.png)

- Run the ls command sudo ls /etc/apache2/sites-available① to show the new file② in the sites-available directory.


![alt text](image-24.png)

- We can now enable the new virtual host using the a2ensite command: sudo a2ensite projectlamp.


![alt text](image-25.png)

- To disable Apache's default website, use the a2dissite command. Type: sudo a2dissite 000-default.


![alt text](image-26.png)


- To ensure your configuration file doesn’t contain syntax errors, run: sudo apache2ctl configtest. You should see "Syntax OK" in the output if your configuration is correct.


![alt text](image-27.png)

- Finally run: sudo systemctl reload apache2. This will reload Apache for the changes to take effect.

Note

- Our new website is now active, but the web root /var/www/projectlamp is still empty. Let's create an index.html file in that location to test that the virtual host works as expected.


- To create the index.html file with the content "Hello LAMP from Jay" in the /var/www/projectlamp directory, use the following command: sudo echo 'Hello LAMP from Jay' > /var/www/projectlamp/index.html.


![alt text](image-28.png)


- Now, let's open our web browser and try to access our website using the IP address:
http://<EC2-Public-IP-Address>:80

Note

Replace <EC2-Public-IP-Address> with your actual EC2 instance's public IP address.

![alt text](image-29.png)


- Remove the index.html file by running the following command: sudo rm /var/www/projectlamp/index.html 


# Enable PHP On The Website

- With the default DirectoryIndex settings on Apache, a file named index.html will always take precedence over an index.php file. To change the precedence of index files (such as index.php over index.html) in Apache, you'll need to edit the dir.conf file. Here’s how you can do it:

- Edit the dir.conf file using a text editor (such as nano or vi): sudo nano /etc/apache2/mods-enabled/dir.conf

- Look for the DirectoryIndex directive within this file. It typically looks like this:

<IfModule mod_dir.c>
- DirectoryIndex index.html index.cgi index.pl index.php index.xhtml index.htm
</IfModule>
To prioritize index.php over index.html, move index.php to the beginning of the list, like this:
<IfModule mod_dir.c>
    DirectoryIndex index.php index.html index.cgi index.pl index.xhtml index.htm
</IfModule>


![alt text](image-30.png)


- Press ctrl + x① on your keyboard to save and exit.


![alt text](image-33.png)

- Type y② to save the changes.


![alt text](image-34.png)

- When prompted to confirm the file name, simply press ENTER③ to save the changes with the existing file name.


![alt text](image-35.png)

- Finally, reload Apache for the changes to take effect: sudo systemctl reload apache2.

![alt text](image-36.png)

- Now, Apache will prioritize index.php over index.html when both files exist in the same directory.

- To create a new file named index.php inside your custom web root folder (/var/www/projectlamp), you can use the following command to open it in the nano text editor: nano /var/www/projectlamp/index.php.

- This will create a new file. Copy and paste the following PHP code into the new file:

<?php

phpinfo();


![alt text](image-32.png)

- Once you've saved and closed the file, go back to your web browser and refresh the page. You should see something like this:


![alt text](image-31.png)

Note

- This page provides information about your server from the perspective of PHP. It is useful for debugging and to ensure that your settings are being applied correctly.

- If you can see this page in your browser, then congratulations🎉 your PHP installation is working as expected. After verifying the relevant information about your PHP server through that page, it's recommended to remove the file you created, as it contains sensitive information about your PHP environment and your Ubuntu server. You can use the rm command to do so: sudo rm /var/www/projectlamp/index.php.

- You can always recreate this page if you need to access the information again later.


# Install Wordpress


- After setting up our LAMP environment, we can start installing WordPress. First, we'll download the WordPress installation files and place them in the default web server root directory: /var/www/html.

- Navigate to the directory using the cd command cd /var/www/html, and then download the WordPress installation files using the following command: sudo wget -c http://wordpress.org/latest.tar.gz


![alt text](image-37.png)

- Extract the files from the downloaded WordPress archive using the command: sudo tar -xzvf latest.tar.gz


![alt text](image-38.png)

- Run the command ls -l to confirm the existence of the wordpress directory in the current location (/var/www/html).


![alt text](image-39.png)


Note

- The files must be owned by the user of your web server. Identify the web server's user and assign the appropriate permissions accordingly. The user www-data is widely adopted as the default user for web server processes, especially on Ubuntu and Debian systems. This user oversees the operation of web server software (like Apache or Nginx) and manages incoming web requests. However, for verification and for tasks involving services that may not have a predefined user, checking the user of the web server is advisable.

- Check the user running your web server with the command: ps aux | grep apache | grep -v grep.


![alt text](image-40.png)


- This command filters processes related to Apache (apache2 on Ubuntu) and displays information about the user running those processes.

- Grant ownership of the WordPress directory and its files to the web server user (www-data) by running the command: sudo chown -R www-data:www-data /var/www/html/wordpress.


![alt text](image-41.png)

# Create a Database For Wordpress


- Access your MySQL root account with the following command: sudo mysql -u root -p①. Enter the password② you set earlier when prompted.


![alt text](image-42.png)


- To create a separate database named wp_db for WordPress to manage, execute the following command in the MySQL prompt: CREATE DATABASE wp_db;


![alt text](image-43.png)39

Note

-This command allows you to create a new database (wp_db) within your MySQL environment. Feel free to name it as you prefer.

- To access the new database, you can create a MySQL user account with a strong password using the following command: CREATE USER jay@localhost IDENTIFIED BY 'wp-password';

![alt text](image-44.png)

-Replace 'wp-password' with your preferred strong password for the MySQL user account.

To grant your created user (jay@localhost) all privileges needed to work with the wp_db database in MySQL, use the following commands:
GRANT ALL PRIVILEGES ON wp_db.* TO jay@localhost;
FLUSH PRIVILEGES;


![alt text](image-45.png)

Note

- This grants all privileges (ALL PRIVILEGES) on all tables within the wp_db database (wp_db.*) to the user jay when accessing from localhost. The FLUSH PRIVILEGES command ensures that MySQL implements the changes immediately. Adjust the database name (wp_db) and username (jay) as per your setup.

- Type exit to exit the MySQL shell.


![alt text](image-46.png)



- Grant executable permissions recursively (-R) to the wordpress folder using the following command: sudo chmod -R 777 wordpress/


![alt text](image-47.png)

Note

- This command sets read (r), write (w), and execute (x) permissions for the owner, group, and others on all files and directories within the wordpress folder. Using 777 permissions is quite permissive and may not be necessary for all files and folders; consider adjusting permissions based on security requirements.

- Change into the WordPress directory by running the command: cd wordpress.


![alt text](image-48.png)

# Configure Wordpress


- Once you've established a database for WordPress, the next crucial step is setting up and configuring WordPress itself. To begin, you'll need to create a configuration file tailored for WordPress.

- Rename the sample WordPress configuration file with the command: mv wp-config-sample.php wp-config.php.

- Edit the wp-config.php file using the command: sudo nano wp-config.php.

![alt text](image-49.png)

- Update the database settings in the wp-config.php file by replacing placeholders like database_name_here, username_here, and password_here with your actual database details.



![alt text](image-50.png)


- Modify the configuration file projectlamp.conf: sudo nano /etc/apache2/sites-available/projectlamp.conf to update the document root to the directory where your WordPress installation is located.


![alt text](image-51.png)


- After updating the document root to /var/www/html directory in your editor, save the changes and exit.


![alt text](image-52.png)


- Reload Apache for the changes to take effect: sudo systemctl reload apache2.

- Once you've completed these steps, you can access your WordPress page to complete the installation. Open your web browser and go to http://<EC2 IP>/wordpress/. This will lead you to the WordPress setup wizard where you can finalize the installation process.

Note

- Replace with the IP address of your EC2 instance when accessing your WordPress page.

- Select your preferred language and then click on Continue to proceed.


![alt text](image-53.png)


- Enter the required information and click on Install WordPress once you have finished.

- Site Title①: Enter the name of your WordPress website. It's recommended to use your domain name for better optimization.
Username②: Choose a username for logging into WordPress.
Password③: Set a secure password to protect your WordPress account.
Your email④: Provide your email address to receive updates and notifications.
Search engine visibility⑤: You can leave this box unchecked to prevent search engines from indexing your site until it's ready.


![alt text](image-54.png)



- WordPress has been successfully installed. You can now log in to your admin dashboard using the previously set up information by clicking the Log In button.


![alt text](image-55.png)


- Enter your username and password, then click Log In to access your WordPress admin dashboard.

![alt text](image-56.png)


Once you successfully log in, you will be greeted by the WordPress dashboard page.
53

Create An A Record
To make your website accessible via your domain name rather than the IP address, you'll need to set up a DNS record. I did this by buying my domain from Namecheap and then moving hosting to AWS Route 53, where I set up an A record.

Note

Visit Project1 for instructions on how to create a hosted zone.

Point your domain's DNS records to the IP addresses of your Apache load balancer server.

In route 53, click on Create record.

54

Paste your IP address➀ and then click on Create records➁ to create the root domain.
55

Click on Create record again, to create the record for your sub domain.
56

Paste your IP address➀, input the Record name(www➁) and then click on Create records➂.
57

To update your Apache configuration file in the sites-available directory to point to your domain name, use the command: sudo nano /etc/apache2/sites-available/projectlamp.conf.
Note

This command opens the projectlamp.conf file in the nano text editor with superuser privileges (sudo). Within the editor, adjust the necessary details to reflect your domain name configuration.

Ensure that the server settings in your Apache configuration point to your domain name, and that the document root accurately points to your WordPress directory. Once you've made these adjustments, save the changes and exit the editor.
<VirtualHost *:80>
    ServerName <Your root domain name>
    ServerAlias <Your sub domain name>
    ServerAdmin webmaster@<Your root domain name>

    DocumentRoot /var/www/html/wordpress

    <Directory /var/www/html/wordpress>
        Options Indexes FollowSymLinks
       # AllowOverride All
        Require all granted
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>

58

Note

The new configuration defines how Apache should handle requests for your domain, and its subdomain. With this configuration: Apache will handle requests for cloudghoul.online and www.cloudghoul.online. Files will be served from the /var/www/html/wordpress directory. Directory listings and symbolic links are allowed. The directory can be accessed by any client. Error logs will be written to /var/log/apache2/error.log. Access logs will be written to /var/log/apache2/access.log in the combined log format.

To update your wp-config.php file with DNS settings, use the following command: sudo nano wp-config.php and add these lines to the file:
/** MY DNS SETTINGS */
define('WP_HOME', 'http://<domain name>');

define('WP_SITEURL', 'http://<domain name>');
Replace http://<domain name> with your actual domain name. Save the changes and exit the editor.

59

Reload your Apache server to apply the changes with the command: sudo systemctl reload apache2, After reloading, visit your website at http://<domain name> to view your WordPress site. Replace with your actual domain name.
60

To log in to your WordPress admin portal, visit http://<domain name>/wp-admin, Enter your username① and password②, then click on log In③. Replace with your actual domain name.
61

Note

My domain name is cloudghoul.online, so i'll visit http://cloudghoul.online/wp-admin.

Now that your WordPress site is successfully configured to use your domain name, the next step is to secure it by requesting an SSL/TLS certificate.
62

Install certbot and Request For an SSL/TLS Certificate
Install certbot by executing the following commands: sudo apt update sudo apt install certbot python3-certbot-apache

Run the command sudo certbot --apache to request your SSL/TLS certificate. Follow the instructions provided by Certbot to select the domain name for which you want to enable HTTPS.

63

You should receive a message confirming that the certificate has been successfully obtained.
64

Visit your website to confirm, and you'll notice that the "not secure" warning no longer appears, indicating that your site is now secure with HTTPS.
65

66

The End Of Project 4