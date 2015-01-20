---
layout: post
title:  "Laravel and Ratchet 建立一个简单博客"
date:   2015-01-11 12:49
categories: Development
tags: ["PHP", "Laravel", "WebSocket"]
---

总结一些案例的主要步骤。


#Hello world 主页案例
------

* `Controller`: php artisan 生成WelcomeController 

{% highlight bash %} $ php artisan controller:make WelcomeController --only=index {% endhighlight %}

* `Controller`: 修改app/controllers/WelcomeController 中的index方法, 

{% highlight php startinline %}
    return View::make('welcome.index');
{% endhighlight %}

* `View`: app/view/welcome/index.blade.php 填写对应视图HTML

* `route`: app/routes.php中修改对应的路由

{% highlight php startinline %}
Route::get('/', function()
{
    return View::make('hello');
});
{% endhighlight %}

#RESTful案例
------
* `route`: app/routes.phpz中加入下面一句话，生成RESTful控制器，articles对应ArticlesController

{% highlight php startinline %}
Route::resource('articles','ArticlesController');
{% endhighlight %}

* `Controller`: php artisan 生成ArticlesController, 用以处理文章的CRUD（Create, Read, Update, Delete)
{% highlight bash %} $ php artisan controller:make ArticleController {% endhighlight %}

* `Controller` :修改ArticlesController.php的create动作，return View::make('article.create');

* `View` :app/view/article/create.blade.php 填写对应视图HTML，可用Form::表单构造器。为了让post转到/articles，修改`Form::open(array('url'=>'articles'))`，对应于ArticlesController的store动作。

* `Model`: app/models/Article.php

{% highlight php startinline %}
class Article extends Eloquent
{ 
    protected $fillable = array('title', 'text');
}
{% endhighlight %}

* `Model`: 准备迁移数据库，迁移文件在app/database/migrateions目录下，文件名2015_01_12_190254_create_articles_table.php

{% highlight bash %} $ php artisan migrate:make create_articles_table --create=articles {% endhighlight %}

* `Model`: 然后修改2015_01_12_190254_create_articles_table.php文件中的up()，加入column的名字

* `Model`: 真正迁移数据库，完成数据库的创建

{% highlight bash %} $ php artisan migrate {% endhighlight %}

* `Controller`,`route`: 修改ArticlesController中的store()存储文章，和show()显示文章
{% highlight php startinline %}
public function store()
{
    $article = Article::create(
    	array('title'=>Input::get('title'), 'text'=>Input::get('text'))
    ); // 使用Article model保存文章

    return Redirect::route('articles.show', array($article->id)); // 交给app/routes去找articles.show，也就是Article Controller中的show($id)方法
}

public function show($id)
{
    $article = Article::find($id);//查找文章
    return View::make('articles.show',compact('article'));//显示
}
{% endhighlight %}

* `View`:app/view/articles/show.blade.php 显示 { { $article->title } } { { $article->text} }


## Authentication 案例
-------
* `Database`：用migrate生成数据库，位于/app/database/migrate，修改其up()方法。
{% highlight bash %}

$ php artisan migrate:make create_articles_table --create=articles
$ php artisan migrate:make create_pages_table --create=pages

// 修改up()方法

$ php artisan migrate

{% endhighlight %} 

{% highlight php startinline %}

Schema::create('articles', function(Blueprint $table)
{
	$table->increments('id');
	$table->string('title');
	$table->string('slug')->nullable();
	$table->text('body')->nullable();
	$table->string('image')->nullable();
	$table->integer('user_id');
	$table->timestamps();
});

{% endhighlight %} 


* `Database`：用seed生成继承Seeder的类，位于/app/database/seed，然后修改其中的run方法。

{% highlight bash %}
$ php artisan generate:seed page//用seed生成继承Seeder的类
$ php artisan generate:seed article//用seed生成继承Seeder的类

// 修改run方法

$ php artisan generate:seed Sentry//用seed生成继承Seeder的类
//...

$ php artisan db:seed//真正的seed

{% endhighlight %} 

* `Model`:

{% highlight bash %}

php artisan generate:model article
php artisan generate:model page

{% endhighlight %} 

* `app/controllers/admin/AuthController.php` using 
{% highlight bash %} $ php artisan generate:controller admin/AuthControler {% endhighlight %} 

* Create a postLogin() function in the AuthController:

{% highlight php startinline %}

void postLogin() {
	$credentials = array('email' => Input::get('email'), 'passwords' => Input::get('password'));

	try{
		$user = Sentry::authenticate($credentials, false);

		if($user){
	    	return Redirect::route('admin.pages.index');
		}

	}catch(\Exception $e){
		return Redirect::route('admin.login')->withErrors(array('login'=>$e->getMesssage()));
	}
}


{% endhighlight %}


* `Route`:

{% highlight php startinline %}

Route::get('admin/logout', array('as' => 'admin.logout', 'uses' => 'App\Controllers\Admin\AuthController@getLogout'));
Route::get('admin/login', array('as' => 'admin.login', 'uses' => 'App\Controllers\Admin\AuthController@getLogin'));
Route::post('admin/login', array('as' => 'admin.login.post', 'uses' => 'App\Controllers\Admin\AuthController@postLogin'));

Route::group(array('prefix' => 'admin', 'before' => 'auth.admin'), function()
{
    Route::any('/', 'App\Controllers\Admin\PagesController@index');
    Route::resource('articles', 'App\Controllers\Admin\ArticlesController');
    Route::resource('pages', 'App\Controllers\Admin\PagesController');
});

{% endhighlight %}

check via:

{% highlight bash %} $ php artisan routes {% endhighlight %} 

<img src="/img/post/route.png" class="img-responsive">








### Reference
------

[Laravel 入门](http://www.golaravel.com/post/getting-started-with-laravel/)

[Laravel 4 系列入门教程](http://lvwenhan.com/laravel/398.html)
