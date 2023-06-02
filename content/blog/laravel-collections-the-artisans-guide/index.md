+++
title = "Laravel Collections: The Artisan's Guide"
date = "2023-01-16T19:00:00Z"
description = "Laravel Collections are really powerful for working with arrays of data. Let's learn how to use the methods they provide to improve our applications."
keywords = ["back-end", "php", "laravel", "laravel collection", "laravel collections", "laravel collection guide", "laravel collections how to", "laravel collection from zero", "laravel artisan"]
topics =["back-end", "php", "laravel"]
+++

## Introduction

**Laravel Collections** are really powerful for working with arrays of data. They provide a fluent, convenient
interface for performing common array operations, such as filtering, formatting and transforming data.
In this blog post, we'll explore some of the key features of **Laravel Collections** and how they can be used
to simplify and streamline your code.

## Creating Collections

The simplest way to create a Collection is to pass an array to the `collect()` method:

```php
$collection = collect(1, 2, 3, 4, 5);
```

When working with **Eloquent Models** and the **Query Builder**, it's also going to return a **Collection** by default.

```php
$users = User::query()
    ->where('is_active', true)
    ->get();
```

## Filtering Data

The **Laravel Collections** have a lot of different methods that allows us to filter the data in our **Collections**.
I'll show you some of the methods that can be used for this goal.

### `filter()`

This is the most common method for filtering data in a **Collection**. It will remove all the items that returns
`false` for the given callback.

```php
$users = collect([
    ['name' => 'John Doe', 'is_active' => true],
    ['name' => 'Mary Doe', 'is_active' => true],
    ['name' => 'Peter Doe', 'is_active' => false],
]);

$filtered = $users->filter(fn ($user) => $user['is_active']);
// [
//     ['name' => 'John Doe', 'is_active' => true],
//     ['name' => 'Mary Doe', 'is_active' => true],
// ]
```

You can also call the `filter()` method without any callback. In this case, it will remove all items that are
`false/empty` like `null`, `false`, `''`, `0`, `[]`.

It's important to note that this method doesn't apply any changes to the original **Collection**, it creates
a whole new **Collection** with the filtered items.

### `where()`

Like the `where()` method used for **Eloquent** queries, the `where()` method can be used to filter data based on a
key/value.

```php
$users = collect([
    ['name' => 'John Doe', 'age' => 15],
    ['name' => 'Mary Doe', 'age' => 20],
    ['name' => 'Peter Doe', 'age' => 30],
    ['name' => 'George Doe', 'age' => 20],
]);

$filtered = $users->where('age', 20);
// [
//     ['name' => 'Mary Doe', 'age' => 20],
//     ['name' => 'George Doe', 'age' => 20],
// ]
```

You can also pass a comparison operator as the second parameter, just like the **Eloquent** `where()` method.

```php
$users = collect([
    ['name' => 'John Doe', 'age' => 15],
    ['name' => 'Mary Doe', 'age' => 20],
    ['name' => 'Peter Doe', 'age' => 30],
    ['name' => 'George Doe', 'age' => 20],
]);

$filtered = $users->where('age', '>=', 20);
// [
//     ['name' => 'Mary Doe', 'age' => 20],
//     ['name' => 'Peter Doe', 'age' => 30],
//     ['name' => 'George Doe', 'age' => 20],
// ]
```

Be aware that the `where()` method just checks if the value is the same, not the type, so `20` and `'20'`
will be the same. If you want to check the value and the type, you can use the `whereStrict()` method instead.

You can also filter for multiple values using the `whereIn()` method and by a range of values using the `whereBetween()` method

```php
$users = collect([
    ['name' => 'John Doe', 'age' => 15],
    ['name' => 'Mary Doe', 'age' => 20],
    ['name' => 'Peter Doe', 'age' => 30],
    ['name' => 'George Doe', 'age' => 20],
]);

$filtered = $users->whereIn('age', [20, 30]);
// [
//     ['name' => 'Mary Doe', 'age' => 20],
//     ['name' => 'Peter Doe', 'age' => 30],
//     ['name' => 'George Doe', 'age' => 20],
// ]
```

```php
$users = collect([
    ['name' => 'John Doe', 'age' => 15],
    ['name' => 'Mary Doe', 'age' => 20],
    ['name' => 'Peter Doe', 'age' => 30],
    ['name' => 'George Doe', 'age' => 20],
]);

$filtered = $users->whereBetween('age', [15, 20]);
// [
//     ['name' => 'John Doe', 'age' => 15],
//     ['name' => 'Mary Doe', 'age' => 20],
//     ['name' => 'George Doe', 'age' => 20],
// ]
```

It's important to note that these methods don't apply any changes to the original **Collection**, they create
a whole new **Collection** with the filtered items.

### `first()`

This method returns the first item of the **Collection** that returns `true` for the given callback.

```php
$users = collect([
    ['name' => 'John Doe', 'age' => 15],
    ['name' => 'Mary Doe', 'age' => 20],
    ['name' => 'Peter Doe', 'age' => 30],
]);

$filtered = $users->first(fn ($user) => $user['age'] > 18);
// ['name' => 'Mary Doe', 'age' => 20]
```

You can also call the `first()` method without any callback. In this case it will return the first item
of the **Collection**.

```php
$users = collect([
    ['name' => 'John Doe', 'age' => 15],
    ['name' => 'Mary Doe', 'age' => 20],
    ['name' => 'Peter Doe', 'age' => 30],
]);

$filtered = $users->first(fn ($user) => $user['age'] > 18);
// ['name' => 'John Doe', 'age' => 15]
```

If you want to throw an exception if no result is found, you can use the `firstOrFail()` method instead. In this
case if no item is found it will throw an `Illuminate\Support\ItemNotFoundException` exception.

### `last()`

This method returns the last item of the Collection that returns `true` for the given callback.

```php
$users = collect([
    ['name' => 'John Doe', 'age' => 15],
    ['name' => 'Mary Doe', 'age' => 20],
    ['name' => 'Peter Doe', 'age' => 30],
]);

$filtered = $users->last(fn ($user) => $user['age'] > 18);
// ['name' => 'Peter Doe', 'age' => 30]
```

You can also call the `last()` method without any callback. In this case it will return the last item
of the **Collection**.

```php
$users = collect([
    ['name' => 'John Doe', 'age' => 15],
    ['name' => 'Mary Doe', 'age' => 20],
    ['name' => 'Peter Doe', 'age' => 30],
]);

$filtered = $users->first(fn ($user) => $user['age'] > 18);
// ['name' => 'Peter Doe', 'age' => 30]
```

### `only()`

This method can be used to filter out the needed items from the **Collection** based on the keys.

```php
$user = collect([
    'id' => 1,
    'name' => 'John Doe',
    'email' => 'john.doe@example.com',
    'username' => 'john_doe',
]);

$filtered = $user->only(['name', 'email']);
// ['name' => 'John Doe', 'email' => 'john.doe@example.com']
```

It's important to note that this method doesn't apply any changes to the original **Collection**, it creates
a whole new **Collection** with the filtered items.

### `except()`

This method can be used to filter out unwanted items from the **Collection** based on the keys.

```php
$user = collect([
    'id' => 1,
    'name' => 'John Doe',
    'email' => 'john.doe@example.com',
    'username' => 'john_doe',
]);

$filtered = $user->except(['name', 'email']);
// ['id' => 1, 'username' => 'john_doe']
```

It's important to note that this method doesn't apply any changes to the original **Collection**, it creates
a whole new **Collection** with the filtered items.

## Formatting and Transforming Data

It's pretty common for applications to get some data, loop over it to transform the value and push the formatted
value into a temporary variable. Instead of doing this, we can use the `map()` method that will perform the given
callback in each item of the **Collection** and return a new **Collection** with the transformed/formatted values.

```php
$users = collect([
    ['name' => 'John Doe', 'email' => 'john.doe@example.com', 'is_active' => true],
    ['name' => 'Mary Doe', 'email' => 'mary.doe@example.com', 'is_active' => true],
    ['name' => 'Peter Doe', 'email' => 'peter.doe@example.com', 'is_active' => true],
]);

$userModels = $users->map(fn ($user) => new User($user));
```

## Debugging Collections

Sometimes we need to debug the values of our **Collections** and the **Laravel Collections** have two
methods out-of-the-box for helping us with that.

### `dump()`

This method dumps the **Collection** items and continues executing the script.

### `dd()`

This method dumps the **Collection** items and ends the execution of the script.

## Hidden Gems

Besides the methods already presented in the sections above. There are some methods that are not used that much
or neither very well-known, but that can be a game changer depending on what we're working on. Below I'll list
some of the methods that I think you should know.

### `undot()`

This method can be used to transform dot-noted strings into arrays.

```php
$data = collect([
    'user.first_name' => 'John',
    'user.last_name' => 'Doe',
    'user.email' => 'john.doe@example.com',
    'user.social.twitter' => '@john_doe',
    'user.social.github' => 'JohnDoe',
]);

$user = $data->undot();
// [
//     "user" => [
//         "first_name" => "John",
//         "last_name" => "Doe",
//         "email" => "john.doe@example.com",
//         "social" => [
//             "twitter" => '@john_doe',
//             "github" => 'JohnDoe',
//         ],
//     ],
// ]
```

### `partition()`

This method separates the **Collection** items in two different ones based on the given callback. The items 
that return `true` for the given callback will be pushed into the first **Collection** and the ones returning
`false` will be pushed into the second one.

```php
$users = collect([
    ['name' => 'John Doe', 'is_active' => true],
    ['name' => 'Mary Doe', 'is_active' => false],
    ['name' => 'Peter Doe', 'is_active' => true],
]);

[$activeUsers, $inactiveUsers] = $users->partition(fn ($user) => $user['is_active']);

$activeUsers->all();
// [
//     ['name' => 'John Doe', 'is_active' => true],
//     ['name' => 'Peter Doe', 'is_active' => true],
// ]

$inactiveUsers->all();
// [
//     ['name' => 'Mary Doe', 'is_active' => false],
// ]
```

### `reject()`

This is the opposite of the `filter()` method, but it's not that well-known.  It will remove all the items that
returns `true` for the given callback.

```php
$users = collect([
    ['name' => 'John Doe', 'is_active' => true],
    ['name' => 'Mary Doe', 'is_active' => true],
    ['name' => 'Peter Doe', 'is_active' => false],
]);

$filtered = $users->reject(fn ($user) => $user['is_active']);
// [
//     ['name' => 'Peter Doe', 'is_active' => false],
// ]
```

### `random()`

This method returns a random item from the **Collection**.

```php
$data = collect([1, 2, 3, 4, 5]);

$data->random();
// 3 - randomly selected
```

If you want more than one item retrieved you can pass an integer as argument.

```php
$data = collect([1, 2, 3, 4, 5]);

$data->random(2);
// [2, 3] - randomly selected
```

### `isEmpty()`

This method returns `true` if the **Collection** has no items or `false` if it has at least one item.

```php
collect([])->isEmpty(); // true
collect([1])->isEmpty(); // false
```

### `isNotEmpty()`

This method is the opposite of the `isEmpty()` method. It will return `true` if the **Collection** has at least
one item and `false` if it doesn't have any items on it.

```php
collect([])->isNotEmpty(); // false
collect([1])->isNotEmpty(); // true
```

## High Order Messages

High Order Messages are like shortcuts that we can use to apply common actions in our Collections. The methods
that support this feature are: `average()`, `avg()`, `contains()`, `each()`, `every()`, `filter()`, `first()`,
`flatMap()`, `groupBy()`, `keyBy()`, `map()`, `max()`, `min()`, `partition()`, `reject()`, `skipUntil()`,
`skipWhile()`, `some()`, `sortBy()`, `sortByDesc()`, `sum()`, `takeUntil()`, `takeWhile()`, and `unique()`.

Imagine that you need to send a Newsletter to all your subscribed users. With the use of this feature you can
do something like this.

```php
User::query()
    ->where('receive_newsletter', true)
    ->each
    ->sendNewsletter();
```

## Lazy Collections

**Collections** are really powerful and **LazyCollections** extend the power of them by using generators, so we can work
with large datasets but keeping the memory usage low.

Imagine that we have millions of products in our database, and we need to perform some actions on them, we can
use the `cursor()` method from **Eloquent/Query Builder** to return a **LazyCollection** instead of a normal **Collection**.

```php
$payments = Payment::query()
    ->where('is_accepted', true)
    ->cursor()
    ->map(fn ($payment) => $this->formatPaymentData($payment));

foreach ($payments as $payment) {
    // LOGIC HERE
}
```

In the example above a single query will be run against the database, but it will also keep only a single item
in memory at a time. Also, the `map()` callback won't be executed right away, but only when we iterate over each
single item in the `foreach` statement.

## Creating Your Own Collection Methods

Besides all the amazing methods that the **Collections** already have **out-of-the-box**, we can create our
own methods since they are **"macroable"**. This means that we can use the `macro()` method to extend them with
our own methods. To do that we need to add our new custom methods in the `boot()` method of the `AppServiceProvider`
class.

```php
class AppServiceProvider extends ServiceProvider
{
    public function boot()
    {
        Collection::macro('toSlug', function () {
            return $this->map(fn ($value) => Str::slug($value, '-'));
        });
    }
}
```

## Conclusion

These are just a few examples of the many powerful methods available on **Laravel Collections**.
By using these methods, you can easily and efficiently manipulate your data, making your code more **readable** and **maintainable**.

I hope that you liked this article and if you do, don’t forget to share this article with your friends!!! See ya! :wink:
