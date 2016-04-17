# apacalblog cms 说明

## What
经过1个月的时间，终于将我的博客重构了一遍，页面和逻辑都重写了，介绍地址[http://cms.apacal.cn](http://cms.apacal.cn), git 地址：[https://github.com/apacal/apacalblog](https://github.com/apacal/apacalblog).

## How
### 下载

在github上可以找到最新的版本，下载下来，或者git clone 下来 。
~~~
git clone https://github.com/apacal/apacalblog
~~~

### 建立和导入数据库
~~~
cd doc
​mysql -uroot -p
~~~

~~~sql
create database apacalblog;

use apacalblog;

source ./apacalblog.sql
~~~

### 修改配置文件
编辑配置文件，修改mysql的连接密码和memcached的连接密码等
~~~
vim Application/Common/Conf/config.php
~~~

~~~php
// pdo
'DB_TYPE'                       =>              'pdo', // 数据库类型
'DB_USER'                       =>              'root', // 用户名

'DB_PWD'                        =>              'dev2014', // 密码
'DB_PORT'                       =>              3306, // 端口
'DB_PREFIX'                       =>             'ablg_',//  数据表前缀
'DB_DSN'                        =>              'mysql:host=localhost;dbname=apacalblog;charset=utf8',


"LOAD_EXT_FILE"                 =>              "MemcahedManager,MemcachedSession",


// Memcache设置
'MemCached'     =>   array(

    'hostname'  =>      '127.0.0.1',
    'port'      =>      '11211',
    'isSasl'    =>      false,
    'username'   =>      'apacal',
    'passwd'    =>      'passwd'
),
'NoCachedDie' => true,
~~~
注意如果你的memcache是有密码的，将isSasl改为true, 并把密码改成您的密码。

### 配置web服务器
修改nginx或者apache的配置文件

nginx默认是不支持pathinfo模式的，想要让nginx支持pathinfo。可以参考我的这篇文章[安装nginx + php5-fpm + pathinfo](http://apacal.cn/article/40.html#nav-home).

### 其他
后台的图片管理使用了ckfinder插件，在plugin/ckfinder目录中。

结束

安装好了就打开网站去体验一下，注意默认用户密码是demo,用户demo.
