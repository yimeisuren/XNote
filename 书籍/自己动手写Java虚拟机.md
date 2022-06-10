# 命令行工具

## 准备工作

### 安装JDK

Java虚拟机和Java类库一起构成了Java运行时环境. 在Oracle网站上下载安装后, 命令行输入`java -version`来查看是否安装成功.

### 安装Go

从[Go语言官网][Go语言官网]下载安装文件, 在命令行输入`go version`来查看是否安装成功. 其它常用子命令包括`help, fmt, install, test`等.

## java命令

Java虚拟机的工作是运行Java应用程序, Java应用程序的入口点是`main()`方法. 如果一个类包含`main()`方法, 那么这个类就可以用来启动Java应用程序, 称这个类为<u>主类</u>.

<u>Java虚拟机如何指导我们要从哪个类启动应用程序呢?</u> 对此, Java虚拟机规范没有明确规定, 也就是说由虚拟机自行决定. 例如**Oracle的Java虚拟机实现是通过java命令来启动的, 主类名由命令行参数指定**. java命令有以下4种形式:

+ `java [-options] class [args]`
+ `java [-options] -jar jarfile [args]`
+ `javaw [-options] class [args]`
+ `javaw [-options] -jar jarfile [args]`

java命令和javaw命令的差别在于javaw命令不会显示命令行窗口, 因此适合启动GUI程序. 可以向java命令传递三组参数: <u>选项, 主类名或者jar文件名, main()方法的参数</u>. 

+ ==选项==由短线`-`开头.
+ ==主类名==需要给出主类的**全类名(完全限定名)**, 如果使用-jar则表示使用jar包中的类, 需要指定jar包的文件路径. 可以指定多个, 在Windows系统中用`;`进行分隔, 在类UNIX系统中使用`:`进行分隔. 还可以使用`*`通配符来匹配所有的JAR文件.

<center>常用的选项及其用途</center>

| 选项               | 用途                   |
| ------------------ | ---------------------- |
| `-version`         | 输出版本信息, 然后退出 |
| `-?/-help`         | 输出帮助信息, 然后退出 |
| `-cp/-classpath`   | 指定用户类路径         |
| `-Dproperty=value` | 设置Java系统属性       |
| `-Xms<size>`       | 设置初始堆空间大小     |
| `-Xmx<size>`       | 设置最大堆空间大小     |
| `-Xss<size>`       | 设置线程栈空间大小     |

## 编写命令行工具

根据java命令的第一种用法, 手动编写一个模仿其格式的命令行工具

```go
package main

import (
	"flag"
	"fmt"
	"os"
)

type Cmd struct {
	helpFlag    bool
	versionFlag bool
	cpOption    string
	class       string
	args        []string
}

func printUsage() {
	fmt.Printf("Usage: %s [-options] class [args...]\n", os.Args[0])
}

/**
函数返回类型为*Cmd
*/
func parseCmd() *Cmd {
	//定义结构体变量
	cmd := &Cmd{}
	//设置flag.Usage变量, 将printUsage()函数赋值给它
	flag.Usage = printUsage
	//使用各种xxxVar()设置需要解析的选项
	flag.BoolVar(&cmd.helpFlag, "help", false, "print help message")
	flag.BoolVar(&cmd.helpFlag, "?", false, "print help message")
	flag.BoolVar(&cmd.versionFlag, "version", false, "print version and exit")
	flag.StringVar(&cmd.cpOption, "classpath", "", "classpath")
	flag.StringVar(&cmd.cpOption, "cp", "", "classpath")
	//使用Parse()函数解析选项, 如果解析失败则调用printUsage将命令的用法打印到控制台上
	flag.Parse()

	args := flag.Args()
	if len(args) > 0 {
		cmd.class = args[0]
		cmd.args = args[1:]
	}
	return cmd
}
```

```go
package main

import "fmt"

func main() {
	cmd := parseCmd()
	if cmd.versionFlag {
		fmt.Println("version 0.0.1")
	} else if cmd.helpFlag || cmd.class == "" {
		printUsage()
	} else {
		startJVM(cmd)
	}
}

func startJVM(cmd *Cmd) {
	fmt.Printf("classpath:%s class:%s args:%v\n", cmd.cpOption, cmd.class, cmd.args)
}
```



# 搜索class文件

第1章介绍了java命令的用法以及它如何启动Java应用程序:

1. 首先启动Java虚拟机
2. 然后加载主类
3. 最后调用主类的main()方法

以`HelloWorld.java`为例, 

+ 在加载HelloWorld类之前, 首先需要加载它的超类, 也就是`java.lang.Object`.
+ 在调用`main()`方法之前, 因为虚拟机需要准备好参数数组, 所以需要加载`java.lang.String`和`java.lang.String[]`类.
+ 把字符串打印到控制台还需要加载`java.lang.System`类

那么, <u>Java虚拟机从哪里寻找到这些类的呢?</u>

## 类路径

Java虚拟机规范并没有规定虚拟机应该从哪里寻找类, 例如<u>Oracle的Java虚拟机根据类路径(classpath)来搜索类</u>, 按照先后顺序可以将类路径分为3部分: `启动类路径(bootstrap classpath), 扩展类路径(extension classpath), 用户类路径(user classpath)`.

**启动类路径**默认对应`jre\lib`目录, Java标准库(大部分在`rt.jar`里面)位于该路径. 可以通过`-Xbootclasspath`选项修改启动类路径(一般不需要这么做)

**扩展类路径**默认对应`jre\lib\ext`目录, 使用Java扩展机制的类位于这个路径

**用户类路径**用来存放自定义的类和第三方的类, 默认值是当前目录, 也就是`.`目录. 可以设置CLASSPATH环境变量来修改用户类路径, 但是不够灵活, 所以不推荐; 更好的方式是通过为java命令传递`-classpath`或`-cp`选项, 选项的优先级更高, 可以覆盖环境变量中的设置.

## 准备工作

需要创建`classpath`子目录, 使用JDK的启动类路径来寻找和加载Java标准库中的类, 因此需要某种方式来指定jre目录的位置. 因此增加一个非标准的选项参数`-Xjre`: 修改结构体, 添加`XjreOption`

```go
package main

import (
	"flag"
	"fmt"
	"os"
)

type Cmd struct {
	helpFlag    bool
	versionFlag bool
	cpOption    string
	class       string
	args        []string

	/**
	用户传递jre所在的路径
	 */
	XjreOption string
}

func printUsage() {
	fmt.Printf("Usage: %s [-options] class [args...]\n", os.Args[0])
}


func parseCmd() *Cmd {
	//定义结构体变量
	cmd := &Cmd{}
	//设置flag.Usage变量, 将printUsage()函数赋值给它
	flag.Usage = printUsage
	//使用各种xxxVar()设置需要解析的选项
	flag.BoolVar(&cmd.helpFlag, "help", false, "print help message")
	flag.BoolVar(&cmd.helpFlag, "?", false, "print help message")
	flag.BoolVar(&cmd.versionFlag, "version", false, "print version and exit")
	flag.StringVar(&cmd.cpOption, "classpath", "", "classpath")
	flag.StringVar(&cmd.cpOption, "cp", "", "classpath")
    //命令行中Xjre到变量XjreOption的映射
	flag.StringVar(&cmd.XjreOption, "Xjre", "", "path to jre")
	flag.Parse()

	args := flag.Args()
	if len(args) > 0 {
		cmd.class = args[0]
		cmd.args = args[1:]
	}
	return cmd
}

```



## 实现类路径

类路径由启动类路径, 扩展类路径和用户类路径三个小路径构成. 而三个小路径又由更小的路径构成, 类似[组合模式(composite pattern)][设计模式]. 接下来将通过组合模式来设计和实现类路径.

### Entry接口

先定义一个接口来表示类路径项. 在`classpath`下创建`entry.go`文件, 在其中定义Entry接口

```go
package classpath

import (
	"os"
	"strings"
)

//pathListSeparator 用来存放路径分隔符, 在windows系统中实际上就是";"
const pathListSeparator = string(os.PathListSeparator)

// Entry 接口中有两个方法
type Entry interface {
	//readClass 负责寻找和加载class文件
	//className class文件的相对路径, 路径之间用`/`分隔, 文件名有.class后缀
	//Go的函数或方法允许有多个返回值, 可以使用最后一个返回值作为错误信息
	//返回值是读取到的字节数据, 最终定位到class文件的Entry
	readClass(className string) ([]byte, Entry, error)
	//String 返回变量的字符串表示相当于Java中的toString()方法
	String() string
}

//newEntry 根据参数创建不同类型的Entry实例
func newEntry(path string) Entry {
	//处理多个类路径
	if strings.Contains(path, pathListSeparator) {
		return newCompositeEntry(path)
	}
	//处理通配符
	if strings.HasSuffix(path, "*") {
		return newWildcardEntry(path)
	}
	//处理jar或zip
	if strings.HasSuffix(path, ".jar") || strings.HasSuffix(path, ".JAR") || strings.HasSuffix(path, ".zip") || strings.HasSuffix(path, ".ZIP") {
		return newZipEntry(path)
	}
	//处理单个文件
	return newDirEntry(path)
}
```

### DirEntry接口

DirEntry表示目录形式的类路径

```java
package classpath

import (
	"io/ioutil"
	"path/filepath"
)

// DirEntry Go结构体不需要显示实现接口, 只需要方法匹配即可. Go没有专门的构造函数, 代码中统一使用newXXX来表示创建结构体实例
type DirEntry struct {
	//absDir 用于存放目录的绝对路径
	absDir string
}

func newDirEntry(path string) *DirEntry {
	//将参数转换为绝对路径
	absDir, err := filepath.Abs(path)
	if err != nil {
		//如果转换过程中出现错误, 则调用panic()终止程序
		panic(any(err))
	}
	return &DirEntry{absDir}
}

func (self *DirEntry) readClass(className string) ([]byte, Entry, error) {
	//先将目录和class文件名拼接成一个完成的路径, 然后调用ReadFile()读取文件内容
	fileName := filepath.Join(self.absDir, className)
	data, err := ioutil.ReadFile(fileName)
	return data, self, err
}

func (self *DirEntry) String() string {
	return self.absDir
}

```

### ZipEntry接口

ZipEntry表示zip或jar形式的类路径

```go
package classpath

import (
	"archive/zip"
	"errors"
	"io/ioutil"
	"path/filepath"
)

type ZipEntry struct {
	absPath string
}

//newZipEntry 相当于构造函数
func newZipEntry(path string) *ZipEntry {
	absPath, err := filepath.Abs(path)
	if err != nil {
		panic(any(err))
	}
	return &ZipEntry{absPath}
}

//定义一个*ZipEntry类型的this变量
func (this *ZipEntry) String() string {
	return this.absPath
}

func (this *ZipEntry) readClass(className string) ([]byte, Entry, error) {
	//打开zip压缩包文件
	fs, err := zip.OpenReader(this.absPath)
	if err != nil {
		return nil, nil, err
	}
	//使用defer来确保打开的文件得以关闭
	defer fs.Close()
	//遍历解压后的文件夹
	for _, f := range fs.File {
		if f.Name == className {
			rc, err := f.Open()
			if err != nil {
				return nil, nil, err
			}
			defer rc.Close()
			//读取文件内容
			content, err := ioutil.ReadAll(rc)
			if err != nil {
				return nil, nil, err
			}
			return content, this, nil
		}
	}
	return nil, nil, errors.New("class not found: " + className)
}
```





[Go语言官网]:https://go.dev/dl/
[设计模式]:设计模式.md