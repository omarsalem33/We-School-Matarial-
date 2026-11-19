# Laravel Practical Task --- School Facilities Directory

**Level:** Beginner\
**Estimated time:** 60--90 minutes\
**Technology:** PHP, Composer, Laravel, Blade, JSON\
**Project name:** `exam21`

------------------------------------------------------------------------

## 1. Task Overview

Build a small Laravel website that displays the facilities available at
a school. The facilities are stored in an array inside a controller.

Your application must: - Display all facilities on a Blade page. -
Provide a JSON endpoint that returns all facilities. - Provide a JSON
endpoint that returns one facility by its ID. - Return a clear JSON
error with HTTP status code `404` when the requested ID does not exist.

### Facility data

Use the following data exactly:

    ID Facility       Building
  ---- -------------- ----------
     1 Computer Lab   A
     2 Science Lab    B
     3 Library        C

------------------------------------------------------------------------

## 2. Requirements

### Part A --- Create and run the Laravel project

1.  Open **Command Prompt**.

2.  Check that Composer is installed:

    ``` bash
    composer -V
    ```

3.  Create a new Laravel project:

    ``` bash
    composer create-project laravel/laravel exam22
    ```

4.  Open the `exam22` folder in Visual Studio Code.

5.  Open the VS Code terminal and run:

    ``` bash
    php artisan serve
    ```

6.  Open `http://localhost:8000` in your browser and show the Laravel
    welcome page.

### Part B --- Create the controller

Create a controller named `FacilityController`:

``` bash
php artisan make:controller FacilityController
```

Inside `app/Http/Controllers/FacilityController.php`, implement:

1.  A **private** method named `getFacilities()` that returns an
    associative array containing the three facilities above. Each
    facility must have:

    -   `name`
    -   `building`

    The array key must be the facility ID.

2.  A method named `index()` that:

    -   Gets the facilities by calling `getFacilities()`.
    -   Returns the view `facilities.index`.
    -   Sends the variable to the view using `compact()`.

### Part C --- Create the Blade page

1.  Create this folder and file:

    `resources/views/facilities/index.blade.php`

2.  Add the heading:

    **School Facilities**

3.  Display all facilities in an HTML unordered list using `@foreach`.

4.  Use Blade output syntax `{{ }}` to display each facility's name and
    building.

### Part D --- Create the JSON endpoints

Add the following methods to `FacilityController`:

1.  `api()` --- returns the complete facilities array. Laravel should
    convert the array into JSON.
2.  `apiShow($id)` --- finds and returns one facility using its ID.

If the ID does not exist, `apiShow($id)` must return:

``` json
{
    "error": "Facility not found"
}
```

The response must use HTTP status code `404`.

### Part E --- Add the routes

In `routes/web.php`, add these routes:

  ------------------------------------------------------------------------------
  HTTP method       URL                      Controller method Purpose
  ----------------- ------------------------ ----------------- -----------------
  GET               `/facilities`            `index`           Display the Blade
                                                               page

  GET               `/api/facilities`        `api`             Return all
                                                               facilities as
                                                               JSON

  GET               `/api/facilities/{id}`   `apiShow`         Return one
                                                               facility or an
                                                               error
  ------------------------------------------------------------------------------

### Part F --- Test your application

Open each URL in the browser:

  -------------------------------------------------------------------------------
  URL                                         Expected result
  ------------------------------------------- -----------------------------------
  `http://localhost:8000/facilities`          HTML page showing all three
                                              facilities

  `http://localhost:8000/api/facilities`      JSON containing all three
                                              facilities

  `http://localhost:8000/api/facilities/2`    JSON for the Science Lab

  `http://localhost:8000/api/facilities/99`   `404` JSON error: Facility not
                                              found
  -------------------------------------------------------------------------------

Finally, run:

``` bash
php artisan route:list
```

Show the Blade page, all JSON test results, and the route list to the
examiner.

------------------------------------------------------------------------

# 3. Complete Solution

Try to complete the task yourself before checking this section.

## Step 1 --- Create the project

``` bash
composer -V
composer create-project laravel/laravel exam22
cd exam22
code .
php artisan serve
```

Visit `http://localhost:8000`.

> Keep the terminal running while testing the website. If port 8000 is
> already in use, stop the other server or run
> `php artisan serve --port=8001` and use port 8001 in your browser.

## Step 2 --- Create the controller

In the VS Code terminal:

``` bash
php artisan make:controller FacilityController
```

Open `app/Http/Controllers/FacilityController.php` and replace its
contents with:

``` php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class FacilityController extends Controller
{
    private function getFacilities()
    {
        return [
            1 => [
                'name' => 'Computer Lab',
                'building' => 'A'
            ],
            2 => [
                'name' => 'Science Lab',
                'building' => 'B'
            ],
            3 => [
                'name' => 'Library',
                'building' => 'C'
            ]
        ];
    }

    public function index()
    {
        $facilities = $this->getFacilities();

        return view('facilities.index', compact('facilities'));
    }

    public function api()
    {
        return $this->getFacilities();
    }

    public function apiShow($id)
    {
        $facilities = $this->getFacilities();

        if (!array_key_exists($id, $facilities)) {
            return response()->json([
                'error' => 'Facility not found'
            ], 404);
        }

        return response()->json($facilities[$id]);
    }
}
```

### Understanding the controller

-   `private function getFacilities()` keeps the sample data in one
    place. It can be used by the other methods in this controller.
-   The array is **associative**: `1`, `2`, and `3` are the keys (the
    facility IDs).
-   `compact('facilities')` creates an array containing the
    `$facilities` variable and passes it to the Blade view.
-   Returning an array from a Laravel route/controller response is
    automatically converted to JSON.
-   `response()->json(..., 404)` returns a JSON response with the HTTP
    status code `404`.

## Step 3 --- Create the Blade view

Create the folder:

``` text
resources/views/facilities
```

Inside it, create `index.blade.php`:

``` blade
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>School Facilities</title>
</head>
<body>
    <h1>School Facilities</h1>

    <ul>
        @foreach ($facilities as $id => $facility)
            <li>
                {{ $facility['name'] }}
                — Building: {{ $facility['building'] }}
            </li>
        @endforeach
    </ul>
</body>
</html>
```

### Understanding the Blade code

-   `@foreach` loops through every item in the `$facilities` array.
-   `$id` receives the array key.
-   `$facility` receives the current facility's data.
-   `{{ }}` safely displays the value in the HTML page.

## Step 4 --- Add the routes

Open `routes/web.php` and add:

``` php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\FacilityController;

Route::get('/facilities', [FacilityController::class, 'index']);

Route::get('/api/facilities', [FacilityController::class, 'api']);

Route::get('/api/facilities/{id}', [FacilityController::class, 'apiShow']);
```

If the file already contains the default `/` welcome route, you can keep
it. Add the three routes below it and do not create a second `<?php`
opening tag.

## Step 5 --- Test the Blade page

Open:

``` text
http://localhost:8000/facilities
```

Expected page content:

# School Facilities

-   Computer Lab --- Building: A
-   Science Lab --- Building: B
-   Library --- Building: C

## Step 6 --- Test the JSON endpoints

### A. All facilities

Open:

``` text
http://localhost:8000/api/facilities
```

Expected JSON:

``` json
{
    "1": {
        "name": "Computer Lab",
        "building": "A"
    },
    "2": {
        "name": "Science Lab",
        "building": "B"
    },
    "3": {
        "name": "Library",
        "building": "C"
    }
}
```

### B. One existing facility

Open:

``` text
http://localhost:8000/api/facilities/2
```

Expected JSON:

``` json
{
    "name": "Science Lab",
    "building": "B"
}
```

### C. A facility that does not exist

Open:

``` text
http://localhost:8000/api/facilities/99
```

Expected response body:

``` json
{
    "error": "Facility not found"
}
```

The HTTP status must be **404 Not Found**. You can confirm the status in
the browser's Developer Tools (Network tab) or with an API client.

## Step 7 --- Check the routes

Run:

``` bash
php artisan route:list
```

You should find these three GET routes:

``` text
GET  facilities
GET  api/facilities
GET  api/facilities/{id}
```

Laravel may display additional columns such as middleware and route
names. That is normal.

------------------------------------------------------------------------

# 4. Final Submission Checklist

Before presenting your work, make sure you can demonstrate all of the
following:

-   [ ] `composer -V` runs successfully.
-   [ ] The Laravel project `exam22` was created.
-   [ ] The Laravel welcome page opens.
-   [ ] `FacilityController` exists.
-   [ ] `getFacilities()` is private and returns the required three
    facilities.
-   [ ] `/facilities` displays the facilities using Blade `@foreach` and
    `{{ }}`.
-   [ ] `/api/facilities` returns all facilities as JSON.
-   [ ] `/api/facilities/2` returns the Science Lab.
-   [ ] `/api/facilities/99` returns the error message with status
    `404`.
-   [ ] `php artisan route:list` shows the three required routes.

## 5. Questions the Examiner May Ask

1.  What is the purpose of a controller in Laravel?
2.  Why is `getFacilities()` declared as `private`?
3.  What is the difference between `web.php` and a controller?
4.  What does `compact('facilities')` do?
5.  What is Blade?
6.  What is the purpose of `@foreach`?
7.  What is the difference between `{{ $value }}` and writing a value
    directly in HTML?
8.  What is JSON, and why do we use it?
9.  What does `response()->json()` do?
10. Why should an API return status code `404` when a facility does not
    exist?

------------------------------------------------------------------------

**Important:** The data in this exercise is stored in a PHP array inside
the controller. No database, migration, model, authentication, or CRUD
operations are required.
