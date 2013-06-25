# 数据库基本用法

- [配置](#configuration)
- [执行查询](#running-queries)
- [数据库事务](#database-transactions)
- [数据库连接管理](#accessing-connections)
- [查询 log](#query-logging)

<a name="configuration"></a>
## 配置

Laravel makes connecting with databases and running queries extremely simple. The database configuration file is `app/config/database.php`. In this file you may define all of your database connections, as well as specify which connection should be used by default. Examples for all of the supported database systems are provided in this file.

Laravel 的数据库操作十分简单，在配置文件 `app/config/database.php` 中定义你使用的数据库的信息， 所有支持的数据库都用配置样例供参考。 注意要指定一个默认使用的数据库连接信息。

Currently Laravel supports four database systems: MySQL, Postgres, SQLite, and SQL Server.

目前支持的数据库： MySQL, Postgres, SQLite, 和 SQL Server 。

<a name="running-queries"></a>
## 执行查询

Once you have configured your database connection, you may run queries using the `DB` class.

配置好以后，就可以用 `DB` 类进行数据库查询了:

**执行 selecct 查询**

	$results = DB::select('select * from users where id = ?', array(1));

The `select` method will always return an `array` of results.

`select` 方法返回查询结果的 `array` 数组。

**insert 插入记录**

	DB::insert('insert into users (id, name) values (?, ?)', array(1, 'Dayle'));

**update 更新记录**

	DB::update('update users set votes = 100 where name = ?', array('John'));

**delete 删除记录**

	DB::delete('delete from users');

> **注意:** `update` 和 `delete` 方法返回操作影响的记录数。

**通用执行 SQL 的方法**

	DB::statement('drop table users');

`DB::listen` 方法可以创建查询事件的监听器:

**监听查询事件**

	DB::listen(function($sql, $bindings, $time)
	{
		//
	});

<a name="database-transactions"></a>
## 数据库事务

To run a set of operations within a database transaction, you may use the `transaction` method:

`transaction` 方法可以实现若干操作的事务性：

	DB::transaction(function()
	{
		DB::table('users')->update(array('votes' => 1));

		DB::table('posts')->delete();
	});

<a name="accessing-connections"></a>
## 数据库连接管理

When using multiple connections, you may access them via the `DB::connection` method:

如果需要使用多个数据库连接，可以通过 `DB::connecton` 切换不同连接：

	$users = DB::connection('foo')->select(...);

You may also access the raw, underlying PDO instance:

也可以通过 `getPdo` 方法使用原生的 PDO  连接:

	$pdo = DB::connection()->getPdo();

Sometimes you may need to reconnect to a given database:

有时候可能需要重连数据库：

	DB::reconnect('foo');

<a name="query-logging"></a>
## 查询 log

By default, Laravel keeps a log in memory of all queries that have been run for the current request. However, in some cases, such as when inserting a large number of rows, this can cause the application to use excess memory. To disable the log, you may use the `disableQueryLog` method:

默认情况下，laravel 会保存当前请求的所有查询。某些情况下， 比如需要插入大量的记录时可能导致一些内存耗光，这时候可以用 `disableQueryLog` 方法禁用该功能：

	DB::connection()->disableQueryLog();