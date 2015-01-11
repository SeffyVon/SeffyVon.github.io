---
layout: post
title:  "Laravel and Ratchet Quick Started"
date:   2015-01-11 11:49
categories: development
tags: ["Development","PHP", "Laravel", "Ratchet"]
---
Laravel PHP框架 + Rachet WebSocket 快速学习笔记。

## Life Cycle
------

1. a request enters your `public/index.php` file. 
2. `bootstrap/start.php` file will be loaded. This file creates `Application` object, which also serve as an IoC container. `enviornment detection` will be performed.
3. Internal `framework/start.php` file configures settings, and load each Service Provider's `register` method. (Each service provider binds one or more `closure` into the container, which allows you to access those bound services within your application.)
4. `app/start` files will be loaded.
5. `app/routes.php` file is loaded.
6. Request object sent to Application, which returns Response object.
7. Response object sent back to client.

## app/start
------
`app/start` files: serve as a simple place to place any "bootstrapping" code. you could register a View composer, configure your logging preferences, set some PHP settings, etc. It's totally up to you. It contains:
`global.php`:(the registration of Logger, the inclusion of your `app/filters.php`, etc.). `local.php`, `artisan.php`.

You may also do pre and post request processing by registering before, after, finish, and shutdown application events. Listeners to these events will be run `before` and `after` each request to your application. 

{% highlight php startinline%}

App::before(function($request)
{
    //
});

App::after(function($request, $response)
{
    //
});


{% endhighlight %}

You may also register a listener on the `matched` event, which is fired when an incoming request has been matched to a route but that route has not yet been executed:


{% highlight php startinline%}

Route::matched(function($route, $request)
{
    //
});

{% endhighlight %}

(The `finish` event is called after the response from your application has been sent back to the client. This is a good place to do any last minute processing your application requires. The `shutdown` event is called immediately after all of the finish event handlers finish processing, and is the last opportunity to do any work before the script terminates. Most likely, you will not have a need to use either of these events.)

## Routing
------

In `app/routes.php`file. The simplest Laravel routes consist of a URI and a Closure callback.
Basic usage including get, post, any HTTPs, etc.

{% highlight php startinline%}

Route::get('/', function()
{
    return 'Hello World';
});

Route::post('foo/bar', function()
{
    return 'Hello World';
});

Route::match(array('GET', 'POST'), '/', function() // for multiple verbs
{
    return 'Hello World';
});

Route::get('foo', array('https', function() // for HTTPS
{
    return 'Must be over HTTPS';
}));

Route::get('user/{id}', function($id) // rooting for parameter
{
    return 'User '.$id;
});

Route::get('user/{name?}', function($name = 'John') // rooting for parameter with default
{
    return $name;
});
Route::get('user/{name}', function($name)
{
    //
})
->where('name', '[A-Za-z]+'); // Regular Exp

{% endhighlight %}

Route filters provide a convenient way of limiting access to a given route, which is useful for creating areas of your site which require authentication.  There are several filters included in the Laravel framework, including an `auth` filter, an `auth.basic` filter, a `guest` filter, and a `csrf` filter. These are located in the `app/filters.php` file.



### First, defining A Route Filter

If the filter returns a response, that response is considered the response to the request and the route will not execute. Any `after` filters on the route are also cancelled.

{% highlight php startinline%}

Route::filter('old', function()
{
    if (Input::get('age') < 200)
    {
        return Redirect::to('home');
    }
});

{% endhighlight %}



### Attaching Filter(s) To A Route, A Controller Action

{% highlight php startinline%}

Route::get('user', array('before' => 'old', function() // 'before' means pre-filter.
{
    return 'You are over 200 years old!'; 
}));// Means successful. Not be directed to Home.

Route::get('user', array('before' => 'old', 'uses' => 'UserController@showProfile')); // Attaching A Filter To A Controller Action

Route::get('user', array('before' => 'auth|old', function() 
{
    return 'You are authenticated and over 200 years old!'; 
}));// multiple filters 'auth' and 'old'

{% endhighlight %}



### Could also Specifying Filter Param

{% highlight php startinline%}

Route::filter('age', function($route, $request, $response) // N.B. 3rd param is a $response
{
    //
});

Route::get('user', array('before' => 'age:200', function()
{
    return 'Hello World';
}));

{% endhighlight %}

### Registering A Class Based Filter

{% highlight php startinline%}

Route::filter('foo', 'FooFilter');

class FooFilter {

    public function filter() // This method would be called by default
    {
        // Filter logic...
    }

}

Route::filter('foo', 'FooFilter@foo'); // If do not wish to use the filter method

{% endhighlight %}

### Named Routes


{% highlight php startinline%}


Route::get('user/profile', array('as' => 'profile', function()
{
    //
}));

Route::get('user/profile', array('as' => 'profile', 'uses' => 'UserController@showProfile'));

{% endhighlight %}

### Route Model Binding

Sometimes you may need to apply filters to a group of routes.

{% highlight php startinline%}

Route::model('user', 'User');

Route::get('profile/{user}', function(User $user)
{
    //
});

{% endhighlight %}

Since we have bound the `{user}` parameter to the User model, a User instance will be injected into the route. So, for example, a request to `profile/1` will become {user} = 1, i.e. inject the `User instance` which has an `ID of 1`.

{% highlight php startinline%}

Route::model('user', 'User', function()
{
	// Pass
		//...

	// Fail 
    	throw new NotFoundHttpException;
});

{% endhighlight %}

## Request and Input
------

{% highlight php startinline%}

$name = Input::get('name');

if (Input::has('name'))
{
    //
}

$input = Input::all();

$input = Input::only('username', 'password');

$input = Input::except('credit_card');

{% endhighlight %}

### Cookies

{% highlight php startinline%}

$value = Cookie::get('name'); // Get

$response = Response::make('Hello World'); // Attaching A New Cookie To A Response

$response->withCookie(Cookie::make('name', 'value', $minutes)); // Attaching A New Cookie To A Response

Cookie::queue($name, $value, $minutes); //Queueing A Cookie For The Next Response, if you would like to set a cookie before a response has been created.

{% endhighlight %}

### Files

{% highlight php startinline%}
$file = Input::file('photo');

if (Input::hasFile('photo'))
{
    //
}

if (Input::file('photo')->isValid())
{
    //
}

Input::file('photo')->move($destinationPath);

Input::file('photo')->move($destinationPath, $fileName);

$path = Input::file('photo')->getRealPath();

$name = Input::file('photo')->getClientOriginalName();

$extension = Input::file('photo')->getClientOriginalExtension();

$size = Input::file('photo')->getSize();

$mime = Input::file('photo')->getMimeType();

{% endhighlight %}

### Request Information
{% highlight php startinline%}

$uri = Request::path();

$method = Request::method();

if (Request::isMethod('post'))
{
    //
}

if (Request::is('admin/*'))
{
    // Determining If The Request Path Matches A Pattern
}

$url = Request::url();

$value = Request::header('Content-Type');

if (Request::secure())
{
    // Determining If The Request Is Over HTTPS
}

if (Request::isJson())
{
    //Determine If The Request Has JSON Content Type
}

if (Request::format() == 'json')
{
    //Checking The Requested Response Format
}

{% endhighlight %}

## Response
------

A `Response` instance inherits from the Symfony\Component\HttpFoundation\Response class, providing a variety of methods for building HTTP responses.

{% highlight php startinline%}

Route::get('/', function()
{
    return 'Hello World';
});

/////////////

$response = Response::make($contents, $statusCode);

$response->header('Content-Type', $value);

return $response;

/////////////

return Response::view('hello')->header('Content-Type', $type);


{% endhighlight %}

### Redirects

{% highlight php startinline%}

return Redirect::to('user/login');

{% endhighlight %}

### View

There is no view in our project, but let me put a basic view in it. :smile:

{% highlight php startinline%}

Route::get('/', function()
{
    return View::make('greeting', array('name' => 'Taylor'));
});

{% endhighlight %}

## Controller
------

Controllers can group related route logic into a class, as well as take advantage of more advanced framework features such as automatic dependency injection.

Controllers are typically stored in the `app/controllers` directory, and this directory is registered in the classmap option of your `composer.json` file by default. User-defined controller which is in other directory needs to register manually. 

Route declarations are not dependent on the location of the controller class file on disk. `So, as long as Composer knows how to autoload the controller class, it may be placed anywhere you wish.`

### A basic controller class:

{% highlight php startinline%}

 class UserController extends BaseController {

    /**
     * Show the profile for the given user.
     */
    public function showProfile($id)
    {
        $user = User::find($id);

        return View::make('user.profile', array('user' => $user));
    }

 }
{% endhighlight %}

### Controller class with filter 

{% highlight php startinline%}

class UserController extends BaseController {

    /**
     * Instantiate a new UserController instance.
     */
    public function __construct()
    {
        $this->beforeFilter('auth', array('except' => 'getLogin'));

        $this->beforeFilter('csrf', array('on' => 'post'));

        $this->afterFilter('log', array('only' =>
                            array('fooAction', 'barAction')));
    }

}

//////////////////

class UserController extends BaseController {

    /**
     * Instantiate a new UserController instance.
     */
    public function __construct()
    {
        $this->beforeFilter('@filterRequests');
    }

    /**
     * Filter the incoming requests.
     */
    public function filterRequests($route, $request)
    {
        //
    }

}

{% endhighlight %}

## TO-DO
------

* 继续掌握Controller
* Vender的用法
* Security
* 看密码学！
* 融合Ratchet

## Reference
------

[Laveral Official Doc](http://laravel.com/docs/4.2/lifecycle)

[Laveral + Ratchet Blog](https://medium.com/laravel-4/laravel-4-real-time-chat-eaa550829538)

