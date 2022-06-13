# string容器

在一个文件中*使用 string类必须包含下面的两行*

```c++
#include <string>			//向编译器请求标准模板库文件string
using namespace std;	
```

*字符串里的单个字符可以通过它的索引访问，就像数组一样*

```c++
假设string s = "nevermore";
则s[0] = 'n', s[1] = 'e';
```

string对象在字符串的末尾会有一个隐式的空字符, 即`s[s.size()] = '\0'`

## 构造器(构造函数)

+ `string()`

  ```c++
  //字符串初始为空
  string s1; 
  ```

  

+ `string(const char* chs)`

  ```c++
  //字符串初始为chs的拷贝
  string s1("abcde");
  //等价形式
  string s1 = "abcde";
  ```

  

+ `string(const string s, unsigned int pos=0, unsigned int n=-1)`

  ```c++
  /*	unsigned int n = -1表示最大的无符号数
  	取s的子串s[pos, pos + n-1], 若越界则按字符串长度的最大(小)值处理
  	pos: 子串的起始索引(无符号数)
  	n: 子串的长度(无符号数)
  */
  string s = "abcdefghijk";
  string s1(s, 2, 4);  	//s1 = "cdef"
  string s2(s, -1, 5); 	//pos = -1, 最大的无符号数, 抛出异常
  string s3(s, 2, 100); 	//范围越界, 按子串的最大长度处理s3 = "cdefghijk"
  ```

## 运算符

+ `=`运算符*(深拷贝)*

  `string& operator=(const string& str)`

  ```c++
  string s = "flower";
  string s1 = s; 			//s1 = "flower"
  s = "hello world";		//修改s值对s1无影响, s1="flower"
  ```

  

+ `[]`运算符*(返回引用, 可修改)*
  `char& operator[](unsigned int pos)`

  ```c++
  string s = "flower";	//s[0] = 'f';
  s[0] = 'e';				//对s本身进行了修改, s = "elower"
  ```

+ `+`运算符
  
  ```c++
  string s1 = "abcd";
  string s2 = "efg";
  string s = s1 + s2; 	//s = "abcdefg"
  ```

+ 比较运算`>`,`<`, `==`
  
  ```c++
  //对字符串的内容按字典序进行排序
  //更深入可以从内存存储的角度对其分析
  string s1 =	"a";
  string s2 = "ab";
  string s3 = "b";
  //s1内存存储 [a][\0]
  //s2内存存储 [a][b][\0]
  //s3内存存储 [b][\0]
  //字符存储在内存中实际上也是ASCII码值, 从低字节向高字节依次对比过去即可排序
  ```
  
  
  
+ `cin>>`输入
  *以空白字符为依次输入的结束标志(不包含结束符)*

  ```c++
  string s;
  string t;
  cin >> s;
  cin >> t;
  //如果输入 hello world, everyone
  //s = "hello"
  //t = "world,"
  ```

+ `getline(cin, <...>)`输入
  *只以回车符`\n`为输入的结束标志(不包含结束符)*

  ```c++
  string s;
  getline(cin, s);
  //假如输入hello world, everyone
  //s = "hello world, everyone"
  ```
  
  该函数有返回值, 当读到文件末尾时, 会返回NULL(`底层0值`), 因此*根据它判断是否读完文件*
  
  ```c++
  #include<fstream>
  string s;
  char* filePath = "<输入文件的文件路径>";
  ifstream ism(filePath, ios::in);
  //以上两句可等价于ifstream ism("<输入文件的文件路径>");
  while(getline(ism, s)){
      //todo
  }
  ```

## 其它常用操作

+ `c_str()` 
  在`open()`中, 必须使用c风格的*字符数组*, 因此需要使用`c_str()`将string类转为字符数组

  ```c++
  string inputFileName;
  cout << "请输入文件名"<<endl;
  cin >> inputFileName;							//读入输入文件名
  ifstream ism;									//空参构造输入缓冲流
  ism.open(inputFileName.c_str(), ios::in);		//打开文件, ios::in表示读模式
  string s;
  while(ism >> s){				//按单词读取直至文件尾; getline(ism, s)可按行读取
      cout << s << end;
  }
  ism.close();
  ```

  
  
+ `erase()`
  `string& erase(unsigned int pos=0, unsigned int n=-1)`

  ```c++
  //pos: 起始位置
  //n: 长度
  //从原字符串s中清除从pos位置开始, 长度为n的子串, 进行溢出处理
  #include <assert.h>
  string s = "abcdefghijk";
  s.erase(5, 3);			//s = "abcdeijk"
  assert(s == "abcdeijk");
  ```
  
  string类还可以看作是`vector<char>`的一个拓展版本
  
  ```c++
  string s = "abcdefghijk";
  s.erase(<迭代器位置>);
  ```
  
  

+ `find()`
  `unsigned int find(const string& str, unsigned int pos=0)const;`
  *返回值类型是无符号整型*
  关于变量类型, 可以使用`typeid(<变量名>).name()`查看





## API小结

|  类型  |       方法        |                       用途/备注                        |
| :----: | :---------------: | :----------------------------------------------------: |
| 初始化 |     无参构造      |                                                        |
|        |     有参构造      |                                                        |
|        |     拷贝构造      |                                                        |
|  赋值  |         =         |                     `=`操作符重载                      |
|        |     .assign()     |                                                        |
|  取值  |        []         |                     `[]`操作符重载                     |
|        |       .at()       |                                                        |
|  拼接  |        +=         |                     `+=`操作符重载                     |
|        |     .append()     |                                                        |
|  查找  |      .find()      |                 从前往后查找第一个字符                 |
|        |     .rfind()      | ==从后往前查找第一个字符, 即从前往后查找最后一个字符== |
|  转换  |     .c_str()      |                     转为char*类型                      |
|        |      stoi()       |                      转为int类型                       |
|        |      stof()       |                     转为double类型                     |
|        |    to_string()    |                     数字转为字符串                     |
|  长度  | .size()/.length() |                                                        |
|  判空  |     .empty()      |                                                        |
|  替换  |     replace()     |                                                        |
|  比较  |     compare()     |            大于返回1, 小于返回-1, 等于返回0            |
|  子串  |     substr()      |                                                        |
|  插入  |     insert()      |                                                        |
|  删除  |      erase()      |                                                        |
|  添加  |    push_back()    |                                                        |
|  弹出  |    pop_back()     |                                                        |

### 代码测试

```c++
#include<iostream>
#include<string>

using namespace std;


int main(){
    
	return 0;    
}
```

```c++
#include <iostream>
#include <string>

using name

int main() 
{
    double f = 23.43;
    double f2 = 1e-9;
    double f3 = 1e40;
    double f4 = 1e-40;
    double f5 = 123456789;
    std::string f_str = std::to_string(f);
    std::string f_str2 = std::to_string(f2); // Note: returns "0.000000"
    std::string f_str3 = std::to_string(f3); // Note: Does not return "1e+40".
    std::string f_str4 = std::to_string(f4); // Note: returns "0.000000"
    std::string f_str5 = std::to_string(f5);
    std::cout << "std::cout: " << f << '\n'
              << "to_string: " << f_str  << "\n\n"
              << "std::cout: " << f2 << '\n'
              << "to_string: " << f_str2 << "\n\n"
              << "std::cout: " << f3 << '\n'
              << "to_string: " << f_str3 << "\n\n"
              << "std::cout: " << f4 << '\n'
              << "to_string: " << f_str4 << "\n\n"
              << "std::cout: " << f5 << '\n'
              << "to_string: " << f_str5 << '\n';
}
```

