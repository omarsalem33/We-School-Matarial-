# Unit 21 Laravel — Lesson 4: Blade Templates, Controllers and Migrations
### Core Web App Features · Blade Layouts · Template Inheritance · CSRF Protection

> **Source:** Unit 21 Laravel Student's Guide (2025–2026), Lesson 4, pp. 43–57, with the answer key on pp. 77–79.
> **Skill codes:** TPK22 (Analyze and solve common web application tasks by writing PHP programs) · TPC5.7 (Using controllers and routes for APIs and URLs)

---

## ⚠️ Mentor's Source-Fidelity Note (read first)

I checked Lesson 4 in the PDF line by line. Here is exactly what it contains and what it does not:

| Item in your request | What the PDF actually has | How this file handles it |
|---|---|---|
| Concepts: CRUD, form validation, flash messages, image upload, Blade layout, template inheritance, CSRF | **Yes** (Sections 1–4) | Explained fully, nothing changed |
| `@extends`, `@yield`, `@section`/`@show`, `@parent`, `{{ }}`, `@if`, `@foreach` | **Yes**, with code (pp. 51–54) | Used exactly as shown in the book |
| Image upload folder | The book says files are stored in a folder, **usually `public/uploads`**, and the name/path is saved in the database (p. 50) | The project follows this |
| `Storage::disk('public')`, `storage:link` | **Not in the book** | Offered only as a 🔧 bonus challenge |
| `enctype="multipart/form-data"`, `@csrf`, `@method('PUT')`, `@method('DELETE')` | **Not in the book.** The book only says CSRF protection works "by requiring a token in forms" (p. 55) | Explained as the practical way to realize the book's idea, tagged 🔧 |
| Migration schemas, `Post` model, `PostController` store/update/destroy | **Not in the book.** Lesson 4's objectives mention them, but no code is given | Written for the project, tagged 🔧 |
| "Common Errors & Fixes" tables | **Not present** in the PDF for Lesson 4 | Part 2 has "Quick Checks" built only from book statements. A separate, clearly labeled 🔧 table covers errors you will hit in the project code |
| Workshop 4 | The workshop itself is **not included** in the PDF | The project is designed from the book's own examples (blog posts: p. 47, profile picture: p. 50) |

Everything tagged 🔧 is the minimum scaffolding needed to make the book's concepts runnable. Please compare it with your official workshop files before handing it to students.

---

# PART 1 — Lesson Overview & Objectives

## What students learn in this lesson (book p. 45)
1. Core Web App Features (CRUD, form validation, flash messages, image upload, Blade layout)
2. Blade Layout
3. Template Inheritance (Layouts)
4. CSRF Protection in Laravel

## Objectives (book pp. 43–44)

**Remembering**
- Recall the purpose of migrations in Laravel.
- Identify the structure of a Blade template file.
- List the basic components of a Laravel controller.

**Understanding**
- Explain how migrations act as version control for database schemas.
- Describe the role of models in MVC and how they interact with databases using Eloquent.
- Understand how Blade templates create dynamic and reusable views.

**Applying**
- Use Artisan commands to create and run migrations.
- Implement CRUD operations using Eloquent models.
- Create and use Blade templates, including template inheritance and components.

**Analyzing**
- Analyze the benefits of migrations for keeping schemas consistent across a team.
- Compare ways of organizing controller code for reusability and maintainability.
- Examine the advantages of Blade components and slots.

**Evaluating**
- Assess the impact of Eloquent models on simplicity and flexibility.
- Evaluate how well Blade separates business logic from presentation.

**Creating**
- Develop and manage tables with migrations (create, update, roll back).
- Build a complete CRUD application with routes, controllers, and models.
- Design dynamic pages with Blade (control structures, components, template inheritance).

---

# PART 2 — Interactive Step-by-Step Tutorial

## 🧱 Section 1 — Core Web App Features

When building modern web apps, the book names **five core concepts**:

| Concept | Definition (book p. 46) |
|---|---|
| **CRUD** | Create, Read, Update, Delete — common operations in apps |
| **Form Validation** | Checking submitted data to ensure it meets rules |
| **Flash Message** | A temporary message (e.g., "Post Created!") shown after an action |
| **Image Upload** | Saving uploaded image files on the server and linking them to the model |
| **Blade Layout** | Laravel's templating system for reusing HTML structure |

> 🧠 **Mental model:** Think of a blog website. You *write* a post (**Create**), see all posts (**Read**), fix a typo (**Update**), remove it (**Delete**). The form *checks* your input (**Validation**), a small banner *confirms* what happened (**Flash Message**), your photo gets *saved* (**Image Upload**), and every page shares the same top bar and footer (**Blade Layout**).

### 1-2 CRUD (Create, Read, Update, Delete)

- **Create** – add new data (e.g., register a new user).
- **Read** – view or retrieve data (e.g., list all blog posts).
- **Update** – modify existing data (e.g., edit a user profile).
- **Delete** – remove data (e.g., delete a comment).

**Real-life example (book): Blog Posts**
- Create: a user writes and submits a new blog post.
- Read: all blog posts are listed on the homepage.
- Update: the user edits the title of the post.
- Delete: the user removes the post.

These actions usually happen **through forms and controller logic** in Laravel.

### 1-3 Form Validation

Form validation ensures the data entered by a user is:
- **Correct in format** (e.g., a valid email address)
- **Complete** (e.g., required fields are not empty)
- **Safe to store** in the database

**Real-life example (book): User Registration Form**
- The **name** must not be empty.
- The **email** must be in a proper format (e.g., `user@example.com`).
- The **password** must be at least 8 characters.

If any of these are wrong or missing, **Laravel returns to the form with error messages.**

### 1-4 Flash Message

A **flash message** is a short, temporary notification shown after the user performs an action. It can show success ("Post created successfully!") or an error ("Failed to upload image.").

**Real-life example (book): Contact Form**
- After sending a message, a flash message appears at the top: *"Thanks! We've received your message."*
- It **disappears after navigating to another page.**
- Laravel handles this using **session messages**.

> 🧠 Mental model: a sticky note that Laravel sticks on the *next* page only, then throws away.

### 1-5 Image Upload

Image upload lets users submit image files, which are **stored and usually linked to a specific user or record.**

**Real-life example (book): Profile Picture**
1. A user selects an image file and clicks "Upload."
2. Laravel stores the file in a folder (**usually `public/uploads`**).
3. The **file name or path is saved in the database.**
4. The uploaded image appears next to the user's name.

> 🔒 **Image validation** (e.g., file size, formats like `.jpg` or `.png`) is important for **security and performance.**

---

## 🎨 Section 2 — Blade Layout

### 2-1 What is Blade?
Blade is Laravel's built-in templating engine. It lets you write **clean, readable, reusable HTML templates** that include PHP logic using special Blade syntax.

### 2-2 Real-life example (book): A Blog Site
- Every page has the **same top navigation bar and footer**.
- Instead of repeating HTML on every page, **a layout file holds the common structure.**
- Pages like home, about, and posts **use this layout and insert their own content** into a main area.

This keeps the site easy to manage and keeps your code **DRY (Don't Repeat Yourself).**

### 2-3 File location and naming
Blade files live in **`resources/views/`** and end with **`.blade.php`**.
Example: `resources/views/welcome.blade.php`

> ⚠️ Name matters: the book says to save the file as `.blade.php`, not just `.php` (p. 23).

**Basic Blade view (book p. 51):**
```blade
<!DOCTYPE html>
<html>
<head>
  <title>Laravel Blade</title>
</head>
<body>
  <h1>Hello, {{ $name }}!</h1>
</body>
</html>
```
- `{{ $name }}` outputs the value of the `$name` variable passed from the controller.
- Blade **automatically escapes HTML** with PHP's `htmlspecialchars()`, which helps prevent **XSS (cross-site scripting)** attacks.

### 2-4 Displaying data
To show a variable:
```blade
{{ $variable }}
```

### 2-5 Control structures in Blade (book p. 52)

**If statement** — dynamically change what the user sees based on a condition:
```blade
@if ($user->isAdmin())
  <p>Welcome, Admin!</p>
@else
  <p>Welcome, User!</p>
@endif
```

**Loop** — display a list of items (users, posts, products):
```blade
@foreach ($users as $user)
  <p>{{ $user->name }}</p>
@endforeach
```

---

## 🧬 Section 3 — Template Inheritance (Layouts)

Blade avoids repeating code (like headers and footers) through **template inheritance**.

> 🧠 **Mental model: a picture frame.** The *master layout* is the frame (title bar, sidebar, container). Each *child view* is the picture you slide inside. You change the picture, never rebuild the frame.

### 3.1 Defining a master layout (book p. 53)
File: `resources/views/layouts/app.blade.php`
```blade
<head>
  <title>App Name - @yield('title')</title>
</head>
<body>
  @section('sidebar')
    This is the master sidebar.
  @show

  <div class="container">
    @yield('content')
  </div>
</body>
```
- `@yield('title')` — placeholder for dynamic page titles.
- `@section('sidebar')` + `@show` — defines a sidebar that **can be overridden or extended.**
- `@yield('content')` — placeholder for page-specific content.

### 3.2 Extending a layout in a child view (book p. 53)
File: `resources/views/child.blade.php`
```blade
@extends('layouts.app')
@section('title', 'Page Title')

@section('sidebar')
  @parent
  <p>This is appended to the master sidebar.</p>
@endsection

@section('content')
  <p>This is my body content.</p>
@endsection
```
- `@extends()` connects the child view to the master layout.
- `@section()` defines content for sections like the sidebar and main content.
- `@parent` includes the **original content from the layout before adding more.**

### 🔑 Cheat-sheet: the directives side by side

| Directive | Where | Job (book wording) |
|---|---|---|
| `@yield('name')` | Layout | Placeholder where content from child views is inserted |
| `@section('name') ... @show` | Layout | Defines **and displays** a reusable block that children may override/extend |
| `@extends('layouts.app')` | Child | Says the child inherits from a parent layout |
| `@section('name') ... @endsection` | Child | Supplies content for a section |
| `@parent` | Child | Keeps the parent's section content, then adds more |

> 🎯 Exam trap (Q79): **`@section` defines content, `@yield` displays it in a layout.** `@section ... @show` is the layout-side version that shows its default content right away.

---

## 🛡️ Section 4 — CSRF Protection in Laravel

**CSRF = Cross-Site Request Forgery.** It is an attack where a malicious website, email, blog, instant message, or program makes a user's browser perform an **unwanted action on a trusted site while the user is authenticated.**

### How the attack works (book diagram, p. 54)
1. A hacker creates a request (in the form of a URL) for their own benefit from a website.
2. The hacker embeds that request in a hyperlink and sends it to a visitor they hope is logged in.
3. The visitor clicks the link, unknowingly sending the request to the site.
4. If the site assumes the request is legitimate, it fulfills it, sending data, funds, or access to the hacker.

### How Laravel defends (book p. 55)
**CSRF protection** is a Laravel security feature that prevents these attacks **by requiring a token in forms.**

> 🧠 Mental model: a **secret handshake stamp**. Laravel hands your form a unique stamp. When the form comes back, Laravel checks the stamp. A forged request from another site doesn't have it, so it is rejected.

> 🔧 **Beyond-the-text:** The book does not show the Blade syntax. In practice, you place `@csrf` inside every form that changes data (see the project). Without it, Laravel refuses the submission (HTTP 419 "Page Expired").

---

## ✅ Quick Checks (built only from statements in the book)

The PDF has no error tables for this lesson, so these self-checks come from rules the book itself states:

| If you notice… | Check this (book rule) |
|---|---|
| Your Blade file doesn't render | It must be saved as **`.blade.php`**, not just `.php` (p. 23, Q27, Q39) |
| Your view can't be found | Blade files live in **`resources/views/`** (Q75) |
| `{{ $name }}` is empty or errors | The variable must be **passed from the controller** (`view('welcome', ['name' => ...])`) (p. 51) |
| Your child page ignores the layout | It needs **`@extends('layouts.app')`** to inherit the master layout (p. 54) |
| Child content doesn't appear | The layout needs a matching **`@yield('content')`** and the child a matching **`@section('content')`** (pp. 53–54) |
| The sidebar's original text disappeared | Add **`@parent`** inside the child's `@section('sidebar')` to keep the layout's content (p. 54) |
| A form is submitted with bad data | **Form validation** should reject incorrect, incomplete or unsafe data and return to the form with error messages (p. 48) |
| A message lingers on every page | A **flash message** should disappear after navigating to another page (p. 49) |
| A user uploads a huge or unsafe file | Apply **image validation**: size and format (`.jpg`, `.png`) (p. 50) |
| A form is open to forged requests | **CSRF protection requires a token in forms** (p. 55) |

### 🔧 Practical errors you will meet in the project (mentor-added, not from the book)

| Symptom | Likely cause | Fix |
|---|---|---|
| **419 Page Expired** on submit | The form has no `@csrf` token | Add `@csrf` as the first line inside the `<form>` |
| Validation passes but **no image saved** | Form missing `enctype="multipart/form-data"` | Add `enctype="multipart/form-data"` to the `<form>` tag |
| **405 Method Not Allowed** when editing or deleting | HTML forms only send GET/POST | Keep `method="POST"` and add `@method('PUT')` or `@method('DELETE')` |
| **Image shows broken icon** | File not in `public/uploads`, or wrong path in `asset()` | Check the file exists in `public/uploads/` and use `asset('uploads/'.$post->image)` |
| **"Add [title] to fillable property"** (mass assignment error) | `$fillable` missing in the model | Add `protected $fillable = ['title','body','image'];` to `Post` |
| **"Table posts doesn't exist"** | Migration not run | Run `php artisan migrate` |
| **"View [posts.index] not found"** | File or folder name mismatch | Confirm `resources/views/posts/index.blade.php` exists |
| **Route not defined [posts.index]** | Route name missing | Add `->name('posts.index')` to the route |
| Validation errors don't show | Layout/view doesn't print `$errors` | Add the `@if ($errors->any())` block to the form (see `create.blade.php`) |
| Old input vanishes after a validation error | `old()` missing | Use `value="{{ old('title') }}"` |
| Flash message never appears | Not printed in the layout | Add the `session('success')` block to `layouts/app.blade.php` |

---

# PART 3 — Important Review Questions & Answers

*Questions 61–80 are the book's own "Do I Know This Already?" items (p. 56–57). Answers match the book's answer key (pp. 77–79). The remaining questions are derived directly from the lesson text.*

## A. "Give the Scientific Name" Questions

| No | Question | Answer |
|---|---|---|
| 61 | What term refers to the four basic operations (Create, Read, Update, Delete) in web applications? | **CRUD** |
| 62 | What is the process of checking user-submitted data to ensure it meets rules called? | **Form Validation** |
| 63 | What temporary notification appears after an action, such as "Post Created!"? | **Flash Message** |
| 64 | What is Laravel's templating system for reusing HTML structures called? | **Blade Layout** |
| 65 | What security attack does Laravel's CSRF protection prevent? | **CSRF** (Cross-Site Request Forgery) |
| 66 | What Blade method (`@yield('content')`) reserves space for dynamic content? | **Yield Directive** |

## B. Matching

| No | Column (A) | Correct match (Column B) |
|---|---|---|
| 67 | CRUD | **C** — Basic operations: Create, Read, Update, Delete |
| 68 | Flash Message | **A** — Temporary message like "Post Created!" |
| 69 | Blade Layout | **B** — Laravel's templating system |
| 70 | Image Upload | **D** — Stores user-uploaded files (e.g., profile pics) |
| 71 | @yield | **E** — Reserves space in a layout for dynamic content |
| 72 | CSRF | **F** — Security attack blocked by Laravel tokens |

## C. Short Answer (73–80)

**73. What does CRUD stand for?**
> Create, Read, Update, Delete.

**74. Why is form validation important?**
> To ensure data is correct, complete, and secure before processing.

**75. Where are Blade files stored in Laravel?**
> In the `resources/views/` directory.

**76. What's the purpose of `@extends` in Blade?**
> To inherit a master layout in a child view.

**77. What does `@yield('content')` do?**
> Defines a section in a layout where child views insert content.

**78. When would you use a flash message?**
> To confirm an action (e.g., "Post deleted successfully").

**79. What's the difference between `@section` and `@yield`?**
> `@section` defines content, while `@yield` displays it in a layout.

**80. Why is CSRF protection necessary?**
> To prevent unauthorized commands from being executed on behalf of a user.

## D. Additional Review Questions (from the lesson text)

**Q81 (Remembering).** List the five core web app concepts in Lesson 4.
> CRUD, Form Validation, Flash Message, Image Upload, Blade Layout.

**Q82 (Understanding).** What three qualities does form validation check for?
> That data is correct in format, complete (required fields not empty), and safe to store in the database.

**Q83 (Understanding).** Name three rules from the book's registration-form validation example.
> The name must not be empty; the email must be in a proper format (e.g., user@example.com); the password must be at least 8 characters.

**Q84 (Understanding).** What happens when validation fails?
> Laravel returns to the form with error messages.

**Q85 (Understanding).** How does Laravel handle flash messages, and when do they disappear?
> It uses session messages. The message disappears after the user navigates to another page.

**Q86 (Applying).** Describe the four steps of the book's profile-picture upload example.
> (1) The user selects an image and clicks Upload. (2) Laravel stores the file in a folder, usually `public/uploads`. (3) The file name or path is saved in the database. (4) The uploaded image appears next to the user's name.

**Q87 (Analyzing).** Why does image validation matter?
> For security and performance, by checking things like file size and format (.jpg, .png).

**Q88 (Understanding).** How does Blade protect against XSS?
> Its `{{ }}` echo statements automatically escape output using PHP's `htmlspecialchars()`, preventing malicious scripts from being injected.

**Q89 (Analyzing).** What problem does template inheritance solve, and which principle does it support?
> It avoids repeating code such as headers and footers on every page, supporting DRY (Don't Repeat Yourself).

**Q90 (Applying).** In a layout, what is the difference between `@yield('sidebar')` style placeholders and `@section('sidebar') ... @show`?
> `@yield` is a placeholder where child content is inserted. `@section ... @show` defines a block (with default content) that is displayed and can be overridden or extended by child views.

**Q91 (Applying).** What does `@parent` do?
> It includes the original content from the layout's section before the child adds more.

**Q92 (Understanding).** What is `@extends` used for, and where do child views go?
> `@extends('layouts.app')` connects a child view to the master layout. The layout lives at `resources/views/layouts/app.blade.php`.

**Q93 (Understanding).** Explain how a CSRF attack works.
> A hacker creates a request and embeds it in a link sent to a visitor who is hopefully logged in. When the visitor clicks, the request is sent to the site unknowingly, and if the site assumes it's legitimate, it fulfills it (sending data, funds, or access to the hacker).

**Q94 (Evaluating).** How does Laravel's CSRF protection stop the attack?
> By requiring a token in forms, so requests that don't carry it are not accepted.

**Q95 (Applying).** Give the two Blade control structures shown in the book and their purposes.
> `@if / @else / @endif` changes what the user sees based on a condition; `@foreach / @endforeach` displays a list of items such as users, posts, or products.

---

# PART 4 — Hands-On Practical Project

## 🎓 Capstone Mini-Project: **Blog Application with Image Uploads**

**Goal:** Build a blog where users can **create, read, update, and delete** posts, each with a title, body and an optional image. The app must use a **master layout with template inheritance**, **form validation**, **flash messages**, **image upload**, and **CSRF protection**: the five core concepts of this lesson.

> 🔧 **Beyond-the-text notice:** The book gives concepts, Blade syntax and the layout example, but no controller, model or migration code for Lesson 4. The code below realizes the book's concepts with the smallest amount of scaffolding. Items tagged 🔧 are not shown in the guide. Compare them with your official workshop.

### What you will build

| Book concept | Where it appears in the project |
|---|---|
| CRUD | `PostController` with index/create/store/show/edit/update/destroy |
| Form Validation | `validate()` in `store` and `update`, errors shown in the forms |
| Flash Message | `->with('success', ...)` + `session('success')` in the layout |
| Image Upload | File saved to `public/uploads`, file name saved in `posts.image` |
| Blade Layout | `layouts/app.blade.php` + `@extends`, `@yield`, `@section/@show`, `@parent` |
| CSRF Protection | `@csrf` in every form |

### Step 0 — Create the project (book commands, pp. 11, 61)
```bash
composer create-project laravel/laravel blog-app
cd blog-app
```

### Step 1 — Model and migration (🔧)
```bash
php artisan make:model Post -m
```

**`database/migrations/xxxx_xx_xx_xxxxxx_create_posts_table.php`** — edit the `up()` method:
```php
public function up(): void
{
    Schema::create('posts', function (Blueprint $table) {
        $table->id();
        $table->string('title');
        $table->text('body');
        $table->string('image')->nullable();   // file name saved here
        $table->timestamps();
    });
}
```

Run the migration (the command appears in the book, p. 61):
```bash
php artisan migrate
```
**✔ Expected result:** a `posts` table exists in your database.

**`app/Models/Post.php`:**
```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    protected $fillable = ['title', 'body', 'image'];
}
```

### Step 2 — Create the controller (book command, p. 21)
```bash
php artisan make:controller PostController
```

**`app/Http/Controllers/PostController.php`:**
```php
<?php

namespace App\Http\Controllers;

use App\Models\Post;
use Illuminate\Http\Request;

class PostController extends Controller
{
    // READ (list) → GET /posts
    public function index()
    {
        $posts = Post::latest()->get();
        return view('posts.index', ['posts' => $posts]);
    }

    // CREATE (form) → GET /posts/create
    public function create()
    {
        return view('posts.create');
    }

    // CREATE (save) → POST /posts
    public function store(Request $request)
    {
        // Form validation: correct, complete, safe
        $validated = $request->validate([
            'title' => 'required|max:255',
            'body'  => 'required',
            'image' => 'nullable|image|mimes:jpg,png|max:2048',   // image validation
        ]);

        // Image upload: save file in public/uploads, keep the name
        if ($request->hasFile('image')) {
            $file = $request->file('image');
            $name = time() . '_' . $file->getClientOriginalName();
            $file->move(public_path('uploads'), $name);
            $validated['image'] = $name;
        }

        Post::create($validated);

        // Flash message via session
        return redirect()->route('posts.index')
                         ->with('success', 'Post created successfully!');
    }

    // READ (one) → GET /posts/{id}
    public function show($id)
    {
        $post = Post::findOrFail($id);
        return view('posts.show', ['post' => $post]);
    }

    // UPDATE (form) → GET /posts/{id}/edit
    public function edit($id)
    {
        $post = Post::findOrFail($id);
        return view('posts.edit', ['post' => $post]);
    }

    // UPDATE (save) → PUT /posts/{id}
    public function update(Request $request, $id)
    {
        $post = Post::findOrFail($id);

        $validated = $request->validate([
            'title' => 'required|max:255',
            'body'  => 'required',
            'image' => 'nullable|image|mimes:jpg,png|max:2048',
        ]);

        if ($request->hasFile('image')) {
            // remove the old file if it exists
            if ($post->image && file_exists(public_path('uploads/' . $post->image))) {
                unlink(public_path('uploads/' . $post->image));
            }
            $file = $request->file('image');
            $name = time() . '_' . $file->getClientOriginalName();
            $file->move(public_path('uploads'), $name);
            $validated['image'] = $name;
        }

        $post->update($validated);

        return redirect()->route('posts.index')
                         ->with('success', 'Post updated successfully!');
    }

    // DELETE → DELETE /posts/{id}
    public function destroy($id)
    {
        $post = Post::findOrFail($id);

        if ($post->image && file_exists(public_path('uploads/' . $post->image))) {
            unlink(public_path('uploads/' . $post->image));
        }

        $post->delete();

        return redirect()->route('posts.index')
                         ->with('success', 'Post deleted successfully!');
    }
}
```
> 🔧 `validate()`, `with()`, `findOrFail()` and `move()` are not in the book. They are the standard Laravel tools that perform the validation, flash message and upload described on pp. 48–50.

### Step 3 — Routes (`routes/web.php`)
Uses the book's route syntax (`[Controller::class, 'method']`, `{id}` parameters). Names are added so redirects work (🔧).

```php
<?php

use App\Http\Controllers\PostController;
use Illuminate\Support\Facades\Route;

Route::get('/', function () {
    return redirect()->route('posts.index');
});

Route::get('/posts',              [PostController::class, 'index'])->name('posts.index');
Route::get('/posts/create',       [PostController::class, 'create'])->name('posts.create');
Route::post('/posts',             [PostController::class, 'store'])->name('posts.store');
Route::get('/posts/{id}',         [PostController::class, 'show'])->name('posts.show');
Route::get('/posts/{id}/edit',    [PostController::class, 'edit'])->name('posts.edit');
Route::put('/posts/{id}',         [PostController::class, 'update'])->name('posts.update');
Route::delete('/posts/{id}',      [PostController::class, 'destroy'])->name('posts.destroy');
```
> ⚠️ Order matters: `/posts/create` must be declared **before** `/posts/{id}`, or Laravel will treat "create" as an id.

Check them:
```bash
php artisan route:list
```
**✔ Expected result:** 7 routes under `posts`.

### Step 4 — Master layout (book pattern, p. 53)

**`resources/views/layouts/app.blade.php`**
```blade
<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>My Blog - @yield('title')</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 0; background: #f4f6f8; color: #222; }
    nav { background: #1f2a44; padding: 14px 24px; }
    nav a { color: #fff; margin-right: 18px; text-decoration: none; }
    .wrapper { display: flex; gap: 24px; max-width: 1000px; margin: 24px auto; padding: 0 16px; }
    aside { width: 220px; background: #fff; padding: 16px; border-radius: 8px; }
    .container { flex: 1; background: #fff; padding: 20px; border-radius: 8px; }
    .alert { padding: 12px 16px; border-radius: 6px; margin-bottom: 16px; }
    .alert-success { background: #d4edda; color: #155724; }
    .alert-danger { background: #f8d7da; color: #721c24; }
    .btn { display: inline-block; padding: 8px 14px; background: #1f2a44; color: #fff; border: none; border-radius: 6px; cursor: pointer; text-decoration: none; }
    .btn-danger { background: #c0392b; }
    .post { border-bottom: 1px solid #e3e3e3; padding: 14px 0; }
    img.thumb { max-width: 160px; border-radius: 6px; display: block; margin: 8px 0; }
    img.full { max-width: 100%; border-radius: 8px; }
    input[type=text], textarea { width: 100%; padding: 8px; margin: 6px 0 14px; box-sizing: border-box; }
    footer { text-align: center; padding: 20px; color: #777; }
  </style>
</head>
<body>
  <nav>
    <a href="{{ route('posts.index') }}">Home</a>
    <a href="{{ route('posts.create') }}">New Post</a>
  </nav>

  <div class="wrapper">
    @section('sidebar')
      <aside>
        <strong>My Blog</strong>
        <p>Welcome to the blog.</p>
      </aside>
    @show

    <div class="container">
      {{-- Flash message (session message) --}}
      @if (session('success'))
        <div class="alert alert-success">{{ session('success') }}</div>
      @endif

      @yield('content')
    </div>
  </div>

  <footer>Unit 21 · Laravel · Blog Application</footer>
</body>
</html>
```
> The layout uses exactly the book's directives: `@yield('title')`, `@section('sidebar') ... @show`, and `@yield('content')`. 🔧 `route()` and `session()` helpers are not in the book.

### Step 5 — Blade views (`resources/views/posts/`)
Create the folder `resources/views/posts/` and add four files.

**`index.blade.php` — Read (list)**
```blade
@extends('layouts.app')
@section('title', 'All Posts')

@section('sidebar')
  @parent
  <p>Total posts: {{ count($posts) }}</p>
@endsection

@section('content')
  <h1>All Posts</h1>
  <a class="btn" href="{{ route('posts.create') }}">+ New Post</a>

  @forelse ($posts as $post)
    <div class="post">
      <h2>{{ $post->title }}</h2>
      @if ($post->image)
        <img class="thumb" src="{{ asset('uploads/' . $post->image) }}" alt="{{ $post->title }}">
      @endif
      <p>{{ \Illuminate\Support\Str::limit($post->body, 120) }}</p>

      <a class="btn" href="{{ route('posts.show', $post->id) }}">View</a>
      <a class="btn" href="{{ route('posts.edit', $post->id) }}">Edit</a>

      <form action="{{ route('posts.destroy', $post->id) }}" method="POST" style="display:inline"
            onsubmit="return confirm('Delete this post?')">
        @csrf
        @method('DELETE')
        <button type="submit" class="btn btn-danger">Delete</button>
      </form>
    </div>
  @empty
    <p>No posts yet. Create the first one!</p>
  @endforelse
@endsection
```
> Uses `@extends`, `@section`, `@parent`, `@if`, and a loop (the book shows `@foreach`; `@forelse` is a Blade variant 🔧 that adds an "empty" case, or you can use `@foreach` if you prefer to stay strictly book-based).

**`create.blade.php` — Create (form)**
```blade
@extends('layouts.app')
@section('title', 'Create Post')

@section('content')
  <h1>Create a New Post</h1>

  @if ($errors->any())
    <div class="alert alert-danger">
      <ul>
        @foreach ($errors->all() as $error)
          <li>{{ $error }}</li>
        @endforeach
      </ul>
    </div>
  @endif

  <form action="{{ route('posts.store') }}" method="POST" enctype="multipart/form-data">
    @csrf

    <label>Title</label>
    <input type="text" name="title" value="{{ old('title') }}">

    <label>Body</label>
    <textarea name="body" rows="6">{{ old('body') }}</textarea>

    <label>Image (jpg or png, max 2MB)</label>
    <input type="file" name="image">

    <br><br>
    <button type="submit" class="btn">Save Post</button>
    <a class="btn" href="{{ route('posts.index') }}">Cancel</a>
  </form>
@endsection
```

**`show.blade.php` — Read (one)**
```blade
@extends('layouts.app')
@section('title', $post->title)

@section('content')
  <h1>{{ $post->title }}</h1>

  @if ($post->image)
    <img class="full" src="{{ asset('uploads/' . $post->image) }}" alt="{{ $post->title }}">
  @endif

  <p>{{ $post->body }}</p>

  <a class="btn" href="{{ route('posts.edit', $post->id) }}">Edit</a>
  <a class="btn" href="{{ route('posts.index') }}">Back</a>
@endsection
```

**`edit.blade.php` — Update (form)**
```blade
@extends('layouts.app')
@section('title', 'Edit Post')

@section('content')
  <h1>Edit Post</h1>

  @if ($errors->any())
    <div class="alert alert-danger">
      <ul>
        @foreach ($errors->all() as $error)
          <li>{{ $error }}</li>
        @endforeach
      </ul>
    </div>
  @endif

  <form action="{{ route('posts.update', $post->id) }}" method="POST" enctype="multipart/form-data">
    @csrf
    @method('PUT')

    <label>Title</label>
    <input type="text" name="title" value="{{ old('title', $post->title) }}">

    <label>Body</label>
    <textarea name="body" rows="6">{{ old('body', $post->body) }}</textarea>

    @if ($post->image)
      <p>Current image:</p>
      <img class="thumb" src="{{ asset('uploads/' . $post->image) }}" alt="{{ $post->title }}">
    @endif

    <label>Replace image (optional)</label>
    <input type="file" name="image">

    <br><br>
    <button type="submit" class="btn">Update Post</button>
    <a class="btn" href="{{ route('posts.index') }}">Cancel</a>
  </form>
@endsection
```

### Step 6 — Run and test in the browser
```bash
php artisan serve
```
Open `http://localhost:8000` (book p. 12).

| # | Test | Steps | Expected result |
|---|---|---|---|
| 1 | **Layout inheritance** | Open Home, then New Post | Same navbar, sidebar and footer on every page |
| 2 | **Create** | New Post → fill title and body → Save | Redirected to the list; green flash: *"Post created successfully!"* |
| 3 | **Flash disappears** | Refresh or click any link | The flash message is gone (book p. 49) |
| 4 | **Validation: empty** | New Post → leave title empty → Save | Back on the form with error messages; typed data kept |
| 5 | **Image upload** | New Post → choose a `.jpg` → Save | Thumbnail appears; file exists in `public/uploads/`; file name stored in the `posts.image` column |
| 6 | **Image validation** | Try uploading a `.pdf` or a file larger than 2MB | Validation error; nothing saved |
| 7 | **Read one** | Click View on a post | Full post and large image |
| 8 | **Update** | Edit → change the title → Update | Flash: *"Post updated successfully!"*; new title in list |
| 9 | **Replace image** | Edit → choose a new image | New image shown; old file removed from `public/uploads/` |
| 10 | **Delete** | Click Delete → confirm | Post removed; flash: *"Post deleted successfully!"* |
| 11 | **CSRF** | In your browser's DevTools, remove the hidden `_token` input and submit a form | **419 Page Expired**, showing CSRF protection working |
| 12 | **`@parent`** | Look at the list page's sidebar | Shows "My Blog / Welcome…" from the layout **plus** "Total posts: N" from the child |

### ✅ Final Verification Checklist
- [ ] All five core concepts work: CRUD, validation, flash message, image upload, Blade layout
- [ ] Every page uses `@extends('layouts.app')` and none repeats the navbar/footer
- [ ] Every form that changes data contains `@csrf`
- [ ] Image upload accepts only `.jpg`/`.png` within the size limit
- [ ] The image **file name** (not the file) is stored in the database
- [ ] Flash message appears once, then disappears after navigating away
- [ ] `php artisan route:list` shows all 7 post routes

### 🌟 Bonus Challenges (for advanced students)
1. **Use the `@parent` directive creatively:** add a second child section that appends "Latest post: …" to the sidebar.
2. **Add an error flash:** when an upload fails, show a red flash message *"Failed to upload image."* (the book's error example, p. 49).
3. 🔧 **Storage disk upload:** replace `move(public_path('uploads'))` with `$request->file('image')->store('posts', 'public')`, run `php artisan storage:link`, and display the image with `asset('storage/' . $post->image)`. *(Not in the book. Compare the two approaches and write down which you prefer and why.)*
4. **Extra field:** add an `author` column with a migration, then update the model, validation and views. Practice rolling back and re-running your migration (book p. 33).
5. **Conditional Blade logic:** show an "Edited" badge using `@if` when `updated_at` differs from `created_at`.
6. **Analysis write-up (5 sentences):** explain how the layout follows the **DRY** principle and why Blade's `{{ }}` escaping protects the page from **XSS**.
7. **Security review:** list two risks of an unvalidated image upload (book p. 50: security and performance) and show how your validation rules address them.

---

## 📌 One-Page Summary for Students

- **CRUD** = Create, Read, Update, Delete.
- **Validation** = data must be correct, complete and safe; failures return to the form with errors.
- **Flash message** = a temporary session message, gone after the next page.
- **Image upload** = file saved in a folder (usually `public/uploads`), name saved in the database; validate size and format.
- **Blade** files live in `resources/views/` and end in `.blade.php`; `{{ }}` escapes output (stops XSS).
- **Layout** (`layouts/app.blade.php`): `@yield` = placeholder, `@section ... @show` = overridable block.
- **Child view**: `@extends` to inherit, `@section ... @endsection` to fill, `@parent` to keep the layout's content.
- **CSRF** = forged requests from other sites; Laravel blocks them by requiring a token in forms.

*Next up: Lesson 5 — Eloquent and Practical Exercises (Laravel UI + Bootstrap, Breeze + Tailwind, secure Admin Dashboard, Authorization).*
