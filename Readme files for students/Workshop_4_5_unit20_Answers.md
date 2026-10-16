# Workshops 4 and 5 - Answers and PHP Code

This answer key follows the practical's tasks. Expand **Show answer/code** under a task to reveal one possible solution. The snippets are learning examples; use the database, file names, and form fields described in the matching task.

> HTML `<details>` sections work in GitHub and many Markdown viewers. If a viewer does not support collapsible sections, the answer code may appear expanded.

## Workshop 4 - JSON, exceptions, and PDO

### Task 1 - PHP data to JSON

**Answer:** Create PHP arrays for one student and three products. Encode compact and pretty versions, then save the pretty student JSON to `students.json`.

<details>
<summary>Show answer/code</summary>

```php
<?php
$student = [
    'name' => 'Youssef',
    'age' => 16,
    'subjects' => ['Math', 'Physics', 'Arabic'],
];

$products = [
    ['name' => 'Keyboard', 'price' => 350],
    ['name' => 'Mouse', 'price' => 150],
    ['name' => 'Monitor', 'price' => 2500],
];

echo '<h2>Compact student JSON</h2><pre>';
echo htmlspecialchars(json_encode($student));
echo '</pre>';

echo '<h2>Pretty student JSON</h2><pre>';
echo htmlspecialchars(json_encode($student, JSON_PRETTY_PRINT));
echo '</pre>';

echo '<h2>Products JSON</h2><pre>';
echo htmlspecialchars(json_encode($products, JSON_PRETTY_PRINT));
echo '</pre>';

file_put_contents('students.json', json_encode($student, JSON_PRETTY_PRINT));
echo 'Saved to students.json';
?>
```

</details>

### Task 2 - Read and display a JSON file

**Answer:** Read `people.json`, decode it as an associative array, check for a JSON error, list the records, and optionally search by city.

Example `people.json`:

```json
{
  "Cairo": {"name": "Mariam", "age": 17},
  "Alexandria": {"name": "Omar", "age": 18},
  "Giza": {"name": "Salma", "age": 16},
  "Aswan": {"name": "Youssef", "age": 17}
}
```

<details>
<summary>Show answer/code</summary>

```php
<?php
$json = file_get_contents('people.json');
$people = json_decode($json, true);

if (json_last_error() !== JSON_ERROR_NONE) {
    exit('Invalid JSON: ' . htmlspecialchars(json_last_error_msg()));
}

echo '<h2>Everyone</h2>';
echo '<ul>';
foreach ($people as $city => $person) {
    echo '<li>' . htmlspecialchars($person['name']) . ' ('
        . (int)$person['age'] . ') - ' . htmlspecialchars($city) . '</li>';
}
echo '</ul>';

if (isset($_GET['city'])) {
    $city = $_GET['city'];
    if (isset($people[$city])) {
        $person = $people[$city];
        echo '<p>Found: ' . htmlspecialchars($person['name'])
            . ', age ' . (int)$person['age'] . '</p>';
    } else {
        echo '<p>Not found for city: ' . htmlspecialchars($city) . '</p>';
    }
}
?>
```

Try a URL such as `read_json.php?city=Cairo`.

</details>

### Task 3 - Simple JSON API endpoint

**Answer:** Return either the full product list or one product in JSON. Return a 404 JSON error for an unknown ID. The client decodes that JSON and renders HTML.

<details>
<summary>Show answer/code - api.php</summary>

```php
<?php
header('Content-Type: application/json; charset=utf-8');

$products = [
    1 => ['name' => 'Keyboard', 'price' => 350],
    2 => ['name' => 'Mouse', 'price' => 150],
];

if (isset($_GET['id'])) {
    $id = filter_var($_GET['id'], FILTER_VALIDATE_INT);
    if ($id === false || !isset($products[$id])) {
        http_response_code(404);
        echo json_encode(['error' => 'Product not found']);
        exit;
    }
    echo json_encode($products[$id]);
} else {
    echo json_encode($products);
}
?>
```

</details>

<details>
<summary>Show answer/code - client.php</summary>

```php
<?php
$response = file_get_contents('http://localhost/api.php?id=1');
$data = json_decode($response, true);

if (isset($data['error'])) {
    echo 'Error: ' . htmlspecialchars($data['error']);
} else {
    echo 'Product: ' . htmlspecialchars($data['name'])
        . ' - ' . (float)$data['price'] . ' EGP';
}
?>
```

</details>

### Task 4 - Safe calculator with try/catch

**Answer:** Validate both values, select the requested operation, throw an exception for bad input or division by zero, and catch it to display a friendly message.

<details>
<summary>Show answer/code</summary>

```php
<?php
function add($a, $b) { return $a + $b; }
function subtract($a, $b) { return $a - $b; }
function multiply($a, $b) { return $a * $b; }
function divide($a, $b) {
    if ($b == 0) {
        throw new Exception('Cannot divide by zero!');
    }
    return $a / $b;
}

$result = null;
$error = null;

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    try {
        $a = $_POST['a'] ?? '';
        $b = $_POST['b'] ?? '';
        $operation = $_POST['operation'] ?? '';

        if (!is_numeric($a) || !is_numeric($b)) {
            throw new Exception('Both inputs must be numeric.');
        }

        switch ($operation) {
            case 'add': $result = add($a, $b); break;
            case 'subtract': $result = subtract($a, $b); break;
            case 'multiply': $result = multiply($a, $b); break;
            case 'divide': $result = divide($a, $b); break;
            default: throw new Exception('Unknown operation.');
        }
    } catch (Exception $e) {
        $error = $e->getMessage();
    }
}
?>
<form method="post">
    <input type="number" step="any" name="a" required>
    <select name="operation">
        <option value="add">Add</option>
        <option value="subtract">Subtract</option>
        <option value="multiply">Multiply</option>
        <option value="divide">Divide</option>
    </select>
    <input type="number" step="any" name="b" required>
    <button type="submit">Calculate</button>
</form>
<?php
if ($error !== null) {
    echo '<p>Error: ' . htmlspecialchars($error) . '</p>';
} elseif ($result !== null) {
    echo '<p>Result: ' . htmlspecialchars((string)$result) . '</p>';
}
?>
```

</details>

### Task 5 - PDO CRUD with JSON output

**Answer:** GET lists books, POST inserts a book decoded from JSON, and GET with `?delete=id` deletes. Prepared statements bind input values. The optional UPDATE branch is not included.

<details>
<summary>Show answer/code</summary>

```php
<?php
header('Content-Type: application/json; charset=utf-8');

try {
    $pdo = new PDO(
        'mysql:host=localhost;dbname=library;charset=utf8mb4',
        'root',
        ''
    );
    $pdo->setAttribute(PDO::ATTR_ERRMODE, PDO::ERRMODE_EXCEPTION);

    if ($_SERVER['REQUEST_METHOD'] === 'GET' && isset($_GET['delete'])) {
        $stmt = $pdo->prepare('DELETE FROM books WHERE id = :id');
        $stmt->execute(['id' => $_GET['delete']]);
        echo json_encode([
            'success' => true,
            'deleted' => (int)$_GET['delete'],
            'rows_affected' => $stmt->rowCount(),
        ]);
    } elseif ($_SERVER['REQUEST_METHOD'] === 'POST') {
        $input = json_decode(file_get_contents('php://input'), true);
        if (!is_array($input)
            || !isset($input['title'], $input['author'], $input['year'])) {
            http_response_code(400);
            echo json_encode(['error' => 'Expected title, author, and year.']);
            exit;
        }

        $stmt = $pdo->prepare(
            'INSERT INTO books (title, author, year) VALUES (:title, :author, :year)'
        );
        $stmt->execute([
            'title' => $input['title'],
            'author' => $input['author'],
            'year' => $input['year'],
        ]);
        echo json_encode(['success' => true, 'id' => $pdo->lastInsertId()]);
    } else {
        $stmt = $pdo->query('SELECT * FROM books');
        echo json_encode($stmt->fetchAll(PDO::FETCH_ASSOC));
    }
} catch (PDOException $e) {
    http_response_code(500);
    // For a classroom exercise the message is shown; a public site should log
    // detailed errors privately and return a generic message to users.
    echo json_encode(['error' => $e->getMessage()]);
}
?>
```

</details>

## Workshop 5 - PHP filters

### Task 1 - Registration form validator

**Answer:** Run one validation filter per field, collect every failure, and show success only if the list is empty.

<details>
<summary>Show answer/code - validation logic</summary>

```php
<?php
$errors = [];

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $email = $_POST['email'] ?? '';
    $website = $_POST['website'] ?? '';
    $region = $_POST['region'] ?? '';
    $age = $_POST['age'] ?? '';

    if (filter_var($email, FILTER_VALIDATE_EMAIL) === false) {
        $errors[] = 'Invalid email';
    }
    if (filter_var($website, FILTER_VALIDATE_URL) === false) {
        $errors[] = 'Invalid website URL';
    }
    if (filter_var($region, FILTER_VALIDATE_IP, FILTER_FLAG_IPV4) === false) {
        $errors[] = 'Invalid IPv4 address';
    }
    if (filter_var($age, FILTER_VALIDATE_INT) === false) {
        $errors[] = 'Age must be a whole number';
    }

    if ($errors === []) {
        echo '<p>Registration successful!</p>';
    } else {
        foreach ($errors as $error) {
            echo '<p>' . htmlspecialchars($error) . '</p>';
        }
    }
}
?>
<form method="post">
    <label>Email <input type="email" name="email" required></label><br>
    <label>Website <input type="url" name="website" required></label><br>
    <label>Preferred server IPv4 <input name="region" required></label><br>
    <label>Age <input name="age" required></label><br>
    <button type="submit">Register</button>
</form>
```

</details>

### Task 2 - Sanitizing dangerous input

**Answer:** Keep raw values for the demonstration, create sanitized versions, and HTML-escape everything when displaying it. Sanitization does not prove validity.

<details>
<summary>Show answer/code</summary>

```php
<?php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $rawComment = $_POST['comment'] ?? '';
    $rawEmail = $_POST['email'] ?? '';
    $rawQuantity = $_POST['quantity'] ?? '';

    $cleanComment = filter_var($rawComment, FILTER_SANITIZE_SPECIAL_CHARS);
    $cleanEmail = filter_var($rawEmail, FILTER_SANITIZE_EMAIL);
    $cleanQuantity = filter_var($rawQuantity, FILTER_SANITIZE_NUMBER_INT);

    echo '<h3>Comment</h3>';
    echo '<p>Raw: ' . htmlspecialchars($rawComment) . '</p>';
    echo '<p>Sanitized: ' . htmlspecialchars($cleanComment) . '</p>';

    echo '<h3>Email</h3>';
    echo '<p>Raw: ' . htmlspecialchars($rawEmail) . '</p>';
    echo '<p>Sanitized: ' . htmlspecialchars($cleanEmail) . '</p>';

    echo '<h3>Quantity</h3>';
    echo '<p>Raw: ' . htmlspecialchars($rawQuantity) . '</p>';
    echo '<p>Sanitized: ' . htmlspecialchars($cleanQuantity) . '</p>';
}
?>
<form method="post">
    <label>Comment <textarea name="comment"></textarea></label><br>
    <label>Email <input name="email"></label><br>
    <label>Quantity <input name="quantity" value="12abc"></label><br>
    <button type="submit">Compare</button>
</form>
<p>Sanitizing changes a string, but does not confirm that it is correct. Validate a value when it must meet a rule.</p>
```

</details>

### Task 3 - `filter_input()` form handler

**Answer:** Retrieve email, age, and name using `filter_input()`. Reject an invalid email or age outside 18–65, require a name, and only show success if there are no errors.

<details>
<summary>Show answer/code</summary>

```php
<?php
$errors = [];
$name = null;

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $email = filter_input(INPUT_POST, 'email', FILTER_VALIDATE_EMAIL);
    $age = filter_input(INPUT_POST, 'age', FILTER_VALIDATE_INT, [
        'options' => ['min_range' => 18, 'max_range' => 65],
    ]);
    $name = filter_input(INPUT_POST, 'name', FILTER_SANITIZE_SPECIAL_CHARS);

    if ($email === false || $email === null) {
        $errors[] = 'Invalid email address.';
    }
    if ($age === false || $age === null) {
        $errors[] = 'Age must be a whole number between 18 and 65.';
    }
    if ($name === false || $name === null || trim($name) === '') {
        $errors[] = 'Name is required.';
    }

    if ($errors === []) {
        echo '<p>Signup successful! Welcome, ' . htmlspecialchars($name) . '</p>';
    } else {
        foreach ($errors as $error) {
            echo '<p>' . htmlspecialchars($error) . '</p>';
        }
    }
}
?>
<form method="post">
    <label>Email <input type="email" name="email" required></label><br>
    <label>Age <input name="age" required></label><br>
    <label>Name <input name="name" required></label><br>
    <button type="submit">Sign up</button>
</form>
```

</details>

### Task 4 - `filter_var_array()` multi-field validation

**Answer:** Put submitted values in one array, apply a matching filter definition, then list the keys that failed.

<details>
<summary>Show answer/code</summary>

```php
<?php
if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    $data = [
        'email' => $_POST['email'] ?? null,
        'age' => $_POST['age'] ?? null,
        'website' => $_POST['website'] ?? null,
    ];

    $filters = [
        'email' => FILTER_VALIDATE_EMAIL,
        'age' => [
            'filter' => FILTER_VALIDATE_INT,
            'options' => ['min_range' => 1, 'max_range' => 120],
        ],
        'website' => FILTER_VALIDATE_URL,
    ];

    $result = filter_var_array($data, $filters);
    $failed = [];
    foreach ($result as $key => $value) {
        if ($value === false || $value === null) {
            $failed[] = $key;
        }
    }

    if ($failed === []) {
        echo '<pre>';
        print_r($result);
        echo '</pre>';
    } else {
        echo 'Failed fields: ' . htmlspecialchars(implode(', ', $failed));
    }
}
?>
<form method="post">
    <label>Email <input name="email"></label><br>
    <label>Age <input name="age"></label><br>
    <label>Website <input name="website"></label><br>
    <button type="submit">Validate</button>
</form>
```

</details>

### Task 5 - Secure contact form capstone

**Answer:** Validate email, sanitize the text fields, retain input on errors, save a valid message as a JSON array, and use a one-time session flash message after redirect.

<details>
<summary>Show answer/code</summary>

```php
<?php
session_start();
$errors = [];
$values = ['name' => '', 'email' => '', 'subject' => '', 'message' => ''];

if ($_SERVER['REQUEST_METHOD'] === 'POST') {
    // Keep original submitted values for safe form refill.
    foreach ($values as $key => $_) {
        $values[$key] = $_POST[$key] ?? '';
    }

    $email = filter_var($values['email'], FILTER_VALIDATE_EMAIL);
    $name = filter_var($values['name'], FILTER_SANITIZE_SPECIAL_CHARS);
    $subject = filter_var($values['subject'], FILTER_SANITIZE_SPECIAL_CHARS);
    $message = filter_var($values['message'], FILTER_SANITIZE_SPECIAL_CHARS);

    if ($email === false) {
        $errors[] = 'Invalid email';
    }
    if (trim($name) === '') {
        $errors[] = 'Name is required';
    }
    if (trim($message) === '') {
        $errors[] = 'Message cannot be empty';
    }

    if ($errors === []) {
        try {
            $entry = [
                'name' => $name,
                'email' => $email,
                'subject' => $subject,
                'message' => $message,
                'date' => date('Y-m-d H:i:s'),
            ];

            $file = 'messages.json';
            $existing = [];
            if (file_exists($file)) {
                $decoded = json_decode(file_get_contents($file), true);
                if (json_last_error() !== JSON_ERROR_NONE || !is_array($decoded)) {
                    throw new RuntimeException('The messages file is not valid JSON.');
                }
                $existing = $decoded;
            }
            $existing[] = $entry;

            $saved = file_put_contents(
                $file,
                json_encode($existing, JSON_PRETTY_PRINT | JSON_UNESCAPED_UNICODE)
            );
            if ($saved === false) {
                throw new RuntimeException('Could not write the messages file.');
            }

            $_SESSION['flash'] = 'Message sent!';
            header('Location: contact.php');
            exit;
        } catch (Throwable $e) {
            $errors[] = 'Could not save message: ' . $e->getMessage();
        }
    }
}

if (isset($_SESSION['flash'])) {
    echo '<p>' . htmlspecialchars($_SESSION['flash']) . '</p>';
    unset($_SESSION['flash']);
}
foreach ($errors as $error) {
    echo '<p>' . htmlspecialchars($error) . '</p>';
}
?>
<form method="post">
    <label>Name
        <input name="name" value="<?= htmlspecialchars($values['name'], ENT_QUOTES, 'UTF-8') ?>">
    </label><br>
    <label>Email
        <input name="email" value="<?= htmlspecialchars($values['email'], ENT_QUOTES, 'UTF-8') ?>">
    </label><br>
    <label>Subject
        <input name="subject" value="<?= htmlspecialchars($values['subject'], ENT_QUOTES, 'UTF-8') ?>">
    </label><br>
    <label>Message
        <textarea name="message"><?= htmlspecialchars($values['message'], ENT_QUOTES, 'UTF-8') ?></textarea>
    </label><br>
    <button type="submit">Send</button>
</form>
```

</details>

## Remember

- Use the validation filter when the input must follow a rule.
- Sanitize or encode values for the intended use; for HTML output, use `htmlspecialchars()`.
- Check JSON decoding and file operations before relying on their result.
- Use PDO prepared statements for values in SQL queries.
- Keep API responses as JSON, including error responses.
