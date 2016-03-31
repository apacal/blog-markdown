# cpp编写explode函数

## What
在之前使用php的使用，经常使用explode函数，将字符串分割成数组，而c++本身不提供这样一个函数，很简单就实现这样的一个函数。

## How
### 使用遍历的方式
​简单直接对string进行遍历，发现是delim char时，进行判断是否为空，不为空push到vector中

~~~cpp
#include <string>
#include <vector>
#include <iostream>
using namespace std;

vector<string> explode(const string& str, const char& ch) {
    string next;
    vector<string> result;
    // For each character in the string
    for (string::const_iterator it = str.begin(); it != str.end(); it++) {
        // If we've hit the terminal character
        if (*it == ch) {
            // If we have some characters accumulated
            if (!next.empty()) {
                // Add them to the result vector
                result.push_back(next);
                next.clear();
            }
        } else {
            // Accumulate the next character into the sequence
            next += *it;
        }
    }
    if (!next.empty())
         result.push_back(next);
    return result;
}

int main (int, char const **) {
    std::string blah = "___this_ is__ th_e str__ing we__ will use__";
    std::vector<string> result = explode(blah, '_');
    for (size_t i = 0; i < result.size(); i++) {
        cout << "\"" << result[i] << "\"" << endl;
    }
    return 0;
}
~~~

注意该函数，只有explode后不为空才会push_back到vector中。输出结果如下
~~~cpp
"this"
" is"
" th"
"e str"
"ing we"
" will use"
~~~

### 使用STL
STL中的getline提供了这样一种形式[http://www.cplusplus.com/reference/string/string/getline/?kw=getline](http://www.cplusplus.com/reference/string/string/getline/?kw=getline)

~~~cpp
istream& getline (istream&  is, string& str, char delim);
~~~
我们可以巧妙使用getline分割。

~~~cpp
#include <string>
#include <vector>
#include <iostream>
#include <sstream>
using namespace std;

vector<string> explode(const string& str, const char& delim)
{

    vector<string> result;
    stringstream data(str);
    string line;
    while(getline(data,line, delim)) {
        result.push_back(line); // Note: You may get a couple of blank lines
    }
    return result;
}

int main (int, char const **) {
    std::string blah = "___this_ is__ th_e str__ing we__ will use__";
    std::vector<string> result = explode(blah, '_');
    for (size_t i = 0; i < result.size(); i++) {
        cout << "\"" << result[i] << "\"" << endl;
    }
    return 0;
}
~~~
使用STL中的方法，有一点需要注意，分割时，不管结果是否为空都会压入result，但是当最后一个元素是空的话，是不会push_back到result中的，输出结果如下。
~~~cpp
""
""
""
"this"
" is"
""
" th"
"e str"
""
"ing we"
""
" will use"
""
~~~

## We
其实c++还是很方便的，特别是stl中有很多强大的地方，之前是搞php的，从php转到c++,觉得也没有太大的难度，只是将[http://php.net/manual/en/](http://php.net/manual/en/)换成了[http​://www.cplusplus.com/](http​://www.cplusplus.com/)。

觉得使用c++重要一点就是善用stl.stl提供的东西和php提供的一些类似，让你脱离去管理内存，只是没有php那么都内置的函数而已，但是这些你都可以简单就写出来。
