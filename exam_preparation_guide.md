# Exam Study Guide & Solutions

This guide breaks down two essential PHP and MySQL practical tasks. Each section states the requirements, presents the complete code solution, and breaks down every block of code in simple terms for exam preparation.

---

## Task 1: Student Search Page (`student.php`)

### Task Requirements
1. Create a new file named `student.php`.
2. Write PHP code at the top to connect to `school_db`.
3. Use `include 'nav.php';` to show the navigation bar.
4. Create an HTML form (`method="POST"`) with an input for the student's name and a "Search" button.
5. Write PHP to receive the searched name, clean it using `trim()`, and search the database using `LIKE`.
6. Use a `while` loop to display the matching student's name, score, and a Pass/Fail badge.
7. Print a "Not Found" error message if the student does not exist.

---

### Complete Solution

```php
<?php
// Database connection
$conn = mysqli_connect("localhost", "root", "", "school_db");
if (!$conn) { 
    die("Database Connection Failed: " . mysqli_connect_error()); 
}
?>
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Student Portal - Check Result</title>
</head>
<body class="bg-light">

    <?php include 'nav.php'; ?>

    <div class="container">
        <div class="row justify-content-center">
            <div class="col-md-6">
                <!-- Search Form Card -->
                <div class="card shadow-sm p-4 mb-4 border-0">
                    <h3 class="text-primary mb-3 text-center">Find Your Result</h3>
                    <form method="POST" action="student.php">
                        <div class="mb-3">
                            <label class="form-label fw-bold">Enter Your Name:</label>
                            <input type="text" name="search_name" class="form-control form-control-lg" placeholder="e.g. Ahmed" required>
                        </div>
                        <button type="submit" class="btn btn-primary w-100 btn-lg">Search</button>
                    </form>
                </div>

                <!-- PHP Search Logic & Result Display -->
                <?php
                if ($_SERVER['REQUEST_METHOD'] == 'POST') {
                    $search_name = trim($_POST['search_name']);
                    
                    // Search database using wildcard matching
                    $search_sql = "SELECT * FROM students WHERE name LIKE '%$search_name%'";
                    $result = mysqli_query($conn, $search_sql);

                    if (mysqli_num_rows($result) > 0) {
                        while ($student = mysqli_fetch_assoc($result)) {
                            $score = $student['score'];
                            $isPass = $score >= 50;
                            $statusText = $isPass ? "Pass" : "Fail";
                            $badgeClass = $isPass ? "alert-success" : "alert-danger";
                            ?>
                            
                            <div class="card shadow p-4 text-center border-0 mb-4">
                                <h4 class="text-secondary">Official Result Card</h4>
                                <hr>
                                <h2 class="text-dark mb-3"><?= htmlspecialchars($student['name']) ?></h2>
                                <h3 class="mb-4">Score: <strong><?= $score ?></strong> / 100</h3>
                                <div class="alert <?= $badgeClass ?> fw-bold fs-4 mb-0">
                                    Status: <?= $statusText ?>
                                </div>
                            </div>

                            <?php
                        }
                    } else {
                        echo "<div class='alert alert-warning text-center fw-bold fs-5 shadow-sm'>
                                No student found matching \"" . htmlspecialchars($search_name) . "\".
                              </div>";
                    }
                }
                ?>
            </div>
        </div>
    </div>

</body>
</html>
```

---

### Step-by-Step Code Explanation

#### 1. Database Connection Block
```php
$conn = mysqli_connect("localhost", "root", "", "school_db");
if (!$conn) { 
    die("Database Connection Failed: " . mysqli_connect_error()); 
}
```
* **`mysqli_connect(...)`**: Connects PHP to the MySQL database server running locally (`localhost`), using username `root`, no password (`""`), and opening the database `school_db`.
* **`if (!$conn)`**: Checks if the connection failed.
* **`die(...)`**: Stops the page from loading and prints an error message (`mysqli_connect_error()`).

#### 2. Navigation Bar File Inclusion
```php
<?php include 'nav.php'; ?>
```
* **`include 'nav.php'`**: Loads external HTML or PHP content from `nav.php` into the current page so navigation remains consistent.

#### 3. HTML Search Form
```html
<form method="POST" action="student.php">
    <input type="text" name="search_name" ... required>
    <button type="submit">Search</button>
</form>
```
* **`method="POST"`**: Sends form data securely in the background without showing query variables in the URL.
* **`name="search_name"`**: Defines the key used to read the input in PHP (`$_POST['search_name']`).

#### 4. Processing Form Input
```php
if ($_SERVER['REQUEST_METHOD'] == 'POST') {
    $search_name = trim($_POST['search_name']);
```
* **`$_SERVER['REQUEST_METHOD'] == 'POST'`**: Ensures the code runs only after the user submits the search form.
* **`trim(...)`**: Removes extra spaces from the beginning and end of the user's input.

#### 5. Database Querying with `LIKE`
```php
$search_sql = "SELECT * FROM students WHERE name LIKE '%$search_name%'";
$result = mysqli_query($conn, $search_sql);
```
* **`LIKE '%$search_name%'`**: Searches for student names containing the input phrase anywhere inside the string.
* **`mysqli_query(...)`**: Executes the SQL query against `school_db`.

#### 6. Displaying Results via Loop & Conditional Styling
```php
if (mysqli_num_rows($result) > 0) {
    while ($student = mysqli_fetch_assoc($result)) {
        $score = $student['score'];
        $isPass = $score >= 50;
        $statusText = $isPass ? "Pass" : "Fail";
        $badgeClass = $isPass ? "alert-success" : "alert-danger";
```
* **`mysqli_num_rows($result) > 0`**: Checks if any matching records were found.
* **`while ($student = mysqli_fetch_assoc($result))`**: Loops through every matching row one by one.
* **Ternary Operator (`$score >= 50 ? ... : ...`)**: Evaluates if the score is 50 or higher. If true, sets status to "Pass" with a green badge (`alert-success`); otherwise, sets status to "Fail" with a red badge (`alert-danger`).
* **`htmlspecialchars(...)`**: Prevents cross-site scripting (XSS) by encoding special HTML characters before rendering the student's name.

#### 7. Handling Unmatched Searches
```php
} else {
    echo "<div class='alert alert-warning text-center fw-bold fs-5 shadow-sm'>
            No student found matching \"" . htmlspecialchars($search_name) . "\".
          </div>";
}
```
* Prints a user-friendly warning message when `mysqli_num_rows()` returns zero rows.

---
---

## Task 2: Database Connection & Batch Insertion Script

### Task Requirements
1. Create a script (`connect.php`) to establish and test a connection to `workshop_db`.
2. Create a script that imports `connect.php`.
3. Create an array containing product details (name, price, quantity).
4. Use a `foreach` loop to iterate through the products and execute `INSERT INTO` queries into the database.
5. Display success messages for each inserted record or an error message if an insertion fails.

---

### Complete Solution

#### File 1: Database Connection (`connect.php`)
```php
<?php
$host = "localhost";
$user = "root";
$pass = "";
$db   = "workshop_db";

// Establish database connection
$conn = mysqli_connect($host, $user, $pass, $db);

// Verify connection status
if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}

echo "Connected successfully. MySQL server version: " . mysqli_get_server_info($conn) . "\n";
?>
```

#### File 2: Product Insertion Script (`insert_products.php`)
```php
<?php
require_once("connect.php");

// Array of product data
$products = [
    ["name" => "Laptop",     "price" => 899.99, "quantity" => 10],
    ["name" => "Mouse",      "price" => 15.50,  "quantity" => 100],
    ["name" => "Keyboard",   "price" => 45.00,  "quantity" => 60],
    ["name" => "Monitor",    "price" => 199.99, "quantity" => 25],
    ["name" => "Headphones", "price" => 59.99,  "quantity" => 40],
];

// Loop through array and insert into database
foreach ($products as $product) {
    $name  = $product['name'];
    $price = $product['price'];
    $qty   = $product['quantity'];

    $sql = "INSERT INTO products (name, price, quantity) VALUES ('$name', $price, $qty)";

    if (mysqli_query($conn, $sql)) {
        echo "Inserted: $name\n";
    } else {
        echo "Error inserting $name: " . mysqli_error($conn) . "\n";
    }
}
?>
```

---

### Step-by-Step Code Explanation

#### 1. Setting Up Database Parameters & Connection (`connect.php`)
```php
$host = "localhost";
$user = "root";
$pass = "";
$db   = "workshop_db";

$conn = mysqli_connect($host, $user, $pass, $db);
```
* Configures database connection parameters using clean variable names.
* **`mysqli_connect()`**: Establishes a connection channel using these four parameters.

#### 2. Checking Connection Status
```php
if (!$conn) {
    die("Connection failed: " . mysqli_connect_error());
}
echo "Connected successfully. MySQL server version: " . mysqli_get_server_info($conn) . "\n";
```
* **`if (!$conn)`**: Executes if the database connection attempt fails.
* **`die(...)`**: Terminates execution immediately and displays the error details.
* **`mysqli_get_server_info($conn)`**: Returns the version number of the connected MySQL server.

#### 3. Importing Connection Script
```php
require_once("connect.php");
```
* **`require_once(...)`**: Loads `connect.php` once before executing the remainder of the script. If the file is missing or fails, execution stops immediately with a fatal error.

#### 4. Multidimensional Array Definition
```php
$products = [
    ["name" => "Laptop",     "price" => 899.99, "quantity" => 10],
    ...
];
```
* Defines an array of associative arrays containing data for each product (`name`, `price`, and `quantity`).

#### 5. Iterating and Query Execution (`foreach`)
```php
foreach ($products as $product) {
    $name  = $product['name'];
    $price = $product['price'];
    $qty   = $product['quantity'];

    $sql = "INSERT INTO products (name, price, quantity) VALUES ('$name', $price, $qty)";
```
* **`foreach ($products as $product)`**: Sequentially visits each product in the `$products` array.
* Assigns array values to `$name`, `$price`, and `$qty` variables.
* Builds an SQL string (`INSERT INTO products ...`) inserting values into table columns.

#### 6. Database Insertion & Output Feedback
```php
    if (mysqli_query($conn, $sql)) {
        echo "Inserted: $name\n";
    } else {
        echo "Error inserting $name: " . mysqli_error($conn) . "\n";
    }
}
```
* **`mysqli_query($conn, $sql)`**: Sends the `INSERT` query to MySQL.
* If successful, prints `"Inserted: [Product Name]"`.
* If a failure occurs, prints the specific database error via **`mysqli_error($conn)`**.


### Task 3: Update Stock with Transaction Logic

#### 📌 Problem Description
Create `restock.php` simulating stock shipment intake:
```php
$shipment = ["Laptop" => 5, "Mouse" => 50, "Drone" => 20];
```
1. Verify if item exists using `SELECT`.
2. If item exists, run `UPDATE` query adding quantity value (`quantity = quantity + X`). Check affected rows with `mysqli_affected_rows()`.
3. If item does not exist, print `"Skipped: <name> product not found"`.

---

#### 💻 Full Solution Code
```php
<?php
require_once("connect.php");

$shipment = [
    "Laptop" => 5,
    "Mouse"  => 50,
    "Drone"  => 20 // Product does not exist
];

foreach ($shipment as $name => $amount) {
    // 1. Verify product existence
    $checkSql    = "SELECT * FROM products WHERE name = '$name'";
    $checkResult = mysqli_query($conn, $checkSql);

    if (mysqli_num_rows($checkResult) === 0) {
        echo "Skipped: $name (product not found)\n";
        continue;
    }

    // 2. Perform atomic stock update
    $updateSql = "UPDATE products SET quantity = quantity + $amount WHERE name = '$name'";
    mysqli_query($conn, $updateSql);

    $affected = mysqli_affected_rows($conn);
    echo "Updated: $name, rows affected: $affected\n";
}

// Render updated table snapshot
echo "\n--- Updated Table ---\n";
$result = mysqli_query($conn, "SELECT * FROM products");
while ($row = mysqli_fetch_assoc($result)) {
    echo $row['name'] . " - " . $row['quantity'] . " in stock\n";
}
?>
```

---

#### 🛠️ Step-by-Step Explanation

* **Step 1: Check Row Existence**
  `mysqli_num_rows($checkResult) === 0` checks whether any database records matched the item search name.

* **Step 2: SQL Relative Increments**
  Using `quantity = quantity + $amount` inside SQL directly avoids database race condition issues compared to calculating additions inside PHP code.

* **Step 3: Check Modification Count**
  `mysqli_affected_rows($conn)` returns exact count of database table rows altered by the latest query operation.

---
