## Create an AWS web server 
-------------------------------------------
Configuration
----------------------------------------
VPC
- Security Group
  - HTTP (80) Anywhere 
  - SSH (22) Anywhere
- Public Subnet
  
EC2 Bash Commands

```
$sudo yum update -y
$sudo yum install -y httpd php
$sudo service httpd start
$sudo chkconfig httpd on*
$sudo groupadd www
$sudo usermod -a -G www ec2-user 
$exit // for the refresh
$sudo chown -R root:www /var/www    
$sudo chmod 2775 /var/www
#Change the directory permissions
$find /var/www -type d -exec sudo chmod 2775 {} +	
find /var/www -type f -exec sudo chmod 0664 {} +       /
```
Connect Apache web server 
```

$cd /var/www
$mkdir inc
$cd inc        // change the directory and create a new subdirectory
$>dbinfo.inc
$nano dbinfo.inc   // Create a new file in the inc directory named dbinfo.inc
```

in nano
```
	<html>
		<title>
			"Hello"
		</title>
		<body>
		Hello
		</body>
	</html>
```
Save File

The web page return hello 

