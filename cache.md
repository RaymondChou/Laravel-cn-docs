# Cache

- [配置](#configuration)
- [Cache 用法](#cache-usage)
- [自增和自减](#increments-and-decrements)
- [多段 Cache](#cache-sections)
- [数据库 Cache](#database-cache)

<a name="configuration"></a>
## 配置

Laravel provides a unified API for various caching systems. The cache configuration is located at `app/config/cache.php`. In this file you may specify which cache driver you would like used by default throughout your application. Laravel supports popular caching backends like [Memcached](http://memcached.org) and [Redis](http://redis.io) out of the box.

Laravel 的 Cache 支持多种后端存储系统，比如 [Memcached](http://memcached.org) 和 [Redis](http://redis.io)，通过同一套 API 即可进行操作，在 `app/config/cache.php` 中配置后端存储的详细信息即可。

The cache configuration file also contains various other options, which are documented within the file, so make sure to read over these options. By default, Laravel is configured to use the `file` cache driver, which stores the serialized, cached objects in the filesystem. For larger applications, it is recommended that you use an in-memory cache such as Memcached or APC.

Cache 配置文件中提供各种选项， 在配置文件中对应的位置有详细说明。默认情况下， laravel 使用文件 `file` 做 cache 的后端存储，在文件系统中保存序列化的 cache 数据。在比较大的系统中，推荐使用基于内存的 cache 存储，比如 Memcached, 和 APC 。

<a name="cache-usage"></a>
## Cache 用法

**保存数据到 Cache**

	Cache::put('key', 'value', $minutes);

**添加数据到 Cache： 如果对应的 key 已经保存了数据，则返回失败**

	Cache::add('key', 'value', $minutes);

**检查某个 key 的数据是否存在**

	if (Cache::has('key'))
	{
		//
	}

**从 Cache 中获得数据**

	$value = Cache::get('key');

**Cache 中获取数据时，指定取不到数据时的默认值**

	$value = Cache::get('key', 'default');

	$value = Cache::get('key', function() { return 'default'; });

**保存不过期的数据**

	Cache::forever('key', 'value');

Sometimes you may wish to retrieve an item from the cache, but also store a default value if the requested item doesn't exist. You may do this using the `Cache::remember` method:

有时候在 Cache 中取不到某个 key 对应数据时，可能需要保存一个值，可以使用 `Cache::remember` 来实现:

	$value = Cache::remember('users', $minutes, function()
	{
		return DB::table('users')->get();
	});

You may also combine the `remember` and `forever` methods:

`rememberForever` 方法是在 remember 的基础上保存不过期的数据:

	$value = Cache::rememberForever('users', function()
	{
		return DB::table('users')->get();
	});

Note that all items stored in the cache are serialized, so you are free to store any type of data.

注意保存在 cache 的数据是自动被序列化了的, 你可以保存任意类型的数据。

**删除某个 key 的数据**

	Cache::forget('key');

<a name="increments-and-decrements"></a>
## 自增和自减

All drivers except `file` and `database` support the `increment` and `decrement` operations:

除了文件 `file` 和 数据库 `database` 类型的后端存储以外，其他都支持自增和自减操作:

**自增某个 key 的值**

	Cache::increment('key');

	Cache::increment('key', $amount);

**自减某个 key 的值**

	Cache::decrement('key');

	Cache::decrement('key', $amount);

<a name="cache-sections"></a>
## 多段 Cache

> **注意:** 文件 `file` 和 数据库 `database` 类型的后端存储不支持多段 Cache

Cache sections allow you to group related items in the cache, and then flush the entire section. To access a section, use the `section` method:

使用多段存储可以操作一组相关的数据，具体使用 `section` 方法:

**操作一段的数据**

	Cache::section('people')->put('John', $john);

	Cache::section('people')->put('Anne', $anne);

You may also access cached items from the section, as well as use the other cache methods such as `increment` and `decrement`:

段数据可以跟其他 cache 数据一样使用 `increment` 和 `decrement` 操作:

**获取一个段的数据**

	$anne = Cache::section('people')->get('Anne');

Then you may flush all items in the section:

你也可以直接删除整个多段的数据:

	Cache::section('people')->flush();

<a name="database-cache"></a>
## 数据库 Cache

When using the `database` cache driver, you will need to setup a table to contain the cache items. Below is an example `Schema` declaration for the table:

使用数据库 `database` 做 cache 的后端存储时，我们需要提前创建 Cache 的表结构， 下面是表的 `Schema` 描述：

	Schema::create('cache', function($table)
	{
		$table->string('key')->unique();
		$table->text('value');
		$table->integer('expiration');
	});
