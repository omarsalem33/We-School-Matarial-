# Lesson 2: Using Composer and Artisan

> **Teacher's source note:** The Student's Guide labels this lesson *Using Composer and Artisan* and includes those tools in its outcomes. Its lesson pages and Workshop 2 teach routing, controllers, Blade views, route parameters, and middleware. This guide follows those actual lesson pages and Workshop 2. Composer is explained only at the introductory level supported by the lesson outcomes and Workshop 1; the PDFs do not give a package-installation exercise for Lesson 2.

## Lesson at a glance

- **Audience:** Third Grade Secondary, Web Development and Programming
- **Suggested time:** 120 minutes
- **Prerequisite:** The `laravel-first-app` project from Workshop 1 runs with `php artisan serve`.
- **Main idea:** A route connects a URL to a response; a controller organizes application logic; a Blade view displays dynamic data; middleware checks a request before it reaches a route.
- **Source scope:** Unit 21 Laravel Student's Guide, Lesson 2 (pages 17-26); Practical Guide, Workshop 2 (pages 12-18). No outside material is added.

## Learning outcomes

By the end of the lesson, students can:

1. Explain what Composer does in a PHP project and what Artisan does in Laravel.
2. Write a basic web route and connect a route to a controller.
3. Create a Blade view and pass data to it from a controller.
4. Explain required and optional route parameters and recognize a route group prefix.
5. Describe middleware as a request filter and explain the purpose of `auth` middleware.
6. Combine routes, a controller, Blade views, and route parameters in the Mini Notes Viewer.

## Teacher's 120-minute plan

| Time | Teaching step | Teacher and student actions |
|---|---|---|
| 0-10 min | Retrieval warm-up | Ask students to name the project folder, the command that starts the local server, and the file that holds web routes. |
| 10-20 min | Composer and Artisan | Clarify Composer's dependency-management role and Artisan's Laravel command-line role. Review `make:controller` and `route:list`. |
| 20-38 min | Routing | Model the `/hello` route. Students predict the response before visiting the URL. |
| 38-55 min | Controllers | Generate `HelloController`, add `greet()`, connect `/greet`, and compare route-file logic with controller logic. |
| 55-75 min | Blade and data | Build `welcome.blade.php`; trace `$name` from controller array to `{{ $name }}`. |
| 75-88 min | Route parameters | Demonstrate `/users/{id}`, optional `{id?}`, and the `/admin` prefix. Keep grouping introductory as the guide directs. |
| 88-98 min | Middleware | Explain the gatekeeper model and inspect the `auth` example. Do not expect it to work unless authentication has already been set up. |
| 98-115 min | Practical activity | Students build the Mini Notes Viewer with the supplied array data and test normal and missing note IDs. |
| 115-120 min | Exit ticket | Students answer the three recap prompts from memory. |

## 1. Composer and Artisan: two different tools

### Composer

Composer manages dependencies in PHP projects. A dependency is a package or library that a project needs. The lesson outcomes ask students to know Composer's purpose and package management role. Workshop 1 uses Composer to check its installation and install the Laravel installer:

```bash
composer -v
composer global require laravel/installer
```

The Lesson 2 practical work does not add or install another Composer package. Keep the distinction clear: Composer manages PHP dependencies; Laravel's Artisan CLI runs Laravel commands and generates application components.

### Artisan

Artisan is Laravel's command-line tool. For this lesson, students use it to create a controller and inspect routes:

```bash
php artisan make:controller HelloController
php artisan route:list
```

The project server is started with:

```bash
php artisan serve
```

**Ask:** Which tool manages PHP packages? Which tool generates a Laravel controller?

## 2. Routing: connect a URL to a response

Routing tells Laravel what to do when a visitor requests a URL. Web routes are written in `routes/web.php`. A route includes an HTTP method, a route URI, and the function or controller action that responds.

```php
Route::get('/hello', function () {
    return 'Hello, Laravel!';
});
```

Here, `GET` is the HTTP method, `/hello` is the URI, and the closure returns the response. With the development server running, visit `http://localhost:8000/hello`.

**Think-pair-share:** What would change if the route path were `/about`? What URL would the visitor open?

## 3. Controllers: keep application logic organized

A controller is a PHP class that handles application logic and returns a response. Controllers help keep logic out of the route file.

Create the controller:

```bash
php artisan make:controller HelloController
```

Add a method inside `HelloController`:

```php
public function greet(){
    return 'Hello from the controller!';
}
```

Connect the URL to the controller method in `routes/web.php`:

```php
use App\Http\Controllers\HelloController;

Route::get('/greet', [HelloController::class, 'greet']);
```

Visit `http://localhost:8000/greet`. The route points to the `greet` method; the controller method returns the message.

**Quick check:** If the route says `'greet'`, what method name must exist in the controller?

## 4. Blade views: show dynamic content

Blade is Laravel's templating engine. Blade files are stored in `resources/views/` and use the `.blade.php` extension. The `{{ }}` syntax safely outputs a variable.

Create `resources/views/welcome.blade.php`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome</title>
</head>
<body>
    <h1>Hello, {{ $name }}!</h1>
</body>
</html>
```

In `HelloController.php`, pass the data to the view:

```php
public function showWelcome(){
    return view('welcome', ['name' => 'Student']);
}
```

Add the route:

```php
Route::get('/welcome', [HelloController::class, 'showWelcome']);
```

The view name `welcome` refers to `welcome.blade.php`. The array key `name` supplies the `$name` variable used in the view. Change the value to `'Ahmed'` and the page will display “Hello, Ahmed!”.

**Board trace:** `controller -> view('welcome', ['name' => 'Ahmed']) -> {{ $name }} -> Hello, Ahmed!`

## 5. Route parameters and grouping

A route parameter makes part of the URL dynamic. Parameters are written in curly braces:

```php
Route::get('/users/{id}', [UserController::class, 'show']);
```

Visiting `/users/1` passes `1` as the `id` value to the `show` method. The method must accept a matching parameter, such as `show($id)`.

Add `?` to make a parameter optional:

```php
Route::get('/users/{id?}', [UserController::class, 'show']);
```

This route can match both `/users/1` and `/users`. The lesson notes that the method should be written to accept an optional value when using this pattern.

Related routes can be grouped under a prefix:

```php
Route::group(['prefix' => 'admin'], function () {
    Route::get('/', [AdminController::class, 'index']);
    Route::get('/users', [AdminController::class, 'users']);
});
```

The group adds `/admin` before the paths inside it. The guide says route grouping will be explained in more detail in a later lesson, so treat this as recognition and a simple example.

**Ask:** Is `{id}` a fixed value or a placeholder? What does the `?` change?

## 6. Middleware: check a request before the route

Middleware filters, checks, or modifies a request before it reaches a route or controller. Think of it as a gatekeeper. The `auth` middleware checks whether a user is logged in:

```php
Route::get('/dashboard', function () {
    return 'Welcome to your dashboard!';
})->middleware('auth');
```

This route is intended for a logged-in user. The Practical Guide says authentication must already be set up (for example with Breeze or Jetstream), and it explicitly asks students to read this example rather than test it live during this workshop. Without that setup, a login route error or redirect is expected.

**Scenario:** A student is logged out and opens `/dashboard`. Which part of the route checks their login state?

## Interactive classroom questions

Use these as pauses during the explanation. Ask students to answer before showing the suggested response.

1. **URL prediction:** If `/hello` returns `Hello, Laravel!`, what will `/hello` show in the browser?
2. **Find the file:** Where do web routes go? **Expected:** `routes/web.php`.
3. **Tool match:** Which command generates a controller? **Expected:** `php artisan make:controller ...`.
4. **Trace the name:** If the controller passes `['name' => 'Mona']`, what does `{{ $name }}` display? **Expected:** `Mona`.
5. **Spot the mismatch:** What is wrong if a route calls `greet` but the controller method is named `greeting`? **Expected:** The names do not match.
6. **Parameter check:** What value is passed by `/users/7` to `{id}`? **Expected:** `7`.
7. **Optional or required:** What allows `/users` to match when no ID is supplied? **Expected:** `{id?}`.
8. **Gatekeeper:** When does middleware run? **Expected:** Before the request reaches the route or controller.

## Practical activity: Mini Notes Viewer

### Goal

Combine a controller, routes, Blade views, and route parameters. This activity uses plain PHP arrays; the workshop says there is no database yet.

### Build steps

1. Generate a controller:

   ```bash
   php artisan make:controller NoteController
   ```

2. Add `index()` and `show($id)` methods to `NoteController` using the three sample notes below. `index()` passes a list to `notes.index`; `show($id)` looks up one note, calls `abort(404)` if it is missing, and passes the selected note to `notes.show`.

   ```php
   public function index(){
       $notes = [
           ['id' => 1, 'title' => 'Intro to Variables', 'topic' => 'PHP Basics'],
           ['id' => 2, 'title' => 'Understanding Loops', 'topic' => 'PHP Basics'],
           ['id' => 3, 'title' => 'What is Routing?', 'topic' => 'Laravel'],
       ];
       return view('notes.index', compact('notes'));
   }

   public function show($id){
       $notes = [
           1 => ['title' => 'Intro to Variables', 'topic' => 'PHP Basics', 'body' => 'Variables store data using $ syntax.'],
           2 => ['title' => 'Understanding Loops', 'topic' => 'PHP Basics', 'body' => 'Loops repeat a block of code.'],
           3 => ['title' => 'What is Routing?', 'topic' => 'Laravel', 'body' => 'Routing connects a URL to a controller.'],
       ];
       $note = $notes[$id] ?? null;
       if (!$note) {
           abort(404);
       }
       return view('notes.show', compact('note'));
   }
   ```

3. In `routes/web.php`, import the controller and add both routes:

   ```php
   use App\Http\Controllers\NoteController;

   Route::get('/notes', [NoteController::class, 'index']);
   Route::get('/notes/{id}', [NoteController::class, 'show']);
   ```

4. Create `resources/views/notes/index.blade.php`:

   ```html
   <!DOCTYPE html>
   <html>
   <head><title>Study Notes</title></head>
   <body>
       <h1>My Study Notes</h1>
       <ul>
           @foreach ($notes as $note)
               <li><a href="/notes/{{ $note['id'] }}">{{ $note['title'] }}</a> — {{ $note['topic'] }}</li>
           @endforeach
       </ul>
   </body>
   </html>
   ```

5. Create `resources/views/notes/show.blade.php`:

   ```html
   <!DOCTYPE html>
   <html>
   <head><title>{{ $note['title'] }}</title></head>
   <body>
       <h1>{{ $note['title'] }}</h1>
       <p><strong>Topic:</strong> {{ $note['topic'] }}</p>
       <p>{{ $note['body'] }}</p>
       <a href="/notes">Back to all notes</a>
   </body>
   </html>
   ```

6. Visit `http://localhost:8000/notes`, open a note, and then visit `/notes/99` to see the 404 fallback.

### Student check-off

- [ ] `/notes` displays all three notes.
- [ ] Selecting a note opens its detail page.
- [ ] The URL value is used as the note ID.
- [ ] An unknown ID displays a 404 response.
- [ ] The data comes from arrays; no database is used.

## Troubleshooting guide

| Symptom | Likely cause | What to check |
|---|---|---|
| `/hello` shows 404 | Route not saved or server not running | Save `routes/web.php`; confirm `php artisan serve` is still running. |
| Blank page or PHP error on a closure route | Missing semicolon or unmatched braces | Compare the route with the example; close the `Route::get()` call with `;`. |
| Welcome page appears instead | The browser is at `/` rather than `/hello` | Check the full address bar path. |
| `HelloController` class not found | Missing controller import | Add `use App\Http\Controllers\HelloController;` at the top of `routes/web.php`. |
| `/greet` is 404 | Route missing or route cache stale | Check with `php artisan route:list`; the guide also lists `php artisan route:clear`. |
| Undefined method `greet()` | Route method name and controller method differ | Match spelling and capitalization in both places. |
| View `[welcome]` not found | View file has wrong name, extension, or location | Confirm `resources/views/welcome.blade.php`. |
| Undefined variable `$name` | Controller did not pass matching data | Match the array key `name` to `$name` in the view. |
| Blade `{{ }}` appears as text | File is `.php`, not `.blade.php` | Use the `.blade.php` extension. |
| Too few arguments to `show()` | Controller method does not accept the route parameter | Add a matching `$id` parameter to the method. |
| `/users` returns 404 | `{id}` is required | Use `{id?}` only when the parameter should be optional. |
| `Route [login] not defined` for `/dashboard` | Authentication scaffolding has not been installed | Expected in this workshop; auth setup is covered later. |
| Dashboard redirects instead of displaying | `auth` middleware is checking a user without a login page set up | Expected until authentication is added. |

## Five-minute recap

Complete these aloud or on a small exit slip:

1. **Composer** manages PHP project dependencies. **Artisan** runs Laravel commands and can generate components.
2. A **route** connects a URL and HTTP method to a response or controller action.
3. A **controller** organizes request logic; a **Blade view** displays HTML and dynamic data.
4. `{id}` is a route parameter; `{id?}` makes it optional; a group prefix is added to the routes inside that group.
5. **Middleware** checks or modifies a request before the route or controller. `auth` checks whether the user is logged in.

## 20 key questions

Try to answer without looking at the answer key first.

1. What does Composer manage in a PHP project?
2. What is Artisan?
3. Which command creates `HelloController`?
4. In which file are the lesson's web routes defined?
5. What does routing connect?
6. In `Route::get('/hello', function () { return 'Hello, Laravel!'; });`, what response is returned?
7. What is a controller?
8. Why are controllers useful?
9. What URL calls the `greet` method in the example?
10. What is Blade?
11. Where should `welcome.blade.php` be saved?
12. What does `{{ $name }}` do in a Blade view?
13. How does a controller pass the value `Ahmed` to the `welcome` view?
14. What is a route parameter? Give the parameter in `/users/{id}`.
15. What value is passed to `show` when the visitor opens `/users/1`?
16. How do you make a route parameter optional?
17. What does the `admin` prefix do in the route group example?
18. What does middleware do, and when does it run?
19. What does the `auth` middleware check?
20. In the Mini Notes Viewer, what should happen at `/notes/99`?

### Answer key

1. It manages PHP project dependencies/packages.
2. Laravel's command-line tool for running commands and generating components.
3. `php artisan make:controller HelloController`.
4. `routes/web.php`.
5. A URL to a function or controller/action that returns a response.
6. The text `Hello, Laravel!`.
7. A PHP class that handles application logic and returns responses.
8. They organize code by moving logic out of the route file.
9. `http://localhost:8000/greet`.
10. Laravel's templating engine for dynamic HTML views.
11. `resources/views/welcome.blade.php`.
12. It safely outputs the value of the `$name` variable.
13. `return view('welcome', ['name' => 'Ahmed']);`.
14. A changing value in the URL; the parameter is `id`.
15. `1`.
16. Add `?` after the parameter name: `{id?}`.
17. It prepends `/admin` to the paths in the group.
18. It filters/checks/modifies a request before it reaches a route or controller.
19. Whether the user is logged in.
20. The controller calls `abort(404)`, so Laravel returns a 404 response.

## Source boundary

Content and code examples follow the two supplied Unit 21 PDFs. Teaching prompts, timing, and question wording are classroom scaffolds around those examples. The Lesson 2 PDFs do not provide a Composer package-install procedure, so none is invented here. Middleware authentication setup is intentionally not part of the live practice because the Practical Guide says to read that example until authentication is installed in a later lesson.
