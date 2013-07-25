# 请求的生命周期

- [概要](#overview)
- [启动文件](#start-files)
- [应用事件](#application-events)

<a name="overview"></a>
## 概要

The Laravel request lifecycle is fairly simple. A request enters your application and is dispatched to the appropriate route or controller. The response from that route is then sent back to the browser and displayed on the screen. Sometimes you may wish to do some processing before or after your routes are actually called. There are several opportunities to do this, two of which are "start" files and application events.

Laravel 请求的生命周期相当简单，请求访问你的应用时，会被派发到相应的路由，或者控制器来处理，执行结果由服务器返回给浏览器处理。有时候我们可能需要在一般的路由调用前后做一些额外的处理，有很多种方式，这里介绍启动文件和应用事件(Application Events)。

<a name="start-files"></a>
## 启动文件

Your application's start files are stored at `app/start`. By default, three are included with your application: `global.php`, `local.php`, and `artisan.php`. For more information about `artisan.php`, refer to the documentation on the [Artisan command line](/docs/commands#registering-commands).

应用的启动文件在 `app/start` 目录，默认情况下会有三个文件 `global.php` `local.php` 和 `artisan.php` 。`artisan.php` 的更多信息可以参考 [Artisan 命令行](/docs/commands#registering-commands).

The `global.php` start file contains a few basic items by default, such as the registration of the [Logger](/docs/errors) and the inclusion of your `app/filters.php` file. However, you are free to add anything to this file that you wish. It will be automatically included on _every_ request to your application, regardless of environment. The `local.php` file, on the other hand, is only called when the application is executing in the `local` environment. For more information on environments, check out the [configuration](/docs/configuration) documentation.

`global.php` 中是注册 [Logger](docs/errors)、 包含 `app/filters.php `过滤器文件之类的基本操作，不管什么环境每个请求都会被包含。 `local.php` 则是只在本地环境被执行。关于本地环境等的信息，参考 [配置](/docs/configuration) 。

Of course, if you have other environments in addition to `local`, you may create start files for those environments as well. They will be automatically included when your application is running in that environment.

当然，除了本地(`local`)环境，其他环境也可以有类似的文件，只在相应的环境被包含。

<a name="application-events"></a>
## 应用事件 Application Events

You may also do pre and post request processing by registering `before`, `after`, `close`, `finish`, and `shutdown` application events: 

通过注册应用的  `before`, `after`, `close`, `finish`, 和 `shutdown` 等事件，可以在请求前后做一些自己的处理。


**Registering Application Events**

	App::before(function()
	{
		//
	});

	App::after(function($request, $response)
	{
		//
	});

Listeners to these events will be run `before` and `after` each request to your application.

这些事件的监听器会在每个请求之前(`before`)或者之后(`after`)执行。
