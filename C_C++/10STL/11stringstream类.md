# stringstream类

将字符串处理成标准输入输出流, 以便使用`>>`, `get()`, `getline()`等操作

## 默认空白字符为分隔符

```c++
#include<sstream>		//包含在sstream头文件中
#include<iostream>
#include<string>
using namespace std;


int main(){
    stringstream ss;	//构造流对象
    string source;		//需要处理的字符串
    string word;		//生成的单词
    ss << source;		//使用source字符串为ss流赋值
    while(ss >> word){	//将ss流中的单词赋值给word
        cout << word << endl;
    }
    return 0;
}
```

## 以指定字符为分隔符

本质还是使用`getline()`

```c++
#include<sstream>		//包含在sstream头文件中
#include<iostream>
#include<string>
using namespace std;


int main(){
    stringstream ss;	//构造流对象
    string source;		//需要处理的字符串
    string word;		//生成的单词
    ss << source;		//使用source字符串为ss流赋值
    
    while(getline(ss, word, '_')){	//将ss流中的单词赋值给word
        cout << word << endl;
    }
    return 0;
}
```

