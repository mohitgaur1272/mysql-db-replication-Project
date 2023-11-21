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
