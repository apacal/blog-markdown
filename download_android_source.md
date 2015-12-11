#android 源码下载
我的系统是64bit Linux mint 17.2, 编译的是android 2.3.1
##source code download

在开发网上下载android源码，首先要设置好代理（bash的http_proxy和git proxy.

```
export http_proxy="web-proxyhk.oa.com:8080"
export https_proxy="web-proxyhk.oa.com:8080"
git config --global https.proxy http://web-proxyhk.oa.com
git config --global http.proxy http://web-proxyhk.oa.com
```
接着就可以按照[source.android.com](http://source.android.com/source/downloading.html)上的说明下载源码了。

```
$ curl https://storage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
$ chmod a+x ~/bin/repo
```

PS:如果设置了还是不行，看看自己有没有使用tmux,我是使用了tmux1.8，会出现不能下载的情况。

## 编译（2.3.1版本）

```
frameworks/base/include/utils/KeyedVector.h:193:31: error： ‘indexOfKey’ was not declared in this scope, and no declarations were found by argument-dependent lookup at the point of instantiation [-fpermissive]
```
出现indexOfKey错误把gcc和g++进行降级到4.4就可以

```
sudo apt-get install gcc-4.4
sudo apt-get install g++-4.4 
sudo rm -rf /usr/bin/gcc 
sudo ln -s /usr/bin/gcc-4.4 /usr/bin/gcc 
gcc -v 
sudo rm -rf /usr/bin/g++ 
sudo ln -s /usr/bin/g++-4.4 /usr/bin/g++ 
g++ -v
```

***
出现```g++: selected multilib '32' not installed

``` 
sudo apt-get install g++-4.4-multilib
```
***
出现
```
dalvik/vm/native/dalvik_system_Zygote.c:191: error: storage size of ‘rlim’ isn’t known
```

```
vim dalvik/vm/native/dalvik_system_Zygote.c

#include <sys/resource.h>

```

***
错误信息
```
/usr/include/zlib.h:34:fatal error: zconf.h: No such file or directory
```
原因是zlib1g-dev有个头文件修改了路径，及zconf.h放到了/usr/include/x86_64-linux-gnu/，所以将其拷贝到/usr/include/下即可了

```
sudo cp /usr/include/x86_64-linux-gnu/zconf.h /usr/include/
```
***
错误信息
```
make: *** [out/host/linux-x86/obj/EXECUTABLES/zipalign_intermediates/zipalign] Error 1
```

安装lib64z1-dev

```
sudo apt-get install lib64z1-dev 
```

***

```
out/target/product/generic/obj/STATIC_LIBRARIES/libwebcore_intermediates/WebCore/HTMLNames.h
```

```
sudo apt-get install libswitch-perl  
```