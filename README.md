# Launch an Amazon EC2 in LAMP Environment



## Scenario
The following procedures help you install an Apache web server with PHP and MySQL support on your Amazon Linux instance (sometimes called a LAMP web server or LAMP stack). You can use this server to host a static website or deploy a dynamic PHP application that reads and writes information to a database. This lab introduces you to Amazon Elastic Compute Cloud ([Amazon EC2](https://aws.amazon.com/ec2/)) using the AWS Management Console.


## Prerequisites
>The workshop’s region will be in ‘N.Virginia’

>Download Putty and PuTTYgen: IF you don’t already have the **PuTTy client/PuTTYgen** installed on your machine, you can download and then launch it from here: https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html


## Lab tutorial
### Create Your VPC
1.1.  In the **AWS Management Console**, on the **service menu**, click **VPC**.

1.2. In the navigation pane, click **Your VPCs**.

1.3. Click **Create VPC**, enter the following details:
* Name tag: **My Lab VPC**
* IPv4 CIDR block: **10.0.0.0/16**

1.4. Click **Yes, Create**.

1.5. In the navigation pane, click **Internet Gateways**.

1.6. Click **Create Internet Gateway**, enter Name tag: **My Lab IGW**, then click **Create**.

1.7. Choose **My Lab IGW**, click **Actions**, click **Attach to VPC**, and then choose **My Lab VPC** you created, click **Yes, Attach**.

1.8. In the navigation pane, click **Subnets**.

1.9. Click **Create Subnet**, enter the following details:
* Name tag: **Public Subnet 1**
* VPC: **My Lab VPC**
* Availability Zone: **us-east-1a**
* IPv4 CIDR block: **10.0.1.0/24**

1.10. Click **Yes, Create**.

1.11. Click **Create Subnet**, enter the following details:
* Name tag: **Public Subnet 2**
* VPC: **My Lab VPC**
* Availability Zone: **us-east-1b**
* IPv4 CIDR block: **10.0.2.0/24**

1.12. Click **Yes, Create**.

1.13. In the navigation pane, click **Route Tables**.

1.14. Click **Create Route Table**, enter the following details:
* Name tag: **Public Route Table**
* VPC: select **My Lab VPC**

1.15. Click **Yes, Create**.

1.16. Select the **Public Route Table** you just created, and then click **Routes** tab in the lower pane of the console, click **Edit**, click **Add another route**, enter the following details:
* Destination: **0.0.0.0/0**
* Target: choose **igw-xxxxxxxx** that you created earlier

1.17. Click **Save**.

1.18. Select the **Public Route Table** you just created, and then click **Subnet Associations** tab in the lower pane of the console, click **Edit**, choose **Public Subnet 1** and **Public Subnet 2** you just created to associate.

1.19. Click **Save**.

### Launch an instance

2.1. On the **service** menu,click **EC2**.

2.2. Click **Launch Instance**.

2.3. In the navigation pane, choose **Quick Start**, in the row for the first **Amazon Linux AMI**, click **Select**.

2.4. On **Step2: Choose a Instance Type** page,make sure **t2.micro** is selected and click **Next: Configure Instance Details**.

2.5. On **Step3: Configure Instance Details** page, enter the following and leave all other values with their default:
* Network: **My Lab VPC**
* Subnet: **Public Subnet 1**
* Auto-assign Public IP: click **Enable**

2.6. Click **Next: Add Storage**, leave all values with their default.

2.7. Click **Next: Tag Instance**.

2.8. On **Step5: Tag Instance** page, enter the following information:
* Key: **Name**
* Value: **Web Server**

2.9. Click **Next: Configure Security Group**.

2.10. On **Setp6: Configure Security Group** page, click **create a new security group**, enter the following information:
* Security group name: **WebServerSG**
* Description: **Web Server Security Group**

2.11. Click **Add Rule**.

2.12. For Type, **click SSH(22),and HTTP(80)**.

![1.png](/images/1.png)

2.13. Click **Review and Launch**.

2.14. Review the instance information and click **Launch**.

2.15. Click **Create a new key pair**, enter the **Key pair name (ex.amazonec2_keypair_virginia)**, click **Download Key Pair**.

2.16. Click **Launch Instances**.

2.17. Scroll down and click **View Instances**.

2.18. Wait for **Web Server** shows 2/2 checks passed in the **Status Checks** column.
* This will take 3-5 minutes. Use the refresh icon at the top right to check for updates.

2.19. On the Description tab in the lower panel, note the **Public IP** for the instance.

### Connect to your instance (Windows only)

3.1. Start PuTTYgen.exe, click **Load**. By default, PuTTYgen display only files with the extension .ppk. to locate your .pem file, select the option to display files of all types.

![2.png](/images/2.png)

3.2. Select your .pem file **(ex. amazonec2_keypair_virginia.pem)**, and then click **Open**. Click **OK** to dismiss the confirmation dialog box.

3.3. Click **Save private key** to save the key in the format that PuTTY can use. PuTTYgen displays a warning about saving the key without a passphrase, click Yes.

3.4. Specify the same name for the key that you used for the key pair **(ex. amazonec2_keypair_virginia.ppk)**. PuTTY automatically adds the .ppk extension.

3.5. Launch **PuTTY** by running the **PuTTY.exe** file you downloaded.

3.6. For **Host Name**, enter the Public IP address from your Web Server instance which you copied into a text editor in the lab.

![3.png](/images/3.png)

![4.png](/images/4.png)

3.7. In the Connection list, click **Connect>SSH>Auth**, click **Browse** to choose your key pair (ex.**amazonec2_keypair_virginia.ppk**), click **Open**.

![5.png](/images/5.png)

3.8. Enter **ec2-user**,and press ENTER.You are now logged into your **Web Server** instance.

![6.png](/images/6.png)

### Connect to your instance (Linux/OSX only)

>Note: This section is for Linux and Max OSX users only. If you are running Windows but have not yet connected to your instance, go back to previous step. If you have already connected to your instance, skip ahead to next step.

4.1. To connect to your EC2 instance, run the following commands in Terminal:
	
	chmod 400  <path and name of pem>
	ssh –i <path and name of pem> ec2-user@<public IP>

* For **path and name of pem**, substitute the path/filename to the .pem file you downloaded.
* For **public IP**, substitute the public IP address for your **Web Server** instance which you copied into a text editor earlier in the lab.

### Installing and start the Web server on EC2

5.1. Update all your software package, enter:
	
    [ec2-user ~]$ sudo yum update -y

5.2. Use yum install command to install multiple software packages and all related dependencies at the same time.

	[ec2-user ~]$ sudo yum install -y httpd24 php70 mysql56-server php70-mysqlnd

5.3. Start Apache web server.

	[ec2-user ~]$ sudo service httpd start
	Starting httpd: 				[ OK ]

5.4. Use the **chkconfig** command to configure the Apache web server to start at each system boot.

	[ec2-user ~]$ sudo chkconfig httpd on
	[ec2-user ~]$ chkconfig --list httpd   

>Note: The chkconfig command does not provide any confirmation message when you successfully use it to enable a service. You can verify that httpd dos on by running the following command:

![7.png](/images/7.png)

5.5. Test your web server. In a web browser, enter Public DNS address (or the public IP address) of your EC2; you should see the Apache test page.

![8.png](/images/8.png)

### Set file permissions of Apache httpd serves

6.1. Add your user (ex. **ec2-user**) to the **apache** group

	[ec2-user ~]$ sudo usermod -a -G apache ec2-user
    
6.2. Log out and log back in again, use **exit** command.

	[ec2-user ~]$ exit
    
6.3. Reconnect to your instance, and then run the following command to verify your membership in the **apache** group.

	[ec2-user ~]$ groups 
    ec2-user wheel apache

![9.png](/images/9.png)

6.4. Change the group ownership of **/var/www** and its contents to the **apache** group.

	[ec2-user ~]$ sudo chown -R ec2-user:apache /var/www

6.5. Change the directory permissions of **/var/www** and its subdirectories to add group write permissions and to set the group ID on future subdirectories.

	[ec2-user ~]$ sudo chmod 2775 /var/www
	[ec2-user ~]$ find /var/www -type d -exec sudo chmod 2775 {} \;

6.6. Recursively change the file permissions of /var/www and its subdirectories to add group write permissions.

	[ec2-user ~]$ find /var/www -type f -exec sudo chmod 0664 {} \;
    
6.7. Add a simple PHP file in the Apache document root to test LAMP web server.

	[ec2-user ~]$ echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php

6.8. In a web browser, enter the URL of the file you just created. This URL is the public DNS address of your EC2, for example:

	http://**my.public.dns.amazonaws.com**/phpinfo.php

![10.png](/images/10.png)

6.9. You can review the item which just type-in via console


## Conclusion

Congratulations! You now have learned how to:
* Logged into Amazon Management Console
* Create an Amazon Linux Instance from and Amazon Machine Image (AMI).
* Find your instance in the Amazon Management Console
* Logged into your instance



