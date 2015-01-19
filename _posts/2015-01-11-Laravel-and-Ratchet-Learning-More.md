---
layout: post
title:  "Laravel and Ratchet 建立一个简单博客"
date:   2015-01-11 12:49
categories: Development
tags: ["PHP", "Laravel", "WebSocket"]
---

新手文档，建立一个简单博客。


#Hello world 主页
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

#RESTful
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

















### Reference
------

[GoLaveral 中文翻译文档](http://www.golaravel.com/post/getting-started-with-laravel/)
