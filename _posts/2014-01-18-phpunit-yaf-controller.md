---
title: PHPUnit单元测试YAF控制层
author: 谇雨
layout: post
permalink: /phpunit-yaf-controller.html
views:
  - 211
categories:
  - PHP
---
在Yaf应用中，创建一个控制层UserController：

    Class UserController extends Yaf_Controller_Abstract {
        // init方法相当于控制器的初始化函数，取消自动渲染视图
        public function init() {
            Yaf_dispatcher::getInstance()->disableView();
        }
        // 输出需要的JSON信息
        private function __responseJson($code=0, $data=FALSE) {
            $response = json_encode(array('code'=>$code, 'data'=>$data));
            $this->getResponse()->setBody($response);
        }
        // JSON Action
        public function jsonAction($uid=0) {
            if ( $uid < 1 ) return $this->__responseJson(-1);

            $user_model = new UserModel();
            $row = $user_model->fetchRowById($uid);
            return $this->__responseJson(0, $row);
        }
    }

<!--more-->

  
测试控制器是一个相对比较不容易理解的过程，由于控制器本身可能存在输出一段JSON后Exit的过程，会导致Response出来的数据无法别测试用例获取。

这里就需要YAF两个特殊的方式来操作：

1. 输出的时候需要用到Yaf\_Response\_Abstract的方法。  
该方法同时可以在Yaf_Dispatchar调度器中通过设置returnResponse控制是否输出数据，因此我们之前在User控制器中使用：
    <pre>
    $this->getResponse()->setBody($response);</pre>

2. YAF本身允许在CLI模式执行，我们可以通过YAF\_Request\_Sample创建一个简单请求，创建request.php
    <pre>
    $request = new Yaf_Request_Simple();
    print_r($request);</pre>

如我们在命令行下执行request.php，可以看到如下结果：  
[<img src="{{ site.url }}/uploads/2014/01/TestYafCli.jpg" alt="" title="TestYafCli" class="alignnone size-full wp-image-953" />][1]

基于以上两点，为此我们来创建一个测试用例：UserControllerTest.php 该文件仅用于测试UserController的业务。

    define('APP_PATH', dirname(__FILE__) . '/../../');
    define('APP_ENV', 'loc');
    error_reporting(E_ERROR | E_PARSE);

    Class UserControllerTest extends PHPUnit_Framework_TestCase {

        private $__application = NULL;
        
        // 初始化实例化YAF应用，YAF application只能实例化一次
        public function __construct() {
            if ( ! $this->__application = Yaf_Registry::get('Application') ) {
                $this->__application = new Yaf_Application(APP_PATH."/config/application.ini", APP_ENV);
                Yaf_Registry::set('Application', $this->__application);
            }
        }

        // 创建一个简单请求，并利用调度器接受Repsonse信息，指定分发请求。
        private function __requestActionAndParseBody($action, $params=array()) {
            $request = new Yaf_Request_Simple("CLI", "Index", "User", $action, $params);
            $response = $this->__application->getDispatcher()
                ->returnResponse(TRUE)
                ->dispatch($request);
            return $response->getBody();
        }

        // 测试 JsonAction UID存在
        public function testJsonUid1Action() {
            $response = $this->__requestActionAndParseBody('Json', array('uid'=>1));
            $data     = json_decode($response, TRUE);
            $this->assertInternalType('array', $data);
            $this->assertEquals('0', $data['code']);
            $this->assertInternalType('string', $data['data']['username']);
            $this->assertRegExp('/^\d+$/', $data['data']['groupid']);
            $this->assertRegExp('/^\d+$/', $data['data']['adminid']);
            $this->assertRegExp('/^\d+$/', $data['data']['regdate']);
        }

        // 测试 JsonAction UID不存在，UID不存在返回的code应该是-1
        public function testJsonUidNotFoundAction() {
            $response = $this->__requestActionAndParseBody('Json');
            $data     = json_decode($response, TRUE);
            $this->assertInternalType('array', $data);
            $this->assertEquals('0', $data['code']);
        }
    }</pre>

注意我们创建请求的过程 (__requestActionAndParseBody)：

1.设置CLI请求的Request对象信息；  
2.通过Application获取调度器Dispatcher；  
3.设置返回接收Response的对象，不自动输出；  
4.通过Dispatcher自动分发指定的Request对象；  
5.获取返回的Response主体信息  
6.进行验证

测试结果：第二个Function由于非法请求返回参数code：-1测试失败。

[<img src="{{ site.url }}/uploads/2014/01/TestYafController.jpg" alt="" title="TestYafController" class="alignnone size-full wp-image-954" />][2]

 [1]: {{ site.url }}/uploads/2014/01/TestYafCli.jpg
 [2]: {{ site.url }}/uploads/2014/01/TestYafController.jpg