# GraphQL Testing Helper for Laravel

Elegant GraphQL testing utilities for Laravel. Works with any GraphQL library. Especially with [Lighthouse](https://lighthouse-php.com/).

## Installation

Install this library with composer

    composer require --dev marvinrabe/laravel-graphql-test

Add the trait to your `TestCase` class:

```php
<?php

namespace Tests;

abstract class TestCase extends BaseTestCase
{
    use MarvinRabe\LaravelGraphQLTest\TestGraphQL;

    // ...
}
```

When your GraphQL endpoint is not `/graphql` you have to specify it manually:

````php
public $graphQLEndpoint = 'graphql';
````

## Usage

### Queries

You can write queries like this:

```php
$this->query('account', ['id' => 123], ['id']);
```

Note that this function returns an `\Illuminate\Foundation\Testing\TestResponse`. Therefore you might use any Laravel testing methods. For example:

```php
$this->query('account', ['id' => 123], ['id'])
  ->assertSuccessful()
  ->assertJsonFragment([
    'id' => 123
  ]);
```

With nested resources:

```php
$this->query('account', ['id' => 123], ['transactions' => ['id']]);
```

Without a third argument it will be assumed that the second one is the selection set:

```php
$this->query('accounts', ['id']);
```

When you only pass the object name, you get the `GraphQLClient` instead of the Laravel `TestResponse`:

```php
$this->query('accounts')->getGql();
```

### Mutations

Same as queries: 

```php
$this->mutation('accounts')->getGql();
$this->mutation('accounts', ['id']);
$this->mutation('accounts', ['id' => 123]); 
```

### Argument Order

For simplicity you can find the correct argument order in the following table:

|   Method |                         Arguments |       Returns |
|---------:|----------------------------------:|--------------:|
| query    | (object)                          | GraphQLClient |
| query    | (object, selectionSet)            | TestResponse  |
| query    | (object, arguments, selectionSet) | TestResponse  |
| mutation | (object)                          | GraphQLClient |
| mutation | (object, selectionSet)            | TestResponse  |
| mutation | (object, arguments, selectionSet) | TestResponse  |

## Special Cases

### Enums

Because PHP has no built in Enum support. You have to use the provided enum helper:

```php
$this->query('accounts', ['status' => $this->enum('closed')], ['id']);
```

Or create a `EnumType` manually:

```php
$this->query('accounts', ['status' => new \MarvinRabe\LaravelGraphQLTest\Scalars\EnumType('closed')], ['id']);
```

## Limitations

The `QueryBuilder` provided by this library is not safe for use in production code. It is designed for ease of use and does not comply to the GraphQL specifications fully. Use it only for testing purposes! You have been warned.
