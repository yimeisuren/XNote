# Java简单语法

## 命令行操作

| dos命令 | 解释                         |
| ------- | ---------------------------- |
| `dir`   | 列出当前目录下的文件和文件夹 |
| `md`    | 创建目录                     |
| `rd`    | 删除目录                     |
| `cd`    | 进入到指令目录               |
| `..`    | 上一级目录                   |
| `.`     | 当前目录                     |
| `\`     | 根目录                       |
| `del`   | 删除文件                     |
| `exit`  | 退出dos命令行                |

## 编译和运行

`javac`命令: 将`.java`文件编译为`.class`文件

`java`命令: 运行`.class`文件

## 从键盘中输入

1. `new`一个`Scanner`对象
2. 调用对象中的`nextXXX()`方法

```java
public class TestScanner {
    public static void main(String[] args) {
//        System.in表示标准输入流, 即键盘
        Scanner scanner = new Scanner(System.in);
//        获取int类型数据
        int i = scanner.nextInt();
        System.out.println(i);

//        获取double类型数据
        double d = scanner.nextDouble();
        System.out.println(d);

//        获取String类型数据
        String s = scanner.next();
        System.out.println(s);
    }
}
```

## 数组

> 数组初始化有默认值, 等价于机器码中的0

```java
public class TestArray {
    public static void main(String[] args) {
//      类型推断
        int[] ids01 = {1, 2, 3, 4, 5, 6};
//        默认初始化值为0
        int[] ids02 = new int[5];
    }
}
```

## 可变个数的形参

```java
    /**
     * 可变个数的形参, 认为是不限制长度的数组即可
     *
     * @param strs
     */
    public void show(String... strs) {
        for (String str : strs) {
            System.out.println(str);
        }
    }
```

## final关键字

+ 修饰类: 不能被继承, 说明该类功能比较完善
+ 修饰方法: 不能被子类重写
+ 修饰变量: 此时的变量称为"常量", 相当于C++中的`const `关键字. 修饰形参时表示该变量为只读属性

## abstract抽象类和抽象方法

> 例如学生类, 可以细分为小学生, 初中生, 高中生, 大学生, 研究生. 当所有的情况都被划分完后, 那么学生类就没有必要创建一个对象了, 因为所有的学生必然可以对应一个更为具体的子类, 此时可以将学生类作为抽象类, 用abstract进行修饰

```java
public abstract class Animal {
    private String name;

    //抽象类也一定有构造器, 包含抽象方法的类一定是抽象类
    Animal() {
        name = "dog or cat";
    }


    abstract void shout();

    abstract void eat();
}
```

## 抽象类的匿名子类

```java
public abstract class Animal {
    public abstract void shout();

    public static void main(String[] args) {
        Animal animal = new Animal() {
            @Override
            public void shout() {
                System.out.println("匿名子类随便叫");
            }
        };
        //d
        animal.shout();
    }
}
```

# 面向对象

类是**抽象**的概念, 对象是具体的**实例**

## Java类及类成员

```java
public class Person {
    private String name;
    private Integer age;
    
    static {
        System.out.println("静态代码块, 只在类加载的时候执行一次");
    }

    {
        System.out.println("代码块, 每次new对象都会执行");
    }
    
    
    /**
     * 无参构造器
     */
    public Person() {
    }

    /**
     * 含参构造器
     *
     * @param name 姓名
     * @param age  年龄
     */
    public Person(String name, Integer age) {
        this.name = name;
        this.age = age;
    }

    public Person(Person p) {
//        为了展示效果, 添加一个后缀
        this.name = p.getName() + "_copy";
        this.age = p.getAge();
    }

    /**
     * 方法
     *
     * @return 获取name属性值
     */
    public String getName() {
        return name;
    }

    public Integer getAge() {
        return age;
    }

    /**
     * 实现链式编程, 设置姓名
     *
     * @param name 输入的姓名字符串
     * @return 当前对象
     */
    public Person setName(String name) {
        this.name = name;
        return this;
    }

    public Person setAge(Integer age) {
        this.age = age;
        return this;
    }


    public static void main(String[] args) {
        Person p1 = new Person("Tom", 18);
//        给对象p1取别名
        Person p2 = p1;
        p2.setName("June");
        System.out.println(p1.getName());

//        使用该方法需要提供一个构造器
        Person p3 = new Person(p1);
        System.out.println(p3.getName());

    }
}

```

## 权限修饰符

| 修饰符      | 类内部 | 同一个包 | 不同包的子类 | 同一个工程 |
| ----------- | ------ | -------- | ------------ | ---------- |
| `private`   | √      |          |              |            |
| `default`   | √      | √        |              |            |
| `protected` | √      | √        | √            |            |
| `public`    | √      | √        | √            | √          |

## 继承性

无论对于属性和还是方法, 默认都省略了`this.方法名/属性`, 如果想要使用父类中的方法, 则使用`super.方法名/属性`

如果子类和父类有同名属性或方法, **二者都会被保留下来**, 只是调用方式不同. 

使用`this`调用子类中的属性或方法, 如果子类中的属性或方法是继承于父类的, 则等同于使用`super`调用父类中的该属性或方法. 只有在调用同名属性或方法时, `this`和`super`的调用结果才有区别

## 多态性

父类的引用指向子类的对象, 但在调用子类和父类的重名方法时, **实际执行的是子类中的方法**, 并且不能执行子类中特有的方法

比如排序, 不同的子类中具体实现排序的方法可能不同, 在写父类的时候只需要关注宏观把握, 具体实现由实际生成的子类去按照对应的特性优化实现

多态性方便实现虚拟方法的调用, 在父类中定义虚拟方法, 在子类中实现

```java
List<Integer> list = new ArrayList<>();
```

### 多态性的优点

可以和函数调用进行类比, 使用函数相比于代码写死的优点是可以根据传入的形参得到不同的结果. 而多态性的优点是可以将父类作为一种变量类型, 传入不同的子类从而去执行不同的方法, 得到不同的运行结果

```java
public abstract class Animal {
    abstract void shout();
    abstract void eat();
}

public class Dog extends Animal {
    @Override
    void shout() {
        System.out.println("汪汪汪");
    }

    @Override
    void eat() {
        System.out.println("狗吃狗粮");
    }
}

public class Cat extends Animal {
    @Override
    void shout() {
        System.out.println("喵喵喵");
    }

    @Override
    void eat() {
        System.out.println("猫吃猫粮");
    }
}

public class TestAnimal {
    void func(Animal animal) {
//        使用父类对象实现虚拟方法的统一
        animal.shout();
        animal.eat();
    }

    public static void main(String[] args) {
        TestAnimal testAnimal = new TestAnimal();
//        多态性的使用, 将子类当作参数传递
        testAnimal.func(new Dog());
        testAnimal.func(new Cat());
    }
}
```

### 强制类型转换(向下转型)

由于多态性中使用父类引用指向子类的对象, 因此不能直接调用子类对象中的特有方法, 但是实际上这些方法是存在于子类对象中的. 向下转型则是为了实现该功能

`instanceof`: 用于判断一个对象实例**本质上**是否属于某个类, 一般用于强制类型转换之前进行判断

## JDK中主要的包

| JDK包       | 功能                                               |
| ----------- | -------------------------------------------------- |
| `java.lang` | 核心类, 包含String、Math、System、Thread等常用功能 |
| `java.net`  | 执行与网络相关的操作                               |
| `java.io`   | 提供多种输入/输出功能                              |
| `java.util` | 工具类, 例如日期时间, 系统特性, 接口的集合框架等   |
| `java.text` | java格式化相关的类                                 |
| `java.sql`  | 进行JDBC数据库编程相关的类                         |
| `java.awt`  | 用于构建图形化用户界面                             |

## MVC设计模式

将整个程序分为三个层次: 视图模型层、控制器层、数据模型层

### 模型层model: 主要处理数据

+ 数据对象封装: `model.bean/domain`
+ 数据库操作类: `model.dao`
+ 数据库: `model.db`

### 视图层view: 显示数据

+ 相关工具类: `view.utils`
+ 自定义类: `view.ui`

### 控制器层controller: 处理业务逻辑

+ 应用界面相关: `controller.activity`
+ 存放fragment: `controller.fragment`
+ 显示列表的适配器: `controller.adapter`
+ 服务相关: `controller.service`
+ 抽取的基类: `controller.base`

# 设计模式



<center>设计模式汇总表</center>

| 类型       | 设计模式           | 特点                   |
| ---------- | ------------------ | ---------------------- |
| 创建型模式 | 工厂方法模式       |                        |
|            | 抽象工厂模式       |                        |
|            | 单例模式           | 类只能存在一个对象实例 |
|            | 建造者模式         |                        |
|            | 原型模式           |                        |
| 解构型模式 | 适配器模式         |                        |
|            | 装饰器模式         |                        |
|            | 代理模式           |                        |
|            | 外观模式           |                        |
|            | 桥接模式           |                        |
|            | 组合模式           |                        |
|            | 享元模式           |                        |
| 行为型模式 | 策略模式           |                        |
|            | 模板方法模式(多态) | 抽象类, 通用组件       |
|            | 观察者模式         |                        |
|            | 迭代子模式         |                        |
|            | 责任链模式         |                        |
|            | 命令模式           |                        |
|            | 备忘录模式         |                        |
|            | 状态模式           |                        |
|            | 访问者模式         |                        |
|            | 中介者模式         |                        |
|            | 解释器模式         |                        |



## 单例模式Singleton

保证某个类只能存在一个对象实例, 并且该类只提供一个取得其对象实例的方法

**要求**

+ 构造器私有化, 并在内部创建对象实例
+ 提供静态方法来获取该唯一对象实例
+ 静态方法只能调用静态属性, 所以该对象实例也是静态的

### 饿汉式

> 不管项目中是否需要使用到该实例对象, 一开始便创建实例对象

```java
public class Bank {
    private String name;
    private String address;

    private static Bank instance = new Bank();

    private Bank() {
    }

    public static Bank getInstance() {
        return instance;
    }

    public String getName() {
        return name;
    }

    public String getAddress() {
        return address;
    }

    public Bank setName(String name) {
        this.name = name;
        return this;
    }

    public Bank setAddress(String address) {
        this.address = address;
        return this;
    }

    @Override
    public String toString() {
        return "Bank{" +
                "name='" + name + '\'' +
                ", address='" + address + '\'' +
                '}';
    }

    public static void main(String[] args) {

//        验证方式一
        Bank.getInstance().setName("花旗银行").setAddress("欧洲");
        System.out.println(Bank.getInstance());
//        验证方式二
        Bank bank01 = Bank.getInstance();
        Bank bank02 = Bank.getInstance();
        System.out.println(bank01 == bank02);
    }
}
```

### 懒汉式

> 一开始并不急着创建对象, 同时调用方法时需要判断实例对象是否为`null`

```java
// 线程不安全的懒汉式
public class Order {
    private Integer no;
    private String name;
    private Integer mount;

    /**
     * 一开始并不急着创建对象
     */
    private static Order instance = null;

    private Order() {
    }

    public static Order getInstance() {
//        需要判断当前实例对象是否为null, 否则此时每次调用都会new一个Order对象, 那样就不是单例模式
        if (instance != null) {
            return instance;
        }
        instance = new Order();
        return instance;
    }
}
```

```java
//线程安全的懒汉式
public class OrderSafe {
    private Integer no;
    private String name;
    private Integer mount;

    private static OrderSafe instance = null;
    private OrderSafe() {
    }

    //只是对获取实例对象的方法使用synchronized来修饰
    public static synchronized OrderSafe getInstance() {
        if (instance != null) {
            return instance;
        }
        instance = new OrderSafe();
        return instance;
    }
    
    //方式一的等价写法, 静态方法需要使用 类名.class
    public static OrderSafe getInstance() {
        synchronized(OrderSafe.class){
            if (instance != null) {
                return instance;
            }
            instance = new OrderSafe();
            return instance;
        }
    }
    
    //方式二, 提高返回的效率, 需要理解为什么有效率提升
    public static OrderSafe getInstance() {
        if(instance == null){
            synchronized(OrderSafe.class){
                if (instance != null) {
                    return instance;
                }
                instance = new OrderSafe();
                return instance;
            }            
        }
    }    
}
```

### 应用场景

+ 网站的计数器
+ 应用程序的日志应用
+ 数据库连接池
+ 读取配置文件的类
+ Application进程
+ Windows中的任务管理器Task Manager
+ Windows中的回收站Recycle Bin

## 模板方法模式Template Method

**抽象类的应用**

功能内部的一部分是确定的, 另外一部分是不确定的, 将不确定的部分暴露出去, 让子类去实现这部分. 函数调用就是最简单的模板方法

能不能实现动态的模板方法呢? **目前的模板方法和工厂方法存在相同的问题. 能不能动态的创建一个方法, 然后让模板函数去执行.**

## 代理模式Proxy

**接口的应用**

**应用场景**

+ **安全代理**: 屏蔽对真实角色的直接访问
+ **远程代理**: 通过代理类处理远程方法调用
+ **延迟加载**: 先加载轻量级的代理对象, 真正需要再加载真实的对象

分为静态代理和动态代理

### 静态代理

针对一个接口就需要写一个代理类

```java
interface Rent {
    /**
     * 租房
     */
    void rent();
}

/**
 * 被代理类
 */
class Customer implements Rent {

    /**
     * 租房
     */
    @Override
    public void rent() {
        System.out.println("租客: 要求房租每月不超过1000");
    }
}


/**
 * 代理类, 并在其中通过多态的方式接收被代理类对象
 */
class Company implements Rent {

    Rent rentObject;

    /**
     * 提供一个构造器, 接收请求的对象
     *
     * @param rentObject 请求租房的对象, 通过接口和多态的方式进行传递
     */
    public Company(Rent rentObject) {
        this.rentObject = rentObject;
    }

    /**
     * 租房
     */
    @Override
    public void rent() {
        ready();
        rentObject.rent();
        transaction();
    }

    private void transaction() {
        System.out.println("双方签订合同");
    }

    private void ready() {
        System.out.println("中介公司进行一些准备工作, 约谈房东");
    }
}

```

### 动态代理

+ 代理类和被代理类实现同一套接口
+ **通用的一个代理类工厂**可以对应多个被代理类, 根据被代理类运行时实现的接口去**动态创建代理类**

```java

```

## 工厂模式

具体的需求案例

> 1. 披萨的种类很多
> 2. 披萨的制作流程比较固定
> 3. 完成披萨的订购

### 无工厂模式

```java

```



### 简单工厂模式

### 工厂方法模式

### 抽象工厂模式

# 多线程

## 基本概念: 程序, 进程和线程

**程序:** 静态的代码

**进程:** 程序的一次运行过程. 有产生, 存在和消亡的过程, 即**生命周期**

**线程:** 一个进程可以并行执行多个线程. 系统会为每个进行分配内存区域, 而进程中每个**线程有自己独立的运行栈和程序计数器**, 线程之间共享堆空间和方法区

### 线程状态State



### wait和sleep的区别

wait只能在同步代码块中调用, 必须释放锁. 

sleep不会释放锁, 因此可以在任何地方调用.

## 线程的创建(重点)

### 继承Thread类

1. 创建一个继承于`Thread`的类
2. 重写`run`方法
3. 调用`start`方法: 启动线程并执行`run`方法

```java
public class MyThread extends Thread {
    private static int count = 100;
    
    @Override
    public void run() {
        while (true) {
            if (count > 0) {
                System.out.println(currentThread().getName() + ":" + count--);
            } else {
                break;
            }
        }
    }

    public static void main(String[] args) {
        MyThread thread01 = new MyThread();
        MyThread thread02 = new MyThread();
        MyThread thread03 = new MyThread();
        thread01.setName("窗口1");
        thread02.setName("窗口2");
        thread03.setName("窗口3");
        thread01.start();
        thread02.start();
        thread03.start();

    }
}
```

###  实现Runnable接口

1. 创建一个实现了`Runnable`接口的类
2. 实现类去实现接口中的`run`方法
3. 创建实现类的对象, 并以该对象作为`Thread`类含参构造器的参数构造Thread类的对象
4. 调用`start`方法

```java
public class MThread implements Runnable {
    /**
     * 使用Runnable接口的方式只生成了一个对象, 因此多个线程共用一个对象中的数据
     */
    private int count = 100;

    @Override
    public void run() {
        while (true) {
            if (count > 0) {
                System.out.println(Thread.currentThread().getName() + ":" + count--);
            } else {
                break;
            }
        }
    }

    public static void main(String[] args) {
//只生成一个MThread类的对象
        Runnable mThread = new MThread();

//通过一个相同的对象生成多个线程
        Thread thread01 = new Thread(mThread, "线程1");
        Thread thread02 = new Thread(mThread, "线程2");
        thread01.start();
        thread02.start();
    }
}
```

### 两种方式的比较

+ 实现Runnable接口更适合用来创建多线程, 
  + 一是没有单继承的局限性, 
  + 二是能天然地体现出共享数据的概念, 在继承Thread类的方法中**共享数据**和**锁**都需要使用`static`进行修饰, 否则不唯一

+ 两种方式都需要重写`run`方法

## 线程的生命周期

`Thread.State`记录了线程的生命周期的状态

+ **新建:** `new`线程对象
+ **就绪:** 执行`start`后, 等待cpu调用
+ **运行:** 占用cpu, 执行`run`方法体中的内容
  + `yield`会从**运行态**返回到**就绪态**
  + `sleep, join, wait, suspend`等方法以及`等待同步锁`会使得当前进程**阻塞**
+ **堵塞:** 比如一些打印输出功能
+ **死亡:** 调用`stop`方法, 或者执行完`run`方法

## 线程的同步/并发安全问题(重点)

> 各种实现方法之间有着细微差异, 但**核心是保证用同一把锁**, 需要区分`this`所指代的是什么对象

+ 同步代码块
+ 同步方法
+ Lock同步锁

### 同步代码块synchronized

+ **锁:** 任何一个类的对象都可以作为锁. 要求多个线程共用同一把锁
+ **临界区:** 需要被同步的代码, 操作共享数据

**优缺点**

+ **优点:** 解决了线程安全问题
+ **局限性:** 操作临界区部分的代码相当于单线程, 稍微降低了一些速度

同步代码块显然不能包含太少代码, 同时也不能包含太多代码, 否则可能会导致逻辑发生变化, 比如下面案例中的`while`和`synchronized`互换

```java
public class MThread implements Runnable {
    /**
     * 使用Runnable接口的方式只生成了一个对象, 因此多个线程共用一个对象中的数据
     */
    private int count = 100;
    /**
     * 需要保证不同的线程共用同一把锁, 使用Runnable接口实现类的方式其实可以不用该
     */
    final Object obj = new Object();

    @Override
    public void run() {
        while (true) {
            synchronized (MThread.class) {
//                某种具体的类也是一个对象, 是更高层次抽象的类的一个对象
//            synchronized(this){
//            synchronized(obj){  
                if (count > 0) {
                    try {
                        Thread.sleep(100);
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                    System.out.println(Thread.currentThread().getName() + ":" + count--);
                } else {
                    break;
                }
            }
        }
    }

    public static void main(String[] args) {
//只生成一个MThread类的对象
        MThread mThread = new MThread();

//通过一个相同的对象生成多个线程
        Thread thread01 = new Thread(mThread, "线程1");
        Thread thread02 = new Thread(mThread, "线程2");
        thread01.start();
        thread02.start();
    }
}
```

### 同步方法

使用`synchronized`关键字对方法名进行修饰. 对于**非static方法**, 锁默认为`this`, 而对于**static方法**, 锁默认为的`类名.class`

### Lock同步锁

lock方式需要手动的申请和释放锁, 而synchronized方式自动释放锁

```java
import java.util.concurrent.locks.ReentrantLock;

public class LockTest implements Runnable {
    private int count = 100;
    /**
     * 实例化lock
     */
    ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        while (true) {
//            lock()和unlock()必须成对使用, lock在try前面, unlock在finally里面
//            获取锁
            lock.lock();
            try {
                if (count > 0) {
                    System.out.println(Thread.currentThread().getName() + ":" + count--);
                } else {
                    break;
                }
            } finally {
                lock.unlock();
            }
        }
    }
}
```

### 死锁问题

隐蔽的死锁问题经常是同步方法中调用其它对象的同步方法, 每次执行同步方法都要获取当前对象, 先后需要两个对象及以上作为锁便满足死锁条件

**避免死锁的策略**

+ 避免嵌套锁
+ 尽量少的使用同步资源



## 线程的通信



# 常用类

## String类

字符串常量池存放在方法区中. String类追根溯源最后都会变成对字符串常量池中字符串的引用.

String类是不可变的(final).

只有当两个数据均处于字符串常量池中时, 使用`+`连接操作得到的结果才位于字符串常量池, 否则会在堆空间中new新的对象.

Comparable表示自然排序, 例如第三方库中的类不方便修改其底层源代码, 但是那些类又没有实现Comparable接口, 而Comparator表示定制排序

### Comparator接口

```java
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

public class Person {
    private String name;
    private int age;
    private String id;

    public Person(String name, int age, String id) {
        this.name = name;
        this.age = age;
        this.id = id;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", id='" + id + '\'' +
                '}';
    }

    public static void main(String[] args) {
        List<Person> arrayList = new ArrayList<>();

        //使用lambda表达式实现Comparator接口, 更容易理解
        Comparator<Person> comparator =
                (p1, p2) -> {
                    if (p1.age == p2.age) {
                        return p1.name.compareTo(p2.name);
                    }
                    return Integer.compare(p2.age, p1.age);
                };

        arrayList.add(new Person("xiong", 24, "260424xxxx"));
        arrayList.add(new Person("hello", 24, "260424xxxx"));
        arrayList.add(new Person("hello", 25, "260424xxxx"));

        arrayList.sort(comparator);

        System.out.println("arrayList: " + arrayList);
    }
}
```

编码和解码



格式化和解析

# 枚举类

## 枚举类的使用

类的对象只有<u>有限个, 确定的</u>. 例如:

+ <u>星期:</u> 一, 二, 三, 四, 五, 六, 日
+ <u>性别:</u> 男, 女

**当需要定义一组常量时, 强烈建议使用枚举类**

如果枚举类中只有一个常量, 则可以作为单例模式的一种实现方式

## 定义枚举类

### 自定义枚举类

在Java5.0之前, 需要自定义枚举类

```java
public class Season {
    //    提供枚举类的私有属性
    private final String seasonName;
    private final String seasonDesc;

    //    私有化构造器
    private Season(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    //    提供静态的枚举类对象
    public static final Season SPRING = new Season("春", "冷");
    public static final Season SUMMER = new Season("夏", "热");
    public static final Season AUTUMN = new Season("秋", "爽");
    public static final Season WINTER = new Season("冬", "冷");
}
```

### enum定义枚举类

在Java5.0之后, 提供了`enum`关键字. 使用enum关键字定义枚举类时, 默认继承Enum类.

```java
enum Period {

    //    枚举类中需要首先声明定义的常量(类中可选项)
    //    常量之间用逗号隔开, 最后一个使用分号
    SPRING("春", "冷"),
    SUMMER("夏", "热"),
    AUTUMN("秋", "爽"),
    WINTER("冬", "冷");

    private final String seasonName;
    private final String seasonDesc;
    
    private Period(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }
}
```

## 枚举类实现接口

可以在枚举类中实现接口方法, 达到统一一致的目的

也可以在枚举类对象中分别实现接口方法, 达到个性化定制的目的

```java
interface Info {
    void publicShow();

    void personShow();
}


//使用enum关键字定义枚举类, 默认继承Enum类
enum Period implements Info {

    //    枚举类中需要首先声明定义的常量(类中可选项)
    //    常量之间用逗号隔开, 最后一个使用分号
    SPRING("春", "冷") {
        @Override
        public void personShow() {
            System.out.println("春天在哪里");
        }
    },
    SUMMER("夏", "热") {
        @Override
        public void personShow() {
            System.out.println("宁夏");
        }
    },
    AUTUMN("秋", "爽") {
        @Override
        public void personShow() {
            System.out.println("秋天不回来");
        }
    },
    WINTER("冬", "冷") {
        @Override
        public void personShow() {
            System.out.println("大约在冬季");
        }
    };

    private final String seasonName;
    private final String seasonDesc;

    private Period(String seasonName, String seasonDesc) {
        this.seasonName = seasonName;
        this.seasonDesc = seasonDesc;
    }

    @Override
    public void publicShow() {
        System.out.println("这是一个万物复苏的季节");
    }
}
```



# 注解

注解, 即元数据(Metadata).

注解需要搭配反射获取值, 从而执行相应程序流程才有意义.

## 自定义注解

```java
public @interface MyAnnotation {
    //    如果只有一个属性, 建议属性名为value
    //    使用default添加默认值
    String value() default "hello";

    //    类型可以是数组, 用来添加多个值
    String[] flag();
}
```

## 元注解

元数据: 用来对数据进行解释说明.

元注解: 用来对注解数据进行解释说明

+ `@Target`: 用来指明`@MyAnnotation`注解可以用来修饰哪些数据类型
+ `@Inherited`: 被其修饰的注解具有继承性, 
+ `@Retention`: 指定所修饰注解的声明周期, 只有声明为<u>RUNTIME</u>的注解才能通过反射获取

```java
@Target({ElementType.FIELD, ElementType.METHOD, ElementType.PARAMETER})
public @interface MyAnnotation {
    //    如果只有一个属性, 建议属性名为value
    //    使用default添加默认值
    String value() default "hello";

    //    类型可以是数组, 用来添加多个值
    String[] flag();
}

```

## 可重复注解

重复注解是指可以重复书写多个的注解

### java8之前的实现方式

需要使用数组来接收值

```java
public @interface MyAnnotation {
    String[] value();
}

@MyAnnotation({@MyAnnotation(value = "java"), @MyAnnotation(value = "c++")})
public class Person {
    private String name;
    private int age;
    private String id;

    public Person(String name, int age, String id) {
        this.name = name;
        this.age = age;
        this.id = id;
    }
}
```

### java8之后的实现方式

使用元注解`@Repeatable`



# Java集合

## Collection接口

### List接口

### Set接口

### Queue接口

#### 优先队列PriorityQueue

优先队列, 也就是大根堆. 通过传入<u>定制的比较器</u>, 可以实现某个值越小, 优先级越高. 

堆的算法中总结来看只有两个算法, 一个是建堆算法, 一个是添加算法(删除算法是一种特殊的添加).

堆是一棵完全二叉树, 父子节点之间的关系为

+ `leftIndex = 2 * index + 1`, 当`index < size / 2`时必定有左子节点, 当`index >= size / 2`时没有子节点
+ `rightIndex = 2 * index + 2`, 必定有左子节点时未必有右子节点
+ `parentIndex = (index - 1) / 2`

## Map接口









# 反射

反射是动态语言的关键, 允许程序在**执行期间**获取**任何类**的内部信息, 并直接操作**任意对象**的内部属性和方法

加载完某个类后, 在**堆内存的方法区**中产生了一个`Class`类型的对象---每个类对应一个且只有唯一一个Class对象. 这个对象包含类的完整的结构信息

## Java反射机制提供的功能

+ 在运行时判断任意一个对象所属的类
+ 在运行时构造任意一个类的对象
+ 在运行时判断任意一个类所具有的成员变量和方法
+ 在运行时获取泛型信息
+ 在运行时调用任意一个对象的成员变量和方法
+ 在运行时处理注解
+ 生成**动态代理**

## 反射相关主要API

+ `java.lang.Class`: 含义是**类的抽象**, 不妨称为**通用类**, 所有的类都是该类的对象, 有些存储**元数据**的感觉
+ `java.lang.reflect.Method`: 类的方法
+ `java.lang.reflect.Field`: 类的成员变量
+ `java.lang.reflect.Constructor`: 类的构造器

## `java.lang.Class`类的理解

1. 程序经过`javac.exe`命令编译后, 会生成多个字节码文件`.class`
2. 当使用`java.exe`执行字节码文件时, 加载到内存中的类便称为运行时类, 此运行时类就是`Class`类的一个对象

**注意:** `Class`类的对象不是`new`出来的, 而是运行时类便对应`Class`类的一个对象

## 获取运行时类的对象的方法

加载到内存中的运行时类会缓存一定的时间, 在这段时间内获取到的都是同一个运行时类的对象

最常使用的是第三种方式, 因为将类作为参数传入, 没有写死代码, 更加灵活

```java
//      获取运行时类的方法一: 通过类名.class直接获取
        Class<Person> personClass1 = Person.class;
        System.out.println(personClass1);
//        方法二: 通过运行时类的对象获取
        Person person = new Person();
        Class<? extends Person> personClass2 = person.getClass();
        System.out.println(personClass2);
//        方法三: 调用Class的静态方法forName(), 传入类的全类名, 从蓝色的java路径开始写
        Class<?> personClass3 = Class.forName("java高级编程.反射.Person");
        System.out.println(personClass3);
```

## 反射中的常用方法

| 方法                  | 作用                                           |
| --------------------- | ---------------------------------------------- |
| `getFields()`         | 获取当前运行时类及其父类中所有的`public`的方法 |
| `getDeclaredFields()` | 获取当前运行时类中所有的方法, 包括`private`    |
| `getModifiers()`      | 获取权限修饰符                                 |
| `getType()`           | 获取类型                                       |
| `getName()`           | 获取变量名                                     |

