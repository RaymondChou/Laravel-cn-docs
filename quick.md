# Laravel 快速入门

- [安装](#installation)
- [路由](#routing)
- [创建一个视图](#creating-a-view)
- [创建一个迁移](#creating-a-migration)
- [Eloquent ORM](#eloquent-orm)
- [数据的展示](#displaying-data)

<a name="installation"></a>
## 安装

要安装 Laravel 框架, 先在其位于 [Github上的版本库](https://github.com/laravel/laravel/archive/master.zip) 下载一份框架的拷贝. 下一步, 在 [安装 Composer](http://getcomposer.org) 之后, 在你的项目根目录下执行 `composer install` 命令. 该命令将会下载和安装框架所需的依赖包.

你也可以在终端调用该命令来安装 Laravel:

	composer create-project laravel/laravel

在安装了框架之后, 检查一下该项目来让你自己熟悉其目录结构. `app` 目录包含 `views`, `controllers`, and `models` 等这些文件夹. 你应用中的大部分代码将会被存放于该目录的某个地方. 你也可以探索一下 `app/config` 目录以及一些对你可用的配置项.

<a name="routing"></a>
## 路由

让我们以创建第一个路由来作为开始. 在 Laravel 中, 最简单的路由是一个指向闭包的路由. 打开 `app/routes.php` 文件然后在底部添加如下路由:

	Route::get('users', function()
	{
		return 'Users!';
	});

现在, 如果你在浏览器里面打开 `/users` 这个路由, 你将会看到作为响应内容而显示的 `Users!` 字样. 好极了! 你已经创建了你的第一个路由.

路由也可以被附加到控制器类上. 例如:

	Route::get('users', 'UserController@getIndex');

该路由告诉框架到 `/users` 的请求应该调用位于 `UserController` 类中的 `getIndex` 方法. 要了解更多关于控制器路由的信息, 参阅 [控制器文档](/docs/controllers).

<a name="creating-a-view"></a>
## 创建一个视图

下一步,我们将会创建一个简单的视图来展示我们的用户数据. 视图文件存放于 `app/views` 目录下, 包含你的应用所需的HTML. 我们将把新创建的视图: `layout.blade.php` 和 `users.blade.php`放置到该目录下. 首先, 让我们创建 `layout.blade.php` 文件:

	<html>
		<body>
			<h1>Laravel Quickstart</h1>

			@yield('content')
		</body>
	</html>

然后, 我们再创建 `users.blade.php` 视图:

	@extends('layout')

	@section('content')
		Users!
	@stop

这里的部分语法可能会使你感到十分奇怪. 这是因为我们正在使用 Laravel 提供的模板系统: Blade. Blade 非常之快, 因为它仅使用很少的一些正则表达式来将你的模板编译为纯PHP语言. Blade 提供强大的功能诸如模板继承和一些标准PHP控制结构方面的语法糖, 比如 `if` 和 `for`. 参阅 [Blade文档](/docs/templates) 来了解更多细节.

现在, 我们有了自己的视图, 让我们在 `/users` 路由中来返回它. 不再返回 `Users!` 字样, 而是用视图来替代:

	Route::get('users', function()
	{
		return View::make('users');
	});

太秒了! 现在你已经建立了一个简单的视图, 它继承自一个布局(视图). 接下来, 让我们开始在数据库层面上的工作.

<a name="creating-a-migration"></a>
## 创建一个迁移

为了创建表来储存我们的数据, 我们将使用 Laravel 的迁移系统. 迁移允许你条理清晰地定义对数据库的修改, 并且很容易地与你团队里的其他人共享它们.

首先, 让我们来配置一个数据库连接. 你可以在 `app/config/database.php` 文件中配置你所有的数据库连接. 默认情况下, Laravel 被配置为使用 SQLite 数据库, 并且一个 SQLite 数据库被存放于 `app/database` 目录下. 如果你想的话, 你也可以修改 `driver` 选项到 `mysql` 然后在数据库配置文件中配置 `mysql` 所需的连接认证信息.

下一步, 要创建迁移的话, 我们将用到 [Artisan CLI](/docs/artisan). 在你项目的根目录下运行如下命令:

	php artisan migrate:make create_users_table

然后, 在 `app/database/migrations` 目录下找到已生成的迁移文件. 该文件包含一个有两个方法的类: `up` 和 `down`. 在 `up` 方法中, 你需要作出所希望的对数据表的更改, 在 `down` 方法中则简单地反向操作即可.

我们来定义一个如下所示的迁移:

	public function up()
	{
		Schema::create('users', function($table)
		{
			$table->increments('id');
			$table->string('email')->unique();
			$table->string('name');
			$table->timestamps();
		});
	}

	public function down()
	{
		Schema::drop('users');
	}

接下来, 我们可以在终端通过 `migrate` 命令来执行我们的迁移. 在项目根目录下执行如下命令即可:

	php artisan migrate

如果你希望回滚一次迁移, 你可以执行 `migrate:rollback` 命令. 现在我们有了一个数据表了, 让我们开始往里面填数据吧!!

<a name="eloquent-orm"></a>
## Eloquent ORM

Laravel 附带了一个极好的ORM: Eloquent. 如果你用过 Ruby on Rails 框架的话, 你会发现 Eloquent 比较亲切, 因为他在数据库交互操作上遵循了 ActiveRecord ORM 设计.

首先, 让我们定义一个模型. 一个 Eloquent 模型能被用于查询一个与之相关联的数据表, 也即代表数据表中给出的一条记录. 别担心, 很快它就会变得有意义的! 模型通常被放置于 `app/models` 目录下. 让我们在该目录内定义一个 `User.php` 模型就像这样:

	class User extends Eloquent {}

需要注意的是我们并不需要告诉 Eloquent 需要用到哪个表. Eloquent 有许多的规约, 其中一条就是使用与模型完全一样的名字作为模型对应的数据库表的名字. 真方便!

使用你偏好的数据库管理工具, 插入一些记录到 `users` 表中去, 然后我们将使用 Eloquent 来检索它们并传递给视图.

现在, 让我们把 `/users` 路由修改为如下样子:

	Route::get('users', function()
	{
		$users = User::all();

		return View::make('users')->with('users', $users);
	});

让我们来逐步分析一下该路由. 首先, 位于 `User` 模型上的 `all` 方法将从 `users` 表中检索出所有的行. 然后, 我们通过 `with` 方法将这些记录传递给视图. `with` 方法接收一个键和一个值作为参数, 使得一段数据在视图中变得可用.

好极了. 现在我们已经准备好在视图中展示这些用户了!

<a name="displaying-data"></a>
## 数据的展示

现在, 我们已经让 `users` 在视图中可用了. 我们可以用此种方式来展示它们:

	@extends('layout')

	@section('content')
		@foreach($users as $user)
			<p>{{ $user->name }}</p>
		@endforeach
	@stop

你可能会很好奇上哪儿去找 `echo` 语句. 当我们使用 Blade 模板引擎的时候, 你可以将数据放到双花括号内部来展示他们. 很简单. 现在你可以在浏览器里打开 `/users` 路由并看见你的用户的名字展示在响应内容里面了.

这仅仅是个开始. 在这个教程里面, 你看见了关于 Laravel 框架非常基础的一面, 但是还有非常多激动人心的知识值得去学习. 持续阅读文档, 并深入钻研 [Eloquent](/docs/eloquent) 和 [Blade](/docs/templates) 的那些对你来说强大的特征. 或者, 你也许会更加感兴趣于 [队列](/docs/queues) 和 [单元测试](/docs/testing), 然后, 你也许想通过 [IoC 容器](/docs/ioc) 在架构方面大秀一下身手, 全都取决于你!
