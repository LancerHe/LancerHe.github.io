---
title: 服务端需要一个新的分层 -“数据缓存层”
author: 谇雨
layout: post
permalink: /server-need-a-new-datastore-layar.html
categories:
  - PHP
tags:
  - 数据缓存层
  - 服务端
---

*实现读取缓存在数据访问层中抽离策略*

### 一. 背景

使用数据缓存在WEB工程中是一个非常有意义的策略，不仅仅可以减少数据库负载，而且当数据缓存在内存中，能大大提高了的读取速度。

在WEB设计中，我们往往忽略一个重要的信息 – Key，每当我们需要缓存的时候，只是单纯的对一个Key进行SET或者GET操作，时间长了，也许就出现了以下的问题：

*   这个数据缓存的Key是什么；
*   这个数据缓存生命周期有多长；
*   如何不获取这个数据缓存，而得到实际的数据做测试；
*   在其他项目中同时可能用到这个数据缓存，如何重置这个缓存
*   ……

这就造成了我们平时常见的一种场景，一位同事查到原因，高喊：

*“Shit！为什么生产环境上的数据总是测试有问题，测试环境都好好的，线上一定是有缓存，但是我们怎么把它清除掉！*“  
<!--more-->

### 二. 引发思考的根源

先来看一个简单的例子：*获取服务器的数据列表*

    // 这是一个读取服务器相关信息的数据访问类
    Class ServerStore {

        // 根据数量获取最新的测试服务器列表数据
        public function fetchTestingList($limit);
    }

看到这个类基本所有人都知道fetchTestingList的方法体要这么写：

    // 这是一个读取服务器相关信息的数据访问类
    Class ServerStore {

        // 根据数量获取最新的测试服务器列表数据
        public function fetchTestingList($limit) {
            // OK，开始找Cache
            $cache_key = "ServerTestingList:{$limit}";
            if ( FALSE !== $data_from_cache = Cache::getInstance()->get($cache_key) ) {
                return $data_from_cache;
            }
            // Cache没找到，找数据表吧
            $data = $this->db->select('id','name','time')
                ->from('server')
                ->limit($limit)
                ->fetchRow();
            // 写缓存，给个600秒意思下吧
            Cache::getInstance()->set($cache_key, $data, 600);
            return $data;
        }
    }

现在我们调用这个方法获取数据：

    $ServerStore = new ServeStore();
    $list = $ServerStore->fetchTestingList(4);

这个对象我们统一称之为**Store(数据访问)**对象

看似习以为常的方法，却隐藏的几个重要的问题：

*   这个缓存周期TTL，600秒不是我需要的，那把这个作为一个参数吧。
*   这个方法我不希望得到缓存数据，想要返回实际数据表读取的数据，那么把是否需要缓存作为一个参数吧，到此已经三个参数。
*   有些方法体的参数个数比较多，而且参数还有数组格式，那么组合的Cache Key不是需要在这里构造规则？
*   我这个ServerStore有10个方法体，我难道要写10次`Cache::getInstance`？我还有5个Store层呢，这是在坑爹吗？
*   我的后台项目中有个需求需要清空这个缓存，要是前台被人偷偷修改了Key的规则，我怎么拿到这个Key！这是坑到家了吗？

这个例子确实是开了一个玩笑，但事实上我们的生产环境上经常出现这样的问题，并不是因为我们的程序逻辑出错了，而是由于我们在一开始设计上就没有考虑到扩展性。

### 三. 独立缓存层的设计

为什么我们将缓存写在**Store**对象内，如果写在**Store**对象外头可以吗？形如这般：

    $ServerStore->ttl(600)->fetchTestingList(4);
    $ServerStore->nocache()->fetchTestingList(4);

那么这应该不是一个**Store**对象可以做到方式，我们不妨将它叫做**Service(数据服务)**对象：

    $ServerService->ttl(600)->fetchTestingList(4);
    $ServerService->nocache()->fetchTestingList(4);

我们可以猜想到：

*   **Service**对象中ttl()方法是应该用来返回一个**Cache(数据缓存)**对象，通过**Cache**对象的fetchTestingList()方法获取数据。
*   **Service**对象中nocache()方法则是用来返回一个之前的**Store**对象，通过**Store**对象的fetchTestingList()方法获取数据。
*   **Service**对象中必然存在两个属性不妨叫做$\_db\_handler, $\_cache\_handler用来切换对应操作访问对象。
*   **Cache**对象中只用于返回Cache中的值。
*   当**Service**对象中使用ttl方法获取不到Cache时(TTL失效)，必然还要自动获取**Store**对象进行读取最新的数据。

根据我们的猜想，画出UML图：  
![datastore uml]({{ site.url }}/uploads/2014/04/cachestore-uml-1.jpg)

为了保证通用性我们将**Cache**和**Service**都继承一个抽象基类：  
![datastore uml]({{ site.url }}/uploads/2014/04/cachestore-uml-2.jpg)

一切看似逻辑没什么异常了，可是我们Key的组合怎么处理呢？怎么创建这个Key？

先来处理第一个问题，从一开始的设计方案看，**ServerStore**中每个方法只会存在一个Key，也就是说这个**Store**中有多少个方法体，对应的就应该有多少个Key。

为此我们可以建立一个**ServerCacheKey(数据缓存键)**对象，里面的每个方法都应该匹配**ServerStore**中对应的方法。那么我们不妨设计一个接口**iServer**，然而**ServerCacheKey，ServerCache**以及**ServerStore**实现这个接口，通过这样的实现**ServerCacheKey**将也有fetchTodayTestingList这个方法，自然能通过对应的参数组合出需要的Key。

![datastore uml]({{ site.url }}/uploads/2014/04/cachestore-uml-3.jpg)

那么第二个问题，创建这个Key的工作可能出现的位置在：

1.  **Cache**对象：要通过创建Key来获取缓存中对应Key的Value；
2.  **Service**对象：当使用**Cache**对象获取缓存的数据失效时，会重新使用**Store**对象从数据表中查出新的值，需要重新通过创建Key的操作重新将新数据写入缓存。

有了接口的帮助，那么实现接口的类，它们的类名，方法，参数就被统一了，为此我们再建立一个**CacheKeyCreater**类通过这一些列的规则就可以创建指定的Key。

注：**ServerCache**可以继承抽象类Call魔术自动通过**CacheKeyCreater**实现的接口方法，进行读取数据缓存方法，因此我们这里移除**ServerCache**实现**iServer**的过程。

![datastore uml]({{ site.url }}/uploads/2014/04/cachestore-uml-4.jpg)

### 四. 程序实现

为了方便程序实现和调试，以下使用ThinkPHP框架模拟程序，将App下Lib目录结构做如下规划：

/Lib/Core：核心类文件，CacheKeyCreater, BaseService, BaseCache等放置处  
/Lib/Interface: 接口类放置处  
/Lib/Service: 服务类放置处  
/Lib/Store: 数据访问类放置处  
/Lib/Cache: 数据缓存类放置处  
/Lib/CacheKey: 数据缓存键类放置处

使用ThinkPHP自带的D函数自动装载对应目录下类库，部分类库需要增加自定义的autoload功能。

接口类**iServer**： 

    // 服务器列表接口
    Interface iServer {
        // 获取今日开服列表
        public function fetchTodayOpeningList($limit);
        // 获取今日测试列表
        public function fetchTodayTestingList($limit);
    }

**CacheKeyCreater** 创建Key类： 

    /**
     * APP Cache Key生成类
     *
     * @category Core
     * @package  Core
     * @author   Lancer <lancer.he@gmail.com>
     * @since    2014-03-06
     */
    Final Class CacheKeyCreater {

        public static function create($class, $func, $args) {
            $cachekey_class = $class . 'CacheKey';
            return call_user_func_array(array($cachekey_class, $func), $args);
        }
    }

Cache抽象基类**BaseCache**： 

    /**
     * APP Cache层抽象基类
     *
     * @category Core
     * @package  Core
     * @author   Lancer <lancer.he@gmail.com>
     * @since    2014-03-06
     */
    Abstract Class BaseCache {
        public function __call($func, $args) {
            $class = str_replace('Cache', '', get_class($this) );
            $key = CacheKeyCreater::create($class, $func, $args);
            return Cache::getInstance()->get($key);
        }
    }

Service抽象基类**BaseService**： 

    /**
     * APP Service层抽象基类，自动选择Cache层/Store层
     *
     * @category Core
     * @package  Core
     * @author   Lancer <lancer.he@gmail.com>
     * @since    2014-03-06
     */
    Abstract Class BaseService {

        protected $_cache_handler;

        protected $_db_handler;

        protected $_class_name;

        protected $_layer_cache_tag = 'Cache';//Cache类放在Cache目录下

        protected $_layer_store_tag = 'Store';//Store类放在Store对象下

        public function __construct() {
            $this->_class_name    = str_replace('Service', '', get_class($this) );
            $this->_cache_handler = D($this->_class_name, $this->_layer_cache_tag);
            $this->_db_handler    = D($this->_class_name, $this->_layer_store_tag);
        }

        public function ttl($ttl) {
            $this->_cache_handler->ttl = $ttl;
            return $this;
        }

        public function nocache() {
            return $this->_db_handler;
        }

        public function __call($func, $args) {
            $data_from_cache = call_user_func_array(array($this->_cache_handler, $func), $args);
            if ( FALSE !== $data_from_cache ) 
                return $data_from_cache;

            $data = call_user_func_array(array($this->_db_handler, $func), $args);
            $key  = CacheKeyCreater::create($this->_class_name, $func, $args);
            Cache::getInstance()->set($key, $data, $this->_cache_handler->ttl );
            return $data;
        }
    }

**ServeStore**：必须实现接口iServer的方法 

    /**
     * 服务器数据读取层
     *
     * @category Cache
     * @package  Core
     * @author   Lancer <lancer.he@gmail.com>
     * @since    2014-03-02
     */
    Class ServerStore implements iServer{

        public function fetchTodayOpeningList($limit=4) {
            $condition  = array('status' => 1, 'audit' => 1 'opendate' => date('Y-m-d') );
            return D('Server')->order('opentime DESC')
                ->where($condition)
                ->limit($limit);
                ->getField('id, game_id, name, opentime, statusval');
        }

        public function fetchTodayTestingList($limit=4) {
            $condition  = array('status' => 0, 'audit' => 1 'opendate' => date('Y-m-d') );
            $pagination = array('limit' => 4);
            return D('Server')->order('opentime DESC')
                ->where($condition)
                ->limit($limit);
                ->getField('id, game_id, name, opentime, statusval');
        }
    }

**ServerCacheKey**：必须实现接口iServer的方法 

    /**
     * 服务器列表缓存键类
     *
     * @category CacheKey
     * @package  Extend
     * @author   Lancer <lancer.he@gmail.com>
     * @since    2014-03-02
     */
    Class ServerCacheKey implements iServer {

        public function fetchTodayOpeningList($limit) {
            return 'ServerTodayOpeningList:' . $limit;
        }

        public function fetchTodayTestingList($limit) {
            return 'ServerTodayTestingList:' . $limit;
        }
    }

**ServerService** / **ServerCache** 由于集成父类，目前暂时是空的： 

    Class ServerCache extends BaseCache {}
    Class ServerService extends BaseService {}

**测试过程**：将设计的程序，我们大胆的在Action下做一个测试：

    Class TestAction extends BaseAction {

        public function cachetest() {
            D('Server', 'Service')->ttl(600)->fetchTodayTestingList(4);
            D('Server', 'Service')->nocache()->fetchTodayOpeningList(4);
            $this->show(' ');
        }
    }

**测试结果：**访问页面后在Memadmin查询：  
![datastore test]({{ site.url }}/uploads/2014/04/cachestore-result-1.jpg)

**测试结果：**利用ThinkPHP自带的追踪SQL记录来查看下效果：  
![datastore test]({{ site.url }}/uploads/2014/04/cachestore-result-2.jpg)

确实只有一条SQL查询，证明我们的设计和程序逻辑是没有问题。

### 五. 小结

通过设计模式分离出一个数据缓存层，解决了以下的问题：

1.  其他关联模块中可以通过**CacheKeyCreater**的方式直接获取到Key进行操作处理；
2.  单元测试时可以自主选择使用缓存层或是数据层进行测试；
3.  通过接口的规数据访问层和数据缓存层的方法，一旦修改出错，必然抛出致命错误，提高开发人员在项目初期对接口进行规范设计的技能，减少对数据层的修改。