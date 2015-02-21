---
title: 异步日志输出方案
author: 谇雨
layout: post
permalink: /asynchronous-log-output-scheme.html
categories:
  - PHP
tags:
  - PHP生命周期
  - 日志输出
  - 异步日志
---

*深入了解PHP生命周期*

### 一．背景

日志在WEB应用中的使用非常广泛，记录访问者IP，访问者操作的数据，接口请求的信息，异常提示信息等，尤其在产品环境中，我们往往需要通过日志来分析当前应用的运营情况，是否在存在一些不可见的未知错误。

在程序实现过程中，我们设计的输出日志往往都是在程序运行当中，如：

*   连接数据库失败了，捕捉到异常后，往本地磁盘写入一条日志；
*   图片上传时间超时，往本地磁盘写一条日志；
*   渲染页面中出现XSS攻击，往网络日志服务器写一条日志；
*   请求对方服务器Api接口，出现超时，往网络日志服务器写一条日志；
*   ……

<!--more-->

这种实时输出日志的方式往往存在两个主要的问题：

1.  对本地磁盘的IO提高或者对网络日志服务器请求出现超时；
2.  若日志写失败，极有可能对后面程序片段的执行产生影响；

既然是日志，那么说明它并不是非常重要的数据，却不能因为记录日志的失败影响重要的业务，那么我们是否可以把日志输入放在所有程序都执行完成的时候处理呢？因此有必要深入了解请求执行的PHP生命周期过程。

### 二．Web请求与PHP生命周期

PHP有两种运行模式：WEB模式，CLI模式

无论是哪种模式，PHP的工作原理都是一致的，都需要SAPI的运行

在Web请求时，Nginx就必然要和FastCGI通信，通过php_fpm调用php-cgi，需要通过SAPI接口，进入PHP的脚本执行。

在CLI模式下直接操作php-cgi也需要通过SAPI执行，以下展示了整个处理过程：

![SAPI处理过程]({{ site.url }}/uploads/2014/09/log-0.jpg)

SAPI也叫Service API，在整个请求过程中承担了很重要的角色，外部应用通过SAPI进行对上层调用，所谓的外部应用可以理解为Apache，FastCgi等，上层也就是我们写的PHP程序。  
以下是SAPI的简单示意图：

![SAPI简单示意图]({{ site.url }}/uploads/2014/09/log-1.jpg)

当请求调用SAPI时，将会按顺序促发以下几个阶段：

1.   **MINIT(Module init)**  
    该阶段将调用PHP\_MINIT\_FUNCTION(extension)  
    该函数作用为遍历需要加载的扩展，并初始化扩展，注册模块常量，类等。

2.   **RINIT(Request init)**  
    该阶段将调用 PHP\_RINT\_FUNCTION(extension)  
    该函数的作用为遍历加载的扩展，并针对请求信息进行一些初始化工作，比如记录请求开始时间，初始化请求$\_POST, $\_GET全局遍历，若开启session模块下，注册全局Session变量等。

3.   **Execute php code**  
    这部分就是自己编写的PHP代码，也就是真正执行的我们代码执行的部分。

4.   **RSHUTDOWN**  
    该阶段将调用PHP\_RSHUTDOWN\_FUNCTION(extension)  
    该函数的作用遍历加载的扩展，并针对请求信息进行一些析构工作，比如记录请求结束时间，把相应的输入写入日志，开启session模块下，全局Session变量写到tmp目录下文件等。

5.   **MSHUTDOWN**  
    该阶段将调用PHP\_MSHUTDOWN\_FUNCTION(extension)  
    该函数的作用为当Web请求结束或命令行执行脚本结束后会执行，完成释放资源操作。

可以通过下图直观的看出php执行test.php生命周期的过程：

![PHP生命周期]({{ site.url }}/uploads/2014/09/log-2.jpg)

其实我们编写的程序在复杂PHP生命周期中往往只运行在第3步，假如我们的日志能在第4步处理，那是不是可以避免文章开头提出的问题？那么除了利用编写扩展注册PHP\_RSHUTDOWN\_FUNCTION方法，我们还可以通过`register_shutdown_function()`注册的自定义函数，这些自定义函数在我们编写的PHP代码结束的时候将会被调用。

`register_shutdown_function()`是php系统函数，注册shutdown的函数将会在PHP生命周期中第4阶段进行执行，如程序结束，或出现`exit`/`die`等命令后都会触发注册函数。

### 三．程序实现

为此利用`register_shutdown_function()`，我们可以设计一个处理事件类，该类能够注册我们需要的事件，在PHP Shutdown的时候针对已经注册的事件进行处理，这些事件中可能就包括我们需要的日志输出。

该类实现的功能很简单：

1.   需要一个增加事件的方法，把我们需要的处理事件过程放入；
2.   需要一个调用事件的方法，把我们增加的所有事件在SHUTDOWN时调用；
3.   最后需要注册方法，注册调用方法至SHUTDOWN中；

以下是该类(Service\_Core\_ShutdownEvent)的实现代码：

    /**
     * Manage php shutdown events.
     * @author Lancer He <lancer.he@gmail.com>
     * @since  2014-08-21
     */
    class Service_Core_ShutdownEvent {

        /**
         * array to store user events.
         * @var array
         */
        private static $_events = array();


        /**
         * register shutdown
         */
        public static function register() {
            register_shutdown_function(array('Service_Core_ShutdownEvent', 'call'));
        }


        /**
         * Register event.
         * @return boolean
         */
        public static function add() {
            $event = func_get_args();

            if ( empty($event) ) {
                trigger_error("Register event need method.");
                return false;
            }

            if ( ! is_callable($event[0]) ) {
                trigger_error("Register event can not be call.");
                return false;
            }
            self::$_events[] = $event;
            return true;
        }


        /**
         * call event when you need.
         */
        public function call() {
            foreach (self::$_events as $event) {
                $callback = array_shift($event);
                call_user_func_array($callback, $event);
            }
        }
    }


对于编写好的处理事件类，我们使用一个简单的Log类来验证这个注册事件类是否可行，这个Log类(Service\_Core\_NetLog)具体的程序就不展示了，主要演示以下两个方法：

    Service_Core_NetLog::trace(string $string);
    Service_Core_NetLog::notice(string $string);

两个方法都比较简单，作用往磁盘中按照一定规则输出一行Log，因此我在一个控制器中来测试它，通过Web访问或者CLI的方式请求：

![日志测试]({{ site.url }}/uploads/2014/09/log-3.jpg)

通过Sleep的方式来验证，上图红框处可以更加直观的验证有两条日志是在程序结束以后写入磁盘。

### 四．小结

通过深入了解PHP生命周期，更直观的理解PHP运行的机制，通过注册shutdown方式(`register_shutdown_function`)在PHP程序结束后处理我们需要的逻辑过程，如日志输出，邮件通知等，降低了程序中的处理额外业务逻辑的风险。