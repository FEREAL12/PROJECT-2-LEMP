# WEB STACK IMPLEMENTATION(LEMP STACK)
### Create a new EC2 Instance 
### Download and install Git Bash
### Launch Git Bash
``` bash
ssh -i feyi-ec2.pem ubuntu@<EC2-Public-IP-address>
```
### Install Nginx Server 
```bash
sudo apt update
sudo apt install nginx
```
Verified that nginx was successfully installed
```bash
sudo systemctl status nginx
```
Opened TCP Port 80 inbound rules
check how we can access it locally in our Ubuntu shell
```bash
curl http://localhost:80
```
### Enter the Public ipv4 address and confirmed that Nginx Web server is installed
![nginx](https://user-images.githubusercontent.com/41236641/116161430-a9ba0d00-a6eb-11eb-94a8-2d4bc1b6a928.JPG)

### Install Mysql
```bash
sudo apt install mysql-server
sudo mysql_secure_installation
```
### Test if I am able to log in to the MySQL console
```bash
sudo mysql
```
### exit the MySQL console
```bash
mysql> exit
```

### Install Php 
```bash
sudo apt install php-fpm php-mysql
```

### Configuring Nginx to Use PHP Processor
#### Create the root web directory
```bash
sudo mkdir /var/www/projectLEMP
```

### assign ownership of the directory with the $USER environment variable to the Current System user
```bash
sudo chown -R $USER:$USER /var/www/projectLEMP
```

### open a new configuration file in Nginx’ sites available directory using your most preferred command line Editor
```bash
sudo vi /etc/nginx/sites-available/projectLEMP
```
### Having run this command, It creates a new blank file. Paste in the following bare-bones configuration
```bash
#/etc/nginx/sites-available/projectLEMP

server {
    listen 80;
    server_name projectLEMP www.projectLEMP;
    root /var/www/projectLEMP;

    index index.html index.htm index.php;

    location / {
        try_files $uri $uri/ =404;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
     }

    location ~ /\.ht {
        deny all;
    }

}
```
### Activate your configuration
```bash
sudo ln -s /etc/nginx/sites-available/projectLEMP /etc/nginx/sites-enabled/
```
### Test your configuration for syntax errors

```bash
sudo nginx -t
```

### Disable default Nginx host 
```bash
sudo unlink /etc/nginx/sites-enabled/default
```

### Reload Nginx
```bash
sudo systemctl reload nginx
```

### To Create an index.html file in that location to test that your new server block works as expected
```bash
sudo echo 'Hello LEMP from hostname' $(curl -s http://169.254.169.254/latest/meta-data/public-hostname) 'with public IP' $(curl -s http://169.254.169.254/latest/meta-data/public-ipv4) > /var/www/projectLEMP/index.html
```
### Testing PHP with NGINX
### creating a test PHP file in your document root. Open a new file called info.php within your document root in the text editor
```bash
vim /var/www/projectLEMP/info.php
```
### paste the php code
```bash
<?php
phpinfo();
```
![php-project2](https://user-images.githubusercontent.com/41236641/116163053-ec311900-a6ee-11eb-87ed-652300453b2f.JPG)

### Retrieving data from Mysql Database with PHP
### First, connect to the MySQL console using the root account
```bash
sudo mysql
```
### create a new database, run the following command from your MySQL console 
```bash
mysql> CREATE DATABASE `example_database`;
```

### Create a new user and grant him full privileges on the database just created
```bash
mysql>  CREATE USER 'example_user'@'%' IDENTIFIED WITH mysql_native_password BY 'password';
```

### Give the user permission over the database
 ```bash
 mysql> GRANT ALL ON example_database.* TO 'example_user'@'%';
 ```
 ### Test if the new user has the proper permissions
 ```bash
 mysql -u example_user -p
 ```
 
 ### confirm that you have access to the database
 ```bash
 mysql> SHOW DATABASES;
 Output
+--------------------+
| Database           |
+--------------------+
| example_database   |
| information_schema |
+--------------------+
2 rows in set (0.000 sec)
 ```
### create a test table named todo_list
```bash
CREATE TABLE example_database.todo_list (
mysql>     item_id INT AUTO_INCREMENT,
mysql>     content VARCHAR(255),
mysql>     PRIMARY KEY(item_id)
mysql> );

mysql> INSERT INTO example_database.todo_list (content) VALUES ("My first important item");
```
To confirm that the data was successfully saved to your table
```bash
mysql>  SELECT * FROM example_database.todo_list;
```
### output
```bash
Output
+---------+--------------------------+
| item_id | content                  |
+---------+--------------------------+
|       1 | My first important item  |
|       2 | My second important item |
|       3 | My third important item  |
|       4 | and this one more thing  |
+---------+--------------------------+
4 rows in set (0.000 sec)

### create a PHP script that will connect to MySQL and query for your content
#### Create a new PHP file in your custom web root directory
```bash
vim /var/www/projectLEMP/todo_list.php

```
### Paste the following code to the todolist.php file script
```bash
<?php
$user = "example_user";
$password = "password";
$database = "example_database";
$table = "todo_list";

try {
  $db = new PDO("mysql:host=localhost;dbname=$database", $user, $password);
  echo "<h2>TODO</h2><ol>";
  foreach($db->query("SELECT content FROM $table") as $row) {
    echo "<li>" . $row['content'] . "</li>";
  }
  echo "</ol>";
} catch (PDOException $e) {
    print "Error!: " . $e->getMessage() . "<br/>";
    die();
}
```
![todolistdb-project2](https://user-images.githubusercontent.com/41236641/116164298-70849b80-a6f1-11eb-8809-99d44774911a.JPG)
