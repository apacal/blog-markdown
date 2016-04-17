# java使用velocity来生成java代码

## What
类似于PHP中的Smarty，Velocity是一个基于java的模板引擎（template engine）。
它允许任何人仅仅简单的使用模板语言（template language）来引用由java代码定义的对象。从而实现界面和Java代码的分离，使得界面设计人员可以和java程序开发人员同步开发一个遵循MVC架构的web站点。
另外，Velocity的能力远不止web站点开发这个领域，例如，它可以从模板（template）产生SQL和PostScript、XML，它也可以被当作一个独立工具来产生源代码和报告，或者作为其他系统的集成组件使用。
Velocity也可以为Turbine web开发架构提供模板服务（template service）。
当然我们也可以使用velocity来动态生成java文件，比如在android开发中根据xml中定义的数据表结构来生成对应的java类。
cpp有对应的ctemplate。

## How

### 获取Velocity相关JAR文件：
从[http://velocity.apache.org/](http://velocity.apache.org/)网站上下载最新的Velocity，这里我们下载了velocity-1.7.zip
相关Jar包添加到项目中：
解压velocity-1.7.zip，发下其根目录下有两个JAR文件：
~~~
velocity-1.7.jar
velocity-1.7-dep.jar
~~~
其中velocity-1.7-dep.jar包含了：
~~~
velocity-1.7.jar commons-collections-3.2.1.jar commons-lang-2.4.jar oro-2.0.8.jar
~~~
这些JAR文件位于解压目录的lib目录下， 在JAR包不冲突的情况下可以直接使用velocity-1.7-dep.jar

载类路径下添加velocity.properties文件：
该文件一般包含如下配置：
~~~
runtime.log = /tmp/velocity_example.log
file.resource.loader.path = /tmp
input.encoding = UTF-8
output.encoding = UTF-8
~~~
~~~
runtime.log指定日志文件存放位置
file.resource.loader.path指定模板的加载位置
input.encoding指定输入编码
output.encoding指定输出编码
~~~
我们也可以在java中动态设置这些信息，
~~~java
try {
    Velocity.init();
    Velocity.setProperty("file.resource.loader.path", templatePath);
}
catch(Exception e) {
    System.out.println("Problem initializing Velocity : " + e );
    return;
}

/* lets make a Context and put data into it */

VelocityContext context = new VelocityContext();
context.put("formatter", formatter);
context.put("packageName", "cn.apacal.codegen.table");
context.put("tableName", "user");

Map<String, String> fields = new HashMap<>();
fields.put("type", "int");
fields.put("content", "String");

context.put("fields", fields);
~~~

### Velocity语法
#### 访问对象属性：
默认是不支持访问对象属性的，可以通过编写方法来变通实现访问属性。
~~~java
public class Field {
    public String type;
    public String defaultValue;

    public String getType() {
        return type;
    }

    public String getDefaultValue() {
        return defaultValue;
    }

    public boolean isNullDefault() {
        if (defaultValue == null || defaultValue.length() <= 0) {
            return false;
        } else {
            return true;
        }
    }

    @Override
    public String toString() {
        return String.format("type[%s], defaultValue[%s]", type, defaultValue);
    }
}
~~~
在模版中使用调用方法来访问属性，在 1.7版本中，定义Field的类必须是一个单独的文件，内嵌类是不起作用。
~~~java
this field type is ${field.getType()}
~~~

#### 遍历List集合：
~~~
#foreach($element in $list)
	#element
#end
~~~

#### 使用判断语句：
~~~
#if($condition)
	true
#else
	false
#end
~~~
#### 获取迭代索引值：
默认使用变量名：~~~$velocityCount~~~
也可以自定义此变量名，在~~~velocity.properties~~~中设置：
~~~directive.foreach.counter.name=index~~~
设置索引起始位置为0：
~~~directive.foreach.counter.initial.value=0~~~

#### 遍历Map变量：
~~~
#foreach($key in $map.keySet())
	$key : $map.get($key)
#end
~~~
####在模板中进行赋值：
~~~
#set(#a=”Hello World!”)
$a

#set($array1=[1..10])
#foreach($entry in $array1)
	#entry
#end
~~~

## code-gen-java项目
[code-gen-java](https://github.com/apacal/code-gen.git)是一个使用~~~velocity~~~来生成java源文件的程序，可以用在一些重复性的编码工作，比如根据xml来生成对应的java文件。
在code-gen-java项目中，是一个根据xml文件生成对应的java源码文件。


### 使用方法
在idea中生成jar文件，然后在终端运行（或者直接在idea中运行）
~~~java -jar jarFile  genType templatePath templateName xmlPath outPath~~~

~~~
java -jar out/artifacts/codegen_jar/codegen.jar db /Users/apacalzhong/IdeaProjects/codegen/template/ table.vm /Users/apacalzhong/IdeaProjects/codegen/template/table.db /Users/apacalzhong/IdeaProjects/codegen/build/
~~~
