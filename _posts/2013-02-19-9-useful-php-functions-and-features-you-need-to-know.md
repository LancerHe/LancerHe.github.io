---
title: 九个PHP很有用的功能
author: 谇雨
layout: post
permalink: /9-useful-php-functions-and-features-you-need-to-know.html
views:
  - 192
categories:
  - PHP
---
文章来源：<a href="http://coolshell.cn/articles/2394.html" target="_blank">http://coolshell.cn/articles/2394.html</a>

英文原文：<a href="http://net.tutsplus.com/tutorials/php/9-useful-php-functions-and-features-you-need-to-know/" target="_blank">http://net.tutsplus.com/tutorials/php/9-useful-php-functions-and-features-you-need-to-know/</a>

下面是九个PHP中很有用的功能，不知道你用过了吗？



#### 1. 函数的任意数目的参数

你可能知道PHP允许你定义一个默认参数的函数。但你可能并不知道PHP还允许你定义一个完全任意的参数的函数

下面是一个示例向你展示了默认参数的函数：

<pre class="lang:php decode:true">// 两个默认参数的函数
function foo($arg1 = '', $arg2 = '') {

    echo "arg1: $arg1\n";
    echo "arg2: $arg2\n";

}

foo('hello','world');
/* 输出:
arg1: hello
arg2: world
*/

foo();
/* 输出:
arg1:
arg2:
*/</pre>

<!--more-->现在我们来看一看一个不定参数的函数，其使用到了

[func\_get\_args()][1]方法：

<pre class="lang:php decode:true">// 是的，形参列表为空
function foo() {

    // 取得所有的传入参数的数组
    $args = func_get_args();

    foreach ($args as $k => $v) {
        echo "arg".($k+1).": $v\n";
    }

}

foo();
/* 什么也不会输出 */

foo('hello');
/* 输出
arg1: hello
*/

foo('hello', 'world', 'again');
/* 输出
arg1: hello
arg2: world
arg3: again
*/</pre>



#### 2. 使用 Glob() 查找文件

很多PHP的函数都有一个比较长的自解释的函数名，但是，当你看到?[glob()][2] 的时候，你可能并不知道这个函数是用来干什么的，除非你对它已经很熟悉了。

你可以认为这个函数就好?[scandir()][3] 一样，其可以用来查找文件。

<pre class="lang:php decode:true">// 取得所有的后缀为PHP的文件
$files = glob('*.php');

print_r($files);
/* 输出:
Array
(
    [0] => phptest.php
    [1] => pi.php
    [2] => post_output.php
    [3] => test.php
)
*/</pre>

还可以查找多种后缀名

<pre class="lang:php decode:true">// 取PHP文件和TXT文件
$files = glob('*.{php,txt}', GLOB_BRACE);

print_r($files);
/* 输出:
Array
(
    [0] => phptest.php
    [1] => pi.php
    [2] => post_output.php
    [3] => test.php
    [4] => log.txt
    [5] => test.txt
)
*/</pre>

你还可以加上路径：

<pre class="lang:php decode:true">$files = glob('../images/a*.jpg');

print_r($files);
/* 输出:
Array
(
    [0] => ../images/apple.jpg
    [1] => ../images/art.jpg
)
*/</pre>

如果你想得到绝对路径，你可以调用[realpath()][4] 函数：

<pre class="lang:php decode:true">$files = glob('../images/a*.jpg');

// applies the function to each array element
$files = array_map('realpath',$files);

print_r($files);
/* output looks like:
Array
(
    [0] => C:\wamp\www\images\apple.jpg
    [1] => C:\wamp\www\images\art.jpg
)
*/</pre>



#### 3. 内存使用信息

观察你程序的内存使用能够让你更好的优化你的代码。

PHP 是有垃圾回收机制的，而且有一套很复杂的内存管理机制。你可以知道你的脚本所使用的内存情况。要知道当前内存使用情况，你可以使用[memory\_get\_usage()][5] 函数，如果你想知道使用内存的峰值，你可以调用[memory\_get\_peak_usage()][6] 函数。

<pre class="lang:php decode:true">echo "Initial: ".memory_get_usage()." bytes \n";
/* 输出
Initial: 361400 bytes
*/

// 使用内存
for ($i = 0; $i < 100000; $i++) {
    $array []= md5($i);
}

// 删除一半的内存
for ($i = 0; $i < 100000; $i++) {
    unset($array[$i]);
}

echo "Final: ".memory_get_usage()." bytes \n";
/* prints
Final: 885912 bytes
*/

echo "Peak: ".memory_get_peak_usage()." bytes \n";
/* 输出峰值
Peak: 13687072 bytes
*/</pre>



#### 4. CPU使用信息

使用 [getrusage()][7] 函数可以让你知道CPU的使用情况。注意，这个功能在Windows下不可用。

<pre class="lang:php decode:true">print_r(getrusage());
/* 输出
Array
(
    [ru_oublock] => 0
    [ru_inblock] => 0
    [ru_msgsnd] => 2
    [ru_msgrcv] => 3
    [ru_maxrss] => 12692
    [ru_ixrss] => 764
    [ru_idrss] => 3864
    [ru_minflt] => 94
    [ru_majflt] => 0
    [ru_nsignals] => 1
    [ru_nvcsw] => 67
    [ru_nivcsw] => 4
    [ru_nswap] => 0
    [ru_utime.tv_usec] => 0
    [ru_utime.tv_sec] => 0
    [ru_stime.tv_usec] => 6269
    [ru_stime.tv_sec] => 0
)

*/</pre>

这个结构看上出很晦涩，除非你对CPU很了解。下面一些解释：

*   ru_oublock: 块输出操作
*   ru_inblock: 块输入操作
*   ru_msgsnd: 发送的message
*   ru_msgrcv: 收到的message
*   ru_maxrss: 最大驻留集大小
*   ru_ixrss: 全部共享内存大小
*   ru_idrss:全部非共享内存大小
*   ru_minflt: 页回收
*   ru_majflt: 页失效
*   ru_nsignals: 收到的信号
*   ru_nvcsw: 主动上下文切换
*   ru_nivcsw: 被动上下文切换
*   ru_nswap: 交换区
*   ru\_utime.tv\_usec: 用户态时间 (microseconds)
*   ru\_utime.tv\_sec: 用户态时间(seconds)
*   ru\_stime.tv\_usec: 系统内核时间 (microseconds)
*   ru\_stime.tv\_sec: 系统内核时间?(seconds)

要看到你的脚本消耗了多少CPU，我们需要看看“用户态的时间”和“系统内核时间”的值。秒和微秒部分是分别提供的，您可以把微秒值除以100万，并把它添加到秒的值后，可以得到有小数部分的秒数。

<pre class="lang:php decode:true">// sleep for 3 seconds (non-busy)
sleep(3);

$data = getrusage();
echo "User time: ".
    ($data['ru_utime.tv_sec'] +
    $data['ru_utime.tv_usec'] / 1000000);
echo "System time: ".
    ($data['ru_stime.tv_sec'] +
    $data['ru_stime.tv_usec'] / 1000000);

/* 输出
User time: 0.011552
System time: 0</pre>

sleep是不占用系统时间的，我们可以来看下面的一个例子：

<pre class="lang:php decode:true">// loop 10 million times (busy)
for($i=0;$i<10000000;$i++) {

}

$data = getrusage();
echo "User time: ".
    ($data['ru_utime.tv_sec'] +
    $data['ru_utime.tv_usec'] / 1000000);
echo "System time: ".
    ($data['ru_stime.tv_sec'] +
    $data['ru_stime.tv_usec'] / 1000000);

/* 输出
User time: 1.424592
System time: 0.004204
*/</pre>

这花了大约14秒的CPU时间，几乎所有的都是用户的时间，因为没有系统调用。

系统时间是CPU花费在系统调用上的上执行内核指令的时间。下面是一个例子：

<pre class="lang:php decode:true">$start = microtime(true);
// keep calling microtime for about 3 seconds
while(microtime(true) - $start < 3) {

}

$data = getrusage();
echo "User time: ".
    ($data['ru_utime.tv_sec'] +
    $data['ru_utime.tv_usec'] / 1000000);
echo "System time: ".
    ($data['ru_stime.tv_sec'] +
    $data['ru_stime.tv_usec'] / 1000000);

/* prints
User time: 1.088171
System time: 1.675315
*/</pre>

我们可以看到上面这个例子更耗CPU。  


#### 5. 系统常量

PHP 提供非常有用的[系统常量][8] 可以让你得到当前的行号 (\_\_LINE\_\_)，文件 (\_\_FILE\_\_)，目录 (\_\_DIR\_\_)，函数名 (\_\_FUNCTION\_\_)，类名(\_\_CLASS\_\_)，方法名(\_\_METHOD\_\_) 和名字空间 (\_\_NAMESPACE\_\_)，很像C语言。

我们可以以为这些东西主要是用于调试，当也不一定，比如我们可以在include其它文件的时候使用?\_\_FILE\_\_ (当然，你也可以在 PHP 5.3以后使用 \_\_DIR\_\_ )，下面是一个例子。

<pre class="lang:php decode:true">// this is relative to the loaded script's path
// it may cause problems when running scripts from different directories
require_once('config/database.php');

// this is always relative to this file's path
// no matter where it was included from
require_once(dirname(__FILE__) . '/config/database.php');</pre>

下面是使用 \_\_LINE\_\_ 来输出一些debug的信息，这样有助于你调试程序：

<pre class="lang:php decode:true">// some code
// ...
my_debug("some debug message", __LINE__);
/* 输出
Line 4: some debug message
*/

// some more code
// ...
my_debug("another debug message", __LINE__);
/* 输出
Line 11: another debug message
*/

function my_debug($msg, $line) {
    echo "Line $line: $msg\n";
}</pre>



#### 6.生成唯一的ID

有很多人使用 md5() 来生成一个唯一的ID，如下所示：

<pre class="lang:php decode:true">// generate unique string
echo md5(time() . mt_rand(1,1000000));</pre>

实，PHP中有一个叫[uniqid()][9] 的函数是专门用来干这个的：

<pre class="lang:php decode:true">// generate unique string
echo uniqid();
/* 输出
4bd67c947233e
*/

// generate another unique string
echo uniqid();
/* 输出
4bd67c9472340
*/</pre>

可能你会注意到生成出来的ID前几位是一样的，这是因为生成器依赖于系统的时间，这其实是一个非常不错的功能，因为你是很容易为你的这些ID排序的。这点MD5是做不到的。

你还可以加上前缀避免重名：

<pre class="lang:php decode:true">// 前缀
echo uniqid('foo_');
/* 输出
foo_4bd67d6cd8b8f
*/

// 有更多的熵
echo uniqid('',true);
/* 输出
4bd67d6cd8b926.12135106
*/

// 都有
echo uniqid('bar_',true);
/* 输出
bar_4bd67da367b650.43684647
*/</pre>

而且，生成出来的ID会比MD5生成的要短，这会让你节省很多空间。  


#### 7. 序列化

你是否会把一个比较复杂的数据结构存到数据库或是文件中？你并不需要自己去写自己的算法。PHP早已为你做好了，其提供了两个函数：[serialize()][10] 和 [unserialize()][11]:

<pre class="lang:php decode:true">// 一个复杂的数组
$myvar = array(
    'hello',
    42,
    array(1,'two'),
    'apple'
);

// 序列化
$string = serialize($myvar);

echo $string;
/* 输出
a:4:{i:0;s:5:"hello";i:1;i:42;i:2;a:2:{i:0;i:1;i:1;s:3:"two";}i:3;s:5:"apple";}
*/

// 反序例化
$newvar = unserialize($string);

print_r($newvar);
/* 输出
Array
(
    [0] => hello
    [1] => 42
    [2] => Array
        (
            [0] => 1
            [1] => two
        )

    [3] => apple
)
*/</pre>

这是PHP的原生函数，然而在今天JSON越来越流行，所以在PHP5.2以后，PHP开始支持JSON，你可以使用 json\_encode() 和 json\_decode() 函数

这看起来更为紧凑一些了，而且还兼容于Javascript和其它语言。但是对于一些非常复杂的数据结构，可能会造成数据丢失。

<pre class="lang:php decode:true">// a complex array
$myvar = array(
    'hello',
    42,
    array(1,'two'),
    'apple'
);

// convert to a string
$string = json_encode($myvar);

echo $string;
/* prints
["hello",42,[1,"two"],"apple"]
*/

// you can reproduce the original variable
$newvar = json_decode($string);

print_r($newvar);
/* prints
Array
(
    [0] => hello
    [1] => 42
    [2] => Array
        (
            [0] => 1
            [1] => two
        )

    [3] => apple
)
*/</pre>



#### 8. 字符串压缩

当我们说到压缩，我们可能会想到文件压缩，其实，字符串也是可以压缩的。PHP提供了[gzcompress()][12]和 [gzuncompress()][13] 函数：

几乎有50% 压缩比率。同时，你还可以使用?[gzencode()][14] 和 [gzdecode()][15] 函数来压缩，只不用其用了不同的压缩算法。

<pre class="lang:php decode:true">$string =
"Lorem ipsum dolor sit amet, consectetur
adipiscing elit. Nunc ut elit id mi ultricies
adipiscing. Nulla facilisi. Praesent pulvinar,
sapien vel feugiat vestibulum, nulla dui pretium orci,
non ultricies elit lacus quis ante. Lorem ipsum dolor
sit amet, consectetur adipiscing elit. Aliquam
pretium ullamcorper urna quis iaculis. Etiam ac massa
sed turpis tempor luctus. Curabitur sed nibh eu elit
mollis congue. Praesent ipsum diam, consectetur vitae
ornare a, aliquam a nunc. In id magna pellentesque
tellus posuere adipiscing. Sed non mi metus, at lacinia
augue. Sed magna nisi, ornare in mollis in, mollis
sed nunc. Etiam at justo in leo congue mollis.
Nullam in neque eget metus hendrerit scelerisque
eu non enim. Ut malesuada lacus eu nulla bibendum
id euismod urna sodales. ";

$compressed = gzcompress($string);

echo "Original size: ". strlen($string)."\n";
/* 输出原始大小
Original size: 800
*/

echo "Compressed size: ". strlen($compressed)."\n";
/* 输出压缩后的大小
Compressed size: 418
*/

// 解压缩
$original = gzuncompress($compressed);</pre>



#### 9. 注册停止函数

有一个函数叫做[register\_shutdown\_function()][16]，可以让你在整个脚本停时前运行代码。让我们看下面的一个示例：

<pre class="lang:php decode:true">// capture the start time
$start_time = microtime(true);

// do some stuff
// ...

// display how long the script took
echo "execution took: ".
        (microtime(true) - $start_time).
        " seconds.";</pre>

上面这个示例只不过是用来计算某个函数运行的时间。然后，如果你在函数中间调用[exit()][17] 函数，那么你的最后的代码将不会被运行到。并且，如果该脚本在浏览器终止（用户按停止按钮），其也无法被运行。

而当我们使用了register\_shutdown\_function()后，你的程序就算是在脚本被停止后也会被运行：

<pre class="lang:php decode:true">$start_time = microtime(true);

register_shutdown_function('my_shutdown');

// do some stuff
// ...

function my_shutdown() {
    global $start_time;

    echo "execution took: ".
            (microtime(true) - $start_time).
            " seconds.";
}</pre>

 [1]: http://us2.php.net/manual/en/function.func-get-args.php
 [2]: http://us.php.net/manual/en/function.glob.php
 [3]: http://php.net/manual/en/function.scandir.php
 [4]: http://php.net/manual/en/function.realpath.php
 [5]: http://us2.php.net/manual/en/function.memory-get-usage.php
 [6]: http://us2.php.net/manual/en/function.memory-get-peak-usage.php
 [7]: http://us2.php.net/manual/en/function.getrusage.php
 [8]: http://php.net/manual/en/language.constants.predefined.php
 [9]: http://us2.php.net/manual/en/function.uniqid.php
 [10]: http://php.net/manual/en/function.serialize.php
 [11]: http://www.php.net/manual/en/function.unserialize.php
 [12]: http://php.net/manual/en/function.gzcompress.php
 [13]: http://www.php.net/manual/en/function.gzuncompress.php
 [14]: http://www.php.net/manual/en/function.gzencode.php
 [15]: http://www.php.net/manual/en/function.gzdecode.php
 [16]: http://www.php.net/manual/en/function.register-shutdown-function.php
 [17]: http://php.net/manual/en/function.exit.php