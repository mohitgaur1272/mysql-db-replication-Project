# Project 

# Hosting a website with data replication in two instance (master and slave). 

## Part 1

## ROADMAP 

Here we will create two instance first instance for save the information of user by login and rajistration page in first part and second instance for database replication in second part.Now, we will use apache server for hosting a website and mysql database for save the rajistration page information and will use php language for backend work of to run all files behind the hosting.

## Now we will create two instance with ami of ubuntu

<img src=1.png>

## connect the our first instance our local system by ssh on terminal
ssh -i "your key" ubuntu@(your publicip).compute-1.amazonaws.com
<img>
## after connecting the instance then run the command
sudo apt update 
<img>

### install the apache2 server for hosting the website 
sudo apt install apache2 -y 
<img>

### start the apache2 service and enable 
sudo systemctl start apache2
sudo systemctl enable apache2 
<img>

## Now we will install the mysql-server 
sudo apt install mysql-server 
<img>

sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf 
## NOTE:we will change the bind address from 127.0.0.1 into 0.0.0.0 in this loaction "/etc/mysql/mysql.conf.d/mysqld.cnf"
<img> 

### and start the mysql service
sudo systemctl start mysql-server 

### Then install the PHP service for backend
sudo apt install php libapache2-mod-php php-mysql -y
<img>

## Now we wil configure the apache2:-

### Set the correct permissions for your web files:
sudo chown -R www-data:www-data /var/www/html
sudo chmod -R 755 /var/www/html

## Create a MySQL User and Database
### login into MySQL:
sudo mysql -u root 

### Create a new user and a Database then after that grant privileges to the database:
CREATE USER 'your_username'@'localhost' IDENTIFIED BY 'your_password';


### Then-
FLUSH PRIVILEGES;

### Create a new database:
create database your_database_name;
### To see your database is created type -
show databases;
### To grant all the access to your user on that database type-
GRANT ALL PRIVILEGES ON your_database_name.* TO 'your_username'@'localhost';


### Then-
FLUSH PRIVILEGES;

## Now we will create all html and php file in /var/www/html/
## so first 
"sudo vim signup.html"

<!DOCTYPE html>
<html>
<head>
    <title>Sign Up</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
        }

        .signup-container {
            width: 400px;
            margin: 0 auto;
            background-color: #fff;
            padding: 20px;
            border-radius: 5px;
            box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
        }

        h2 {
            text-align: center;
            color: #333;
        }

        .form-group {
            margin: 10px 0;
        }

        label {
            display: block;
            font-weight: bold;
            margin-bottom: 5px;
            color: #333;
        }

        input[type="text"],
        input[type="password"],
        input[type="email"],
        input[type="tel"] {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid #ccc;
            border-radius: 5px;
        }

        .btn {
            width: 100%;
            padding: 10px;
            background-color: #007bff;
            color: #fff;
            border: none;
            border-radius: 5px;
            cursor: pointer;
        }

        .btn:hover {
            background-color: #0056b3;
        }
    </style>
</head>
<body>
    <div class="signup-container">
        <h2>Sign Up</h2>
        <form action="database.php" method="post">
            <div class="form-group">
                <label for="username">Username:</label>
                <input type="text" id="username" name="username" required>
            </div>

            <div class="form-group">
                <label for="password">Password:</label>
                <input type="password" id="password" name="password" required>
            </div>

            <div class="form-group">
                <label for="email">Email:</label>
                <input type="email" id="email" name="email" required>
            </div>

            <div class="form-group">
                <label for="phone">Phone Number:</label>
                <input type="tel" id="phone" name="phone" required>
            </div>

            <input type="submit" value="Sign Up" class="btn">
        </form>
    </div>
</body>
</html>


## Second 
"sudo vim login.html"



## Now our 2 html main page are done and we will create database.php file for backand service connect the database
