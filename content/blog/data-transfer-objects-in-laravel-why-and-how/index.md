+++
title = "Data Transfer Objects in Laravel- Why and How"
date = "2022-12-16T12:47:03Z"
author = "Wendell Adriel"
authorTwitter = "wendell_adriel"
tags = ["back-end", "php", "laravel", "architecture"]
keywords = ["php", "laravel", "architecture", "dto", "laravel dto", "data transfer object", "laravel data transfer object"]
description = "Data Transfer Objects can be a great way to improve your applications. Let's learn why and how to use them in Laravel"
cover = "img/blog/laravel_dtos.png"
showFullContent = false
hideComments = false
+++

## Introduction

**Data Transfer Object (DTO)** is a pattern that if used correctly can improve the quality and the maintainability
of applications. In this article, we will learn why you should be using **DTOs** in your applications and how to
do it in **Laravel**.

## What is a Data Transfer Object

A Data Transfer Object (DTO) is a design pattern used to transfer data between software systems or layers
in a software architecture. It is a simple object that contains a set of data, typically in the form of
properties or fields, that represents a specific entity or concept in the system. The main purpose of a DTO
is to decouple the different layers or components of a software system, allowing them to communicate and
exchange data without needing to know the details of each other's implementation.

In summary, a DTO is a simple object that is used to transfer data between different layers or components
of a software system, helping to decouple and isolate these components from each other.

## Why to use DTOs

From small and simple codebases to large and complex ones, there are some standards and patterns we can use to
make sure that our applications have a good quality code, it's easy to maintain, adapt and update.

Data structure standards play a huge role in this. If I know the data structure used in a piece of code, it's
really easy to maintain that and also easy to change it and that's where the **DTOs** come into play.

Let's say that I have this code:

```php
class UserController extends Controller
{
    public function store(Request $request): JsonResponse
    {
        return response()->json([
            $this->service->createUser($request->all()),
            Response::HTTP_CREATED
        ]);
    }
}
```

Even that's a simple piece of code, if I created it one year ago and now I have to change the `createUser` 
method, I'll probably not remember which data the `$request->all()` method returns.

I could refactor this to use a **Custom Request** class:

```php
class UserController extends Controller
{
    public function store(CreateUserRequest $request): JsonResponse
    {
        return response()->json([
            $this->service->createUser($request->validated()),
            Response::HTTP_CREATED
        ]);
    }
}
```

With the above code, if I needed to update the `createUser` method and I don't remember which data it uses,
I could go to the `CreateUserRequest` to check. But that creates other issues:

1. The **validation** of the data is now coupled to the **HTTP Request**. If I need to call the `createUser`
method from within my code, I will need to validate the data manually again.

2. You have the data mapped, but since the `$request->validated()` returns an array, you can't force the type
of data passed to the `createUser` method.

If we apply the **DTO** pattern here we can solve both issues described above. We can map and validate the
data in the **DTO** instead, so if I need to call the `createUser` method from within my code, the validation
will be automatically done since I need to pass a **DTO** to the method, and it will also force the type of
data that we could pass to the `createUser` method since we will now need to pass a specific **DTO** for it.

## How to use DTOs

As explained above, **DTOs** can be simple objects to map properties. So the most basic implementation of a
**DTO** would be to create a simple class like this:

```php
class CreateUserDTO
{
    public function __construct(
        private string $name,
        private string $email,
        private string $username,
        private string $password
    ) {}

    // Add getters, setters, validation
}
```

Then you can create your **DTO** from the **Request**:

```php
class UserController extends Controller
{
    public function store(Request $request): JsonResponse
    {
        return response()->json([
            $this->service->createUser(new CreateUserDTO(...$request->all())),
            Response::HTTP_CREATED
        ]);
    }
}
```

Here: `new CreateUserDTO(...$request->all())` we are using the **named arguments** from **PHP 8** to create an
instance of the `CreateUserDTO`.

With this, we solve the **issues** that we detailed in the last section. Your code now has **improved quality**
and **maintainability**.

## Conclusion

In this article, we learned what are **Data Transfer Objects**, why we should be using them in our applications
and how to use them in **Laravel**.

If you want to start using **DTOs** in your applications, I created
**[this package](https://github.com/WendellAdriel/laravel-validated-dto)** that's an easy way to start using
**DTOs** by providing a **base DTO class** with **validation** and **type-casting out-of-the-box** and also an **artisan
command** for creating your **DTOs**.

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink:
