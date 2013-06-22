# Artisan 命令

- [介绍](#introduction)
- [用法](#usage)

<a name="introduction"></a>
## 介绍

Artisan is the name of the command-line interface included with Laravel. It provides a number of helpful commands for your use while developing your application. It is driven by the powerful Symfony Console component.

artisan 是 laravel 的命令行工具，提供一些辅助开发的功能。它主要是受 php 框架 Symfony 的控制台组件启发。

<a name="usage"></a>
## 用法

To view a list of all available Artisan commands, you may use the `list` command:

`list` 子命令可以查看所有可用的 artisan 子命令。

**列出所有可用命令**

	php artisan list

Every command also includes a "help" screen which displays and describes the command's available arguments and options. To view a help screen, simply precede the name of the command with `help`:

我们可以通过 `help` 子命令获得某个子命令的使用方法， 以 migrate 子命令为例:

**获得 migrate 命令的帮助信息**

	php artisan help migrate

You may specify the configuration environment that should be used while running a command using the `--env` switch:

有时候我们需要指定命令执行的环境，使用 `--env` 参数：

**指定配置环境**

	php artisan migrate --env=local

You may also view the current version of your Laravel installation usin--g the `--version` option:

通过 `--version` 选项可以获得当前安装的 laravel 版本:

**现实当前 laravel 版本**

	php artisan --version
