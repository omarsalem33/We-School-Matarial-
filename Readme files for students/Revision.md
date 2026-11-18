# School Clubs Directory (Revision 21)

## Description

A very small Laravel web application for a school/vocational institute (e.g. WE Applied Technology Schools) that publishes the list of student clubs and their weekly meeting day. Clubs are stored in an array inside a controller, shown on a Blade page, and also exposed as a simple JSON service (with a clean error message) that other apps, such as a mobile app or a school portal, can consume.

## System Architecture / Tech Stack

| Layer        | Technology                                  |
| ------------ | ------------------------------------------- |
| Framework    | Laravel (PHP) using the MVC pattern         |
| Language     | PHP 8.2+                                    |
| Dependencies | Composer                                    |
| View engine  | Blade                                       |
| Data store   | In-memory PHP array inside `ClubController` |
| Dev server   | `php artisan serve` (http://localhost:8000) |

Request flow:

```
Browser -> routes/web.php -> ClubController -> Blade view (HTML) or JSON response
```

## Features

- Default Laravel welcome page at `/`
- HTML page listing all clubs at `/clubs`
- JSON service for all clubs at `/api/clubs`
- JSON service for one club at `/api/clubs/{id}`
- Clean 404 JSON error: `{"error": "Club not found"}`

## Setup Instructions

```cmd
composer -V
composer create-project laravel/laravel exam21
cd exam21
code .
php artisan make:controller ClubController
php artisan serve
php artisan route:list
```

Run `php artisan serve` in one terminal and leave it running. Use a second terminal for other commands such as `php artisan route:list`.

## Project Requirements (School Context)

| #   | Requirement                                              | Deliverable                                               |
| --- | -------------------------------------------------------- | --------------------------------------------------------- |
| 1   | Verify Composer and create the project                   | `exam21` folder                                           |
| 2   | Run the app and show the welcome page                    | http://localhost:8000                                     |
| 3   | `ClubController` with private `getClubs()` and `index()` | `app/Http/Controllers/ClubController.php`                 |
| 4   | `/clubs` route and Blade view with `@foreach`            | `routes/web.php`, `resources/views/clubs/index.blade.php` |
| 5   | `api()` and `/api/clubs`                                 | JSON of all clubs                                         |
| 6   | `apiShow($id)` and `/api/clubs/{id}`                     | JSON of one club                                          |
| 7   | 404 error handling                                       | `{"error":"Club not found"}`                              |
| 8   | Test in browser and check routes                         | `php artisan route:list`                                  |

## Data Model

| id  | name     | day     |
| --- | -------- | ------- |
| 1   | Robotics | Sunday  |
| 2   | Chess    | Monday  |
| 3   | Drama    | Tuesday |

---

# Revision Notes

## User Stories

- As a student, I want to see all clubs and their days on one page so I can choose a club to join.
- As a developer of the school portal, I want a JSON endpoint for all clubs so I can display them in another app.
- As a developer, I want to fetch one club by id and get a clear 404 error if it does not exist.

## Step 1: Check Composer and create the project

```cmd
composer -V
composer create-project laravel/laravel exam21
```

`composer -V` prints the installed version, which proves Composer works. `create-project` downloads the Laravel skeleton and its dependencies into `exam21`.

## Step 2: Open in VS Code and run

```cmd
cd exam21
code .
php artisan serve
```

Open **http://localhost:8000** and show the Laravel welcome page to the examiner.

## Step 3: Create the controller

```cmd
php artisan make:controller ClubController
```

Replace the contents of `app/Http/Controllers/ClubController.php` with:

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\JsonResponse;
use Illuminate\View\View;

class ClubController extends Controller
{
    /**
     * Data source: the clubs array (key = club id).
     * Private, so only this controller can read it.
     */
    private function getClubs(): array
    {
        return [
            1 => ['name' => 'Robotics', 'day' => 'Sunday'],
            2 => ['name' => 'Chess',    'day' => 'Monday'],
            3 => ['name' => 'Drama',    'day' => 'Tuesday'],
        ];
    }

    /**
     * Show the clubs in the Blade page (/clubs).
     */
    public function index(): View
    {
        $clubs = $this->getClubs();

        return view('clubs.index', compact('clubs'));
    }

    /**
     * JSON service: all clubs (/api/clubs).
     * Laravel automatically converts a returned array to JSON.
     */
    public function api(): array
    {
        return $this->getClubs();
    }

    /**
     * JSON service: one club (/api/clubs/{id}).
     * Returns a 404 JSON error if the id does not exist.
     */
    public function apiShow($id): JsonResponse
    {
            $clubs = collect($this->getClubs());
            if (!$clubs->has($id)) {
                return response()->json(['error' => 'Club not found'], 404);
            }

            return response()->json($clubs->get($id));
    }
}
```

**Explanation:** `getClubs()` is the single source of truth, and its keys are the club ids. `index()` uses `compact('clubs')` to create `['clubs' => $clubs]` for the view. `apiShow()` checks the key with `array_key_exists()` before reading it, so an unknown id never causes an "undefined key" error.

## Step 4: Routes and Blade view

**`routes/web.php`**

```php
<?php

use App\Http\Controllers\ClubController;
use Illuminate\Support\Facades\Route;

// Default Laravel welcome page
Route::get('/', function () {
    return view('welcome');
});

// HTML page: list of clubs
Route::get('/clubs', [ClubController::class, 'index']);

// JSON service: all clubs
Route::get('/api/clubs', [ClubController::class, 'api']);

// JSON service: one club by id
Route::get('/api/clubs/{id}', [ClubController::class, 'apiShow']);
```

**`resources/views/clubs/index.blade.php`** (create the `clubs` folder first)

```blade
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>School Clubs</title>
</head>
<body>
    <h1>School Clubs</h1>

    <ul>
        {{-- $id is the array key, $club holds name and day --}}
        @foreach ($clubs as $id => $club)
            <li>{{ $club['name'] }} - {{ $club['day'] }}</li>
        @endforeach
    </ul>
</body>
</html>
```

**Explanation:** `@foreach` loops over the array sent by `compact()`. `{{ }}` prints values with HTML escaping, which protects against XSS.

Expected page at **http://localhost:8000/clubs**:

```
School Clubs
- Robotics - Sunday
- Chess - Monday
- Drama - Tuesday
```

## Step 5: JSON service for all clubs

Open **http://localhost:8000/api/clubs**. Expected JSON:

```json
{
  "1": { "name": "Robotics", "day": "Sunday" },
  "2": { "name": "Chess", "day": "Monday" },
  "3": { "name": "Drama", "day": "Tuesday" }
}
```

Because the keys start at 1 and not 0, PHP encodes the array as a JSON object, so the ids stay visible as keys.

## Step 6: JSON service for one club

Open **http://localhost:8000/api/clubs/2**. Expected JSON:

```json
{ "name": "Chess", "day": "Monday" }
```

## Step 7: Error handling

```php
if (!array_key_exists($id, $clubs)) {
    return response()->json(['error' => 'Club not found'], 404);
}
```

Open **http://localhost:8000/api/clubs/99**. Expected JSON with HTTP status 404:

```json
{ "error": "Club not found" }
```

## Step 8: Test and check routes

| URL             | Expected result                              |
| --------------- | -------------------------------------------- |
| `/`             | Laravel welcome page                         |
| `/clubs`        | HTML list of 3 clubs                         |
| `/api/clubs`    | JSON of all clubs                            |
| `/api/clubs/2`  | `{"name":"Chess","day":"Monday"}`            |
| `/api/clubs/99` | `{"error":"Club not found"}` with status 404 |

In a second terminal inside `exam21`, run:

```cmd
php artisan route:list
```

Your project routes should appear like this (extra framework routes such as `storage/{path}` and `up` are normal):

```
GET|HEAD   /
GET|HEAD   clubs                ClubController@index
GET|HEAD   api/clubs            ClubController@api
GET|HEAD   api/clubs/{id}       ClubController@apiShow
```

## Exam Tips

- If a page shows a 404 or a blank error, check that `use App\Http\Controllers\ClubController;` is at the top of `routes/web.php`.
- The view path must be `resources/views/clubs/index.blade.php` (folder `clubs`, file `index.blade.php`), because `view('clubs.index')` maps the dot to a folder separator.
- Keep `php artisan serve` running in one terminal and use a second terminal for other commands.
- Always show the examiner both the success case (`/api/clubs/2`) and the error case (`/api/clubs/99`).
