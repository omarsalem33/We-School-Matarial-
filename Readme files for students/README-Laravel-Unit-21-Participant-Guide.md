# Unit 21: Web Development and Programming with Laravel

A participant reference for the session. Follow the five lessons in order. The examples build a small Posts application and connect Laravel routing, controllers, Blade, database migrations, Eloquent, CRUD, file uploads, and authentication.

> **Version note:** The presentation combines topics and package commands from different Laravel releases. Core ideas such as routes, controllers, Blade, migrations, and Eloquent remain broadly applicable, but installation steps and authentication starter kits can change. Check the Laravel version used in your project before running package-specific commands.

## Learning outcomes

By the end of the session, you should be able to:

- Explain the purpose of Laravel and the MVC pattern.
- Set up a local Laravel project and identify its key folders.
- Explain how Composer and Artisan support PHP development.
- Define routes, use HTTP methods, and connect routes to controllers.
- Pass data to Blade views and use conditions, loops, and layouts.
- Create and run database migrations.
- Use Eloquent models, relationships, factories, seeders, and queries.
- Build basic CRUD operations with validation and user feedback.
- Explain secure image upload, authentication, and authorization.

## Application used in the examples

We will build a simple application for creating and viewing posts. A post has a title, body, and optional image. Later, we will protect management actions so only permitted users can make changes.

## Request flow

A typical page request follows this path:

```text
Browser → Route → Controller → Model / Database
                           ↓
Browser ← HTML response ← Blade view
```

The route selects the code that handles the request. The controller coordinates the work. A model reads or writes data when needed. A Blade view renders HTML for the browser.

---

# Lesson 1: Introduction to Laravel and Installation

## 1. What is Laravel?

Laravel is a free, open-source PHP framework for building websites and web applications. It provides a consistent structure and tools for common tasks such as routing, database access, validation, sessions, authentication, and rendering pages.

A framework does not decide what your application should do. It gives your team a structure and reusable components so you can focus on the application’s behavior.

## 2. MVC architecture

Laravel commonly organizes web applications using Model, View, and Controller:

- **Model:** Represents application data and provides a way to work with it. `Post` is an example model.
- **View:** Presents information to the user. Laravel uses Blade templates for many server-rendered pages.
- **Controller:** Handles a request and coordinates the work needed to return a response.

For example, when a visitor opens the posts list, a route sends the request to a controller. The controller asks the `Post` model for recent posts, then passes those posts to a Blade view. The view turns them into HTML.

Keep each part focused. A Blade file should render the page, rather than run database queries. A controller should coordinate a request, rather than become a home for every rule in a large application.

## 3. Development environment

A local Laravel project typically needs:

- PHP in a version supported by the chosen Laravel release.
- Composer for PHP dependencies.
- A database such as SQLite, MySQL, or PostgreSQL.
- Node.js and npm when the project needs to build frontend assets.
- A local environment such as Laragon, XAMPP, Docker, or WSL, depending on the team setup.

Check which tools are available:

```bash
php -v
composer --version
node -v
npm -v
```

The exact PHP extensions and versions depend on the Laravel release and packages in the project.

## 4. Create and run a project

One common way to create a project is:

```bash
composer create-project laravel/laravel laravel-posts
cd laravel-posts
php artisan --version
php artisan serve
```

- `composer create-project` creates the project and installs its dependencies.
- `cd laravel-posts` moves into the project folder.
- `php artisan --version` displays the installed Laravel version.
- `php artisan serve` starts a local development server. Open the local URL shown in the terminal.

This local server is for development. A production deployment requires an appropriate web server and deployment configuration.

## 5. Important project folders

| Folder or file | Purpose |
|---|---|
| `app/` | Application code, including models and controllers |
| `routes/web.php` | Routes for browser-based web pages |
| `resources/views/` | Blade view templates |
| `resources/` | Frontend source files such as CSS and JavaScript |
| `database/migrations/` | Versioned database schema changes |
| `database/factories/` | Definitions for generating sample records |
| `database/seeders/` | Classes that insert initial or sample data |
| `public/` | Web entry point and public assets |
| `config/` | Application and package configuration |
| `storage/` | Application-managed files and logs |
| `vendor/` | Composer-installed dependencies; do not edit these files manually |

You do not need to memorize every folder. Learn where to look for the files used in this session.

## 6. The `.env` file

The `.env` file holds settings that vary between environments, such as the database connection and application URL.

```dotenv
APP_NAME="Laravel Posts"
APP_ENV=local
APP_DEBUG=true
APP_URL=http://127.0.0.1:8000
DB_CONNECTION=sqlite
```

Important practices:

- Do not commit a real `.env` file or its secrets to source control. A project can provide a `.env.example` with safe placeholder values.
- `APP_DEBUG=true` is useful during local development, but production should not expose detailed error pages to users.
- Treat `APP_KEY` as a secret application encryption key. Do not share or casually replace it.
- Configure the database for the environment where the application runs.

---

# Lesson 2: Composer and Artisan

## 1. Composer and PHP dependencies

Composer manages PHP libraries used by a project. The `composer.json` file describes dependencies, while `composer.lock` records the exact versions installed for the project.

Common commands:

```bash
composer require vendor/package
composer install
composer update
```

- `composer require` adds a package to the project.
- `composer install` installs the versions recorded in `composer.lock`. This is commonly used after checking out an existing project.
- `composer update` resolves newer compatible versions and may change the lock file. Review dependency updates before applying them to a shared project.

## 2. Artisan command-line tool

Artisan is Laravel’s command-line interface. It can show available commands, generate application files, inspect routes, and run framework tasks.

```bash
php artisan list
php artisan help make:controller
php artisan make:controller HelloController
php artisan route:list
```

Generated files provide a Laravel-shaped starting point. Read and complete them instead of treating generation as a substitute for understanding the code.

Useful commands for this session include:

```bash
php artisan make:model Post -m
php artisan make:controller PostController --resource
php artisan migrate
php artisan route:list
```

The `-m` option creates a migration along with the model. The `--resource` option creates common controller methods for a resource.

---

# Lesson 3: Routing, REST, and Views

## 1. Routes

Routing defines how the application responds to an HTTP request for a URL. Web routes are commonly placed in `routes/web.php`.

A simple route:

```php
use Illuminate\Support\Facades\Route;

Route::get('/hello', function () {
    return 'Hello, Laravel!';
});
```

When a browser sends `GET /hello`, Laravel runs the closure and returns the text. This is useful for a small demonstration. As the behavior grows, move it into a controller.

## 2. Route to a controller

Generate a controller:

```bash
php artisan make:controller HelloController
```

Add a method to `HelloController`:

```php
public function greet()
{
    return 'Hello from the controller!';
}
```

Connect the route in `routes/web.php`:

```php
use App\Http\Controllers\HelloController;

Route::get('/greet', [HelloController::class, 'greet']);
```

The route delegates the request to the `greet` method. Open `/greet` in the browser to see the response.

## 3. HTTP methods and REST

HTTP methods communicate the kind of operation a client is requesting. REST-style routes commonly organize requests around resources such as posts.

| Method and URL | Typical purpose |
|---|---|
| `GET /posts` | List posts |
| `GET /posts/5` | Show one post |
| `POST /posts` | Create a post |
| `PUT /posts/5` | Replace a post’s data |
| `PATCH /posts/5` | Update part of a post |
| `DELETE /posts/5` | Delete a post |

Use `GET` for reading. A request that changes or deletes data should use an appropriate method, rather than a `GET` link.

## 4. Route parameters and named routes

A route parameter captures part of the URL:

```php
Route::get('/users/{id}', [UserController::class, 'show'])
    ->name('users.show');
```

For `/users/42`, Laravel passes `42` to the controller method. A named route lets a view generate a URL without hard-coding the path:

```blade
<a href="{{ route('users.show', ['id' => $user->id]) }}">View user</a>
```

Optional parameters such as `{id?}` are supported, but use them only when the optional and supplied cases have clear behavior. Separate list and detail routes are often easier to understand.

## 5. Route groups and middleware

Groups can share a URL prefix, a name prefix, or middleware:

```php
Route::prefix('admin')
    ->middleware('auth')
    ->name('admin.')
    ->group(function () {
        Route::get('/posts', [AdminPostController::class, 'index'])
            ->name('posts.index');
    });
```

- `prefix('admin')` adds `/admin` to the URL.
- `middleware('auth')` requires the user to be authenticated.
- `name('admin.')` prefixes route names.

Middleware runs during request handling and can inspect, continue, or reject a request. Authentication middleware checks whether a user is signed in. It does not by itself decide whether that user may manage a particular post.

Inspect the routes in the project:

```bash
php artisan route:list
```

## 6. Return a Blade view and pass data

Create `resources/views/welcome.blade.php`:

```blade
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Welcome</title>
</head>
<body>
    <h1>Hello, {{ $name }}!</h1>
</body>
</html>
```

In a controller:

```php
public function showWelcome()
{
    return view('welcome', ['name' => 'Student']);
}
```

The view name `welcome` maps to `resources/views/welcome.blade.php`. The array passes a value named `name` to the template. Blade’s `{{ }}` syntax escapes output by default, which helps prevent user-provided text from being interpreted as HTML.

Avoid `{!! !!}` for untrusted input. It renders unescaped HTML and can create cross-site scripting vulnerabilities.

## 7. Blade conditions and loops

```blade
@if ($user->is_admin)
    <p>Welcome, Admin!</p>
@else
    <p>Welcome, User!</p>
@endif

@forelse ($posts as $post)
    <article>
        <h2>{{ $post->title }}</h2>
        <p>{{ $post->body }}</p>
    </article>
@empty
    <p>No posts yet.</p>
@endforelse
```

`@if` and `@else` choose which markup to render. `@forelse` loops through a collection and provides an empty-state branch. Keep database queries and application rules out of the template; prepare the data before rendering the view.

## 8. Layout inheritance

A shared layout avoids repeating the same page structure.

`resources/views/layouts/app.blade.php`:

```blade
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>@yield('title', 'Posts')</title>
</head>
<body>
    <header>Posts application</header>
    <main>@yield('content')</main>
</body>
</html>
```

`resources/views/posts/index.blade.php`:

```blade
@extends('layouts.app')

@section('title', 'All posts')

@section('content')
    <h1>All posts</h1>
@endsection
```

`@extends` selects the layout. `@section` fills placeholders defined with `@yield`. Blade components can also package reusable interface elements.

---

# Lesson 4: Database Interaction, Eloquent, Migrations, and Controllers

## 1. Migrations

A migration describes a change to the database schema in code. It lets a team review and apply schema changes consistently across development environments.

Create a model and migration:

```bash
php artisan make:model Post -m
```

A migration for a posts table can look like this:

```php
use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration {
    public function up(): void
    {
        Schema::create('posts', function (Blueprint $table) {
            $table->id();
            $table->string('title');
            $table->text('body');
            $table->string('image_path')->nullable();
            $table->timestamps();
        });
    }

    public function down(): void
    {
        Schema::dropIfExists('posts');
    }
};
```

- `up()` applies the change.
- `down()` describes how to reverse it.
- `id()` adds a primary key.
- `nullable()` allows a column to have no value.
- `timestamps()` adds `created_at` and `updated_at` columns.

Run and inspect migrations:

```bash
php artisan migrate
php artisan migrate:status
php artisan migrate:rollback
```

A rollback may remove tables or columns and their data, depending on the migration. Understand its effect before using it, especially in an environment with data you need to keep.

## 2. Models and Eloquent ORM

A model represents application data. Eloquent is Laravel’s ORM, which lets you query and modify database records through model classes.

`app/Models/Post.php`:

```php
<?php

namespace App\Models;

use Illuminate\Database\Eloquent\Model;

class Post extends Model
{
    protected $fillable = ['title', 'body', 'image_path'];
}
```

`$fillable` lists fields that may be assigned through mass assignment, such as `Post::create($data)`. This helps limit which fields can be set this way. It does not replace validation or authorization.

Examples:

```php
$posts = Post::latest()->get();
$post = Post::findOrFail($id);

$post = Post::create([
    'title' => 'First post',
    'body' => 'Example content',
]);

$post->update(['title' => 'Updated title']);
$post->delete();
```

`findOrFail` returns a not-found response if the record does not exist. For large lists, use pagination instead of loading every record into one page.

## 3. Eloquent relationships

A relationship describes how records in two tables connect. For example, one user can have many posts, and each post can belong to one user.

In `User.php`:

```php
public function posts(): HasMany
{
    return $this->hasMany(Post::class);
}
```

In `Post.php`:

```php
public function user(): BelongsTo
{
    return $this->belongsTo(User::class);
}
```

A `user_id` foreign key is commonly stored in the `posts` table. Other relationship types include `hasOne`, `belongsToMany`, and polymorphic relationships. Choose a relationship based on the actual data model.

## 4. Query Builder

Laravel’s Query Builder provides a fluent PHP interface for constructing database queries:

```php
use Illuminate\Support\Facades\DB;

$posts = DB::table('posts')
    ->where('title', 'like', '%Laravel%')
    ->orderByDesc('created_at')
    ->get();
```

Use Eloquent when model behavior and relationships are useful. Use Query Builder when its direct query interface suits the task. Do not build SQL by concatenating user input into a query.

## 5. Factories and seeders

A **factory** defines how to create sample records. A **seeder** runs code that inserts initial or sample data, often by using a factory.

```bash
php artisan make:factory PostFactory --model=Post
php artisan make:seeder PostSeeder
```

A simplified factory definition:

```php
return [
    'title' => fake()->sentence(),
    'body' => fake()->paragraphs(3, true),
];
```

A seeder can create sample posts:

```php
Post::factory()->count(10)->create();
```

Connect the seeder through `DatabaseSeeder`, then run:

```bash
php artisan db:seed
```

`php artisan migrate:fresh --seed` drops and recreates tables before seeding. Do not run it against a database containing data you need to keep.

## 6. Controllers and resource actions

Generate a resource controller:

```bash
php artisan make:controller PostController --resource
```

A resource controller commonly includes these actions:

| Action | Purpose |
|---|---|
| `index` | List records |
| `create` | Show a create form |
| `store` | Validate and save a new record |
| `show` | Show one record |
| `edit` | Show an edit form |
| `update` | Validate and save changes |
| `destroy` | Delete a record |

These action names will be used in the practical exercise.

---

# Lesson 5: Practical Eloquent Exercise, CRUD, and Authentication

## 1. Add resource routes

In `routes/web.php`:

```php
use App\Http\Controllers\PostController;
use Illuminate\Support\Facades\Route;

Route::resource('posts', PostController::class);
```

This registers conventional routes for the resource. Run `php artisan route:list` to see the exact routes and HTTP methods in your application.

## 2. Read and display posts

In `PostController`:

```php
public function index()
{
    return view('posts.index', [
        'posts' => Post::latest()->paginate(10),
    ]);
}
```

The controller retrieves the ten most recent posts and passes them to the view.

`resources/views/posts/index.blade.php`:

```blade
@extends('layouts.app')

@section('title', 'Posts')

@section('content')
    <h1>Posts</h1>
    <a href="{{ route('posts.create') }}">Create a post</a>

    @forelse ($posts as $post)
        <article>
            <h2>
                <a href="{{ route('posts.show', $post) }}">{{ $post->title }}</a>
            </h2>
            <p>{{ $post->body }}</p>
            <a href="{{ route('posts.edit', $post) }}">Edit</a>
        </article>
    @empty
        <p>No posts yet.</p>
    @endforelse

    {{ $posts->links() }}
@endsection
```

The template displays the records and provides pagination links. It also shows a message when the collection is empty.

## 3. Create and validate a post

In `PostController`:

```php
use Illuminate\Http\Request;

public function create()
{
    return view('posts.create');
}

public function store(Request $request)
{
    $data = $request->validate([
        'title' => ['required', 'string', 'max:150'],
        'body' => ['required', 'string', 'max:10000'],
    ]);

    Post::create($data);

    return redirect()->route('posts.index')
        ->with('success', 'Post created successfully.');
}
```

Validation checks the incoming values before saving. If validation fails, Laravel redirects back with errors and the previous input. If validation succeeds, only validated data is passed to `create`. The redirect prevents a browser refresh from resubmitting the same form and carries a temporary success message.

The model should allow these fields:

```php
protected $fillable = ['title', 'body', 'image_path'];
```

`resources/views/posts/create.blade.php`:

```blade
@extends('layouts.app')

@section('title', 'Create a post')

@section('content')
    <h1>Create a post</h1>
    <form method="POST" action="{{ route('posts.store') }}">
        @csrf

        <label>Title
            <input name="title" value="{{ old('title') }}">
        </label>
        @error('title') <p>{{ $message }}</p> @enderror

        <label>Body
            <textarea name="body">{{ old('body') }}</textarea>
        </label>
        @error('body') <p>{{ $message }}</p> @enderror

        <button type="submit">Save</button>
    </form>
@endsection
```

- The HTML `name` values must match the keys used in validation.
- `@csrf` includes the CSRF token required for a web form.
- `old()` restores input after validation errors.
- `@error` displays a field’s validation message.

**Try it:** Submit the form with an empty title. Observe the validation error and confirm that the post was not saved. Then enter valid data and submit again.

## 4. Show one post

In the controller:

```php
public function show(Post $post)
{
    return view('posts.show', compact('post'));
}
```

Laravel can resolve the `{post}` route value to a `Post` model. This feature is called route model binding. If no matching record exists, Laravel returns a not-found response.

`resources/views/posts/show.blade.php`:

```blade
@extends('layouts.app')

@section('title', $post->title)

@section('content')
    <h1>{{ $post->title }}</h1>
    <p>{{ $post->body }}</p>
@endsection
```

## 5. Update and delete a post

An edit form can submit a PUT request using method spoofing:

```blade
<form method="POST" action="{{ route('posts.update', $post) }}">
    @csrf
    @method('PUT')

    <input name="title" value="{{ old('title', $post->title) }}">
    <textarea name="body">{{ old('body', $post->body) }}</textarea>

    <button type="submit">Save changes</button>
</form>
```

In the controller:

```php
public function update(Request $request, Post $post)
{
    $data = $request->validate([
        'title' => ['required', 'string', 'max:150'],
        'body' => ['required', 'string', 'max:10000'],
    ]);

    $post->update($data);

    return redirect()->route('posts.show', $post)
        ->with('success', 'Post updated successfully.');
}
```

A delete form:

```blade
<form method="POST" action="{{ route('posts.destroy', $post) }}">
    @csrf
    @method('DELETE')
    <button type="submit">Delete</button>
</form>
```

HTML forms submit GET or POST directly. `@method('PUT')` and `@method('DELETE')` add a hidden field so Laravel can handle the intended method. Use a state-changing method for deletion rather than a GET link.

## 6. Flash messages

A flash message is temporary session data commonly used to confirm a completed action.

The controller can redirect with a message:

```php
return redirect()->route('posts.index')
    ->with('success', 'Post created successfully.');
```

Display it in a layout or view:

```blade
@if (session('success'))
    <div role="status">{{ session('success') }}</div>
@endif
```

Use escaped output when displaying the message.

## 7. Image uploads

An HTML form must use `multipart/form-data` to send a file:

```blade
<form method="POST" action="{{ route('posts.store') }}" enctype="multipart/form-data">
    @csrf
    <input type="file" name="image" accept="image/*">
    <button type="submit">Save</button>
</form>
```

The `accept` attribute helps the browser’s file picker, but it is not server-side validation. Validate and store the file on the server:

```php
$data = $request->validate([
    'title' => ['required', 'string', 'max:150'],
    'body' => ['required', 'string', 'max:10000'],
    'image' => ['nullable', 'image', 'max:2048'],
]);

if ($request->hasFile('image')) {
    $data['image_path'] = $request->file('image')->store('posts', 'public');
}
unset($data['image']);

Post::create($data);
```

The example stores the file and saves its path in the database. It does not store the image’s binary contents in the post row. Treat uploaded filenames as untrusted. In local development, files on the `public` disk may require:

```bash
php artisan storage:link
```

Sensitive files should use private storage and controlled access rather than being exposed publicly.

## 8. Authentication and authorization

**Authentication** answers: “Who is this user?” A login system verifies a user’s identity.

**Authorization** answers: “What is this user allowed to do?” For example, may this user edit this specific post?

A route protected by authentication middleware:

```php
Route::get('/dashboard', function () {
    return 'Welcome to your dashboard!';
})->middleware('auth');
```

The `auth` middleware requires a signed-in user. It does not automatically mean that the user is an administrator or the owner of a record.

Use policies, gates, roles, or another clear authorization approach to protect administrative actions. Check permission on the server for every sensitive operation. Hiding an edit button in Blade is not sufficient because a user can send a request directly.

## 9. Laravel UI, Breeze, and starter kits

The presentation demonstrates Laravel UI with Bootstrap and Laravel Breeze with Tailwind as examples of authentication scaffolding. These package-based workflows are version-dependent and may not match the current Laravel release. Check the project’s version and its official documentation before using installation commands.

The core idea is that a starter kit can provide a starting point for authentication pages and flows such as registration and login. Laravel applications can use different starter options, so choose one that matches the project’s Laravel version and frontend needs.

---

# Practical build checklist

Use this order when following the exercise:

1. Confirm PHP, Composer, the database, and the Laravel version.
2. Create the project and review its folders.
3. Configure the local database in `.env`.
4. Create the `Post` model and migration.
5. Define the posts table and run `php artisan migrate`.
6. Add a `PostController` and resource route.
7. Implement `index`, `create`, and `store` first.
8. Create the Blade layout and post views.
9. Validate submitted values and show validation errors.
10. Add `show`, `edit`, `update`, and `destroy`.
11. Add a flash message after successful actions.
12. Add optional image upload with server-side validation.
13. Protect management routes with authentication and record-level authorization.
14. Use `php artisan route:list` to inspect routes and test the application in a browser.

## Common issues to check

- **404 response:** Check the URL and `php artisan route:list`.
- **Database connection error:** Check the local `.env` values and confirm the database exists.
- **View not found:** Check the path under `resources/views` and the view name passed to `view()`.
- **Mass assignment error:** Review the model’s `$fillable` fields and the validated data.
- **419 Page Expired:** Confirm the form includes `@csrf` and uses the web session correctly.
- **Image does not display:** Confirm the file was stored, the `public` storage link exists when needed, and the saved path is correct.
- **Unexpected data loss:** Check whether a destructive migration or `migrate:fresh` command was run.
- **Access control gap:** Remember that authentication and authorization answer different questions.

## Review questions

1. What role does a route play in handling a request?
2. What is the difference between a model, a view, and a controller?
3. What is the difference between a migration and a model?
4. What do Composer and Artisan each do?
5. Why should a web form include `@csrf`?
6. Why does `{{ $value }}` matter when displaying user-provided text?
7. What is the difference between a factory and a seeder?
8. What does `auth` middleware verify, and what does a policy verify?
9. Why should uploaded files be validated on the server?
10. Why should you avoid running `migrate:fresh` on a database with data you need?

## Key takeaways

Laravel organizes an application around clear responsibilities. Routes direct requests, controllers coordinate work, models and Eloquent handle records, migrations describe database structure, and Blade renders pages. Composer manages PHP dependencies, while Artisan provides Laravel commands. Validation, CSRF protection, safe output, file handling, authentication, and authorization all help make the application reliable and secure.

## Official documentation

- [Laravel Documentation](https://laravel.com/docs)
- [Routing](https://laravel.com/docs/routing)
- [Controllers](https://laravel.com/docs/controllers)
- [Blade Templates](https://laravel.com/docs/blade)
- [Migrations](https://laravel.com/docs/migrations)
- [Eloquent ORM](https://laravel.com/docs/eloquent)
- [Validation](https://laravel.com/docs/validation)
- [File Storage](https://laravel.com/docs/filesystem)
- [Authentication](https://laravel.com/docs/authentication)
- [Authorization](https://laravel.com/docs/authorization)
- [Starter Kits](https://laravel.com/starter-kits)

Verify package installation steps against the Laravel version used in the project.
