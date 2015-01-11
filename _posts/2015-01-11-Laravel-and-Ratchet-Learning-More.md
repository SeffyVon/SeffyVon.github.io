---
layout: post
title:  "Laravel and Ratchet Learning More"
date:   2015-01-11 12:49
categories: development
tags: ["PHP Laravel", "WebSocket"]
---

Learning More of Security Configuration.

# Security
------

Config: `app/config/auth.php`, for tweaking the behavior of the authentication facilities.

By default, Laravel includes `a User model` in your `app/models` directory which may be used with the default database `Eloquent` authentication driver.

Classes used: `Hash`, `Auth`, [Link to Doc](http://laravel.com/docs/4.2/securit)

### Authenticating Users

{% highlight php startinline%}

if (Auth::attempt(array('email' => $email, 'password' => $password)))
{
    return Redirect::intended('dashboard');
}

if (Auth::check())
{
    // The user is logged in...
}

if (Auth::attempt(array('email' => $email, 'password' => $password, 'active' => 1))) // multi-condition
{
    // The user is active, not suspended, and exists.
}

{% endhighlight %}

### Accessing The Logged-In User

{% highlight php startinline%}

$email = Auth::user()->email;

{% endhighlight %}

### Encryption

`Crypt` class

{% highlight php startinline%}

$encrypted = Crypt::encrypt('secret');

$decrypted = Crypt::decrypt($encryptedValue);

/* Set mode */

Crypt::setMode('ctr');

Crypt::setCipher($cipher);

{% endhighlight %}


## Cache
------

Config: `app/config/cache.php`, appointing which cache driver you would like used by default.

Laravel supports popular caching backends like `Memcached` and `Redis` out of the box.

By default, Laravel is configured to use the `file cache` driver, which stores the serialized, cached objects in the filesystem. 

For `larger` applications, it is recommended that you use an `in-memory cache` such as Memcached or APC.


[link](http://laravel.com/docs/4.2/cache)

## Session
------
[link](http://laravel.com/docs/4.2/session)

## Database
------

## TO-DO
------

* Controller
* Vender的用法
* 看密码学！
* 融合Ratchet

## Reference
------

[Laveral Official Doc](http://laravel.com/docs/4.2/lifecycle)

[Laveral + Ratchet Blog](https://medium.com/laravel-4/laravel-4-real-time-chat-eaa550829538)