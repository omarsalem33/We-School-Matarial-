# Unit 21 Laravel — Lesson 3: Routing, REST and Views
### Eloquent ORM · Database Interaction · Routing & Controllers · RESTful APIs

> **Source:** Unit 21 Laravel Student's Guide (2025–2026), Lesson 3, pp. 29–42, with the answer key on pp. 75–77.
> **Skill codes:** TPK22 (Analyze and solve common web application tasks by writing PHP programs) · TPC5.7 (Using controllers and routes for APIs and URLs)

---

## ⚠️ Mentor's Source-Fidelity Note (read first)

I checked the book line by line before writing this file. Here is exactly what Lesson 3 does and does not contain:

| Item you asked for | What the book actually has | How this file handles it |
|---|---|---|
| Concepts, definitions, flows | Yes (Sections 1–3) | Rewritten in full, nothing added |
| Commands & code for Lesson 3 | **None.** Lesson 3 is conceptual. The only commands in the book up to this point are `php artisan make:controller`, `php artisan route:list`, `php artisan serve` (Lessons 1–2) and `php artisan migrate` (Lesson 5) | Project uses those commands; anything beyond them is clearly tagged 🔧 **Beyond-the-text** |
| "Common errors in this step" tables | **Not present** in the provided PDF for Lesson 3 | I did not invent book content. Part 2 has "Quick Checks" built only from statements the book makes |
| "The solution is in the workshop" | The workshop itself was **not** included in the PDF | The capstone mirrors the book's own API flow (p. 39: `GET /api/students`) |

Everything tagged 🔧 is the minimum scaffolding needed to make the book's concepts runnable. Please compare it with your workshop files.

---

# PART 1 — Lesson Overview & Objectives

## What students learn in this lesson (book p. 31)
1. Laravel Eloquent ORM
2. Laravel Database Interaction
3. Routing and Controllers

## Objectives (book pp. 29–30)

**Remembering**
- Recall the basic concepts of routing in Laravel.
- Identify the different route verbs and their uses in HTTP methods.
- List the key components of a Blade view.

**Understanding**
- Explain the role of routing and how it directs requests to the right controller.
- Describe route parameters and optional parameters.
- Understand the structure and purpose of views, particularly with Blade.

**Applying**
- Implement basic routes (simple routes and controller routes).
- Use route grouping and middleware to organize and secure routes.
- Create and use Blade templates to render HTML.

**Analyzing**
- Analyze route verbs to make sure RESTful principles are followed.
- Compare methods of passing data to views and choose the right one.

**Evaluating**
- Assess the impact of middleware on security and performance.
- Evaluate the benefits of Blade over other templating systems.

**Creating**
- Develop a simple web application using routes that handle different HTTP request types.
- Design dynamic pages with Blade (layout inheritance, components, conditional logic).

---

# PART 2 — Interactive Step-by-Step Tutorial

## 🧠 Section 1 — Laravel Eloquent ORM

### 1-1 What is it?
**ORM = Object-Relational Mapping.** Eloquent is Laravel's built-in way to talk to a database using **PHP objects and classes instead of writing SQL directly**.

### 🧩 Mental model: the translator
Imagine you speak PHP and the database speaks SQL. Eloquent is the translator standing between you:

```
 Your PHP code  ──call──►  Eloquent ORM  ──generates SQL──►  DB Connection  ──execute──►  Database
 (Model 1..4)  ◄─result──                ◄────result───────                ◄──result───
```
*(This is the diagram on book p. 32.)*

### The golden rule (memorize it)
| In the database | In Eloquent |
|---|---|
| A **table** | A **model** (a PHP class) |
| A **row** | A **model instance** (an object) |

> 🎯 Exam trap: "A Laravel model represents a single row" is **False**. The *model* is the table; one *instance* is a row.

### 1-2 Why use Eloquent? (3 reasons from the book)
- Clean, readable syntax
- Built-in relationship management
- Integration with Laravel features such as validation, seeding, and factories

---

## 🗄️ Section 2 — Laravel Database Interaction

### 2-1 Introduction
Laravel gives you tools to work with databases "in a clean and organized way": **migrations, models, seeders, relationships, and more.**

### 2-2 The six concepts at a glance (book p. 33)

| Concept | Description |
|---|---|
| **Migration** | A version-controlled file that creates or modifies a database table |
| **Model** | A PHP class representing a database table |
| **Factory** | Generates fake data for testing or seeding |
| **Seeder** | A class that populates tables with initial or test data |
| **Relationship** | Describes how models (tables) are connected (e.g., one-to-many) |
| **Query Builder** | A fluent interface for writing database queries |

### 🏗️ Mental model: building a house
| Laravel concept | House analogy |
|---|---|
| Migration | The **blueprint** (defines structure; can be changed or rolled back) |
| Model | The **door to each room**, letting your code get in and out of a table |
| Factory | A **machine that stamps out fake furniture** for testing |
| Seeder | The **moving crew** that places furniture (data) in the rooms |
| Relationship | The **hallways** connecting rooms |
| Query Builder | A **simple remote control** instead of hand-written SQL |

*(Analogies are mine, for teaching only. Definitions in the table above are the book's.)*

### 2-3 Migration
- **What:** a file that defines changes to the *structure* of your database. You can use it to **create, update, or delete tables and columns**.
- **Why it matters:**
  - Keeps track of all changes made to the database.
  - Makes teamwork easier by sharing database changes across the project.
  - You can **roll back or re-run** changes easily.

### 2-4 Model
- **What:** a PHP class that represents a table, letting you work with its data in an object-oriented way.
- **Why it matters:** it makes data handling more intuitive. Each model connects directly to a table and provides methods to **retrieve, insert, update, or delete** data.

### 2-5 Factory
- **What:** a tool for creating **fake data** for testing or filling the database with sample information during development.
- **Why it matters:** you can test features without real user data, and quickly generate many records.

### 2-6 Seeder
- **What:** inserts specific sample or initial data. It can **work with factories or include manually written data**.
- **Why it matters:** gives a starting point in development and lets you test with predefined data sets.

### 2-7 Relationship
- **What:** describes how two tables are connected. Types in the book: **One-to-One, One-to-Many, Many-to-Many**.
- **Why it matters:** makes related data easy to access and models real-life links, e.g., *users and their posts*, *students and their courses*.

### 2-8 Query Builder
- **What:** lets you write database queries in **simple PHP syntax instead of raw SQL**.
- **Why it matters:** makes database interaction easier and safer, and helps build complex queries in a readable way.

> 💡 Book diagram (p. 36): the **Model** sits inside **Eloquent**, which uses the **Eloquent Builder** on top of the **Query Builder**, which sends SQL queries to the database.

---

## 🛣️ Section 3 — Routing and Controllers (API focus)

### 3-1 Routing (API Routes)
**Routing defines how your application responds to a specific HTTP request** (GET, POST, PUT, DELETE) at a given endpoint/URL.

- It connects a client request (like `/api/users`) to a function or controller method.
- **API routes are placed in `routes/api.php`**, separate from web routes (`routes/web.php`).
- 🔑 **Key idea:** every API request goes through a route that decides how to handle it.

### 3-2 Controller
A **controller** is a PHP class that organizes your application's logic. It contains methods that handle incoming requests.

- Keeps code clean by separating logic from routes.
- Each method usually matches **one API operation** (list users, create a post, delete a comment).
- **Example flow:** `Request → Route → Controller Method → Response`

### 3-3 Request Handling: HTTP verbs = CRUD

| HTTP method | What it does | CRUD letter |
|---|---|---|
| **GET** | Retrieve data | **R**ead |
| **POST** | Create new data | **C**reate |
| **PUT / PATCH** | Update existing data | **U**pdate |
| **DELETE** | Remove data | **D**elete |

Book example (p. 38), using an *articles* resource:

| Request | Meaning |
|---|---|
| `POST /articles/` | Create id: 1, title: Startups |
| `GET /articles/1` | Read id: 1 |
| `PUT /articles/1` | Update id: 1, title: Startups → Business |
| `DELETE /articles/1` | Delete id: 1 |

### 3-4 Response Formatting
A response is what the API returns after processing a request. It can be a **success message**, a **data object** (like user info), or an **error message**.

- Responses must be **clear and consistent**, often in **JSON**.
- Good API design uses meaningful **status codes**: **200 OK, 404 Not Found, 500 Server Error**.

### 3-5 API Resources
An **API Resource** is a class that **formats model data before sending it in a response**, producing clean, structured JSON.

- Controls which fields appear in the response.
- Keeps API output consistent across the app.

### 3-6 The Full API Flow (the big picture)

```
1. Client sends a request              e.g. GET /api/students
2. Route matches it → correct controller
3. Controller processes it using the Model
4. Model interacts with the Database
5. Controller prepares and returns a Response (often JSON)
6. (Optional) Resource formats the response
```

### 🧭 Where Lesson 3 connects to earlier lessons (book pp. 20–25)
These Lesson 2 ideas are the foundation for the objectives listed under "Applying":
- `Route::get('/users/{id}', [UserController::class, 'show']);` — **route parameter**
- `Route::get('/users/{id?}', ...)` — **optional parameter** (matches `/users/1` and `/users`)
- `Route::group(['prefix' => 'admin'], function () { ... });` — **route grouping**
- `->middleware('auth')` — **middleware** as a gatekeeper
- `return view('welcome', ['name' => 'Ahmed']);` + `{{ $name }}` — **passing data to a Blade view**

---

## ✅ Quick Checks (built only from statements in the book)

The book has no error tables for this lesson, so these are self-checks derived from its own rules:

| If you notice… | Check this (book rule) |
|---|---|
| Your API route doesn't seem to be an "API route" | API routes are typically defined in **`routes/api.php`**, not `routes/web.php` (p. 36, Q47/Q52) |
| Data is being deleted by a GET request | Wrong verb. **DELETE** removes data; **GET** only retrieves (Q49) |
| An update uses the wrong method | Updates use **PUT/PATCH** (Q54) |
| Responses look inconsistent between endpoints | Use a consistent JSON structure and consider an **API Resource** (p. 38–39) |
| A missing record returns a success message | Use a meaningful status code such as **404 Not Found** (p. 38) |
| You want to see which routes exist | `php artisan route:list` shows all your app's routes (p. 13) |
| A route's controller method isn't found | Confirm the controller exists in `app/Http/Controllers/` (created by `php artisan make:controller`) (p. 21) |

---

# PART 3 — Important Questions & Model Answers

*Questions 41–60 are the book's own "Do I Know This Already?" items (pp. 41–42). Answers match the book's answer key (pp. 75–77). Short-answer items below are derived directly from the lesson text.*

## A. True / False

| No | Statement | Answer | Why (from the book) |
|---|---|---|---|
| 41 | Eloquent ORM allows direct SQL queries without using PHP objects. | **False** | Eloquent works through PHP objects and classes *instead of* writing SQL directly |
| 42 | Migrations help track database changes and allow rollbacks. | **True** | Migrations keep track of changes and can be rolled back or re-run |
| 43 | A Laravel model represents a single row in a database table. | **False** | A model represents a **table**; an instance represents a row |
| 44 | Factories are used to create real user data in production. | **False** | Factories create **fake** data for testing/seeding |
| 45 | Seeders can only insert manually written data, not fake data. | **False** | Seeders can work with factories or use manually written data |
| 46 | The Query Builder requires writing raw SQL for all database operations. | **False** | It lets you use simple PHP syntax instead of raw SQL |
| 47 | API routes in Laravel are defined in `routes/web.php`. | **False** | API routes go in `routes/api.php` |
| 48 | Controllers handle business logic and interact with models. | **True** | Controllers process requests using the Model |
| 49 | GET request is used to delete data in RESTful APIs. | **False** | DELETE removes data; GET retrieves |
| 50 | API Resources help format JSON responses consistently. | **True** | They format model data into structured JSON |

## B. Multiple Choice

| No | Question | Answer |
|---|---|---|
| 51 | What does Eloquent ORM allow developers to do? | **B** — Interact with databases using PHP objects |
| 52 | Where are Laravel API routes typically defined? | **C** — `routes/api.php` |
| 53 | Which feature helps generate fake data for testing? | **B** — Factories |
| 54 | Which HTTP method updates data in RESTful APIs? | **C** — PUT/PATCH |
| 55 | What does a seeder do in Laravel? | **B** — Populates tables with test/initial data |
| 56 | Which is NOT a type of Eloquent relationship? | **D** — Table-to-View |
| 57 | What is the main advantage of Query Builder? | **B** — Provides a fluent PHP interface for database queries |
| 58 | Which component processes HTTP requests and returns responses? | **C** — Controller |
| 59 | What is the default response format for Laravel APIs? | **C** — JSON |
| 60 | What does CRUD stand for? | **A** — Create, Read, Update, Delete |

## C. Short-Answer / Understanding / Analyzing

**Q1 (Remembering).** What is Eloquent ORM?
> Laravel's built-in Object-Relational Mapping tool for interacting with a database using PHP objects and classes instead of writing SQL directly.

**Q2 (Remembering).** List the three reasons the book gives for using Eloquent.
> Clean, readable syntax; built-in relationship management; integration with Laravel features like validation, seeding, and factories.

**Q3 (Remembering).** Name the six database concepts in Laravel's database interaction.
> Migration, Model, Factory, Seeder, Relationship, Query Builder.

**Q4 (Understanding).** What is the relationship between a model, a table, and a row in Eloquent?
> Each model represents a table, and each model instance represents a row.

**Q5 (Understanding).** Why are migrations important?
> They keep track of all database changes, make teamwork easier by sharing database changes across the project, and let you roll back or re-run changes.

**Q6 (Understanding).** How do a seeder and a factory work together?
> A factory generates fake data; a seeder inserts sample or initial data into the database and can use factories or manually written data.

**Q7 (Understanding).** Which relationship types does Laravel support, and give a real-life example.
> One-to-One, One-to-Many, Many-to-Many; for example users and their posts, or students and their courses.

**Q8 (Understanding).** What is routing in the context of APIs, and where do API routes live?
> Routing defines how the app responds to a specific HTTP request at a given endpoint. API routes are usually in `routes/api.php`, separate from web routes.

**Q9 (Understanding).** Describe the flow of a Laravel request.
> Request → Route → Controller Method → Response.

**Q10 (Applying).** Match each HTTP method to its CRUD operation.
> GET = Read; POST = Create; PUT/PATCH = Update; DELETE = Delete.

**Q11 (Applying).** List the six steps of a full Laravel API request, using `GET /api/students`.
> (1) The client sends the request. (2) The route matches it and sends it to the correct controller. (3) The controller processes it using the Model. (4) The Model interacts with the database. (5) The controller prepares and returns a response (often JSON). (6) Optionally, a Resource formats the response.

**Q12 (Analyzing).** Why is using the correct HTTP verb important for a RESTful API?
> Each verb maps to one CRUD operation (e.g., GET retrieves, DELETE removes), so the wrong verb breaks RESTful principles. For example, using GET to delete data is incorrect (Q49).

**Q13 (Analyzing).** Why use an API Resource instead of returning model data directly?
> A Resource controls which fields appear in the response and keeps API output consistent across the app.

**Q14 (Analyzing).** Which status codes does the book name as examples of good API design?
> 200 OK, 404 Not Found, 500 Server Error.

**Q15 (Analyzing).** Why separate logic into controllers rather than routes?
> It keeps code clean by separating logic from routes; each controller method typically handles one API operation.

---

# PART 4 — Hands-On Practical Project

## 🎓 Capstone Mini-Project: **Student API**

**Goal:** Build a small RESTful API that follows the book's full API flow (p. 39), using `GET /api/students` as its starting endpoint, and then completes full CRUD (Create, Read, Update, Delete).

**Concepts integrated:** Migration · Model · Seeder/Factory · Controller · API routes · HTTP verbs · JSON responses · Status codes · API Resource

> 🔧 **Beyond-the-text notice:** The book's Lesson 3 gives no code. The commands and code below use only the concepts defined in this lesson. Commands not shown anywhere in the guide are tagged 🔧. Please compare them against your official workshop. 

### Step 0 — Project set-up (book pp. 11, 61)
```bash
composer create-project laravel/laravel student-api
cd student-api
php artisan serve
```
Visit `http://localhost:8000` to confirm you see the Laravel welcome page.

🔧 **Instructor note on `routes/api.php`:** The book says API routes live in `routes/api.php`. In newer Laravel versions (11 and 12) a fresh project may not include that file until you run `php artisan install:api`. This command is **not** in the guide. If the file is missing in your project, run it once, then continue. The `/api` prefix is applied to routes in that file automatically.

### Step 1 — Create the Model and Migration (🔧)
```bash
php artisan make:model Student -m
```
This creates the `Student` model (a PHP class for the `students` table) and a migration (the table blueprint). In the migration's `up()` method, define columns such as:

```php
$table->id();
$table->string('name');
$table->string('email')->unique();
$table->integer('age');
$table->timestamps();
```
Run it (this command appears in the book, p. 61):
```bash
php artisan migrate
```

**✔ Expected result:** a `students` table exists in your database.

### Step 2 — Allow the model to accept data (🔧)
In `app/Models/Student.php`:
```php
protected $fillable = ['name', 'email', 'age'];
```

### Step 3 — Add sample data (Factory/Seeder concepts) (🔧)
Use a **seeder** to insert a few starter students (e.g., 3 manually written rows), or a **factory** to generate fake ones. This practices the two ideas from sections 2-5 and 2-6.

```bash
php artisan make:seeder StudentSeeder
php artisan db:seed --class=StudentSeeder
```

### Step 4 — Create the Controller (book command, p. 21)
```bash
php artisan make:controller StudentController
```
Add one method per CRUD operation. Each method matches **one API operation**, as the book says:

```php
<?php

namespace App\Http\Controllers;

use App\Models\Student;
use Illuminate\Http\Request;

class StudentController extends Controller
{
    // READ (list)  → GET /api/students
    public function index()
    {
        return response()->json(Student::all(), 200);
    }

    // READ (one)   → GET /api/students/{id}
    public function show($id)
    {
        $student = Student::find($id);
        if (!$student) {
            return response()->json(['message' => 'Student not found'], 404);
        }
        return response()->json($student, 200);
    }

    // CREATE       → POST /api/students
    public function store(Request $request)
    {
        $student = Student::create($request->all());
        return response()->json($student, 201);
    }

    // UPDATE       → PUT/PATCH /api/students/{id}
    public function update(Request $request, $id)
    {
        $student = Student::find($id);
        if (!$student) {
            return response()->json(['message' => 'Student not found'], 404);
        }
        $student->update($request->all());
        return response()->json($student, 200);
    }

    // DELETE       → DELETE /api/students/{id}
    public function destroy($id)
    {
        $student = Student::find($id);
        if (!$student) {
            return response()->json(['message' => 'Student not found'], 404);
        }
        $student->delete();
        return response()->json(['message' => 'Student deleted'], 200);
    }
}
```
> 🔧 Status code `201` (created) is not named in the book. The book names 200, 404 and 500; using 201 for creation is optional. You may return 200 if you want to stay strictly within the text.
> 🔧 `$request->all()` with no validation is only acceptable for this learning exercise. Validation is introduced later in the guide (Lesson 4, "Form Validation").

### Step 5 — Define the API Routes (`routes/api.php`)
```php
<?php

use App\Http\Controllers\StudentController;
use Illuminate\Support\Facades\Route;

Route::get('/students',          [StudentController::class, 'index']);
Route::get('/students/{id}',     [StudentController::class, 'show']);
Route::post('/students',         [StudentController::class, 'store']);
Route::put('/students/{id}',     [StudentController::class, 'update']);
Route::delete('/students/{id}',  [StudentController::class, 'destroy']);
```
This uses the syntax from the book (`[Controller::class, 'method']`, route parameters like `{id}`).

Check them:
```bash
php artisan route:list
```
**✔ Expected result:** five routes appear under `api/students`.

### Step 6 — Format output with an API Resource (🔧, optional "Resource" step from p. 39)
```bash
php artisan make:resource StudentResource
```
In `app/Http/Resources/StudentResource.php`, choose which fields to expose:
```php
public function toArray(Request $request): array
{
    return [
        'id'    => $this->id,
        'name'  => $this->name,
        'email' => $this->email,
    ];
}
```
Then in the controller:
```php
use App\Http\Resources\StudentResource;

public function index()
{
    return StudentResource::collection(Student::all());
}
```
**✔ Expected result:** `age` and timestamps no longer appear in the output, showing that a Resource "helps control what fields are shown."

### Step 7 — Test every endpoint (Postman is introduced in Lesson 5)
Until you reach Lesson 5, you can test the `GET` endpoints in the browser at `http://localhost:8000/api/students`. For POST, PUT and DELETE, Lesson 5's objectives name **Postman** as the testing tool.

| # | Request | Body (JSON) | Expected response |
|---|---|---|---|
| 1 | `GET /api/students` | — | `200` + JSON list of students |
| 2 | `GET /api/students/1` | — | `200` + one student |
| 3 | `GET /api/students/999` | — | `404` + "Student not found" |
| 4 | `POST /api/students` | `{"name":"Ahmed","email":"ahmed@example.com","age":17}` | New student returned |
| 5 | `PUT /api/students/1` | `{"name":"Ahmed Ali"}` | Updated student returned |
| 6 | `DELETE /api/students/1` | — | `200` + "Student deleted" |

### ✅ Final Verification Checklist
- [ ] `php artisan route:list` shows 5 student routes
- [ ] Each route uses the correct HTTP verb for its CRUD action
- [ ] Controller methods only handle logic; routes contain no logic
- [ ] The `students` table exists and was built by a migration
- [ ] Responses are JSON with sensible status codes (200 / 404)
- [ ] A Resource limits which fields are returned
- [ ] You can trace one request through all six steps of the book's flow

### 🌟 Bonus Challenges (for advanced students)
1. **Route grouping (book 3-5):** wrap all student routes in `Route::group(['prefix' => 'students'], ...)` to keep them tidy.
2. **Optional parameter (book 3-4):** make `/students/{id?}` serve both "one student" and "all students" from one route.
3. **Relationships (book 2-7):** create a `Course` model and relate students and courses (Many-to-Many, the book's own example).
4. **Factory power (book 2-5):** use a factory to generate 50 fake students at once and look at how the list looks.
5. **Middleware (book Lesson 2, Section 4):** protect the delete route with the `auth` middleware. Note that the book says `auth` only works once authentication is set up (Lesson 5 covers Breeze and Laravel UI).
6. **Analysis write-up:** in 5 sentences, explain why your API follows RESTful principles by mapping each verb to a CRUD action.

---

## 📌 One-Page Summary for Students

- **Eloquent** = talk to the DB with PHP objects, not SQL. Model = table, instance = row.
- **Migration** (structure) → **Model** (access) → **Factory** (fake data) → **Seeder** (insert data) → **Relationship** (connect tables) → **Query Builder** (simple queries).
- **Routes** (`routes/api.php` for APIs) send each request to a **Controller** method.
- **GET** read · **POST** create · **PUT/PATCH** update · **DELETE** remove.
- Responses are usually **JSON** with status codes like **200**, **404**, **500**; **API Resources** keep them tidy.
- **Flow:** Client → Route → Controller → Model → Database → Response (→ Resource).

*Next up: Lesson 4 — Blade Templates, Controllers and Migrations.*
