# 查询构造器

- [简介](#introduction)
- [Select 操作](#selects)
- [Join 操作](#joins)
- [高级 Where 条件判断](#advanced-wheres)
- [聚合操作](#aggregates)
- [原始表达式](#raw-expressions)
- [Insert 操作](#inserts)
- [Update 操作](#updates)
- [Delete 操作](#deletes)
- [Union 操作](#unions)
- [查询缓存](#caching-queries)

<a name="introduction"></a>
## 简介

数据库查询构造器提供了一个方便、流利的接口来创建和执行数据库查询的操作. 它能被用于执行你应用中用到的大多数数据库操作, 并且能够在所有受支持的数据库系统上工作.

> **注意:** Laravel 查询构造器始终使用 PDO 参数绑定的方式来保护你的应用免受 SQL 注入的攻击, 所以没有必要再过滤一遍传入的绑定字符串.

<a name="selects"></a>
## Select 操作

**从一个表中检索所有行**

	$users = DB::table('users')->get();

	foreach ($users as $user)
	{
		var_dump($user->name);
	}

**从一个表中检索单一行**

	$user = DB::table('users')->where('name', 'John')->first();

	var_dump($user->name);

**检索单一行中的单一列**

	$name = DB::table('users')->where('name', 'John')->pluck('name');

**检索一个关于列值的列表**

	$roles = DB::table('roles')->lists('title');

该方法将返回一个关于 'roles' 表中的 'title' 列的值的数组. 你也可以指定自定义关键列来作为返回的结果:

	$roles = DB::table('roles')->lists('title', 'name');

**指定 Select 子句**

	$users = DB::table('users')->select('name', 'email')->get();

	$users = DB::table('users')->distinct()->get();

	$users = DB::table('users')->select('name as user_name')->get();

**添加 Select 子句到一个已存在的查询中去**

	$query = DB::table('users')->select('name');

	$users = $query->addSelect('age')->get();

**使用 Where 操作符**

	$users = DB::table('users')->where('votes', '>', 100)->get();

**Or 语句**

	$users = DB::table('users')
	                    ->where('votes', '>', 100)
	                    ->orWhere('name', 'John')
	                    ->get();

**使用 Where Between 子句**

	$users = DB::table('users')
	                    ->whereBetween('votes', array(1, 100))->get();

**通过一个数组来使用 Where In 子句**

	$users = DB::table('users')
	                    ->whereIn('id', array(1, 2, 3))->get();

	$users = DB::table('users')
	                    ->whereNotIn('id', array(1, 2, 3))->get();

**使用 Where Null 子句来找出值为空的记录**

	$users = DB::table('users')
	                    ->whereNull('updated_at')->get();

**Order By, Group By, 和 Having 子句**

	$users = DB::table('users')
	                    ->orderBy('name', 'desc')
	                    ->groupBy('count')
	                    ->having('count', '>', 100)
	                    ->get();

**偏移量(Offset) & 限定值(Limit)**

	$users = DB::table('users')->skip(10)->take(5)->get();

<a name="joins"></a>
## Join 操作

查询构造器也可以用来创建 join 语句. 示例如下:

**基础 Join 语句**

	DB::table('users')
	            ->join('contacts', 'users.id', '=', 'contacts.user_id')
	            ->join('orders', 'users.id', '=', 'orders.user_id')
	            ->select('users.id', 'contacts.phone', 'orders.price');

你也可以指定更为高级的 join 子句:

	DB::table('users')
	        ->join('contacts', function($join)
	        {
	        	$join->on('users.id', '=', 'contacts.user_id')->orOn(...);
	        })
	        ->get();

<a name="advanced-wheres"></a>
## 高级 Where 用法

有时候你需要创建更为高级的 where 子句就如 "where exists" 或者嵌套的参数分组. Laravel 查询构造器一样可以处理这些:

**参数分组**

	DB::table('users')
	            ->where('name', '=', 'John')
	            ->orWhere(function($query)
	            {
	            	$query->where('votes', '>', 100)
	            	      ->where('title', '<>', 'Admin');
	            })
	            ->get();

上述查询将产生如下 SQL 语句:

	select * from users where name = 'John' or (votes > 100 and title <> 'Admin')

**Exist 语句**

	DB::table('users')
	            ->whereExists(function($query)
	            {
	            	$query->select(DB::raw(1))
	            	      ->from('orders')
	            	      ->whereRaw('orders.user_id = users.id');
	            })
	            ->get();

上述查询将产生如下 SQL 语句:

	select * from users
	where exists (
		select 1 from orders where orders.user_id = users.id
	)

<a name="aggregates"></a>
## 聚合操作

查询构造器也能提供诸多的聚合操作方法, 例如 `count`, `max`, `min`, `avg` 和 `sum`.

**使用聚合方法**

	$users = DB::table('users')->count();

	$price = DB::table('orders')->max('price');

	$price = DB::table('orders')->min('price');

	$price = DB::table('orders')->avg('price');

	$total = DB::table('users')->sum('votes');

<a name="raw-expressions"></a>
## 原始表达式

有时你也许需要在查询中使用原始的 SQL 表达式. 这些表达式将以字符串形式插入到查询中去, 所以一定要确保不要产生 SQL 注入点! 要创建原始表达式, 你可以使用 `DB::raw` 方法:

**使用原始表达式**

	$users = DB::table('users')
	                     ->select(DB::raw('count(*) as user_count, status'))
	                     ->where('status', '<>', 1)
	                     ->groupBy('status')
	                     ->get();

**增加或减少一列的值**

	DB::table('users')->increment('votes');

	DB::table('users')->decrement('votes');

<a name="inserts"></a>
## Insert 操作

**向表中插入记录**

	DB::table('users')->insert(
		array('email' => 'john@example.com', 'votes' => 0)
	);

如果该表有一个递增id, 使用 `insertGetId` 方法来插入记录并取得该递增的id:

**向有递增id的表中插入记录**

	$id = DB::table('users')->insertGetId(
		array('email' => 'john@example.com', 'votes' => 0)
	);

> **注意:** 当使用 PostgreSQL 的时候, insertGetId 方法需要确保递增列的列名为 id 才行.

**向表中插入多行记录**

	DB::table('users')->insert(array(
		array('email' => 'taylor@example.com', 'votes' => 0),
		array('email' => 'dayle@example.com', 'votes' => 0),
	));

<a name="updates"></a>
## Update 操作

**更新表中的记录**

	DB::table('users')
	            ->where('id', 1)
	            ->update(array('votes' => 1));

<a name="deletes"></a>
## Delete 操作

**删除表中的记录**

	DB::table('users')->where('votes', '<', 100)->delete();

**删除表中所有记录**

	DB::table('users')->delete();

**清空一个表**

	DB::table('users')->truncate();

<a name="unions"></a>
## Union 操作

查询构造器也提供了一个快捷方式来将两个查询联合起来:

**执行联合查询**

	$first = DB::table('users')->whereNull('first_name');

	$users = DB::table('users')->whereNull('last_name')->union($first)->get();

`unionAll` 方法也是可用的, 并且和 `union` 方法有着相同的方法签名.

<a name="caching-queries"></a>
## 查询缓存

通过 `remember` 方法你可以很方便的缓存查询结果:

**缓存一个查询的结果**

	$users = DB::table('users')->remember(10)->get();

在这个例子中, 查询的结果将被缓存10分钟. 一旦结果已缓存, 查询将不会被执行, 而查询结果将由你应用中指定的默认缓存驱动中取出.