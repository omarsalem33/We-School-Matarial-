# Unit 21 Laravel — Lesson 5: Eloquent and Practical Exercises
### Laravel UI + Bootstrap · Laravel Breeze + Tailwind · Secure Admin Dashboard · Authorization

> **Source:** Unit 21 Laravel Student's Guide (2025–2026), Lesson 5, pp. 58–68, unit terminology pp. 68–71, answer key pp. 79–80.
> **Skill codes:** TPK22 (Analyze and solve common web application tasks by writing PHP programs) · TPC5.7 (Using controllers and routes for APIs and URLs) · TPC5.9 (Create RESTful services and handle errors in data requests)

---

## ⚠️ Mentor's Source-Fidelity Note (read first)

I checked Lesson 5 in the PDF line by line. Here is exactly what it contains and what it does not:

| Item in your request | What the PDF actually has | How this file handles it |
|---|---|---|
| Laravel UI + Bootstrap setup | **Yes**, exact commands (p. 61) | Used exactly as written |
| Laravel Breeze + Tailwind setup | **Yes**, exact commands (p. 62) | Used exactly as written |
| The "Activity" (two projects, "Welcome to My Store") | **Yes** (p. 63) | Included as Project Part A |
| Admin dashboard concept, why secure it, authentication vs authorization | **Yes** (pp. 64–65) | Explained fully |
| The 4-step admin approach (`is_admin` column → middleware → restrict → view) | **Yes**, as an outline only (p. 64) | Turned into a full tutorial, code tagged 🔧 |
| The three authorization tools: Policies, Gates, custom middleware ("which we'll use in this case") | **Yes**, as a list (p. 65) | Explained; the project uses custom middleware as the book says |
| `IsAdmin` middleware code, `bootstrap/app.php` alias, 403 response | **Not in the book** | Written for the project, tagged 🔧 |
| `Gate::define`, `Gate::allows`, `abort_unless`, `AppServiceProvider` | **Not in the book.** The book only says Gates are for "general authorization logic (e.g., `is_admin` checks)" (p. 80) | Written for the project, tagged 🔧 |
| Seeding users | Seeders are defined in Lesson 3 (p. 35); no user-seeder code exists | Written for the project, tagged 🔧 |
| "Common Errors & Fixes" tables | **Not present** in the PDF for Lesson 5 | Part 2 has "Quick Checks" built only from book statements. A separate, clearly labeled 🔧 table covers the errors you will meet in the project |
| Workshop 5 | The book says "The solution is in the workshop" (p. 64), but the workshop is **not included** in the PDF | The project is built from the book's own 4-step outline |

Everything tagged 🔧 is the minimum scaffolding needed to make the book's outline runnable. Please compare it with your official workshop files before using it in class.

---

# PART 1 — Lesson Overview & Objectives

## What students learn in this lesson (book p. 60)
1. Laravel UI + Bootstrap Authentication
2. Laravel Breeze + Tailwind CSS Authentication
3. Create a secure Admin Dashboard
4. Set Authorization rules

## Objectives (book p. 58)

**Remembering**
- Recall the basic commands for creating models and migrations in Laravel.
- Identify the steps for setting up a Laravel project and configuring the database.

**Understanding**
- Explain the role of Eloquent ORM in managing database interactions.
- Describe how Blade templates create dynamic views.
- Understand seeding a database and why it matters in development.

**Applying**
- Use Artisan commands to create models, migrations, and seeders.
- Implement CRUD operations with Eloquent models.
- Develop Blade templates to display lists and details.

**Analyzing**
- Analyze the structure of a simple Laravel API (routes, controllers, models).
- Compare HTTP request methods in RESTful services.

**Evaluating**
- Assess how well Eloquent simplifies database operations.
- Evaluate Postman for testing and debugging API endpoints.

**Creating**
- Develop a simple blog application (models, migrations, controllers, views).
- Design and implement a RESTful API (routes, controllers, CRUD).
- Use Postman to test the API endpoints with various HTTP methods.

> 📝 **Teacher's note:** the objectives table (p. 58) lists blog and API goals, while the lesson's actual content (pp. 60–67) covers authentication, admin dashboards and authorization. This file follows the **content**, and uses the skills practiced in Lessons 3–4 as the foundation.

---

# PART 2 — Interactive Step-by-Step Tutorial

## 🔑 Authentication vs. Authorization (the most important idea of the lesson)

The book's diagram (p. 65) makes it simple:

| | **Authentication** | **Authorization** |
|---|---|---|
| Question it answers | **Who are you?** | **Are you allowed to do that?** |
| What it does | Validates that a system is being accessed by the right person | Checks the user's permissions to access data |
| Example | Logging in with a username and password | Letting only admins into the dashboard |

> 🧠 **Mental model: a concert.** The **ticket check** at the gate is authentication (are you a real ticket holder?). The **VIP wristband** is authorization (are you allowed backstage?). Having a ticket does **not** mean you can go backstage.

> 🎯 Exam trap (Q86): "Authorization ensures users are logged in" is **False**. That is authentication.

---

## 🅰️ Section 1 — Laravel UI + Bootstrap Authentication

### Overview (book p. 61)
- **Laravel UI** provides authentication views built with **Bootstrap 5**.
- Useful if you're familiar with Bootstrap components and utility classes.

### Practical steps

**Step 1: Install a Laravel project**
```bash
composer create-project laravel/laravel laravel-bootstrap-auth
cd laravel-bootstrap-auth
```

**Step 2: Install Laravel UI**
```bash
composer require laravel/ui
```

**Step 3: Generate auth scaffolding with Bootstrap**
```bash
php artisan ui bootstrap --auth
npm install && npm run dev
php artisan migrate
```

> 🧠 **What is "scaffolding"?** Think of the metal frame builders put up before walls go in. `php artisan ui bootstrap --auth` generates the ready-made frame of an authentication system (login and register pages, a nav bar, a home page) so you don't write them from scratch.

### Now your project includes (book p. 62)
- `/login` and `/register` pages styled with Bootstrap
- A navigation bar
- A home dashboard (after login)

### Folder structure added
- **Views:** `resources/views/auth/`, `resources/views/layouts/app.blade.php`
- **CSS/JS:** `resources/css/app.css`, `resources/js/bootstrap.js`

---

## 🅱️ Section 2 — Laravel Breeze + Tailwind CSS Authentication

### Overview (book p. 62)
- **Laravel Breeze** uses minimal, modern **Tailwind CSS** styling.
- Recommended for lightweight, flexible front ends and full customization.

### Practical steps

**Step 1: Install a Laravel project**
```bash
composer create-project laravel/laravel laravel-tailwind-auth
cd laravel-tailwind-auth
```

**Step 2: Install Laravel Breeze**
```bash
composer require laravel/breeze --dev
php artisan breeze:install
npm install && npm run dev
php artisan migrate
```

> 🔧 **Beyond-the-text:** newer versions of Breeze ask you which stack you want when you run `php artisan breeze:install`. Choose the **Blade** option, which matches the book's Tailwind + Blade description (the book mentions "Blade components" on p. 62).

### Now your project includes
- Clean Tailwind-based `/login` and `/register` pages
- A modern layout using Blade components and responsive utilities

### Folder structure added (book p. 63)
- **Views:** `resources/views/auth/`, `resources/views/layouts/`
- **Components:** `resources/views/components/`
- **Tailwind config:** `tailwind.config.js`

---

## ⚖️ Bootstrap Stack vs. Tailwind Stack at a glance

| | **Laravel UI** | **Laravel Breeze** |
|---|---|---|
| CSS framework | **Bootstrap 5** (front-end framework for responsive design) | **Tailwind CSS** (utility-first CSS framework) |
| Install | `composer require laravel/ui` | `composer require laravel/breeze --dev` |
| Generate | `php artisan ui bootstrap --auth` | `php artisan breeze:install` |
| Then run | `npm install && npm run dev`, `php artisan migrate` | `npm install && npm run dev`, `php artisan migrate` |
| Style | Familiar Bootstrap components and utility classes | Minimal, modern, lightweight, fully customizable |
| Views folder | `resources/views/auth/`, `layouts/app.blade.php` | `resources/views/auth/`, `layouts/`, `components/` |
| Extra files | `resources/css/app.css`, `resources/js/bootstrap.js` | `tailwind.config.js` |

> 🧠 **Same destination, different paint.** Both give you registration, login and a dashboard after login. The difference is the CSS stack and the structure of the views.

---

## 🛡️ Section 3 — Create a Secure Admin Dashboard

### 3-1 What is an Admin Dashboard? (book p. 64)
A **protected area** of your website where **only administrators** can manage content, users, orders, or settings.

### 3-2 Why secure it?
A public or unprotected dashboard is a **security risk**. Laravel lets us secure routes with:
- **Authentication middleware** → the user must be logged in
- **Authorization middleware** → the user must have a specific permission (e.g., `is_admin`)

> 🧠 **Mental model: a building with two doors.** Door 1 (**`auth`**): "Are you logged in?" Door 2 (**admin check**): "Are you an admin?" A normal user passes Door 1 but is stopped at Door 2.

### 3-3 The book's approach (4 steps)
1. Add an **`is_admin`** column to the `users` table
2. Create **middleware** to check if the user is an admin
3. **Restrict** dashboard access using this middleware
4. Build a simple admin **dashboard view**

> 📌 The book then says: *"Note: The solution is in the workshop."* The code for these four steps is in the Part 4 project, tagged 🔧.

### Reminder: what middleware is (Lesson 2, p. 25)
Middleware is like a **gatekeeper**. It **checks or modifies requests before they reach your routes or controllers**. The book's example: `->middleware('auth')` makes a route work **only if the user is logged in**.

---

## 🔐 Section 4 — Authorization

### 4-1 What is Authorization? (book p. 65)
Authorization defines **what a logged-in user can or cannot do**, such as:
- Allowing **admins** to access the dashboard or manage users
- Preventing **normal users** from accessing sensitive areas

### 4-2 Laravel's three authorization tools

| Tool | Best for (book wording) | Used in this lesson? |
|---|---|---|
| **Policies** | Model-based logic (model-specific authorization rules) | Concept only |
| **Gates** | General logic (general authorization checks, e.g., `is_admin`) | Concept (code in the 🔧 project) |
| **Custom middleware** | Filtering requests before they reach a route | **Yes, "which we'll use in this case"** |

> 🧠 **Middleware vs. Gates in one sentence:** *Middleware stands at the door of a route and filters the request; a Gate is a reusable yes/no question ("can this user do X?") you can ask from anywhere.*

---

## ✅ Quick Checks (built only from statements in the book)

The PDF has no error tables for this lesson, so these self-checks come from rules the book itself states:

| If you notice… | Check this (book rule) |
|---|---|
| `npm`, `npm run dev` or `npm install` fails | **Node.js** (with npm) must be installed; npm manages front-end dependencies (p. 11, Q6) |
| `composer` command not found | **Composer** is required to install Laravel and its packages (p. 11, Q5/Q13) |
| `php artisan ui ...` is not recognized | Laravel UI must be installed first with `composer require laravel/ui` (p. 61, Q101) |
| `php artisan breeze:install` is not recognized | Breeze must be installed first with `composer require laravel/breeze --dev` (p. 62) |
| `/login` or `/register` pages look unstyled | Compile the front-end assets with **`npm run dev`** (Q99) |
| Login or register fails with a database error | Run **`php artisan migrate`** (the `users` table must exist) (pp. 61–62) |
| Wrong pages appear in the Bootstrap project | Bootstrap auth views live in `resources/views/auth/` and the layout in `resources/views/layouts/app.blade.php` (p. 62) |
| You can't find Breeze's pages | Breeze's views are in **`resources/views/auth/`** (Q97) |
| A normal user can open the admin area | The route lacks the **authorization** step (the `is_admin` check). The `auth` middleware alone only confirms login (pp. 64–65, Q86/Q100) |
| A logged-out visitor can open the dashboard | The route lacks the **`auth` middleware** (p. 25) |
| You can't get Laravel UI and Breeze working in one project | The book builds them in **two separate projects (or branches)** (p. 63) |

### 🔧 Practical errors you will meet in the project (mentor-added, not from the book)

| Symptom | Likely cause | Fix |
|---|---|---|
| **`npm run build` / "Vite manifest not found"** | Front-end assets not compiled | Run `npm install`, then `npm run dev` (keep it running) or `npm run build` for a one-time build |
| `npm run dev` and `php artisan serve` conflict | Both need their own terminal | Open **two terminals**: one for `php artisan serve`, one for `npm run dev` |
| **`SQLSTATE: Column not found: is_admin`** | The `is_admin` migration has not been run | Run `php artisan migrate` (check with `php artisan migrate:status`) |
| **`Target class [admin] does not exist`** | Middleware alias missing or misspelled | Register the alias in `bootstrap/app.php` (Laravel 11/12) and use the same name in `->middleware('admin')` |
| **`Class "App\Http\Middleware\IsAdmin" not found`** | Wrong file name, namespace or `use` line | File must be `app/Http/Middleware/IsAdmin.php` with `namespace App\Http\Middleware;` |
| **Everyone gets 403, even the admin** | `is_admin` is `0`/null for that user, or the field isn't set | Check the `users` table; re-run the seeder; confirm `is_admin` is `1` |
| `is_admin` not saved when seeding | Column missing from `$fillable` | Add `'is_admin'` to `$fillable` in `app/Models/User.php` |
| **`Gate::allows` always false** | Gate name differs (typo) or isn't defined in `boot()` | Use exactly the same name in `Gate::define` and `Gate::allows`; define it inside `boot()` |
| Admin redirected to `/login` repeatedly | Not logged in, or session issue | Log in again; clear caches with `php artisan optimize:clear` |
| **419 Page Expired** on login or form submit | Missing CSRF token in a custom form | Add `@csrf` inside the `<form>` (Breeze forms already include it) |
| `php artisan make:middleware` is not recognized | Wrong folder | Run it from the project root (where `artisan` lives) |
| Changes to middleware seem ignored | Cached configuration | Run `php artisan optimize:clear` |

---

# PART 3 — Important Review Questions & Answers

*Questions 81–101 are the book's own "Do I Know This Already?" items (pp. 66–67). Answers match the book's answer key (pp. 79–80). The remaining questions are derived directly from the lesson text.*

## A. True / False

| No | Statement | Answer | Why (from the book) |
|---|---|---|---|
| 81 | Laravel UI provides authentication scaffolding with Tailwind CSS. | **False** | Laravel UI uses **Bootstrap**; Breeze uses Tailwind |
| 82 | Breeze is a Laravel package for authentication with Tailwind CSS. | **True** | Breeze uses minimal, modern Tailwind CSS styling |
| 83 | Middleware is used to validate form data. | **False** | Middleware is a filter that checks requests before they reach a route; form validation is a separate process |
| 84 | Policies are used for model-specific authorization. | **True** | Policies are for model-based logic |
| 85 | Gates are only for authentication, not authorization. | **False** | Gates are general **authorization** logic checks |
| 86 | Authorization ensures users are logged in before accessing a page. | **False** | That is authentication; authorization determines permissions |
| 87 | Admin dashboards should be accessible to all users. | **False** | They are protected areas for administrators only |
| 88 | Laravel UI is deprecated in Laravel 12. | **False** | The book's answer key marks this False |

## B. Matching

| No | Column (A) | Correct match (Column B) |
|---|---|---|
| 89 | Laravel UI | **C** — Generates Bootstrap auth scaffolding |
| 90 | Breeze | **A** — Uses Tailwind CSS for auth views |
| 91 | Policies | **B** — Model-specific authorization rules |
| 92 | is_admin | **F** — Database flag for admin users |
| 93 | Middleware | **D** — Filters HTTP requests |
| 94 | Tailwind | **E** — Utility-first CSS framework |

## C. Short Answer (95–101)

**95. What's the difference between authentication and authorization?**
> Authentication verifies identity (login); authorization checks permissions (access control).

**96. How do you generate Bootstrap auth views in Laravel?**
> Run `php artisan ui bootstrap --auth`.

**97. Where are Breeze's authentication views stored?**
> In `resources/views/auth/`.

**98. What's the purpose of Gates in Laravel?**
> To define general authorization logic (e.g., `is_admin` checks).

**99. Which npm command compiles front-end assets for Breeze?**
> `npm run dev`.

**100. What is the purpose of the `auth` middleware?**
> Ensures only logged-in users can access a route.

**101. What command installs Laravel UI?**
> `composer require laravel/ui`

## D. Additional Review Questions (from the lesson text)

**Q102 (Understanding).** Compare Laravel UI and Laravel Breeze.
> Laravel UI provides authentication views built with Bootstrap 5 and suits developers familiar with Bootstrap components and utility classes. Breeze uses minimal, modern Tailwind CSS styling and is recommended for lightweight, flexible front ends and full customization.

**Q103 (Remembering).** List the full command sequence for the Laravel UI + Bootstrap setup.
> `composer create-project laravel/laravel laravel-bootstrap-auth`, `cd laravel-bootstrap-auth`, `composer require laravel/ui`, `php artisan ui bootstrap --auth`, `npm install && npm run dev`, `php artisan migrate`.

**Q104 (Remembering).** List the full command sequence for the Breeze + Tailwind setup.
> `composer create-project laravel/laravel laravel-tailwind-auth`, `cd laravel-tailwind-auth`, `composer require laravel/breeze --dev`, `php artisan breeze:install`, `npm install && npm run dev`, `php artisan migrate`.

**Q105 (Remembering).** What does a Laravel UI Bootstrap project include after setup?
> `/login` and `/register` pages styled with Bootstrap, a navigation bar, and a home dashboard after login.

**Q106 (Remembering).** What does a Breeze project include after setup?
> Clean Tailwind-based `/login` and `/register` pages and a modern layout using Blade components and responsive utilities.

**Q107 (Remembering).** Which folders and files does each stack add?
> Laravel UI: `resources/views/auth/`, `resources/views/layouts/app.blade.php`, `resources/css/app.css`, `resources/js/bootstrap.js`. Breeze: `resources/views/auth/`, `resources/views/layouts/`, `resources/views/components/`, and `tailwind.config.js`.

**Q108 (Understanding).** What is an admin dashboard?
> A protected area of a website where only administrators can manage content, users, orders, or settings.

**Q109 (Evaluating).** Why is an unprotected dashboard dangerous, and how does Laravel secure routes?
> A public or unprotected dashboard is a security risk. Laravel secures routes with authentication middleware (user must be logged in) and authorization middleware (user must have a specific permission, e.g., `is_admin`).

**Q110 (Applying).** What are the four steps of the book's secure admin dashboard approach?
> (1) Add an `is_admin` column to the `users` table. (2) Create middleware to check if the user is an admin. (3) Restrict dashboard access using this middleware. (4) Build a simple admin dashboard view.

**Q111 (Understanding).** Give two examples of authorization from the book.
> Allowing admins to access the dashboard or manage users; preventing normal users from accessing sensitive areas.

**Q112 (Analyzing).** Compare Policies, Gates and custom middleware.
> Policies handle model-based logic, Gates handle general logic, and custom middleware filters requests before they reach a route. The workshop uses custom middleware.

**Q113 (Analyzing).** Explain middleware versus gates for protecting an admin area.
> Middleware sits in front of a route and filters the request, checking or modifying it before it reaches the route or controller. A Gate defines a general authorization rule (such as an `is_admin` check) that can be reused wherever that permission needs to be checked.

**Q114 (Understanding).** What does the book's `auth` middleware example do, and what must be true for it to work?
> `->middleware('auth')` makes a route work only if the user is logged in. It works only if Laravel authentication is already set up (e.g., with Breeze or Jetstream).

**Q115 (Applying).** What must each project in the book's Activity include?
> A registration page, a login and logout flow, a welcome dashboard after login, and a custom message on the home page ("Welcome to My Store").

**Q116 (Analyzing).** What should happen when a logged-in normal user tries to open an admin page? *(extends the book's authorization idea; the 403 status code is a 🔧 practical detail)*
> They are authenticated but not authorized, so the request must be stopped. The standard response is HTTP 403 Forbidden.

---

# PART 4 — Hands-On Practical Project

## 🎓 Capstone Mini-Project: **Building a Secure Admin Dashboard with Role-Based Access Control**

**Goal:** Build a Laravel application with registration and login (Breeze + Tailwind), an `is_admin` flag on users, an **admin-only dashboard** protected by custom middleware and by a Gate, and a normal user area, then test both roles.

> 🔧 **Beyond-the-text notice:** The book gives the 4-step approach and says "the solution is in the workshop," but the workshop is not in the PDF. The code below implements the book's outline. Items tagged 🔧 are not in the guide. Compare them with your official workshop.

### Book concepts used

| Book concept | Where it appears |
|---|---|
| Laravel Breeze + Tailwind | Authentication scaffolding (Part B, Step 1) |
| `is_admin` column | Migration (Step 2) |
| Authentication middleware (`auth`) | Protects the dashboard routes (Step 5) |
| Custom authorization middleware | `IsAdmin` (Steps 3–4) |
| Gates | `access-admin` Gate (Step 6) |
| Routes grouped under `/admin` | Book's grouping example from Lesson 2 (Step 5) |
| Blade views and layouts | Admin dashboard view (Step 7) |
| Seeders | Test users (Step 8) |

---

## Part A — The book's Activity (do this first)

**Task (book p. 63):** Build two Laravel projects (or branches):
1. One using **Laravel UI with Bootstrap**
2. One using **Laravel Breeze with Tailwind CSS**

Each project must include:
- A registration page
- A login and logout flow
- A welcome dashboard after login
- A custom message on the home page: **"Welcome to My Store"**

### A-1 Laravel UI + Bootstrap project
```bash
composer create-project laravel/laravel laravel-bootstrap-auth
cd laravel-bootstrap-auth
composer require laravel/ui
php artisan ui bootstrap --auth
npm install && npm run dev
php artisan migrate
php artisan serve
```
Open `http://localhost:8000`. Then edit the home page view (`resources/views/welcome.blade.php`) so it shows **Welcome to My Store**. 🔧 *(Keep `npm run dev` running in a second terminal.)*

### A-2 Breeze + Tailwind project
```bash
composer create-project laravel/laravel laravel-tailwind-auth
cd laravel-tailwind-auth
composer require laravel/breeze --dev
php artisan breeze:install
npm install && npm run dev
php artisan migrate
php artisan serve
```
Edit the home page the same way.

### A-3 Check each project
- [ ] Register a new account works
- [ ] Login works and lands on a welcome dashboard
- [ ] Logout works and returns you to the public pages
- [ ] The home page displays **Welcome to My Store**
- [ ] Compare the two: look at `resources/views/auth/` in each and write 3 differences

---

## Part B — The Secure Admin Dashboard (Breeze project)

Continue inside the Breeze project `laravel-tailwind-auth` (or create a new one named `secure-admin` with the same Breeze commands).

### Step 1 — Authentication with Breeze (book commands)
Already done in A-2. Start both servers in **two terminals**:
```bash
php artisan serve        # terminal 1
npm run dev              # terminal 2
```

### Step 2 — Add the `is_admin` column (book approach, step 1) 🔧
```bash
php artisan make:migration add_is_admin_to_users_table --table=users
```
Edit the new file in `database/migrations/`:
```php
public function up(): void
{
    Schema::table('users', function (Blueprint $table) {
        $table->boolean('is_admin')->default(false);
    });
}

public function down(): void
{
    Schema::table('users', function (Blueprint $table) {
        $table->dropColumn('is_admin');
    });
}
```
Run it:
```bash
php artisan migrate
```
**✔ Expected result:** the `users` table has an `is_admin` column; every user is a normal user by default.

Open **`app/Models/User.php`** and make Laravel accept and understand the new field:
```php
protected $fillable = [
    'name',
    'email',
    'password',
    'is_admin',     // added
];
```
In the same file, add `'is_admin' => 'boolean'` to the casts (inside the `casts()` method, or the `$casts` property if your version uses one):
```php
protected function casts(): array
{
    return [
        'email_verified_at' => 'datetime',
        'password' => 'hashed',
        'is_admin' => 'boolean',   // added
    ];
}
```

### Step 3 — Create the `IsAdmin` middleware (book approach, step 2) 🔧
```bash
php artisan make:middleware IsAdmin
```
Edit **`app/Http/Middleware/IsAdmin.php`**:
```php
<?php

namespace App\Http\Middleware;

use Closure;
use Illuminate\Http\Request;
use Symfony\Component\HttpFoundation\Response;

class IsAdmin
{
    public function handle(Request $request, Closure $next): Response
    {
        // Not logged in, or logged in but not an admin → stop with 403 Forbidden
        if (!auth()->check() || !auth()->user()->is_admin) {
            abort(403, 'Access denied. Admins only.');
        }

        return $next($request);   // admin → let the request continue
    }
}
```
> 🧠 **Read it like a bouncer:** *"Are you logged in AND an admin? No? → 403, you shall not pass. Yes? → `$next($request)`, go ahead."*

### Step 4 — Register the middleware alias (🔧)
In Laravel 11/12 middleware aliases are registered in **`bootstrap/app.php`**:
```php
->withMiddleware(function (Middleware $middleware) {
    $middleware->alias([
        'admin' => \App\Http\Middleware\IsAdmin::class,
    ]);
})
```
Now `'admin'` can be used in routes. *(If you ever see `Target class [admin] does not exist`, this step is missing.)*

> 🔧 On older Laravel versions (10 and earlier) the alias goes in `app/Http/Kernel.php` under `$routeMiddleware`. The book does not cover either.

### Step 5 — Restrict the dashboard (book approach, step 3) 🔧
Create the controller (book command, Lesson 2):
```bash
php artisan make:controller AdminController
```
**`app/Http/Controllers/AdminController.php`**
```php
<?php

namespace App\Http\Controllers;

use App\Models\User;

class AdminController extends Controller
{
    public function index()
    {
        return view('admin.dashboard', [
            'totalUsers'  => User::count(),
            'totalAdmins' => User::where('is_admin', true)->count(),
        ]);
    }

    public function users()
    {
        return view('admin.users', ['users' => User::all()]);
    }
}
```
**`routes/web.php`** — uses the book's route-group syntax (Lesson 2, section 3-5) with both `auth` and the admin check:
```php
use App\Http\Controllers\AdminController;

Route::middleware(['auth', 'admin'])
    ->prefix('admin')
    ->group(function () {
        Route::get('/',      [AdminController::class, 'index'])->name('admin.dashboard');
        Route::get('/users', [AdminController::class, 'users'])->name('admin.users');
    });
```
> 🧠 `auth` = Door 1 (logged in?). `admin` = Door 2 (is_admin?). Both must pass.
> The URLs become `/admin` and `/admin/users` (the book's grouping example, p. 24).

Check them:
```bash
php artisan route:list
```

### Step 6 — Add a Gate (Authorization tool #2) 🔧
Gates are the book's tool for "general authorization logic (e.g., `is_admin` checks)." Define one in **`app/Providers/AppServiceProvider.php`**:
```php
<?php

namespace App\Providers;

use App\Models\User;
use Illuminate\Support\Facades\Gate;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    public function register(): void
    {
        //
    }

    public function boot(): void
    {
        Gate::define('access-admin', function (User $user) {
            return $user->is_admin;
        });
    }
}
```

**Use the Gate in a controller** (add to `AdminController::users()` as a second layer of defence):
```php
use Illuminate\Support\Facades\Gate;

public function users()
{
    abort_unless(Gate::allows('access-admin'), 403);   // 403 if the gate says no

    return view('admin.users', ['users' => User::all()]);
}
```

**Use the Gate in Blade** (show the admin link only to admins):
```blade
@can('access-admin')
  <a href="{{ route('admin.dashboard') }}">Admin Dashboard</a>
@endcan
```

| Tool | Used here as |
|---|---|
| `Gate::define('access-admin', ...)` | Writes the rule once |
| `Gate::allows('access-admin')` | Asks the question: yes or no? |
| `abort_unless(condition, 403)` | "Unless the answer is yes, stop with 403" |
| `@can('access-admin')` | Shows or hides parts of a Blade page |

### Step 7 — Build the admin views (book approach, step 4) 🔧
Create the folder `resources/views/admin/` with two files. Breeze's layout is a Blade **component** (`<x-app-layout>`), as the book notes on p. 62.

**`resources/views/admin/dashboard.blade.php`**
```blade
<x-app-layout>
    <x-slot name="header">
        <h2 class="font-semibold text-xl text-gray-800 leading-tight">
            Admin Dashboard
        </h2>
    </x-slot>

    <div class="py-12">
        <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
            <div class="bg-white shadow-sm sm:rounded-lg p-6">
                <p class="text-lg">Welcome, Admin {{ auth()->user()->name }}!</p>

                <div class="grid grid-cols-2 gap-4 mt-6">
                    <div class="p-4 bg-blue-50 rounded">
                        <p class="text-sm text-gray-600">Total users</p>
                        <p class="text-3xl font-bold">{{ $totalUsers }}</p>
                    </div>
                    <div class="p-4 bg-green-50 rounded">
                        <p class="text-sm text-gray-600">Admins</p>
                        <p class="text-3xl font-bold">{{ $totalAdmins }}</p>
                    </div>
                </div>

                <a href="{{ route('admin.users') }}" class="inline-block mt-6 underline">
                    Manage users →
                </a>
            </div>
        </div>
    </div>
</x-app-layout>
```

**`resources/views/admin/users.blade.php`**
```blade
<x-app-layout>
    <x-slot name="header">
        <h2 class="font-semibold text-xl text-gray-800 leading-tight">
            Users
        </h2>
    </x-slot>

    <div class="py-12">
        <div class="max-w-7xl mx-auto sm:px-6 lg:px-8">
            <div class="bg-white shadow-sm sm:rounded-lg p-6">
                <table class="w-full text-left">
                    <thead>
                        <tr class="border-b">
                            <th class="py-2">Name</th>
                            <th class="py-2">Email</th>
                            <th class="py-2">Role</th>
                        </tr>
                    </thead>
                    <tbody>
                        @foreach ($users as $user)
                            <tr class="border-b">
                                <td class="py-2">{{ $user->name }}</td>
                                <td class="py-2">{{ $user->email }}</td>
                                <td class="py-2">
                                    @if ($user->is_admin)
                                        Admin
                                    @else
                                        User
                                    @endif
                                </td>
                            </tr>
                        @endforeach
                    </tbody>
                </table>
            </div>
        </div>
    </div>
</x-app-layout>
```
*(The `@if` and `@foreach` directives are exactly the ones in the book, p. 52.)*

Optional: add the Gate-controlled link to the Breeze navigation (`resources/views/layouts/navigation.blade.php`) using the `@can` snippet from Step 6.

### Step 8 — Create test users with a Seeder (Lesson 3 concept) 🔧
Edit **`database/seeders/DatabaseSeeder.php`**:
```php
<?php

namespace Database\Seeders;

use App\Models\User;
use Illuminate\Database\Seeder;
use Illuminate\Support\Facades\Hash;

class DatabaseSeeder extends Seeder
{
    public function run(): void
    {
        User::create([
            'name'     => 'Admin User',
            'email'    => 'admin@example.com',
            'password' => Hash::make('password123'),
            'is_admin' => true,
        ]);

        User::create([
            'name'     => 'Normal User',
            'email'    => 'user@example.com',
            'password' => Hash::make('password123'),
            'is_admin' => false,
        ]);
    }
}
```
Run it:
```bash
php artisan db:seed
```
> ⚠️ These credentials are for **local testing only**. Never use them in a real deployment.

### Step 9 — Test the login flow: regular user vs. admin

| # | Test | Steps | Expected result |
|---|---|---|---|
| 1 | **Logged out** | Visit `/admin` without logging in | Redirected to `/login` (the `auth` door) |
| 2 | **Register** | Register a brand-new account | Account created, lands on the user dashboard; `is_admin` = 0 in the database |
| 3 | **Normal user blocked** | Log in as `user@example.com`, then visit `/admin` | **403 Forbidden** ("Access denied. Admins only.") |
| 4 | **Normal user blocked (Gate)** | As the normal user visit `/admin/users` | **403 Forbidden** |
| 5 | **Link hidden** | As the normal user, look at the navigation | No "Admin Dashboard" link (`@can` is false) |
| 6 | **Admin allowed** | Log out, log in as `admin@example.com`, visit `/admin` | Admin dashboard with user counts |
| 7 | **Admin users page** | Click "Manage users" | Table of all users with roles |
| 8 | **Link shown** | As admin, check the navigation | "Admin Dashboard" link visible |
| 9 | **Logout** | Click Logout, then visit `/admin` | Redirected to `/login` again |
| 10 | **Database check** | Look at the `users` table | `is_admin` = 1 for admin, 0 for the others |

### ✅ Final Verification Checklist
- [ ] Both stacks from the book's Activity were built (Laravel UI + Bootstrap, Breeze + Tailwind)
- [ ] The `users` table has an `is_admin` boolean column, default false
- [ ] `IsAdmin` middleware exists and aborts with 403 for non-admins
- [ ] The `admin` alias is registered and used in the route group
- [ ] Admin routes use **both** `auth` and `admin`
- [ ] The `access-admin` Gate is defined and used with `Gate::allows` / `abort_unless`
- [ ] A regular user gets 403; an admin gets in; a logged-out visitor is sent to login
- [ ] You can explain, in your own words, **authentication vs. authorization** and **middleware vs. Gates**

### 🌟 Bonus Challenges (for advanced students)
1. **Policies (book: model-specific authorization):** create a Policy so that only the author of a post (from Lesson 4's blog) can edit or delete it. *(Concept in the book, code not provided.)*
2. 🔧 **Middleware via Gate:** protect a route with `->middleware('can:access-admin')` and compare it with your custom `IsAdmin` middleware. Which one is easier to reuse?
3. **Two roles → more roles:** replace the boolean with a `role` column (`admin`, `editor`, `user`). Update the migration, the Gate and the middleware.
4. **Friendly 403 page:** create a custom error view for 403 responses with a message and a "Go back" button. 🔧
5. **Admin actions:** add a button on the users page that promotes or demotes a user. Protect it with the Gate and `@csrf` (forms need a token, book p. 55).
6. **Block self-demotion:** prevent an admin from removing their own admin rights.
7. **Compare the stacks:** build the same login page in both projects from Part A and write 5 sentences comparing the Bootstrap and Tailwind structures, using the comparison table in Part 2.
8. **Security review (Evaluating):** list three ways an unprotected admin area could be abused, and for each one say which tool from this lesson stops it (authentication, middleware, Gate or Policy).

---

## 📌 One-Page Summary for Students

- **Authentication** = *who are you?* (login). **Authorization** = *are you allowed?* (permissions).
- **Laravel UI** → `composer require laravel/ui` → `php artisan ui bootstrap --auth` → **Bootstrap 5** views.
- **Breeze** → `composer require laravel/breeze --dev` → `php artisan breeze:install` → **Tailwind CSS** views.
- Both then run `npm install && npm run dev` and `php artisan migrate`.
- **Admin dashboard** = protected area for admins only. Unprotected = security risk.
- **Book's 4 steps:** `is_admin` column → middleware → restrict routes → build the view.
- **`auth` middleware** = logged in? **Custom middleware** = admin? **Gates** = reusable authorization rule. **Policies** = model-specific rules.
- 🔧 Admin check fails → **403 Forbidden**.

*This completes Unit 21, Lessons 3–5.*
