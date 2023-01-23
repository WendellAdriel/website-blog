+++
title = "Laravel AaaS - Actions as a Service"
date = "2023-01-23T10:16:21Z"
author = "Wendell Adriel"
authorTwitter = "wendell_adriel"
tags = ["back-end", "php", "laravel", "architecture"]
keywords = ["php", "laravel", "architecture", "laravel actions", "laravel services", "AaaS", "Actions as a Service", "invokable controllers", "architectural pattern"]
description = "Action classes and Invokable Controllers are a hot topic in Laravel nowadays. In this article I'll explain why I think Invokable Controllers are a bad idea and an architectural pattern I created and named as AaaS."
cover = "img/blog/laravel_aaas.png"
showFullContent = false
hideComments = false
+++

## Introduction

Laravel is an amazing framework. We can build products really quick with all the features and DX it provides.
Usually there are many ways to do things in Laravel, there's no single correct way of doing it, and sometimes
it's really down to a personal level on how we want to structure our applications.

**Action** classes and **Invokable Controllers** are a hot topic nowadays. I see a lot of people using and talking
about them. I also used and experimented with these ideas, and in this article I'm going to explain why I think
that **Invokable Controllers** are a bad idea and about an **Architectural Pattern** I created and I've been
using that I named **AaaS - Actions as a Service**.

Again, as I said before, there are many ways to do things in **Laravel** and I'm going to show you one of those. I
like it, and it makes sense to me to organize my applications like this, but if it doesn't make sense to you,
you're free to organize your applications in your way.

## Action Classes

**Action** classes are classes with the purpose of executing a single action. They are classes with usually a single
(public) method. A really simple example of an **Action** class would be for creating a new user.

{{< code language="php" >}}
namespace App\Actions\Users\CreateUser;

use App\Models\User;

class CreateUser
{
    /**
     * @param  string  $name
     * @param  string  $email
     * @return User
     */
    function handle(string $name, string $email): User
    {
        User::query()->create([
            'name' => $name,
            'email' => $email,
        ]);
    }
}
{{< /code >}}

As you can see in the above (simplified) implementation, the class `CreateUser` is only used for creating a new
User. In a real code you probably will have more logic in this class, and it can even have other private methods
for splitting the logic in a more readable and maintainable way, but the idea is to have a class that has a
single purpose.

## Invokable Controllers

The concept of **Action** classes are usually largely used for creating **Invokable Controllers** that are
**Controller** classes with the purpose of executing a single action. So instead of having multiple methods
in it, the Controller will have only an `__invoke()` method defined and that's what's going to be executed.
Let's see the above **Action** example implemented as an **Invokable Controller**.

{{< code language="php" >}}
namespace App\Http\Controllers\Users;

use App\Http\Controllers\Controller;
use App\Http\Requests\Users\CreateUserRequest;
use App\Models\User;

class CreateUserController extends Controller
{
    /**
     * @param  CreateUserRequest  $request
     * @return User
     */
    function __invoke(CreateUserRequest $request): User
    {
        $data = $request->validated();
        User::query()->create([
            'name' => $data['name'],
            'email' => $data['email'],
        ]);
    }
}
{{< /code >}}

## Why Invokable Controllers are BAD

**Invokable Controllers** are a hot topic right now in **Laravel** and this concept is being used heavily by a lot of
developers. Personally I think it's **BAD**. I'm not criticizing people that use it, if this works for your
application, continue doing that, but I'll explain why I don't use it.

As far as I saw, the developers using **Invokable Controllers** use it to avoid having **huge** Controllers. I
totally get it, but IMO the controllers shouldn't be **huge** even if they have multiple methods. In fact, I've
been working with **APIs** on **Laravel** daily for the past 5 years and all my Controller methods have at max
5 lines of code and that's because I use the Controllers only as a **Communication Layer** as I talked about 
**[in this article](https://wendelladriel.com/blog/structuring-a-server-side-application)**.  So to me, it
doesn't make sense to create a file that will have only a few lines of code. IMO the **Controllers**
should only be used to:

1. Get the **Request**
2. Map the input properties
3. Send the mapped input to the **Service Layer**
4. Get the result from the **Service Layer** and send back a **Response**

## The AaaS Pattern

I really liked the idea of **Action** classes, but as I mentioned above, to me, it didn't make sense to implement
it as **Invokable Controllers**. So I've been using **Action** classes in a different way. I've been using them
as my **Service Layer**, and that's how I named this pattern **AaaS - Actions as a Service**.

This is an **Architectural Pattern** like **MVC** and even that I created this because of **Laravel**, you can apply it
to other frameworks and even other programming languages if you want to. This pattern have four principles
that I'll explain below.

### Thin Communication Layer

The **Communication Layer** is the layer of our applications that receives the user input. In a **Web** app this is
our **Controllers**, in a **CLI** app it's the **Commands**. This layer should only:

1. Get the input from the user
2. Map the input properties
3. Send the mapped input to the Service Layer
4. Get the result from the Service Layer and send it back to the user

### Detached Validation

The data validation should **NOT** be tied to the **Communication Layer**. It means that the data validation should
be tied to the data itself or to the **Action** it's used to accomplish. In a **Laravel** application it means that
the data validation should **NOT** be done using Request Forms per example, but in a **DTO** or in the
**Action** itself.

### Mapped Input

The input needed to execute an **Action** should be mapped into a **DTO (Data Transfer Object)** when it needs
multiple input properties to improve  the code quality and maintainability of the **Actions** of the
application.

### Single Purpose Actions

All the **Business Logic** of the application should be in **Action** classes and each **Action** class
should be responsible for a single **Action**. You can call another **Action** in an **Action** if needed,
to avoid code duplication.

## How to Implement the AaaS Pattern

Now that you know what's the **AaaS Pattern**, let's see a simple example on how to apply it in a **Laravel** app.
Let's imagine a simple API CRUD implementation for the Users of our application.

Let's first take a look at the `UserController` class.

{{< code language="php" >}}
namespace App\Http\Controllers\Users;

use App\Actions\Users\DeleteUser;
use App\Actions\Users\FetchUsers;
use App\Actions\Users\SaveUser;
use App\Actions\Users\ShowUser;
use App\DTOs\Users\FetchUsersDTO;
use App\DTOs\Users\SaveUserDTO;
use App\Http\Controllers\Controller;
use Illuminate\Http\JsonResponse;
use Illuminate\Http\Request;
use Illuminate\Http\Response;

class UserController extends Controller
{
    /**
     * @param  Request  $request
     * @param  FetchUsers  $action
     * @return JsonResponse
     */
    public function index(Request $request, FetchUsers $action): JsonResponse
    {
        return response()->json($action->handle(FetchUsersDTO::fromRequest($request)));
    }

    /**
     * @param  int  $userId
     * @param  ShowUser  $action
     * @return JsonResponse
     */
    public function show(int $userId, ShowUser $action): JsonResponse
    {
        return response()->json($action->handle($userId));
    }

    /**
     * @param  Request  $request
     * @param  SaveUser  $action
     * @return JsonResponse
     */
    public function store(Request $request, SaveUser $action): JsonResponse
    {
        return response()->json($action->handle(SaveUserDTO::fromRequest($request)), Response::HTTP_CREATED);
    }

    /**
     * @param  Request  $request
     * @param  int  $userId
     * @param  SaveUser  $action
     * @return JsonResponse
     */
    public function update(Request $request, int $userId, SaveUser $action): JsonResponse
    {
        return response()->json($action->handle(SaveUserDTO::fromRequest($request), $userId), Response::HTTP_OK);
    }

    /**
     * @param  int  $userId
     * @param  DeleteUser  $action
     * @return JsonResponse
     */
    public function destroy(int $userId, DeleteUser $action): JsonResponse
    {
        $action->handle($userId);
        return response()->noContent();
    }
}
{{< /code >}}

As you can see, all the methods in the **Controller** are really simple, following the **Thin Communication Layer**
principle.

For the **Detached Validation** and **Mapped Input** principles, I'll be using a package that I created:
**[Validated DTO](https://github.com/WendellAdriel/laravel-validated-dto)** to simplify the use of DTOs,
but you can use the package of your choice or even not use any package at all.

Let's take a look at the `FetchUsersDTO` and `SaveUserDTO` classes.

{{< code language="php" >}}
namespace App\DTOs\Users;

use WendellAdriel\ValidatedDTO\Casting\BooleanCast;
use WendellAdriel\ValidatedDTO\Casting\IntegerCast;
use WendellAdriel\ValidatedDTO\ValidatedDTO;

class FetchUsersDTO extends ValidatedDTO
{
    public int $page;
    public int $per_page;
    public bool $active_only;

    /**
     * @return array
     */
    protected function rules(): array
    {
        return [
            'page' => ['sometimes', 'integer'],
            'per_page' => ['sometimes', 'integer'],
            'active_only' => ['sometimes', 'boolean'],
        ];
    }

    /**
     * @return array
     */
    protected function defaults(): array
    {
        return [
            'page' => 1,
            'per_page' => 20,
            'active_only' => true,
        ];
    }

    /**
     * @return array
     */
    protected function casts(): array
    {
        return [
            'page' => new IntegerCast(),
            'per_page' => new IntegerCast(),
            'active_only' => new BooleanCast(),
        ];
    }
}
{{< /code >}}

{{< code language="php" >}}
namespace App\DTOs\Users;

use WendellAdriel\ValidatedDTO\Casting\StringCast;
use WendellAdriel\ValidatedDTO\ValidatedDTO;

class SaveUserDTO extends ValidatedDTO
{
    public string $name;
    public string $email;

    /**
     * @return array
     */
    protected function rules(): array
    {
        return [
            'name' => ['required', 'string'],
            'email' => ['required', 'email'],
        ];
    }

    /**
     * @return array
     */
    protected function defaults(): array
    {
        return [];
    }

    /**
     * @return array
     */
    protected function casts(): array
    {
        return [
            'name' => new StringCast(),
            'email' => new StringCast(),
        ];
    }
}
{{< /code >}}

As you can see, the validation of the data is now in the **DTO**, attached to the data itself and not to the
**Communication Layer**. If I need to call the **Action** using the **DTO** from a **CLI** command or another
**Action**, I don't need to **manually validate** the data if I would need if the validation was tied to
the **Communication Layer**, for example using **Form Requests**.

Now, for the last principle - **Single Purpose Actions** - let's see our `FetchUsers`, `ShowUser`,
`SaveUser` and `DeleteUser` **Action** classes.

{{< code language="php" >}}
namespace App\Actions\Users;

use App\DTOs\Users\FetchUsersDTO;
use App\Models\User;
use Illuminate\Database\Eloquent\Collection;

class FetchUsers
{
    /**
     * @param  FetchUsersDTO $dto
     * @return Collection
     */
    public function handle(FetchUsersDTO $dto): Collection
    {
        $query = User::query();

        if ($dto->active_only) {
            $query->where('is_active', true);
        }

        return $query->skip(($dto->page - 1) * $dto->per_page)
            ->take($dto->per_page)
            ->get();
    }
}
{{< /code >}}

{{< code language="php" >}}
namespace App\Actions\Users;

use App\Models\User;
use Illuminate\Database\Eloquent\ModelNotFoundException;

class ShowUser
{
    /**
    * @param  int  $userId
    * @return User
    *
    * @throws ModelNotFoundException
    */
    public function handle(int $userId): User
    {
        return User::query()->findOrFail($userId);
    }
}
{{< /code >}}

{{< code language="php" >}}
namespace App\Actions\Users;

use App\DTOs\Users\SaveUserDTO;
use App\Models\User;
use Illuminate\Database\Eloquent\ModelNotFoundException;

class SaveUser
{
    public __construct(
        private ShowUser $showAction
    ) {}

    /**
     * @param  SaveUserDTO  $dto
     * @param  int|null  $userId
     * @return User
     *
     * @throws ModelNotFoundException
     */
    public function handle(SaveUserDTO $dto, ?int $userId = null): User
    {
        $user = is_null($userId)
            ? new User()
            : $this->showAction->handle($userId);

        $user->fill($dto->toArray());
        $user->save();

        return $user;
    }
}
{{< /code >}}

{{< code language="php" >}}
namespace App\Actions\Users;

use App\Models\User;
use Illuminate\Database\Eloquent\ModelNotFoundException;

class DeleteUser
{
    public __construct(
        private ShowUser $showAction
    ) {}

    /**
     * @param  int  $userId
     * @return void
     *
     * @throws ModelNotFoundException
     */
    public function handle(int $userId): void
    {
        $user = $this->showAction->handle($userId);
        $user->delete();
    }
}
{{< /code >}}

As you can see, each **Action** class has a single purpose. For more complex cases, you can even split the
**Save Action** and/or the **DTO** in two different ones: `CreateUser` and `UpdateUser` **Actions** and
`CreateUserDTO` and `UpdateUserDTO` **Data Transfer Objects**. For this simplified example that was not
needed, that's why it was combined into a **Save Action**.

## Conclusion

As I said at the beginning of this article, there's different ways on approaching solutions and implementations
with **Laravel** and any other framework or programming language. The one I presented here in this article is just
one of them and that personally it's working for me for simple and small projects but also for large and complex
ones.

I also presented a pattern that I created, the **AaaS Pattern**, that you can apply to any project you work on and
that's a way of having a codebase that's easy to work on, maintain and update as you need to.

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink: