Laravel Multicache
=========

> Adds array caching to Laravels cache drivers and custom drivers.

[![Build Status](http://img.shields.io/travis/pulkitjalan/laravel-multicache.svg?style=flat-square)](https://travis-ci.org/pulkitjalan/laravel-multicache)
[![Scrutinizer Code Quality](http://img.shields.io/scrutinizer/g/pulkitjalan/laravel-multicache/master.svg?style=flat-square)](https://scrutinizer-ci.com/g/pulkitjalan/laravel-multicache/)
[![Coverage Status](https://img.shields.io/scrutinizer/coverage/g/pulkitjalan/laravel-multicache/master.svg?style=flat-square)](https://scrutinizer-ci.com/g/pulkitjalan/laravel-multicache/code-structure/master)
[![License](http://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](http://www.opensource.org/licenses/MIT)
[![Latest Version](http://img.shields.io/packagist/v/pulkitjalan/laravel-multicache.svg?style=flat-square)](https://packagist.org/packages/pulkitjalan/laravel-multicache)
[![Total Downloads](https://img.shields.io/packagist/dt/pulkitjalan/laravel-multicache.svg?style=flat-square)](https://packagist.org/packages/pulkitjalan/laravel-multicache)

## Requirements

Laravel >= 5.1

PHP >= 5.5.9

## Installation

Install via composer - edit your `composer.json` to require the package.

```js
"require": {
    "pulkitjalan/laravel-multicache": "0.1.*"
}
```

Then run `composer update` in your terminal to pull it in.

Now add the following to the `providers` array in your `config/app.php`

```php
PulkitJalan\Cache\Providers\MultiCacheServiceProvider::class
```

## Usage

Any existing cache drivers and custom drivers will have access to the following methods:

```php
    /**
     * Determine if an array of items exists in the cache.
     *
     * @param  array  $keys
     * @return array
     */
    public function hasMulti(array $keys);

    /**
     * Retrieve an array of items from the cache by keys.
     *
     * @param  array  $keys
     * @param  mixed  $default
     * @return array
     */
    public function getMulti(array $keys, $default = null);

    /**
     * Retrieve an array of items from the cache and delete them.
     *
     * @param  array  $keys
     * @param  mixed  $default
     * @return array
     */
    public function pullMulti(array $keys, $default = null);

    /**
     * Store an array of items in the cache.
     *
     * @param  array  $items
     * @param  \DateTime|int  $minutes
     * @return void
     */
    public function putMulti(array $items, $minutes);

    /**
     * Store an array of items in the cache if the key does not exist.
     *
     * @param  array  $items
     * @param  \DateTime|int  $minutes
     * @return bool
     */
    public function addMulti(array $items, $minutes);

    /**
     * Store an array of items in the cache indefinitely.
     *
     * @param  array  $items
     * @return void
     */
    public function foreverMulti(array $items);

    /**
     * Get an array of items from the cache, or store the default value.
     *
     * @param  array  $keys
     * @param  \DateTime|int  $minutes
     * @param  \Closure  $callback
     * @return mixed
     */
    public function rememberMulti(array $keys, $minutes, Closure $callback);

    /**
     * Get an array of items from the cache, or store the default value forever.
     *
     * @param  array  $keys
     * @param  \Closure  $callback
     * @return mixed
     */
    public function searMulti(array $keys, Closure $callback);

    /**
     * Get an array of items from the cache, or store the default value forever.
     *
     * @param  array  $keys
     * @param  \Closure  $callback
     * @return mixed
     */
    public function rememberForeverMulti(array $keys, Closure $callback);

    /**
     * Remove an array of items from the cache.
     *
     * @param  array  $keys
     * @return bool
     */
    public function forgetMulti(array $keys);
```
  
Most of the existing methods like `has`, `get`, `put`... will also accept an array and automatically run the relevant "Multi" function.

## Examples

Below are a few examples of how to use the functions and what they return.

### Get

```php
$keys = [
    'one', // exists
    'two', // does not exist
    'three', // exists
];

Cache::getMulti($keys, 'Nooo');

// or

Cache::get($keys, 'Nooo');

// will return: ['one' => 'data', 'two' => 'Nooo', 'three' => 'data']
```

### Put

The `put` method works a little differently to `putMulti`. Where `putMulti` accepts a key value array as the first parameter and the number of minutes to store for as the second parameter, the `put` method takes two separate arrays as the first two parameters and minutes as the third parameter.

Eg:

```php
$data = [
  'key1' => 'value1',
  'key2' => 'value2',
  'key3' => 'value3',
];

Cache::putMulti($data, 10);

// or

Cache::put(array_keys($data), array_values($data), 10);
```

## How does it work?

For any driver that does not have an underlying `multi` method, the methods will call the non-multi version of the method for every item in the array.

For example, if we are using the `apc` driver, which does not offer its own `getMulti` method, then the `get` method will be called for every item in the array.

Now if we are using the `memcached` driver, which does have its own `getMulti` method, then that method will be called once and the data returned.

Currently the `MemcachedStore`, `DatabaseStore`, `RedisStore` and the `ArrayStore` are the only ones to offer their own `Multi` methods. **More to come soon...**
