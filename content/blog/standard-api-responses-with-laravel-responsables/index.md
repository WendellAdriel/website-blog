+++
title = "Standard API Responses With Laravel Responsables"
date = "2022-12-21T11:13:20Z"
author = "Wendell Adriel"
authorTwitter = "wendell_adriel"
tags = ["back-end", "php", "laravel", "architecture", "api"]
keywords = ["php", "laravel", "architecture", "api", "responses", "responsables", "laravel responses", "laravel custom responses", "laravel responsables", "laravel api", "laravel api response"]
description = "Data structure standards are the way to go for having good maintainability in our applications. Let's learn how we can use Laravel Responsables to create standardised API responses for our applications."
cover = "img/blog/laravel_responsables.png"
showFullContent = false
hideComments = false
+++

## Introduction

Imagine working with an application that each endpoint returns the data for successful and error calls in a
different way. If it's a small and simple app maintained by a single person it can be a little easier, but if
the application starts to grow and more people start joining the team, in a small window of time it will be
total chaos to maintain and improve the application.

Having a standardised structure for the responses of your application can help a lot in improving its quality
and maintainability of it. In this article, we will learn a simple way to do this using **Laravel Responsable**
classes.

## What's Laravel Responsable Classes?

Laravel has an interface: `Illuminate\Contracts\Support\Responsable` that can be used to create classes that
can be converted into **HTTP Responses**. This is a really simple interface that has only one method:

```php
interface Responsable
{
    /**
     * Create an HTTP response that represents the object.
     *
     * @param  \Illuminate\Http\Request  $request
     * @return \Symfony\Component\HttpFoundation\Response
     */
    public function toResponse($request);
}
```

## Creating Custom Response Classes

Creating custom **Response** classes gives us a lot of power, but we need to be careful about how to use it.
I already saw many codebases and many articles explaining how to use custom **Response** classes to format
data and apply some logic in the data before sending it, but personally, I don't think that's a good way of
using this feature. For me, code formatting and logic should be in **another layer** of the application.

But one amazing thing we can do with this feature is to create **standardised responses** for our applications.
Having this in our applications can highly improve their quality and maintainability because you already
know the structure to expect from your application.

In the sections below we will create two different custom response classes, one for **successful API responses**
and another one for **failed API responses**.

## Creating the API Success Response

Imagine that in your application your endpoints always return the data needed for it, but it also can return
some **metadata** that can contain a **message** or **notification** to show to the user, **pagination** info,
etc. With this in mind, we can create a simple custom **Response** class that will always return two
properties in your responses: `data` and `metadata`. The custom **Response** class would be something like this:

```php
<?php

namespace App\Http\Responses;

use Illuminate\Contracts\Support\Responsable;
use Illuminate\Http\Response;

class ApiSuccessResponse implements Responsable
{
    /**
     * @param  mixed  $data
     * @param  array  $metadata
     * @param  int  $code
     * @param  array  $headers
     */
    public function __construct(
        private mixed $data,
        private array $metadata,
        private int $code = Response::HTTP_OK,
        private array $headers = []
    ) {}

    /**
     * @param  $request
     * @return \Symfony\Component\HttpFoundation\Response|void
     */
    public function toResponse($request)
    {
        return response()->json(
            [
                'data' => $this->data,
                'metadata' => $this->metadata,
            ],
            $this->code,
            $this->headers
        );
    }
}
```

With this class, we can ensure that:

1. The response will always be in **JSON** format.
2. We will always return the `data` and `metadata` properties.
3. We can customize the **HTTP Code** and **headers** if needed.

Now you can use this response for your endpoints:

```php
class UserController extends Controller
{
    public function store(CreateUserRequest $request): JsonResponse
    {
        $user = $this->service->create($request->all());
        return new ApiSuccessResponse(
            $user,
            ['message' => 'User was created successfully'],
            Response::HTTP_CREATED
        );
    }
}
```

## Creating the API Error Response

Imagine that in your application you want to always return an **error message** when something goes wrong, but
you want to also add some **debug** information if the debug is set to `true`. With this in mind, we can
create another simple custom **Response** class that will do that:

```php
<?php

namespace App\Http\Responses;

use Illuminate\Contracts\Support\Responsable;
use Illuminate\Http\Response;
use Throwable;

class ApiErrorResponse implements Responsable
{
    public function __construct(
        private string $message,
        private ?Throwable $exception = null,
        private int $code = Response::HTTP_INTERNAL_SERVER_ERROR,
        private array $headers = []
    ) {}

    /**
     * @param  $request
     * @return \Symfony\Component\HttpFoundation\Response|void
     */
    public function toResponse($request)
    {
        $response = ['message' => $this->message];

        if (! is_null($this->exception) && config('app.debug')) {
            $response['debug'] = [
                'message' => $this->exception->getMessage(),
                'file'    => $this->exception->getFile(),
                'line'    => $this->exception->getLine(),
                'trace'   => $this->exception->getTraceAsString()
            ];
        }

        return response()->json($response, $this->code, $this->headers);
    }
}
```

With this class, we can ensure that:

1. The response will always be in **JSON** format.
2. We will always return the `message` property.
3. The **debug** info will be added if needed.
4. We can customize the **HTTP Code** and **headers** if needed.

Now you can use this response for your endpoints:

```php
class UserController extends Controller
{
    public function store(CreateUserRequest $request): JsonResponse
    {
        try {
            $user = $this->service->create($request->all());
            return new ApiSuccessResponse(
                $user,
                ['message' => 'User was created successfully'],
                Response::HTTP_CREATED
            );
        } catch (Throwable $exception) {
            return new ApiErrorResponse(
                'An error occurred while trying to create the user',
                $exception
            );
        }
    }
}
```

## Conclusion

In this article, we learned how to use **Laravel Responsable** classes to create standardised **API** responses
for our applications to improve their **quality** and **maintainability**. The examples in this article were
just to show how to use this feature, but you can customize the custom **Response** classes for the needs of
your projects.

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink:
