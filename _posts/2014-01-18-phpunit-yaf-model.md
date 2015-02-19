---
title: PHPUnit单元测试YAF模型层
author: 谇雨
layout: post
permalink: /phpunit-yaf-model.html
views:
  - 53
categories:
  - Yaf
---
在Yaf应用中，创建一个模型层UserModel：

<pre class="lang:php decode:true">&lt;?php
Class UserModel {
    // 初始化连接数据库，pre_common_member只有三条数据ID:1,2,3
    public function __construct() {
        $this-&gt;_db = Core_LinkMySQL::get('hiapk_x2');
    }

    // 通过uid获取用户名，数据不存在时返回FALSE
    public function fetchUsernameById($id) {
        return $this-&gt;_db-&gt;select('username')
            -&gt;from('pre_common_member')
            -&gt;where('uid', $id)
            -&gt;fetchValue();
    }

    // 通过uid获取用户行数据，数据不存在时返回FALSE
    public function fetchRowById($id) {
        return $this-&gt;_db-&gt;select('username', 'groupid', 'adminid', 'regdate')
            -&gt;from('pre_common_member')
            -&gt;where('uid', $id)
            -&gt;fetchOne();
    }
}</pre>

<!--more-->

创建一个测试用例用于测试该模型层，需要几个条件：  
1.必须实例化Yaf_Application Final类；  
2.同时载入我们的Application必备的配置文件；  
3.官方文档有注明，Yaf_Application代表的是一个产品/项目，必须保证单例。

基于以上3点，模型层的测试用例就很好编写了，创建一个Test Case：UserModelTest.php 该文件仅用于测试UserModel的业务。

<pre class="lang:php decode:true">&lt;?php
define('APP_PATH', dirname(__FILE__) . '/../../');
define('APP_ENV', 'loc');
error_reporting(E_ERROR | E_PARSE);

Class UserModelTest extends PHPUnit_Framework_TestCase {

    // 用于保存模型的单例
    private static $__model = NULL;

    // 初始化实例化YAF应用，PHPUnit每次test函数都会实例化对象一次，
    // YAF application保证单例
    public function __construct() {
        if ( ! Yaf_Registry::get('Application') ) {
            $application = new Yaf_Application(APP_PATH."/config/application.ini", APP_ENV);
            Yaf_Application::set('Application', $application);
        }

        if ( ! self::$__model )
            self::$__model = new UserModel();
    }

    // 测试 fetchRowById
    public function testFetchRowById() {
        $uid = 3;
        $row = self::$__model-&gt;fetchRowById($uid);
        $this-&gt;assertInternalType('array', $row);
        $this-&gt;assertStringMatchesFormat('%s', $row['username']);
        $this-&gt;assertStringMatchesFormat('%i', $row['groupid']);
        $this-&gt;assertStringMatchesFormat('%i', $row['adminid']);
        $this-&gt;assertStringMatchesFormat('%i', $row['regdate']);
    }

    // 测试 fetchUsernameById，由于我的数据表中只有3条数据，查询结果如果不存在是返回FALSE
    public function testFetchUsernameById() {
        $uid  = 4;
        $name = self::$__model-&gt;fetchUsernameById($uid);
        $this-&gt;assertInternalType('string', $name, $uid . ':' . gettype($name) );
    }
}</pre>

测试结果：第二个Function由于查不到指定数据返回FALSE测试类型失败。  
[<img class="alignnone size-full wp-image-948" title="TestYafModel" src="http://www.crackedzone.com/wp-content/uploads/2014/01/TestYafModel.jpg" alt="" />][1]

 [1]: http://www.crackedzone.com/wp-content/uploads/2014/01/TestYafModel.jpg