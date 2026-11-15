# Running a Laravel MVC Project Online (GitHub Codespaces)

This guide provides step-by-step instructions and terminal commands to create, set up, and run a Laravel MVC application entirely in the cloud using **GitHub Codespaces** (VS Code Online) without installing any local software.

---

## 1. Create a GitHub Repository & Open Codespaces

1. Log in to your [GitHub](https://github.com) account and create a **New Repository**.
2. Check the option **Add a README file** and click **Create repository**.
3. On your repository page, click the green **Code** button.
4. Switch to the **Codespaces** tab and click **Create codespace on main**.
5. Wait for the cloud environment to initialize and open VS Code in your browser.

---

## 2. Install & Run Laravel

Open the integrated terminal in VS Code (`Ctrl + ~` or `Terminal > New Terminal`) and execute the following commands:

### Step 1: Create a new Laravel project in the current directory
```bash
composer create-project laravel/laravel .
```

### Step 2: Generate the application security key
```bash
php artisan key:generate
```

### Step 3: Start the development server
Run the server bound to `0.0.0.0` so GitHub Codespaces can forward the port to your browser:
```bash
php artisan serve --host=0.0.0.0 --port=8000
```

> **Note:** A pop-up notification will appear in the bottom-right corner indicating that port `8000` is running. Click **Open in Browser** to view your live Laravel application.

---

## 3. Build a Basic MVC Example

Follow these commands and snippets to create a simple Route, Controller, and View.

### Step 1: Generate a Controller
```bash
php artisan make:controller PageController
```

Open `app/Http/Controllers/PageController.php` and update the `index` method to pass data to a view:
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class PageController extends Controller
{
    public function index()
    {
        $userName = "Developer";
        $courseName = "Laravel MVC";

        return view('welcome-mvc', compact('userName', 'courseName'));
    }
}
```

### Step 2: Create a Blade View
Create a new file at `resources/views/welcome-mvc.blade.php` and add the following HTML:
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Laravel MVC App</title>
</head>
<body>
    <h1>Welcome, {{ $userName }}!</h1>
    <p>You are successfully running {{ $courseName }} online.</p>
</body>
</html>
```

### Step 3: Define the Route
Open `routes/web.php` and register the new route:
```php
<?php

use Illuminate\Support\Facades\Route;
use App\Http\Controllers\PageController;

Route::get('/mvc-page', [PageController::class, 'index']);
```

### Step 4: Preview Your MVC Page
Ensure `php artisan serve --host=0.0.0.0 --port=8000` is running, then append `/mvc-page` to your Codespace preview URL in the browser.