# Schema 构建起

- [介绍](#introduction)
- [创建和删除表](#creating-and-dropping-tables)
- [添加列](#adding-columns)
- [重命名列](#renaming-columns)
- [删除列](#dropping-columns)
- [检查是否存在](#checking-existence)
- [添加索引](#adding-indexes)
- [外键](#foreign-keys)
- [删除索引](#dropping-indexes)
- [存储引擎](#storage-engines)

<a name="introduction"></a>
## 介绍

The Laravel `Schema` class provides a database agnostic way of manipulating tables. It works well with all of the databases supported by Laravel, and has a unified API across all of these systems.

laravel 的 `Schema` 类提供一套操作 laravel 所支持的所有数据库的封装.

<a name="creating-and-dropping-tables"></a>
## 创建和删除表

To create a new database table, the `Schema::create` method is used:

`Schema::create` 用来创建一个数据库表:

	Schema::create('users', function($table)
	{
		$table->increments('id');
	});

The first argument passed to the `create` method is the name of the table, and the second is a `Closure` which will receive a `Blueprint` object which may be used to define the new table.

`create` 方法的第一个参数是表明，第二个参数是一个闭包 `Closure` 函数，返回定义表结构的 `Blueprint` 对象。

To rename an existing database table, the `rename` method may be used:

`rename` 方法重命名已经存在的表:

	Schema::rename($from, $to);

To specify which connection the schema operation should take place on, use the `Schema::connection` method:

`Schema::connection` 方法可以指定操作的数据库连接:

	Schema::connection('foo')->create('users', function($table)
	{
		$table->increments('id'):
	});

To drop a table, you may use the `Schema::drop` method:

`Schema::drop` 和 `Schema::dropIfExists` 用来删除表:

	Schema::drop('users');

	Schema::dropIfExists('users');

<a name="adding-columns"></a>
## 添加列

To update an existing table, we will use the `Schema::table` method:

如果要更新一个表的结构，可以使用 `Schema::table` 方法:

	Schema::table('users', function($table)
	{
		$table->string('email');
	});

The table builder contains a variety of column types that you may use when building your tables:

表构建器支持以下列类型：

命令  | 描述
------------- | -------------
`$table->increments('id');`  |  Incrementing ID to the table (primary key).
`$table->string('email');`  |  VARCHAR equivalent column
`$table->string('name', 100);`  |  VARCHAR equivalent with a length
`$table->integer('votes');`  |  INTEGER equivalent to the table
`$table->bigInteger('votes');`  |  BIGINT equivalent to the table
`$table->smallInteger('votes');`  |  SMALLINT equivalent to the table
`$table->float('amount');`  |  FLOAT equivalent to the table
`$table->decimal('amount', 5, 2);`  |  DECIMAL equivalent with a precision and scale
`$table->boolean('confirmed');`  |  BOOLEAN equivalent to the table
`$table->date('created_at');`  |  DATE equivalent to the table
`$table->dateTime('created_at');`  |  DATETIME equivalent to the table
`$table->time('sunrise');`  |  TIME equivalent to the table
`$table->timestamp('added_on');`  |  TIMESTAMP equivalent to the table
`$table->timestamps();`  |  Adds **created\_at** and **updated\_at** columns
`$table->softDeletes();`  |  Adds **deleted\_at** column for soft deletes
`$table->text('description');`  |  TEXT equivalent to the table
`$table->binary('data');`  |  BLOB equivalent to the table
`$table->enum('choices', array('foo', 'bar'));` | ENUM equivalent to the table
`->nullable()`  |  Designate that the column allows NULL values
`->default($value)`  |  Declare a default value for a column
`->unsigned()`  |  Set INTEGER to UNSIGNED

If you are using the MySQL database, you may use the `after` method to specify the order of columns:

使用的是 MySQL 的话，还可以通过 `after` 方法指定列的顺序:

** after 方法指定列顺序**

	$table->string('name')->after('email');

<a name="renaming-columns"></a>
## 重命名列

To rename a column, you may use the `renameColumn` method on the Schema builder:

用 `renameColumn` 方法重命名列：

**重命名列**

	Schema::table('users', function($table)
	{
		$table->renameColumn('from', 'to');
	});

> **注意:** 不支持重命名 `enum` 类型的列

<a name="dropping-columns"></a>
## 删除列

**删除 users 表的列 votes**

	Schema::table('users', function($table)
	{
		$table->dropColumn('votes');
	});

**删除多列**

	Schema::table('users', function($table)
	{
		$table->dropColumn('votes', 'avatar', 'location');
	});

<a name="checking-existence"></a>
## 检查是否存在

You may easily check for the existence of a table or column using the `hasTable` and `hasColumn` methods:

可以使用 `hasTable` 检查表、`hasColumn` 检查列是否存在：

**检查表**

	if (Schema::hasTable('users'))
	{
		//
	}

**检查列**

	if (Schema::hasColumn('users', 'email'))
	{
		//
	}

<a name="adding-indexes"></a>
## 添加索引

The schema builder supports several types of indexes. There are two ways to add them. First, you may fluently define them on a column definition, or you may add them separately:

schema 构建器支持多种类型的索引，可以通过在创建表的列定义时指定索引或者另外单独添加索引。

**定义列时指定索引**

	$table->string('email')->unique();

可单独添加的索引如下:

命令  | 描述
------------- | -------------
`$table->primary('id');`  |  添加主键
`$table->primary(array('first', 'last'));`  |  添加复合键
`$table->unique('email');`  |  添加唯一索引
`$table->index('state');`  |  添加一般索引

<a name="foreign-keys"></a>
## 外键

Laravel also provides support for adding foreign key constraints to your tables:

laravel 也支持外键:

**添加外键**

	$table->foreign('user_id')->references('id')->on('users');

In this example, we are stating that the `user_id` column references the `id` column on the `users` table.

上面我们把 users 表的 id 关联为当前表 user_id 列的外键(?译者注：不知道咋描述，真累)。

You may also specify options for the "on delete" and "on update" actions of the constraint:

还可以指定 `on delete` 和 `on update` 的选项: 

	$table->foreign('user_id')
          ->references('id')->on('users')
          ->onDelete('cascade');

To drop a foreign key, you may use the `dropForeign` method. A similar naming convention is used for foreign keys as is used for other indexes:

`dropForeign` 方法可以删除外键，[?译者注: 这句没明白] A similar naming convention is used for foreign keys as is used for other indexes:

	$table->dropForeign('posts_user_id_foreign');

<a name="dropping-indexes"></a>
## 删除索引

To drop an index you must specify the index's name. Laravel assigns a reasonable name to the indexes by default. Simply concatenate the table name, the names of the column in the index, and the index type. Here are some examples:

删除索引需要指定索引名，索引名遵循规范： 表名_列名_索引类型: 

命令  | 描述
------------- | -------------
`$table->dropPrimary('users_id_primary');`  |  删除 users 表的 id 主键
`$table->dropUnique('users_email_unique');`  |  删除 users 表的 email 列的唯一索引
`$table->dropIndex('geo_state_index');`  |  删除 geo 表的 state 列的索引

<a name="storage-engines"></a>
## 存储引擎

To set the storage engine for a table, set the `engine` property on the schema builder:

创建表的时候设置 `engine` 属性来指定存储引擎:

    Schema::create('users', function($table)
    {
        $table->engine = 'InnoDB';

        $table->string('email');
    });
