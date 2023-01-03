+++
title = "API Versioning in Laravel"
date = "2022-12-29T17:09:41Z"
author = "Wendell Adriel"
authorTwitter = "wendell_adriel"
tags = ["back-end", "php", "laravel", "architecture", "api"]
keywords = ["php", "laravel", "architecture", "api", "version", "versioning", "laravel api version", "laravel api versioning", "laravel api"]
description = "Have you ever heard of API versioning?  This is very handy when you need to add an alternative logic for some endpoints but want to keep the current logic for some users. Let's learn a simple way to achieve that in Laravel"
cover = "img/blog/api_versioning_laravel.png"
showFullContent = false
hideComments = false
+++

## Introduction

A lot of companies, especially **SaaS** ones use this technique in their applications and this can be very
handy depending on the context of your application.

There are many ways to implement API versioning in **Laravel**. The one I'll show in this post is one of the
simplest ones to start using this technique in your applications.

## What's API Versioning

API versioning is the practice of releasing multiple versions of an **Application Programming Interface (API)**
to support **different functionality**, **features**, or **backwards compatibility**. It allows developers to
update and improve their **APIs** over time **without breaking existing integrations** or **affecting users**.
When an **API** is updated, a new version is usually released with a new version number, and the previous
version is maintained for a period of time to allow users to transition to the new version. This allows
developers to add new features or make changes to their **API** without disrupting the functionality of
existing integrations. It also allows users to choose which version of the **API** they want to use,
depending on their needs and the compatibility of their systems.

## Preparing the Routes

First, let's prepare our routes files for the versions of the **API**. Create a folder inside the `routes` folder:

```
mkdir routes/api
```

Next, let's create two PHP files inside the newly created folder:

```
touch routes/api/v1.php
touch routes/api/v2.php
```

Now, let's edit the `routes/api.php` file to look like this:

{{< code language="php" >}}
<?php

use Illuminate\Support\Facades\Route;

/*
|--------------------------------------------------------------------------
| API Routes
|--------------------------------------------------------------------------
|
| Here is where you can register API routes for your application. These
| routes are loaded by the RouteServiceProvider within a group which
| is assigned the "api" middleware group. Enjoy building your API!
|
*/

require __DIR__.'/api/v1.php';
require __DIR__.'/api/v2.php';
{{< /code >}}

## Creating Test Routes

To make sure that our **API** versions are working, let's create some test routes.

Update the `routes/api/v1.php` file to look like this:

{{< code language="php" >}}
<?php

use Illuminate\Support\Facades\Route;

Route::prefix('v1')->group(function () {
    Route::get('test', function () {
        return response()->json(['message' => 'API V1 working']);
    });
});
{{< /code >}}

Now, update the `routes/api/v2.php` file to look like this:

{{< code language="php" >}}
<?php

use Illuminate\Support\Facades\Route;

Route::prefix('v2')->group(function () {
    Route::get('test', function () {
        return response()->json(['message' => 'API V2 working']);
    });
});
{{< /code >}}

## Testing the Versioned Routes

Let's use **PHPUnit** which comes configured out-of-the-box in our **Laravel** application to test our newly
created versioned routes.

Create a new test file called `ApiVersionTest.php` inside `tests/Feature` and update it to look like this:

{{< code language="php" >}}
<?php

namespace Tests\Feature;

use Tests\TestCase;

class ApiVersionTest extends TestCase
{
    public function testV1RouteReturnsSuccessResponse()
    {
        $this->get('api/v1/test')
            ->assertOk()
            ->assertJsonStructure(['message']);
    }

    public function testV2RouteReturnsSuccessResponse()
    {
        $this->get('api/v2/test')
            ->assertOk()
            ->assertJsonStructure(['message']);
    }
}
{{< /code >}}

Now, using **Laravel Artisan**, run: `php artisan test` and you'll see something like this in your terminal:

```
PASS  Tests\Feature\ApiVersionTest
✓ v1 route returns success response
✓ v2 route returns success response
```

## Additional Tips

Now that we have our **API** versioning working, depending on the application context we can still:

> Create a new field in the `users` table to track which **API** version each user should use.


> Create different **middleware groups** for each version in `app/Http/Kernel class`.

## Conclusion

In this post, we learned what's **API versioning** and a really **simple** and **straightforward** way to
implement it in our **Laravel** applications.

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink: