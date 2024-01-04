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
sudo vim signup.html

```
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
```

## Second 
sudo vim login.html

```

<!DOCTYPE html>
<html>
<head>
    <title>Login</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            background-color: #f4f4f4;
        }

        .login-container {
            width: 300px;
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
        input[type="password"] {
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
    <div class="login-container">
        <h2>Login</h2>
        <form action="login.php" method="post">
            <div class="form-group">
                <label for="username">Username:</label>
                <input type="text" id="username" name="username" required>
            </div>

            <div class="form-group">
                <label for="password">Password:</label>
                <input type="password" id="password" name="password" required>
            </div>

            <input type="submit" value="Login" class="btn">
        </form>
    </div>
</body>
</html>

```
## Now our 2 html main page are done and we will create database.php file for backand service connect the database
sudo vim database.php

```
<?php
$servername = "localhost";
$username = "mohit"; // Replace with your MySQL username
$password = "mohit123"; // Replace with your MySQL password
$dbname = "test"; // Replace with your MySQL database name

// Create a connection
$conn = new mysqli($servername, $username, $password, $dbname);

// Check connection
if ($conn->connect_error) {
    die("Connection failed: " . $conn->connect_error);
}

// Get form data from the signup form
if ($_SERVER["REQUEST_METHOD"] == "POST") {
    $username = $_POST["username"];
    $password = $_POST["password"];
    $email = $_POST["email"];
    $phone = $_POST["phone"];

    // Insert data into the 'users' table (change 'users' to your table name)
    $sql = "INSERT INTO users (username, password, email, phone) VALUES ('$username', '$password', '$email', '$phone')";

    if ($conn->query($sql) === TRUE) {
        echo "Registration Completed";
    } else {
        echo "Error: " . $sql . "<br>" . $conn->error;
    }
}

$conn->close();
?>

```
## NOTE:- here some changes you will have to do this file that I presented this sign (//)  in database.php file.

### now we will create index.php for signup.html file because this file will help to signup.html for backend work.
"sudo vim index.php"

```
<?php

session_start();

if (isset($_SESSION["user_id"])) {

    $mysqli = require __DIR__ . "/database.php";

    $sql = "SELECT * FROM user
            WHERE id = {$_SESSION["user_id"]}";

    $result = $mysqli->query($sql);

    $user = $result->fetch_assoc();
}

?>
<!DOCTYPE html>
<html>
<head>
    <title>Home</title>
    <meta charset="UTF-8">
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/water.css@2/out/water.css">
</head>
<body>

    <h1>Home</h1>

    <?php if (isset($user)): ?>

        <p>Hello <?= htmlspecialchars($user["name"]) ?></p>

        <p><a href="logout.php">Log out</a></p>

    <?php else: ?>

        <p><a href="login.html">Log in</a> or <a href="signup.html">sign up</a></p>

    <?php endif; ?>

</body>
</html>
```
## NOTE:- now we will create login.php file for login.html that will help login.html file for backend word and match the data in database.
"sudo vim login.php"
```
<?php
// This is a simple example and should not be used in a production environment.
// In a real application, you should use secure practices like password hashing and validation.

if ($_SERVER["REQUEST_METHOD"] === "POST") {
    // Retrieve user input from the login form
    $username = $_POST["username"];
    $password = $_POST["password"];

    // You should replace these values with actual user data and validation logic.
    $validUsername = "mohit"; // Replace with a valid username
    $validPassword = "mohit123"; // Replace with a valid password

    // Check if the entered username and password match the valid values
    if ($username === $validUsername && $password === $validPassword) {
        // Authentication successful
        // You can set a session variable here to track the user's login status.
        session_start();
        $_SESSION["user"] = $username;
        header("Location: home.php"); // Redirect to the home page after successful login
        exit;
    } else {
        // Authentication failed
        echo "Invalid username or password. Please try again.";
    }
}
?>
```
### If our database matches username and password of user so then user redirect our home.php file or not so the user will see "Invalid username or password. Please try again"
"sudo vim home.php"
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Beautiful Home Page</title>
    <style>
        body {
            font-family: 'Arial', sans-serif;
            background-color: #f8f8f8;
            margin: 0;
            padding: 0;
        }

        header {
            background-color: #333;
            color: #fff;
            padding: 15px 0;
            text-align: center;
        }

        nav {
            display: flex;
            justify-content: center;
            background-color: #007BFF;
            padding: 10px 0;
        }

        nav a {
            color: #fff;
            text-decoration: none;
            padding: 10px 20px;
            margin: 0 10px;
            border-radius: 5px;
            transition: background-color 0.3s ease;
        }

        nav a:hover {
            background-color: #0056b3;
        }

        .main-content {
            max-width: 800px;
            margin: 20px auto;
            padding: 20px;
            background-color: #fff;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
            border-radius: 8px;
        }

        footer {
            background-color: #333;
            color: #fff;
            padding: 15px 0;
            text-align: center;
        }
    </style>
</head>
<body>

    <header>
        <h1>Welcome to Our new Website</h1>
    </header>

    <nav>
        <a href="#">Home</a>
        <a href="#">About</a>
        <a href="#">Services</a>
        <a href="#">Contact</a>
    </nav>

    <div class="main-content">
        <h2>Home Page Content Goes Here</h2>
        <p>This is a very usefull website.</p>
        <!-- Your main content goes here -->
    </div>

    <footer>
        &copy; 2023 Your Beautiful Website Name
    </footer>

</body>
</html>

```
## Now we have to create the table in the my sql for our incoming users which will provide us their data in this form-(name,password,email and phone number).
## Log in to MySQL:
```
sudo mysql -u your_useer_name -p 
```
## Type -
```
show databases;
```
## Select that database which you created earlier.
## Type -
```
use your_database_name;
```
## Type to create your table -
```
create table your_tablename (username varchar(50), password varchar(50), email varchar(50), phone bigint(50)); 
```
## To see your table is created type -
```
select * from your_tablename;
```
# Now you have created table for data entry and everything is done...lets move on part 2.

# PART-2

# ROADMAP 

I have two instance first master and second slave and master instance store any data so data show  in slave instance.
# SOLUTION------------------------------------------
## To set up MySQL database replication between two AWS Ubuntu instances, where Instance 1 acts as the master and Instance 2 acts as the slave, follow these steps:
## 1- Install MySQL on both instances:
## SSH into each AWS Ubuntu instance and install MySQL server:
```
sudo apt update
sudo apt install mysql-server
```

## 2- Configure MySQL on the master (Instance 1):
## Open the MySQL configuration file on the master server:
```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
## Locate the [mysqld] section and add the following lines to enable binary logging:
```
server-id=1
log_bin = /var/log/mysql/mysql-bin.log
```
## I am showing an example how my mysqld.cnf file looks like-
```
#
# The MySQL database server configuration file.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

[mysqld]
#
# * Basic Settings
#
user            = mysql
# pid-file      = /var/run/mysqld/mysqld.pid
# socket        = /var/run/mysqld/mysqld.sock
# port          = 3306
# datadir       = /var/lib/mysql


# If MySQL is running as a replication slave, this should be
# changed. Ref https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_tmpdir
# tmpdir                = /tmp
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0
mysqlx-bind-address     = 127.0.0.1
#
# * Fine Tuning
#
key_buffer_size         = 16M
# max_allowed_packet    = 64M
# thread_stack          = 256K

# thread_cache_size       = -1

# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover-options  = BACKUP

# max_connections        = 151

# table_open_cache       = 4000

#
# * Logging and Replication
#
# Both location gets rotated by the cronjob.
#
# Log all queries
# Be aware that this log type is a performance killer.
# general_log_file        = /var/log/mysql/query.log
# general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
# slow_query_log                = 1
# slow_query_log_file   = /var/log/mysql/mysql-slow.log
# long_query_time = 2
# log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
 server-id              = 1
 log_bin                        = /var/log/mysql/mysql-bin.log
# binlog_expire_logs_seconds    = 2592000
max_binlog_size   = 100M
# binlog_do_db          = include_database_name
# binlog_ignore_db      = include_database_name
```
## Save the file and restart MySQL:
```
sudo systemctl restart mysql
```

## 3-Create a replication user on the master:
## Log in to MySQL as the root user:
```
sudo mysql -u root
```
## Once logged in, run the following SQL commands to create a replication user and grant appropriate privileges:
```
CREATE USER 'replication_user_you_created'@'%' IDENTIFIED BY 'your_password_here';
```
```
GRANT ALL PRIVILEGES ON *.* TO 'your_user'@'%';
```
```
FLUSH PRIVILEGES;
```
# Get the master's binary log position:
## While still logged in as root on the master, note the current binary log position:
```
SHOW MASTER STATUS;
```
# NOTE - You'll need the values of File and Position for configuring the slave later.Lets move on the [instance-2]

## 4- Configure MySQL on the slave (Instance 2):

## Open the MySQL configuration file on the slave server:
```
sudo vim /etc/mysql/mysql.conf.d/mysqld.cnf
```
## Locate the [mysqld] section and add the following lines to enable replication:
```
server-id=2
```
## I am showing an example how my mysqld.cnf file looks like-
```
# The MySQL database server configuration file.
#
# One can use all long options that the program supports.
# Run program with --help to get a list of available options and with
# --print-defaults to see which it would actually understand and use.
#
# For explanations see
# http://dev.mysql.com/doc/mysql/en/server-system-variables.html

# Here is entries for some specific programs
# The following values assume you have at least 32M ram

[mysqld]
#
# * Basic Settings
#
user            = mysql
# pid-file      = /var/run/mysqld/mysqld.pid
# socket        = /var/run/mysqld/mysqld.sock
# port          = 3306
# datadir       = /var/lib/mysql


# If MySQL is running as a replication slave, this should be
# changed. Ref https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_tmpdir
# tmpdir                = /tmp
#
# Instead of skip-networking the default is now to listen only on
# localhost which is more compatible and is not less secure.
bind-address            = 0.0.0.0
# mysqlx-bind-address   = 127.0.0.1
#
# * Fine Tuning
#
key_buffer_size         = 16M
# max_allowed_packet    = 64M
# thread_stack          = 256K

# thread_cache_size       = -1

# This replaces the startup script and checks MyISAM tables if needed
# the first time they are touched
myisam-recover-options  = BACKUP

# max_connections        = 151

# table_open_cache       = 4000

#
# * Logging and Replication
#
# Both location gets rotated by cronjob
#
# Log all queries
# Be aware that this log type is a performance killer.
# general_log_file        = /var/log/mysql/query.log
# general_log             = 1
#
# Error log - should be very few entries.
#
log_error = /var/log/mysql/error.log
#
# Here you can see queries with especially long duration
# slow_query_log                = 1
# slow_query_log_file   = /var/log/mysql/mysql-slow.log
# long_query_time = 2
# log-queries-not-using-indexes
#
# The following can be used as easy to replay backup logs or for replication.
# note: if you are setting up a replication slave, see README.Debian about
#       other settings you may need to change.
# server-id             = 1
server-id               = 2
# log_bin                       = /var/log/mysql/mysql-bin.log
# binlog_expire_logs_seconds    = 2592000
max_binlog_size   = 100M
# binlog_do_db          = include_database_name
# binlog_ignore_db      = include_database_name
```
## Save the file and restart MySQL:
```
sudo systemctl restart mysql
```

## NOTE - we have to make sure that our inbound and outbound rules have access to allow traffic from port 3306 for my sql and add new rule in inbound rules and allow,

# ALL/ICMP traffic and anywhere in CIDR and save the rules.

# 5-Start the replication process on the slave:

## Log in to MySQL as the root user on the slave:
```
sudo mysql -u root
```
## Run the following SQL command to configure the replication settings on the slave:
```
CHANGE MASTER TO MASTER_HOST='master_ip_address', MASTER_USER='replication_user', MASTER_PASSWORD='password', MASTER_LOG_FILE='binlog_file_from_master', MASTER_LOG_POS=binlog_position_from_master;
```
# master_ip_address: The private or public IP address of Instance 1 (master).
# replication_user: The replication user you created earlier.
# password: The password for the replication user.
# binlog_file_from_master and binlog_position_from_master: The values you obtained from the SHOW MASTER STATUS; command on the master.

## 6- Start replication on the slave:

## After running the CHANGE MASTER TO command, start the replication process on the slave:
```
START SLAVE;
```
## 7- Check slave status:
##  Verify that the replication process is running without errors on the slave:
```
SHOW SLAVE STATUS\G
```
## Check the Slave_IO_Running and Slave_SQL_Running fields to ensure both are Yes.
## Now we have to go to the master instance (instance1) and login to mysql to create a database and insert a table.
## Log in to MySQL:
```
sudo mysql -u your_user -p 
```
## Create a new database:
```
create database your_database_name;
```
```
show databases;
```
```
use your_database_name;
```
## Create anytype of table you want to create.i am taking an example of creating an employee
## Table which is -
```
CREATE TABLE employees (
    employee_id INT PRIMARY KEY,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    hire_date DATE,
    department VARCHAR(100)
);
```
## To see your tables type-
```
show tables;
```

## To insert data in your table type-
```
insert into table_name ('name','city','standard') values('kitty','england','A');
```
## To see what data have you typed in your table type -
```
select * from table_name;
```

## Now check the entry in slave instance.
## Everything is done and With these steps, you should have set up MySQL database replication between the two AWS Ubuntu instances. Any changes made to the master database on Instance 1 will now be replicated to the slave database on Instance 2

## ROADMAP
## Now we have to use master instance as registration database and slave instance as login database by which, whenever user type information on registration page it will be saved in master instance and replicate the data in slave instance and whenever user try to login then it will use slave instance to cross verify the user data and give access.

# SOLUTION----------------------------------------------------------------------------
## To do this task firstly, we have to change the servername from localhost to public-ip of master instance in database.php which we created in /html/.
## Go to your website instance,connect your instance. After the connection type-
```
cd /var/www/html
```
## Edit the file-
```
sudo vim database.php
```
## Change the servername to public-ip of your master instance. press esc and type:wq exit from the file.
## Move on to the slave instance, Now we have to create a new mysql user for the cross verification of data in slave instance for login.php. which will verfiy the user and let user access the welcome page.

## To create user in mysql type -
```
sudo mysql
```
```
CREATE USER 'replication_user_you_created'@'%' IDENTIFIED BY 'your_password_here';
```
```
GRANT ALL PRIVILEGES ON *.* TO 'your_user'@'%';
```
```
FLUSH PRIVILEGES;
```
## Now GO BACK IN WEBSITE INSTANCE and open the login.php file and change the servername to public_ip of slave instance.change the username to the user you just created im slave instance.
## Change the password with your user-password and change the dbname with the database you created in MASTER INSTANCE IN MYSQL. FOR EX- if i created moon database with the help of mysql user which i created in master instance then i have to copy paste the name of moon database in the place of dbname.
## type-
```
cd /var/www/html
```
```
sudo vim login.php
```
## Change everthing which i mentioned above and press esc and type:wq exit from the file.
# Everything is done and we have completed our project.
