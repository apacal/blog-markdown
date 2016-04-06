# PHP使用Memcahced和把session放到memecached中　　　


## What

前段时间阿里云开放了了免费的128MB的OCS，就申请了一个，把自己网站一些比较常用的数据放到Memcached里，将session也放到Memcached中。


### 申请阿里云的OCS

在阿里云官方网站登陆，可以看到OCS服务，开通即可，开通后可以知道自己的hostname, username, passwd等。[阿里云](http://www.aliyun.com/)

### 根据说明安装php-memcached

参看[官方的说明](http://help.aliyun.com/view/11108324_13703944.html?spm=5176.7225337.1997284509.12.pY7QvR)，安装完测试一下看是否可以连接到OCS


### 编写memcached instance

考虑到memcached instance 可能要在多个模块使用，需要写一个单实例的instance


~~~php
// file MemcachedManager.php
class MemcachedManager {
    public static $mySelf = null;
    public static $memcached = null;

    public static function getInstance() {
        if (!(self::$mySelf instanceof self)){
            self::$mySelf = new self;
        }
        return self::$memcached;
    }

    public function __construct() {
        $config = C('MemCached');

        self::$memcached = new Memcached;  //声明一个新的memcached链接
        self::$memcached->setOption(Memcached::OPT_COMPRESSION, false); //关闭压缩功能
        self::$memcached->setOption(Memcached::OPT_BINARY_PROTOCOL, true); //使用binary二进制协议
        self::$memcached->addServer(isset($config['hostname']) ? $config['hostname'] : 'http://localhost', isset($config['port']) ? $config['port'] : '11211'); //添加OCS实例地址及端口号

        if (isset($config['isSasl']) && $config['isSasl'] === true) {
            self::$memcached->setSaslAuthData(isset($config['username']) ? $config['username'] : 'apacal', isset($config['passwd']) ? $config['passwd'] : 'dev2014'); //设置OCS帐号密码进行鉴权
        }

    }


}
~~~
在这之后就可以使用MemcachedManager::getInstance()函数来获取memcached的instance



### 编写session interface

使用php的``bool session_set_save_handler ( SessionHandlerInterface $sessionhandler [, bool $register_shutdown = true ] )`` 函数来自定义session存储机制

~~~php
// file MemcachedSession.php
require_once dirname(__FILE__) ."/MemcachedManager.php";
session_start();

class MemcachedSession implements SessionHandlerInterface {

    public function open($save_path, $sessionName)
    {
        if (MemcachedManager::getInstance()) {
            return true;
        }
    }

    public function close()
    {
        return true;
    }

    public function read($id)
    {
        return MemcachedManager::getInstance()->get($id);
    }

    public function write($id, $data)
    {
        return MemcachedManager::getInstance()->set($id, $data, SESSION_TTL ? SESSION_TTL : 60*60) ? true : false;
        //return MemcachedManager::getInstance()->set($id, $data, 60*60*24) ? true : false;
    }

    public function destroy($id)
    {
        return MemcachedManager::getInstance()->delete($id);
    }

    public function gc($maxlifetime)
    {
        // don not need to flush session, memcached has ttl

        return true;
    }
}


try {
    set_session_hander(new MemcachedSession(), MemcachedManager::getInstance());
}catch(Exception $e) {
    print_r($e);
}

function set_session_hander($handler, Memcached $cachedHandler) {
    if ($cachedHandler->set('hello', 'hello') === true) {
        if (!session_set_save_handler($handler, true) == true) {
            //echo "fail";
        }
    } else {
        $isToDie = C('NoCachedDie');
        if ($isToDie === true) {
            die('cacehed can not connect');
        }
    }
}
~~~

在应用开始前将上面两个文件包含进来，就完成。我是使用ThinkPHP框架的，所以有一些框架的函数，例如``C函数等``

## We

之前使用过redis，个人体验，觉得redis的内置结构更多，接口也更全，memcache相对弱一点，好了，现在可以对一些数据进行缓存了;可以看看/var/lib/php目录下，有没有session文件产生，验证session是否放到了Memcached中。




