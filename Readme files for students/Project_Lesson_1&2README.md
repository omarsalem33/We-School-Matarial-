# Student Study Hub

## Laravel Lessons 1 and 2 - Hands-on Training Project

Build a small study-notes website for a class. The project begins with a Laravel app and grows one feature at a time. Students will trace each browser URL through a route and controller, then see how a Blade view turns data into a page.

> **Curriculum boundary:** This project follows Lesson 1, *Introduction to Laravel and Installation*, and Lesson 2, *Using Composer and Artisan*, as presented in the Student's Guide and Practical Guide PDFs. The lessons' practical content covers Laravel and MVC, project setup and folders, Artisan, web routes, controllers, Blade, data passed to views, route parameters (including optional parameters), route grouping, and middleware basics. Notes stay in PHP arrays: this project does not use a database, Eloquent, migrations, authentication setup, or APIs.

## Project scenario

Your class wants one simple place to review short programming notes. A student can open the notes list and click a note to read its details. The class teacher can also see a separate set of teacher notes under a URL prefix.

The notes are sample data written in the controller. The goal is to understand how the pieces connect, not to build a complete production website.

## Learning goals

By the end, you should be able to:

- Explain what Laravel is, why a framework is useful, and what MVC means.
- Identify the jobs of PHP, Composer, the Laravel Installer, Artisan, the local development server, and the main project folders used here.
- Start a Laravel project, generate a controller, and inspect registered routes with Artisan.
- Connect a URL to a direct response or a controller method in `routes/web.php`.
- Pass data from a controller to a Blade view and display it with `{{ }}` and `@foreach`.
- Use a route parameter to select one note, explain an optional parameter, and recognize a route group prefix.
- Describe middleware as a request filter and explain why the `auth` example cannot be tested in this project.

## What you will build

| URL | What it demonstrates | Expected result |
|---|---|---|
| `/hello` | A basic GET route with a closure | A short greeting |
| `/greet` | A route connected to a controller | A greeting returned by `StudyController` |
| `/notes` | Controller data passed to a Blade view | A list of study notes with links |
| `/notes/1` | A required route parameter | The detail page for note 1 |
| `/notes/999` | A missing item check | A Laravel 404 page |
| `/welcome` | A named value passed to Blade | `Hello, Student!` (or your chosen name) |
| `/teacher` and `/teacher/notes` | A route group with a prefix | Two teacher area responses |

## Before you begin

Use the setup tools listed in Lesson 1's practical guide: PHP, Composer, Node.js/npm, a code editor such as VS Code, and a terminal. The guide recommends PHP 8.1+ and uses MySQL in its setup prompts; this training feature itself does not read or write a database. Git is useful for saving your work. Follow your teacher's setup instructions if your classroom uses XAMPP or Laragon.

Check the tools from a terminal:

```bash
php -v
composer -v
node -v
npm -v
git -v
```

If a command is not recognized, stop and ask the teacher to help check the installation or PATH before continuing.

Composer manages Laravel's PHP dependencies (the packages the project needs). In this project, use it to install the Laravel Installer as shown below; students do not need to add a separate package. This keeps the work focused on the two lessons' Composer and Artisan ideas.

## Project map

```text
student-study-hub/
├── app/
│   └── Http/Controllers/
│       └── StudyController.php     # Request handling and sample notes
├── config/                         # Application settings
├── public/                         # Web-accessible entry point and assets
├── resources/
│   └── views/
│       ├── notes/
│       │   ├── index.blade.php     # Notes list
│       │   └── show.blade.php      # One note
│       └── welcome.blade.php       # Dynamic welcome page
├── routes/
│   └── web.php                     # Browser URLs and their actions
├── .env                            # Local environment settings; keep private
└── artisan                         # Laravel command-line entry point
```

Lesson 1 also introduces `app/` for application code, `routes/` for URLs, `resources/views/` for Blade templates, `public/` for public files, and `config/` for settings. Find each one in your own project before editing. The diagram shows only the files this project asks you to create or change; a Laravel project contains other generated files too.

## Milestone 1 - Create and run the Laravel project

### 1. Create the project

If the Laravel Installer is not already installed, install it once:

```bash
composer global require laravel/installer
```

Create the project:

```bash
laravel new student-study-hub
```

When the installer asks questions, use the classroom choices. The Practical Guide suggests **None** for starter kit, **Pest** for testing framework, **MySQL** for database, and running the default migrations and npm install/build. Do not add authentication or database features to the project tasks below.

Enter the project and start the local server:

```bash
cd student-study-hub
php artisan serve
```

Artisan can also list the commands available in this Laravel project:

```bash
php artisan list
```

Open the local address printed by Artisan (usually `http://localhost:8000`). Leave the terminal running while you use the site.

### Checkpoint

- You can see the Laravel welcome page in your browser.
- `php artisan list` displays available Artisan commands; `php artisan serve` starts the local development server.
- In your editor, you can point to `app/`, `routes/`, `resources/views/`, `public/`, and `config/`.
- In one sentence, explain what Composer does and what Artisan does. They have different jobs: Composer manages PHP packages; Artisan runs Laravel project commands.

**Think before moving on:** Which tool created the project? Which command started the local server? Where is the welcome page's Blade file?

## Milestone 2 - Make a first route and inspect Artisan

Open `routes/web.php`. Add this route below the existing route definitions:

```php
Route::get('/hello', function () {
    return 'Hello, Study Hub!';
});
```

Visit `http://localhost:8000/hello`. You should see `Hello, Study Hub!`.

Now inspect the project routes from a second terminal opened in the project folder:

```bash
php artisan route:list
```

Find `/hello` in the output. The route file connects a URL to an action; the closure above returns a response directly.

### Checkpoint

Without looking at the code, tell a partner what `Route::get`, `/hello`, and the function each contribute. Then change the message to a phrase of your own and reload the page.

**Expected output:** Your new phrase appears at `/hello`, and `/hello` is listed by Artisan.

## Milestone 3 - Move request logic into a controller

Generate a controller with Artisan:

```bash
php artisan make:controller StudyController
```

Open `app/Http/Controllers/StudyController.php`. Inside the class, add this method:

```php
public function greet()
{
    return 'Welcome to the Student Study Hub!';
}
```

At the top of `routes/web.php`, add the import with the other `use` statements:

```php
use App\Http\Controllers\StudyController;
```

Then add a route:

```php
Route::get('/greet', [StudyController::class, 'greet']);
```

Visit `http://localhost:8000/greet`.

### Checkpoint

Trace the request aloud: browser URL `/greet` -> route in `routes/web.php` -> `greet()` in `StudyController` -> text response in the browser. Explain why this keeps the route file simpler than putting every action there.

**Expected output:** `/greet` displays the controller's greeting. `php artisan route:list` shows the route.

## Milestone 4 - Show a list of notes with Blade

### 1. Add a list action and sample data

In `StudyController`, add this method inside the class:

```php
public function index()
{
    $notes = [
        ['id' => 1, 'title' => 'Variables', 'topic' => 'PHP Basics'],
        ['id' => 2, 'title' => 'Loops', 'topic' => 'PHP Basics'],
        ['id' => 3, 'title' => 'Routes', 'topic' => 'Laravel'],
    ];

    return view('notes.index', compact('notes'));
}
```

The array is temporary sample data held in PHP. `compact('notes')` passes the `$notes` variable to the view.

### 2. Create the Blade file

Create the folder `resources/views/notes/`, then create `index.blade.php` inside it:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Study Notes</title>
</head>
<body>
    <h1>My Study Notes</h1>
    <ul>
        @foreach ($notes as $note)
            <li>
                <a href="/notes/{{ $note['id'] }}">{{ $note['title'] }}</a>
                - {{ $note['topic'] }}
            </li>
        @endforeach
    </ul>
</body>
</html>
```

`view('notes.index', ...)` points to `resources/views/notes/index.blade.php`. Blade's `{{ }}` displays values, and `@foreach` repeats the list item for each note.

### 3. Connect the list URL

In `routes/web.php`, add:

```php
Route::get('/notes', [StudyController::class, 'index']);
```

Visit `http://localhost:8000/notes`.

### Checkpoint

- The page shows all three titles and topics.
- Point to the controller variable, the `view()` call, and the Blade loop.
- Add a fourth note yourself. Refresh and explain why it appears without writing a fourth `<li>` by hand.

**Expected output:** A heading, followed by three or more linked note titles with their topics.

## Milestone 5 - Open one note with a route parameter

Add a detail method to `StudyController`:

```php
public function show($id)
{
    $notes = [
        1 => [
            'title' => 'Variables',
            'topic' => 'PHP Basics',
            'body' => 'Variables store values that a program can use.',
        ],
        2 => [
            'title' => 'Loops',
            'topic' => 'PHP Basics',
            'body' => 'Loops repeat a block of instructions.',
        ],
        3 => [
            'title' => 'Routes',
            'topic' => 'Laravel',
            'body' => 'A route connects a URL to an action.',
        ],
    ];

    $note = $notes[$id] ?? null;

    if (!$note) {
        abort(404);
    }

    return view('notes.show', compact('note'));
}
```

Create `resources/views/notes/show.blade.php`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>{{ $note['title'] }}</title>
</head>
<body>
    <h1>{{ $note['title'] }}</h1>
    <p><strong>Topic:</strong> {{ $note['topic'] }}</p>
    <p>{{ $note['body'] }}</p>
    <p><a href="/notes">Back to all notes</a></p>
</body>
</html>
```

Add the parameter route **after** `/notes` in `routes/web.php`:

```php
Route::get('/notes/{id}', [StudyController::class, 'show']);
```

Click a note from the list, or visit `/notes/1`. Laravel takes the value in `{id}` and passes it to `show($id)`. The `?? null` check and `abort(404)` give a not-found response for an ID that is not in the sample array.

### Checkpoint

- `/notes/1`, `/notes/2`, and `/notes/3` show different note details.
- `/notes/999` gives a 404 response.
- Explain how `{id}` in the URL becomes `$id` in the controller.
- Change the title for note 2 in the array. Reload `/notes/2` and `/notes` to find the two places where the sample data is written. Why must the list and detail data agree?

**Expected output:** Each valid ID shows one detail page; an unknown ID shows a not-found page.

## Milestone 6 - Pass a named value to a view

Add this method to `StudyController`:

```php
public function welcome()
{
    return view('welcome', ['name' => 'Student']);
}
```

Create `resources/views/welcome.blade.php`:

```html
<!DOCTYPE html>
<html>
<head>
    <title>Welcome</title>
</head>
<body>
    <h1>Hello, {{ $name }}!</h1>
    <p>Choose a note from the <a href="/notes">study notes</a>.</p>
</body>
</html>
```

Add the route:

```php
Route::get('/welcome', [StudyController::class, 'welcome']);
```

Visit `/welcome`. Change `Student` to your first name in the controller, then reload.

### Checkpoint

Match the data key `'name'` to the Blade variable `$name`. What would happen if one were spelled differently? Why does the filename need to end in `.blade.php`?

**Expected output:** The page greets the name provided by the controller.

## Milestone 7 - Optional parameters, route groups, and middleware

These are small learning demonstrations from Lesson 2. Keep them separate from the main notes feature and discuss each one before writing it.

### A. Optional route parameter (teacher demonstration)

The guide shows that `{id?}` makes a parameter optional. A method that receives an optional value needs a default value too:

```php
public function lookup($id = null)
{
    return $id ? "You requested note {$id}." : 'No note ID was provided.';
}
```

Add the method to `StudyController`, then add this route:

```php
Route::get('/lookup/{id?}', [StudyController::class, 'lookup']);
```

Try `/lookup` and `/lookup/2`. Observe how the same route can match with or without the value. This is a route-parameter exercise, not database lookup.

### B. Group related URLs under a prefix

Add this group to `routes/web.php`:

```php
Route::prefix('teacher')->group(function () {
    Route::get('/', function () {
        return 'Teacher home';
    });

    Route::get('/notes', function () {
        return 'Teacher notes area';
    });
});
```

Try `/teacher` and `/teacher/notes`. The prefix is added to each URL in the group. The Student's Guide introduces route grouping as an organizing idea for later lessons; this simple example does not add login or special access.

### C. Middleware: understand the request filter

Middleware is a filter that can check or modify a request before it reaches its route or controller. The lesson uses `auth` as an example:

```php
Route::get('/dashboard', function () {
    return 'Welcome to your dashboard!';
})->middleware('auth');
```

**Do not add this route to the runnable project.** The Practical Guide says authentication has not been installed yet, and the Student's Guide says the auth middleware works only after authentication is set up. Be ready to explain what the filter is intended to check and why visiting this example now may lead to a missing login route.

### Checkpoint

- State what makes a parameter optional in the URL and in the method.
- Identify the shared prefix in the teacher routes.
- Explain when middleware runs and what the `auth` example checks.

## Final project checklist

- [ ] Laravel project starts with `php artisan serve`.
- [ ] I can describe Laravel and give one reason to use a framework.
- [ ] I can explain Model, View, and Controller in simple words, and identify which parts of this project are a controller and a view.
- [ ] I know Composer manages PHP dependencies and Artisan provides Laravel commands.
- [ ] I can find `app/`, `routes/`, `resources/views/`, `public/`, `config/`, `.env`, and `artisan`.
- [ ] `/hello`, `/greet`, `/notes`, `/notes/1`, `/welcome`, `/lookup`, `/lookup/2`, `/teacher`, and `/teacher/notes` behave as expected.
- [ ] I can use `php artisan route:list` to inspect registered routes.
- [ ] I can explain why middleware is only discussed, not activated, in this project.
- [ ] I added at least one original note and can trace its data from controller to browser.

## Troubleshooting guide

| Symptom | Check |
|---|---|
| `laravel` command is not found | Check the Composer global bin PATH as described in the Practical Guide, then restart the terminal. |
| Project install hangs | Check network and Composer output; the guide notes security software or a slow connection can interfere. Ask the teacher for help with local security settings. |
| `/hello` or another URL gives 404 | Confirm the URL spelling, save `routes/web.php`, keep the server running, and check `php artisan route:list`. |
| Controller class is not found | Confirm the `use App\Http\Controllers\StudyController;` import and the controller's namespace. |
| Controller method is not found | Check that the method name in the route matches the method in `StudyController`. |
| View not found | Check exact spelling and path under `resources/views/`; `view('notes.index')` maps to `notes/index.blade.php`. |
| Undefined variable in Blade | Check that the controller passed the matching key, such as `'name'` for `$name`, or `compact('notes')` for `$notes`. |
| Blade tags show as text | Check that the file ends in `.blade.php`, not only `.php`. |
| `/notes` is empty | Check that `$notes` has items and the loop uses the same variable name. |
| Server page cannot load | Make sure `php artisan serve` is still running. If port 8000 is busy, use the guide's `php artisan serve --port=8001` and open that port. |
| Database connection/migration error during initial setup | The setup guide's MySQL choice may require local database configuration. Ask the teacher for help. The notes feature itself does not use the database. |

When asking for help, report the URL, the command or action you tried, and the exact error text. Do not paste secrets from `.env` into a public chat or repository.

## Reflection questions

Write short answers in your own words:

1. What problem does Laravel help a PHP developer solve?
2. In this project, what is the job of a route? What is the job of the controller?
3. How does the controller give the notes to the Blade view?
4. What would be harder if the list of notes were written by hand as repeated HTML?
5. What does `/notes/{id}` allow the website to do? What happens when the ID is unknown?
6. Why do related teacher URLs share a prefix?
7. At what point does middleware inspect a request? What is the `auth` example checking?
8. Which command helped you see the route table? Which command generated your controller?
9. Name one error you met and the evidence that helped you solve it.
10. If you had to teach this project to another student, how would you draw the path from URL to page?

## Lesson-scope challenges

Complete these only after the main checklist. Do not add databases, CRUD, APIs, authentication packages, or other later-lesson features.

1. **Create a new note:** Add one entry to both sample arrays, link to it from `/notes`, and show its detail page. Explain why the IDs must match.
2. **Make a topic page:** Add a controller method that passes a topic string to a Blade view, then add a route such as `/topics/laravel`. Show the topic using `{{ }}`.
3. **Add a route and trace it:** Create a route that returns a short direct response. Compare it with a route that calls a controller method. Draw both request paths.
4. **Route map:** Use `php artisan route:list` and make a two-column table of URL and action for the routes you created.
5. **Explain, don't activate:** In a few sentences or a diagram, show where `auth` middleware sits between a request and its route. Keep the example unregistered because authentication is outside this project's setup.

## Assessment rubric (100 points)

| Area | Points | Evidence |
|---|---:|---|
| Setup and Laravel vocabulary | 15 | Project runs; student explains Laravel, Composer, Artisan, MVC, and local server in their own words. |
| Folder and command understanding | 15 | Student locates the lesson folders and uses `make:controller`, `route:list`, and `serve`, explaining each. |
| Routes and controller flow | 20 | `/hello`, `/greet`, and notes routes work; student traces URL -> route -> controller/closure -> response. |
| Blade and data passing | 20 | List, welcome, and detail views display passed data; student explains `view()`, `{{ }}`, and `@foreach`. |
| Parameters, grouping, middleware | 15 | Required and optional parameters and prefix group are demonstrated; middleware behavior and setup limitation are explained accurately. |
| Independent thinking and reflection | 15 | Student adds an original note, completes reflection answers, and describes one debugging step without simply copying. |

**Performance guide:** 90-100: explains and adapts confidently; 75-89: project works with small prompts; 60-74: partial understanding and needs guided practice; below 60: revisit route/controller/view flow together and retry the checkpoints.

## Teacher guidance

### Suggested lesson flow (two 60-minute sessions)

**Session 1:**

1. 10 min - Ask students to predict what happens when a browser requests a URL. Introduce framework, Composer, Artisan, and MVC from Lesson 1.
2. 15 min - Create and run the project; have students locate the five folders before editing.
3. 15 min - Build `/hello` and inspect `route:list`. Ask students to explain each part of `Route::get`.
4. 20 min - Generate `StudyController`, connect `/greet`, and pair-trace the request from browser to response.

**Session 2:**

1. 10 min - Retrieval warm-up: students draw the URL -> route -> controller -> response flow from memory.
2. 20 min - Build the notes list with sample data and Blade. Pause before adding the loop and ask students to predict its output.
3. 15 min - Add the detail route and parameter. Ask why `/notes/999` needs a not-found response.
4. 10 min - Demonstrate the optional parameter and prefix group; students identify the differences in the URLs.
5. 5 min - Discuss middleware as a gatekeeper and why `auth` cannot be tested yet. Collect one exit-ticket answer: “How does data get from PHP into a Blade page?”

### Teaching moves that build understanding

- Ask students to predict the browser output before saving each change, then compare the prediction with the result.
- Use three colored labels for **route**, **controller**, and **view**. Students point to the matching file or code as they trace each request.
- Let pairs alternate roles: one student explains the code while the other asks “where does this value come from?” Switch roles at each checkpoint.
- When a page fails, ask students to inspect the URL, route list, method name, and view path in that order. Encourage them to read the error before changing code.
- Accept student-authored note content. Assess their explanation and tracing, not identical wording.
- Keep `auth` middleware conceptual. Do not score students down for not installing authentication; it is outside the two-lesson project scope.

### Teacher check for understanding

Before marking the project complete, ask each student to explain one note request without reading from the README: what URL is requested, which route matches it, what the controller sends, and what the view displays. Follow up by asking what changes for a missing ID.

## Curriculum sources

This README was prepared from the two provided Unit 21 PDFs only:

- *Unit 21 - Laravel - Student's Guide* - Lessons 1 and 2.
- *Unit21* (Practical Guide) - Workshop 1 setup and Laravel fundamentals; Workshop 2 routing, controllers, Blade, parameters, middleware, and the in-scope static-array practice approach.

The project combines the lesson concepts into one student task. It intentionally stops before the PDFs' later database, Eloquent, API, authentication installation, and advanced routing content.
