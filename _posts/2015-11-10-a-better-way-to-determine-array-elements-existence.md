---
title:    A better way to determine array elements existence
layout:   post
category: tutorial-tips
tags:     [php, skill]
feature:  /assets/img/i-love-php.jpg
---

In a project PHP, you have implement a method get attribute with default value.
Someone using `isset()` function to implement it, other people using
`array_key_exists()` function. What is better way?

<!--more-->

## The story
In a project PHP, you have implement a method get attribute with default value.
This snippet code is your issue.

```php
class Foo
{
    /**
     * @var array
     */
    protected $attributes = [];

    /**
     * Constructor
     *
     * @param array $attributes
     */
    public function __construct($attributes = [])
    {
        $this->attributes = $attributes;
    }

    /**
     * Get attribute
     *
     * @param string $name    The name of attribute
     * @param mixed  $default The default value of attribute if it not existed
     * @return mixed The value of default value of attribute
     */
    public function getAttribute($name, $default = null)
    {
        // Determine array attributes existence
        // If attribute existed then return it's value
        // else return default value
    }
}
```

Someone using `isset()` function ti implement it as the following code.

```php
    public function getAttribute($name, $default = null)
    {
        return isset($this->attributes[$name]) ? $this->attributes[$name] : $default;
    }
```

This code works fine, but, it works fine for most of cases only. In some other cases
(and it's quite often actually), using this code  to check the existence of
an array element can be very **DANGEROUS**.

## What’s wrong with isset()
Perhaps `isset()` is one of the most frequently used function that do a very frequent task:
determine if a variable has been set. It is simple, and more importantly is **FAST**,
is **VERY FAST**. However, the returned result of `isset()` can be misleading sometimes.

According to the PHP's manual: [`isset()` -- Determine if a variable is set AND is not NULL][isset-doc]

So the case that the `isset()` cause you danger is: the element does exist in
the array but it is set **NULL**. i.e. `$Arr['MyElemenet'] = NULL;` In this case, `isset()`
always return **FALSE**. Professional programmers should be aware of this.

## The right solution: array_key_exists()
The right way to check  if an element exists in an array is to use `array_key_exists()`.
The `array_key_exists()` will tell if the given key or index has been "created"
in the array regardless the value of the element. So to tell if elements 'MyElement'
exists in the array $Arr, we should use this:

```php
    public function getAttribute($name, $default = null)
    {
        return array_key_exists($name, $this->attributes) ? $this->attributes[$name] : $default;
    }
```

## Why array_key_exists() still sucks?
However, `array_key_exits()` still sucks. Yes, it's more reliable than `isset()`,
but it's **SLOW**.  We benchmarked the `array_key_exists()` and `isset()` methods
as shown below and find that `array_key_exists()` is almost 5 times slower than `isset()`.

## A better way
To take the speed advantage of `isset()` while keeping the reliable result from `array_key_exists()`,
we combined the both: Usually an element being set **NULL** is a rare case, so in most of the time,
`isset()` is still reliable. When `isset()` fails, we should do an additional checking by `array_key_exists()`
to double confirm that the key really doesn't exist. It turns out that the below code works the best:

```php
    public function getAttribute($name, $default = null)
    {
        return isset($this->attributes[$name]) || array_key_exist($name, $this->attributes) ? $this->attributes[$name] : $default;
    }
```

The beauty of PHP (also many other modem languages) is that it doesn't require
the whole conditional statement being fully parsed. So the PHP engine actually only
evaluate the result of `isset()`. If `isset()` returns **FALSE**, it then evaluate `array_key_exists()`.
If `isset()` returns **TRUE**, `array_key_exists()` is never evaluated.
That's saying the sequence of the two conditions cannot be reversed.

## Benchmarking
We did a simple benchmarking base on the `isset()`, `array_key_exists()` and the combined method,
and the result of the combined method is very promising.

_Source [zome off][zome-off]


[isset-doc]: http://php.net/manual/en/function.isset.php
[zome-off]: http://www.zomeoff.com/php-fast-way-to-determine-a-key-elements-existance-in-an-array/