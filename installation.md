# 安装

- [安装 Composer](#install-composer)
- [安装 Laravel](#install-laravel)
- [服务器环境要求](#server-requirements)
- [配置](#configuration)
- [简洁的链接](#pretty-urls)

<a name="install-composer"></a>
## 安装 Composer

Laravel 使用 [Composer](http://getcomposer.org) 来管理依赖关系. 首先, 下载一份 `composer.phar`的拷贝. 一旦你有了这个PHAR包, 你可以将它放到本地项目目录里面或者移到 `usr/local/bin` 目录下来保证能够在系统任一地点使用. 在 Windows 上, 你可以使用 Windows 版的 Composer [安装工具](https://getcomposer.org/Composer-Setup.exe).

<a name="install-laravel"></a>
## 安装 Laravel

### 通过下载

一旦 Composer 已安装, 下载 [最新版](https://github.com/laravel/laravel/archive/master.zip) Laravel 框架然后解压到你服务器上一个目录内. 然后在你的Laravel应用的根目录下执行 `php composer.phar install` (或者 `composer install`) 命令来安装这个框架的所有依赖. 这需要你服务器上已经安装了 Git 才能完成整个安装过程.

### 通过 Composer Create-Project

你也可以通过在终端调用Composer的 `create-project` 命令来安装Laravel框架:

	composer create-project laravel/laravel

一旦 Laravel 已安装, 你可以通过 `php composer.phar update` 命令来升级该框架.

<a name="server-requirements"></a>
## 服务器环境要求

Laravel框架对系统存在一些要求:

- PHP >= 5.3.7
- MCrypt PHP Extension

<a name="configuration"></a>
## 配置

Laravel 几乎不需要进行配置就可以立即投入使用. 自由地开始开发工作吧! 然而, 你可能会想查阅 `app/config/app.php` 配置文件以及其文档. 它包含了一些类似于 `timezone` 和 `locale` 之类的选项, 你可以根据你的应用来自由的进行修改.

> **注意:** 一个你确定需要修改的配置项就是 `key` 选项, 位于 `app/config/app.php` 文件里面. 该项的值应该被设置成一个32位的随机字符串, 这个 key 被用于加密操作, 除非你恰当的设置了该项, 否则加密的值将不能保证安全. 你可以通过 artisan 命令 `php artisan key:generate` 来快速的设置该项的值.

<a name="permissions"></a>
### 权限
Laravel 需要一项权限许可 - 位于 app/storage 下的目录需要服务器的可写权限.

<a name="paths"></a>
### 路径

该框架的部分部分目录路径是可配置的. 要修改这些目录的路径的话, 请参阅 `bootstrap/paths.php` 文件.

> **注意:** Laravel 仅放置那些必须公开的文件到 public 文件夹下, 旨在保护你的应用代码以及本地存储. 推荐的做法是你或者将 public 文件夹设定为你站点的 documentRoot (也就是 web 根目录) 或者将 public 文件夹下的内容放入站点的根目录内然后将 Laravel 相关的其他文件移出根目录.

<a name="pretty-urls"></a>
## 简洁的链接

与框架同存的 `public/.htaccess` 文件被用于去除 URL 中的 `index.php`. 如果你使用 Apache 来运行你的 Laravel 应用程序, 请确保开启了 `mod_rewrite` 模块.

如果该 `.htaccess` 文件在你安装的 Apache 下不工作的话, 试试这个:

	Options +FollowSymLinks
	RewriteEngine On

	RewriteCond %{REQUEST_FILENAME} !-d
	RewriteRule ^(.+)/$ http://%{HTTP_HOST}/$1 [R=301,L]

	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteRule ^ index.php [L]
