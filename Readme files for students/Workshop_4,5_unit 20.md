# Workshops 4 and 5 - PHP Practice Guide

This guide simplifies the tasks in Workshops 4 and 5 of the Practical Guide. It follows the supplied task descriptions and solutions. Each task is a small practice project: build the inputs, process them in clear steps, then check that the output matches the expected result.

## A simple way to approach every task

1. Identify what data comes in and where it comes from: a PHP array, a file, a URL parameter, a form, or JSON.
2. Decide what operation is required: encode/decode, validate/sanitize, calculate, or query the database.
3. Handle failures explicitly: invalid JSON, missing records, invalid form values, or database exceptions.
4. Produce the requested output format: HTML for a page, JSON for an API, or a saved file.
5. Try both a normal example and an error case.

---

# Workshop 4 - JSON, exceptions, and PDO

## Task 1 - Convert PHP data to JSON

**In plain language:** Put a student's details and a list of three products into PHP arrays, turn them into JSON, compare compact and readable formatting, and save the student's JSON to `students.json`.

**Approach**

1. Make an associative array for the student (`name`, `age`, `subjects`).
2. Call `json_encode($student)` for compact JSON and `json_encode($student, JSON_PRETTY_PRINT)` for indented JSON.
3. Make a list of three product arrays, each with a name and price, then encode the whole list.
4. Use `file_put_contents('students.json', ...)` to save the student's pretty JSON.
5. Wrap displayed JSON in `<pre>` so spacing and line breaks are visible in the browser.

**Check:** Both student formats and the product list appear, and `students.json` contains the student data.

**Think it through:** PHP arrays hold the data; `json_encode()` changes its format; `file_put_contents()` persists a string to disk. Pretty printing changes readability, not the underlying information.

## Task 2 - Read and display a JSON file

**In plain language:** Load people from `people.json`, show each person's name, age, and city, and optionally find one person by city.

**Approach**

1. Create valid JSON in `people.json` with at least four city entries; each city maps to a person with `name` and `age`.
2. Read the file with `file_get_contents()` and decode it using `json_decode($json, true)` so the result is an associative array.
3. Check for a decoding error with `json_last_error()` (or show `json_last_error_msg()`) before looping.
4. Loop through city and person pairs and format each record.
5. If `$_GET['city']` is present, look for that key. Show the matching record or a clear “Not found” message.

**Check:** The page lists every entry; a known city returns its person, and an unknown city gives a not-found message.

**Think it through:** Decode before accessing fields. Decide how to handle malformed JSON before the loop, so the page does not silently show nothing.

## Task 3 - Make a simple JSON API

**In plain language:** Create `api.php` that returns all products, or one product selected by `?id=`, as JSON. Create `client.php` that requests a product and displays it as HTML.

**Approach**

1. Set `Content-Type` to `application/json` before sending the API response.
2. Store the products in an array with IDs as keys.
3. If there is no `id`, encode and return the complete list. If there is an `id`, find that item and return it.
4. If the ID does not exist, set HTTP status 404, return a JSON error object, and stop processing.
5. In `client.php`, fetch the endpoint with `file_get_contents()`, decode the response with `json_decode(..., true)`, then display either the error or product details as HTML.
6. Try a valid ID and an unknown ID; inspect response content type and status as requested in the practical.

**Check:** The API response is valid JSON, an unknown ID returns 404 and an error object, and the client displays a useful result.

**Think it through:** The API speaks JSON; the client converts that JSON back to PHP data and chooses what the page should show.

## Task 4 - Safe calculator with try/catch

**In plain language:** Make a four-operation calculator that catches bad input and division by zero and displays readable errors rather than crashing.

**Approach**

1. Create functions for add, subtract, multiply, and divide.
2. In the divide function, throw an exception if the second number is zero.
3. Make a form for two values and an operation; process it only after submission.
4. Check that both values are numeric. If not, throw an exception with a clear message.
5. Use a `switch` (or equivalent) to call the function chosen by the user. Handle an unrecognized operation with an error too.
6. Wrap validation and calculation in `try` and catch `Exception`; show `$e->getMessage()`.

**Check:** Try each operation, text instead of a number, and zero as the divisor. Each invalid case should produce a friendly error.

**Think it through:** Validate before calculating. Exceptions let predictable problems follow one path to a readable error message.

## Task 5 - PDO CRUD API with JSON responses

**In plain language:** Build `books_api.php` to list books, add one from JSON, or delete one by ID, while returning JSON for success and database errors.

**Approach**

1. Use the `books` table from Session 2, or recreate it. Connect with PDO inside `try` and enable `PDO::ERRMODE_EXCEPTION`.
2. Set the response content type to JSON.
3. Choose the action from the HTTP method and query parameters:
   - **GET without `delete`:** select all books and return them as a JSON array.
   - **POST:** read the request body from `php://input`, decode JSON to an associative array, and use a prepared `INSERT` statement.
   - **GET with `delete=id`:** use a prepared `DELETE` statement with the supplied ID.
4. Return a JSON success response for insert/delete, and return caught `PDOException` information as an error JSON response with status 500.
5. Try listing, inserting, deleting, and a failed connection as the practical requests. The optional extension adds UPDATE for full CRUD.

**Check:** All actions return valid JSON, queries use placeholders/prepared statements, and connection or database errors are caught.

**Think it through:** Keep the SQL action separate from the response. Prepared statements keep supplied values out of the SQL text; JSON keeps the response format consistent.

---

# Workshop 5 - PHP filters

## Key distinction: validation and sanitization

- **Validation** asks whether a value meets a rule; it commonly returns a valid value or `false`.
- **Sanitization** changes or encodes a value; the result can still be logically wrong. For example, cleaning an email string does not prove it is a real, valid email address.
- Validate values that must meet a rule. Escape values when displaying them in HTML.

## Task 1 - Registration form validator

**In plain language:** Validate email, website URL, IP address, and age separately. Succeed only when all four pass.

**Approach**

1. Make `register.php` with fields for email, website, region/IP, and age.
2. On POST, run `filter_var()` with `FILTER_VALIDATE_EMAIL`, `FILTER_VALIDATE_URL`, `FILTER_VALIDATE_IP`, and `FILTER_VALIDATE_INT` respectively.
3. Add a separate helpful error for each failed field; do not stop after the first error.
4. Show “Registration successful!” only when the error list is empty.

**Check:** Submit all valid values, then make each field invalid in turn. The page should report each specific problem and only succeed when all pass.

**Think it through:** Each field has its own data rule. Collecting errors lets the user fix several fields in one attempt.

## Task 2 - Sanitize potentially dangerous input

**In plain language:** Compare raw and cleaned comment, email, and quantity values to see what sanitizing filters change, then explain why cleaning is not the same as checking correctness.

**Approach**

1. Create a form with comment and email fields; include a quantity example such as `12abc`.
2. On submission, keep each raw value and make a sanitized version with `FILTER_SANITIZE_SPECIAL_CHARS`, `FILTER_SANITIZE_EMAIL`, and `FILTER_SANITIZE_NUMBER_INT` respectively.
3. Display the before-and-after values side by side.
4. Add a short note: sanitization transforms characters, but does not guarantee the result is valid for its purpose. Use validation for correctness.

**Check:** The comment's special characters are represented safely, unwanted email characters are removed, and the quantity example becomes a number-like string. It still takes validation to decide whether each result is acceptable.

**Think it through:** Do not treat a cleaned value as proof of validity. Also, when showing raw input in a browser, escape it with `htmlspecialchars()` so the demonstration itself does not execute submitted HTML or scripts.

## Task 3 - Handle form input with `filter_input()`

**In plain language:** Read and filter email, age, and name directly from a POST form. Age must be an integer from 18 through 65.

**Approach**

1. Make a POST form for email, age, and name.
2. Use `filter_input(INPUT_POST, 'email', FILTER_VALIDATE_EMAIL)`.
3. Use `FILTER_VALIDATE_INT` for age with `min_range` 18 and `max_range` 65.
4. Use `FILTER_SANITIZE_SPECIAL_CHARS` for the name, then check that it is not empty.
5. Collect a specific error for every failed field; show success and the cleaned name only if there are no errors.

**Check:** An invalid email, decimal or out-of-range age, and blank name each produce the expected error. Valid values pass.

**Think it through:** `filter_input()` combines retrieving a request value and applying its filter. Treat `false`/`null` validation results as failures, and treat a sanitized empty name as missing.

## Task 4 - Validate multiple values with `filter_var_array()`

**In plain language:** Validate email, age from 1 to 120, and website URL in one call, then report which fields failed.

**Approach**

1. Build `$data` from the three submitted values.
2. Build `$filters` with a matching rule for each key: email, integer age with a 1–120 range, and URL.
3. Run `$result = filter_var_array($data, $filters)`.
4. Loop through results and collect keys whose values are `false` (also guard against `null` if a field can be absent).
5. Print the result in `<pre>` on full success; otherwise show the failed field names.

**Check:** Test with all valid values and with one or more invalid values. The error list should identify every failed key.

**Think it through:** Keep data keys and filter keys aligned. The result preserves those keys, making it possible to tell the user which input failed.

## Task 5 - Secure contact form (capstone)

**In plain language:** Collect name, email, subject, and message; validate and clean them, save a valid message, and show a one-time confirmation. If inputs fail, show errors and refill the form.

**Approach**

1. Start the session before any HTML output. Build a POST form with the four fields.
2. Validate email using `FILTER_VALIDATE_EMAIL`. Sanitize name, subject, and message with `FILTER_SANITIZE_SPECIAL_CHARS`; require the name and message as the supplied solution does.
3. If validation fails, list the errors and refill the form with the submitted values. Escape values again with `htmlspecialchars()` when placing them into HTML attributes or textareas.
4. If valid, save the cleaned record in a `try` block. The practical allows JSON file storage or PDO. Include a timestamp as the solution does.
5. After saving, set `$_SESSION['flash']`, redirect to `contact.php`, and `exit`. On the redirected request, display the flash message and unset it so it appears once.
6. Catch save errors and show a useful error while retaining the form values.

**Check:** Invalid email or required fields produce errors and keep the user's entries. A valid submission is saved, redirects, then shows the confirmation once.

**Think it through:** There are separate stages: validate, sanitize, save, then confirm. A redirect after POST prevents a browser refresh from repeating the save.

---

# Source-aligned clarifications and suggested improvements

These are small adjustments for clarity and safer demonstrations. The original requirement is shown first, followed by the suggested change.

1. **Workshop 4, Task 1 - expected output count**
   - **Original:** “Expected result: Two JSON outputs on screen (compact + pretty)…” The task also asks for a products JSON list, and the supplied solution displays three blocks: compact student JSON, pretty student JSON, and pretty products JSON.
   - **Suggested modification:** Say “Display compact and pretty student JSON, plus the products JSON list; save the student JSON to `students.json`.” This matches all listed steps and the solution.

2. **Workshop 4, Task 2 - JSON error handling**
   - **Original:** If decoding returns `null`, display a friendly error and check `json_last_error()`.
   - **Suggested modification:** Check `json_last_error()` immediately after decoding, then display `json_last_error_msg()` and stop before looping. This follows the requested check while making the failure path explicit.

3. **Workshop 5, Task 1 - IP version**
   - **Original:** The form asks for an IPv4 address, while the solution uses `FILTER_VALIDATE_IP`, which accepts valid IP addresses generally.
   - **Suggested modification:** If the exercise specifically requires IPv4, pass the IPv4-only flag to the filter (or state that IPv6 is also accepted). This makes the wording and behavior agree.

4. **Workshop 5, Task 2 - displaying “raw” input**
   - **Original:** “Display both the raw and sanitized versions side by side.”
   - **Suggested modification:** Keep the raw value for comparison, but HTML-escape it with `htmlspecialchars()` when rendering. The raw string can still contain a `<script>` tag; printing it unescaped would run it rather than safely show it.

5. **Workshop 5, Task 5 - JSON file append wording**
   - **Original:** The task offers “append the cleaned submission (as JSON, using `json_encode()`) to a `messages.json` file,” while the supplied solution reads the existing JSON array, appends the new item to that array, and rewrites the complete JSON file.
   - **Suggested modification:** Describe this as “read the existing JSON array, add the new record, then write the updated JSON array back.” Raw `FILE_APPEND` can concatenate JSON documents and leave the file invalid as one JSON value.

6. **Workshop 5, Task 5 - restoring submitted form values**
   - **Original:** The steps require preserving user input on validation failure; the solution refills fields but does not sanitize every field as part of validation.
   - **Suggested modification:** Preserve the submitted values separately, escape them with `htmlspecialchars()` when inserting them into the form, and use the validated email and sanitized text fields for saving. This keeps the form refill requirement clear and safe.

---

## Quick reference

| Need | PHP tool from these workshops |
|---|---|
| PHP data to JSON | `json_encode()` |
| JSON text to PHP array | `json_decode($json, true)` |
| JSON decoding error | `json_last_error()`, `json_last_error_msg()` |
| Read/write a file | `file_get_contents()`, `file_put_contents()` |
| Validate one value | `filter_var()` with a `FILTER_VALIDATE_*` filter |
| Read and filter POST input | `filter_input()` |
| Filter an array of values | `filter_var_array()` |
| Sanitize input | `FILTER_SANITIZE_*` filters |
| Safe HTML output | `htmlspecialchars()` |
| Catch expected failures | `try`, `throw`, `catch` |
| Database query with values | PDO prepared statements |
