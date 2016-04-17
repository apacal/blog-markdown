# PHPSTORM Xdebug单步调试

## What
以前写php都是用vim ，print_r, echo, var_dump来调试验证程序正确与否，从实习用了phpstorm后，发现一些好的ide对效率还是有很大的帮助，本文就来介绍下然后配置好phpstrom＋xdebug。

## How

### 什么是xdebug，它与phpstorm是怎么工作的

xdebug是一款php调试插件，支持远程调试，就是在php文件运行的时候，能通过tcp协议，来发送调试信息到远程端口，ide在收到调试信息的时候，可以向xdebug发送单步运行，中止运行，停止运行等命令。这样就实现了类似vs那样强大的调试功能。
当服务端收到一个xdebug的请求（浏览器发出，通过特定的请求字段或者cookie值来标志），就会主动和请求者的9000端口建立调试连接。(9000端口是在php.ini文件中定义的，也可以是其他端口，关闭ide的监控时，通过抓包可以看出，服务端向客户机的9000发送请求连接，ide发了个rst断开了)

### 安装xdebug和配置xdebug

如果是使用ubuntu的直接使用apt-get 安装
~~~
(apacal)-(jobs:0)-(~/yEd)
(! 1989)-> sudo apt-cache search xdebug | grep php
~~~

找到xdebug的ini文件，加入监听端口和key等信息，通过 remote_host 和 remort_port 两个参数指定调试客户端位置。对于本地调试缺省即可，如果要远程调试，把参数改到对应接收调试信息的客户端地址即可

~~~
(apacal)-(jobs:0)-(~/yEd)
(! 1995)-> sudo vim /etc/php5/fpm/conf.d/20-xdebug.ini

zend_extension=xdebug.so
xdebug.idekey="PHPSTORM"

xdebug.remote_host=127.0.0.1
xdebug.remote_port=9000
xdebug.remote_enable=on
~~~

重启php，查看是否有xdebug的信息

~~~
(apacal)-(jobs:0)-(~/yEd)
(! 1998)-> sudo service php5-fpm restart

php5-fpm stop/waiting

php5-fpm start/running, process 28489
~~~

~~~
(apacal)-(jobs:0)-(~/yEd)
(! 1999)-> php -i | grep xdebug | grep 9000

xdebug.remote_port => 9000 => 9000
~~~
PHP的配置好了，下面进行配置ide。
### 配置phpstorm

File>Settings>PHP>Debug 下，找到 Xdebug 部分配置信息，看 Debug port 是否是 9000 （其实就是要和前面服务端php.ini里的remote_port保持一致）；  

File>Settings>PHP>Debug>DBGp Proxy 下，IDE key 随便填（填在php.ini中的xdebug.idekey的值，后面浏览器扩展设置部分也会用到这个key值）；Host填你的XDebug所在服务器地址（本地调试那就是 localhost），Port 填你的HTTP应用所在的端口，缺省当然就是 80 ；

File>Settings>PHP>Servers 下，可以添加多个需要Debug的主机信息，便于以后快速切换使用；

phpstorm有一个电话的按钮，点击绿色表示监控xdebug的连接，红色表示不监控。


服务端和ide都配置好了，那问题来了，php和ide是怎么建立连接，这里一般就是使用浏览器来做这个事情。

### 浏览器配置

方法一：在请求的url后面加上XDEBUG_SESSION_START=PHPSTORM  
　　表示注册一个xdebug交互，session为PHPSTORM   
　　比如：http://localhost/phpinfo.php?XDEBUG_SESSION_START=PHPSTORM   
方法二：安装浏览器的插件   
　　对于Firefox来说，可以安装类似 easy Xdebug (with moveable icon) 这样的扩展来快速激活Debug状态。！注意！有些xdebug相关扩展可能存在不兼容问题导致设置无效，请选择能够正常工作的扩展。一个不行换另一个试试。安装完之后需要设置一下扩展的参数。主要就是 Value of he debug cookie 的值，设置成与前面在DBGp Proxy下设置的IDE key一样即可。    
　　浏览器配置，如果插件不行就使用方法一来。  

关于调试的步骤：  

激活PHPStorm IDE的 "Start Listen for PHP Debug Connections" 按钮（右上角 Debug 区类似小电话的图标，带红色禁止小圆圈标志Start Listen for PHP Debug Connections表示未激活，变绿Stop Listen for PHP Debug Connections表示在监听中）；  
在PHPStorm中设置想要的断点；  
在浏览器中激活xdebug cookie（如果是Firefox+easy Xdebug扩展的话，点击工具栏的两个图标Toggle xdebug cookie和Toggle Xdebug Profiler cookie，变红表示cookie已成功设置）；  
在浏览器中访问对应的PHP页面，正常情况下窗口会弹到 PHPStorm IDE中并进入Debug状态；   

## We
配置好了，可以告别var_dump的老方法了，对开发效率还是有些提高。   

参考：[remote-drupalphp-debugging-xdebug-and-phpstorm](http://randyfay.com/content/remote-drupalphp-debugging-xdebug-and-phpstorm)
