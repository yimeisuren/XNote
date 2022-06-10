[TOC]



# Java多线程编程核心技术

## Java多线程技能

### 线程随机性演示

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        super.run();
        for (int i = 0; i < 1000; i++) {
            System.out.println(Thread.currentThread().getName());
        }
    }

    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.setName("thread_my");
        //调用start()会通知"线程规划器"; 如果调用run(), 则表示main线程的同步执行, 实际上就是单线程
        myThread.start();
        for (int i = 0; i < 1000; i++) {
            System.out.println(Thread.currentThread().getName());
        }
    }
}
```

### <u>执行start()的顺序不代表执行run()的顺序</u>

start()表示线程的启动顺序, 告诉"线程规划器"可以一切准备就绪

run()表示线程的执行顺序, 由"线程规划器"从已经就绪的线程中选择一个运行

```java
public class MyThread extends Thread {
    private final int flag;

    static final int COUNTS = 10;

    public MyThread() {
        this.flag = 0;
    }

    public MyThread(int flag) {
        this.flag = flag;
    }

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        for (int i = 0; i < COUNTS; i++) {
            MyThread myThread = new MyThread(i);
            myThread.setName("thread_" + i);
            myThread.start();
        }
    }
}
```

### 实现Runnable接口

如果创建的线程类已经有一个父类, 由于Java单继承的特性, 此时想要实现多线程就不能继承Thread而需要实现Runnable接口. 实现Runnable接口和继承Thread的本质并没有多大区别, Thread也是一个实现了Runnable接口的类.

```java
public class MyRunnable implements Runnable {
    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }

    public static void main(String[] args) {
        Runnable runnable = new MyRunnable();
        Thread thread = new Thread(runnable);
        thread.setName("root");
        thread.start();
    }
}
```

<u>继承Thread方式中run函数是啥时候执行的呢?</u>

### Servlet技术中造成的非线程安全问题

```java
import static java.lang.Thread.sleep;

public class LoginServlet {
    private static String usernameRef;
    private static String passwordRef;

    public static void doPost(String username, String password) {
        usernameRef = username;
        if (username.equals("root")) {
            try {
                sleep(50);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
        passwordRef = password;
        System.out.println("username: " + usernameRef + " password: " + passwordRef);
    }


    public static void main(String[] args) throws InterruptedException {
        ThreadA threadA = new ThreadA();
        ThreadB threadB = new ThreadB();
        threadA.start();
        threadB.start();
    }
}


class ThreadA extends Thread {
    @Override
    public void run() {
        LoginServlet.doPost("root", "root");
    }
}

class ThreadB extends Thread {
    @Override
    public void run() {
        LoginServlet.doPost("xiong", "xiong");
    }
}
```

### Thread.currentThread()和this不同

+ Thread.currentThread()表示执行该代码段的线程, 即当前正在执行的线程
+ this表示该类的实例化对象, 在继承了Thread类后默认名与实例化时的线程名相同

```java
public class MyThread extends Thread {
    public MyThread5() {
        System.out.println("**************************");
        System.out.println("Thread.currentThread().getName(): " + Thread.currentThread().getName());
        System.out.println("Thread.currentThread().isAlive(): " + Thread.currentThread().isAlive());
        System.out.println("this.getName(): " + this.getName());
        System.out.println("this.isAlive(): " + this.isAlive());
        System.out.println("**************************");
    }

    @Override
    public void run() {
        System.out.println("**********************************************");
        System.out.println("Thread.currentThread().getName(): " + Thread.currentThread().getName());
        System.out.println("Thread.currentThread().isAlive(): " + Thread.currentThread().isAlive());
        System.out.println("this.getName(): " + this.getName());
        System.out.println("this.isAlive(): " + this.isAlive());
        System.out.println("**********************************************");
    }

    public static void main(String[] args) {
//        线程名: Thread-0
        MyThread mythread = new MyThread();
//        线程名: Thread-1        
        Thread thread = new Thread(mythread);
        thread.setName("thread");
        thread.start();
    }
}
```

### Thread.sleep()使得当前正在执行的进行休眠

如果当前类继承了Thread, 根据多态性还可以使用`this.sleep()`, 但感觉没有必要

```java
public class MyThread extends Thread {
    @Override
    public void run() {
        Thread.sleep(10);
        System.out.println("Thread.currentThread().getName(): " + Thread.currentThread().getName());
    }

    public static void main(String[] args) throws InterruptedException {
        Thread thread = new MyThread();
        thread.setName("thread");
        thread.start();
        Thread.currentThread().setName("root");
        Thread.sleep(10);
        System.out.println("Thread.currentThread().getName(): " + Thread.currentThread().getName());
    }
}
```

### getStackTrace()方法获取当前活动线程的堆栈跟踪信息

```java
public class MyThread extends Thread {
    public void a() {
        b();
    }

    private void b() {
        c();
    }

    private void c() {
        d();
    }

    private void d() {
        run();
    }


    @Override
    public void run() {
        StackTraceElement[] elements = Thread.currentThread().getStackTrace();
        for (StackTraceElement element : elements) {
            System.out.println("element.getClassName(): " + element.getClassName());
            System.out.println("element.getFileName(): " + element.getFileName());
            System.out.println("element.getLineNumber(): " + element.getLineNumber());
            System.out.println("element.getMethodName(): " + element.getMethodName());
            System.out.println("*****************************************************");
        }
    }

    public static void main(String[] args) {
        MyThread thread = new MyThread();
        thread.a();
    }
}
```

### getAllStackTrace()获取所有活动线程的堆栈跟踪信息

<u>掌握如果遍历查找map即可</u>, 使用`map.keySet().iterator()`获得迭代器, 使用`Key key = (Key) it.next()`将找到键的名字, 通过`it.hasNext()`判断是否到达容器尾端

### 停止线程

停止线程在Java语言中并不像`break`语句那样干脆, 还需要一些技巧性的处理. <u>停止一个线程意味着在线程处理完任务之前停止正在做的操作，也就是放弃当前的操作</u>，虽然这看起来非常简单，但是必须做好防范措施，以便达到预期的效果. 

在Java中有3种方法可以使正在运行的线程终止运行: 

1. 使用退出标志使线程正常退出. 
2. 使用`stop()`方法强行终止线程，但是这个方法不推荐使用，因为`stop()`和`suspend()`、`resume()`一样，都是<u>作废过期</u>的方法，使用它们可能发生不可预料的结果. 
3. 使用`interrupt()`方法中断线程. 

#### 停止不了的线程

`interrupt()`方法仅仅是在当前线程中做了一个停止的标记，并不是真正停止线程. 如下代码所示, 子线程仍然会执行完counts次循环

```java
public class MyThread extends Thread {
    int counts = 1000;

    @Override
    public void run() {
        for (int i = 0; i < counts; i++) {
            System.out.println("i: " + i);
        }
    }

    public static void main(String[] args) {
        Thread thread = new MyThread();
        thread.start();
        System.out.println("thread.isInterrupted(): " + thread.isInterrupted());//false
        thread.interrupt();
        System.out.println("thread.isInterrupted(): " + thread.isInterrupted());//true
    }
}
```

#### 判断线程是否为停止状态

+ `isInterrupte()`
+ `interrupted() = currentThread.isInterrupte(true)`

二者都会先检查当前中断状态并返回, 但`interrupted()`会<u>清除中断状态, 即中断状态由true变为false</u>, 而`isInterrupte()`只会检查中断状态.

<u>interrupt()和sleep()不能同时使用, 在sleep状态下中断或在中断状态下sleep都会导致异常并清除中断状态</u>

```java
public class MyThread extends Thread {
    int counts = 1000;

    @Override
    public void run() {
        try {
            for (int i = 0; i < counts; i++) {
                Thread.sleep(100);	  //写在循环里面只是为了测试两种可能的情况, 有可能先sleep再interrupt, 或者顺序反过来
                System.out.println("i: " + i);
            }
        } catch (InterruptedException e) {
            System.out.println("异常: 中断状态下sleep");
            e.printStackTrace();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        Thread thread = new MyThread();
        thread.start();
        Thread.sleep(200);
        thread.interrupt();
    }
}

```



#### 停止线程的方法

+ <u>检查中断状态, 直接返回或抛出异常</u>

  ```java
  if (currentThread().isInterrupted()) {
      return; //使用return可能存在一定量的代码冗余, 例如输出日志信息
  }
  ```

+ 使用`stop()`暴力停止线程<u>(已废弃)</u>

  因为会使得程序执行结果不可控

### 暂停线程



### yield()释放CPU



### 线程优先级

优先级高的线程可以获得更多的CPU时间片.

优先级具有继承性, <u>子线程继承父线程的优先级</u>.

优先级具有随机性, <u>优先级高不是绝对优先执行, 而是有更大概率被调度上处理机</u>.

### 用户线程和守护线程

当用户线程完全结束后, 则JVM会结束

```java
public class MyThread {
    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            System.out.println("Thread.currentThread().getName(): " + Thread.currentThread().getName());
            System.out.println("Thread.currentThread().isDaemon(): " + Thread.currentThread().isDaemon());
            while (true) {

            }
        });
//        设置为守护线程, 如果不设置为守护线程, 则会陷入while(true){}的死循环中, 程序不会终止
        thread.setDaemon(true);
        thread.start();
        System.out.println("Thread: main is over");
    }
}
```



## 对象及变量的并发访问

多线程并发一定需要涉及到对<u>共享数据</u>的处理, 例如对多个对象的static类变量, 对同一个对象的成员变量. 在成员方法内部的变量是局部变量, 每个线程都会有自己的一份局部变量数据, 因此不涉及到共享数据, 不存在并发问题.





### Lock同步锁

lock方式需要手动的申请和释放锁, 而synchronized方式自动释放锁

lock()和unlock()必须成对使用, lock在try前面, unlock在finally里面

```java
import java.util.concurrent.locks.ReentrantLock;

public class LockTest implements Runnable {
    private int count = 100;
    /**
     * 实例化lock, ReentrantLock可重入锁
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

### 线程间通信

#### <u>多线程编程步骤(上篇)</u>

1. 编写资源类, 并提供相应的操作
2. 在Runnable接口的实现类中调用资源类, 通过其提供的操作组合实现具体业务
3. 创建多线程

```java
/**
 * 资源类: 售票员
 * 并提供售票功能
 */
class Ticket {
    private int number = 30;

    synchronized public void sale() {
        if (number > 0) {
            System.out.println(Thread.currentThread().getName() + "正在售票, 剩余票数: " + number--);
        }
    }
}

/**
 * 调用售票员的售票功能
 */
class MRunnable implements Runnable {
    Ticket ticket = new Ticket();

    @Override
    public void run() {
        for (int i = 0; i < 30; i++) {
            ticket.sale();
        }
    }
}

/**
 * 创建多个线程
 */
public class SaleTicket {
    public static void main(String[] args) {
        MRunnable runnable = new MRunnable();
        Thread threadA = new Thread(runnable, "threadA");
        Thread threadB = new Thread(runnable, "threadB");
        Thread threadC = new Thread(runnable, "threadC");
        threadA.start();
        threadB.start();
        threadC.start();
    }
}
```

另一种感觉更为优雅的实现方式,直接让资源类实现Runnable接口, 在run()方法中实现具体的业务逻辑

<u>存在的疑问: 为什么在这种情况下run()方法中只调用number次会造成票数好像没卖完的现象?</u>

```java
/**
 * 资源类: 售票员
 * <p>
 * 并提供售票功能
 */
class Ticket implements Runnable {
    private static final int COUNTS = 300;
    //临界资源
    private int number = COUNTS;

    synchronized public void sale() {
        if (number > 0) {
            System.out.println(Thread.currentThread().getName() + "正在售票, 剩余票数: " + number--);
        }
    }

    @Override
    public void run() {
        //这里不能使用number, 对临界资源的调用应该只能出现在同步代码块中, 否则出现异常
        for (int i = 0; i < COUNTS; i++) {
            sale();
        }
    }
}


/**
 * 创建多个线程
 */
public class SaleTicket {
    public static void main(String[] args) {
        Runnable ticket = new Ticket();
        Thread threadA = new Thread(ticket, "ticketA");
        Thread threadB = new Thread(ticket, "ticketB");
        Thread threadC = new Thread(ticket, "ticketC");
        threadA.start();
        threadB.start();
        threadC.start();
    }
}
```

#### <u>多线程编程步骤(中篇)</u>

1. 编写资源类, 提供相应的操作
2. 在操作中遵循<u>判断, 干活, 通知其它线程</u>的逻辑 
3. 创建多线程

需要保证多个线程使用的是同一个资源类对象, 这里使用单例模式设计资源类, 还可以在main()函数中创建ticket对象, 通过匿名类和lambda表达式的方式来创建多线程.

##### 存在虚假唤醒问题的版本

使用if进行判断会存在虚假唤醒问题, 因为唤醒会使得线程从wait()处开始执行, 因此如果使用if进行判断, 在线程第二次执行时便跳过了if判断, 因此<u>wait()需要在循环中进行使用</u>

```java
class Ticket {
    static Ticket ticket;
    private static final int COUNTS = 0;
    private int number = COUNTS;

    private Ticket() {
    }


    synchronized public static Ticket getInstance() {
        if (ticket == null) {
            ticket = new Ticket();
        }
        return ticket;
    }

    synchronized public void incr() throws InterruptedException {
//        判断
        if (number != 0) {
            wait();
        }
//        干活
        number++;
        System.out.println(Thread.currentThread().getName() + ": " + number);
//        通知其它线程
        notifyAll();
    }

    synchronized public void decr() throws InterruptedException {
        if (number == 0) {
            wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName() + ": " + number);
        notifyAll();
    }
}

class MRunnableIncr implements Runnable {
    Ticket ticket = Ticket.getInstance();

    @Override
    public void run() {
        try {
            for (int i = 0; i < 10; i++) {
                ticket.incr();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class MRunnableDecr implements Runnable {
    Ticket ticket = Ticket.getInstance();

    @Override
    public void run() {
        try {
            for (int i = 0; i < 10; i++) {
                ticket.decr();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}


/**
 * 创建多个线程
 */
public class SaleTicket {
    public static void main(String[] args) {
        Thread threadA = new Thread(new MRunnableIncr(), "incr");
        Thread threadB = new Thread(new MRunnableDecr(), "decr");
        threadA.start();
        threadB.start();
    }
}
```

##### 解决虚假唤醒问题的版本

```java
class Ticket {
    static Ticket ticket;
    private static final int COUNTS = 0;
    private int number = COUNTS;

    private Ticket() {
    }


    synchronized public static Ticket getInstance() {
        if (ticket == null) {
            ticket = new Ticket();
        }
        return ticket;
    }

    synchronized public void incr() throws InterruptedException {
//        判断
        while (number != 0) {//将if换成while即可
            wait();
        }
//        干活
        number++;
        System.out.println(Thread.currentThread().getName() + ": " + number);
//        通知其它线程
        notifyAll();
    }

    synchronized public void decr() throws InterruptedException {
        while (number == 0) {//将if换成while即可
            wait();
        }
        number--;
        System.out.println(Thread.currentThread().getName() + ": " + number);
        notifyAll();
    }
}

class MRunnableIncr implements Runnable {
    Ticket ticket = Ticket.getInstance();

    @Override
    public void run() {
        try {
            for (int i = 0; i < 10; i++) {
                ticket.incr();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}

class MRunnableDecr implements Runnable {
    Ticket ticket = Ticket.getInstance();

    @Override
    public void run() {
        try {
            for (int i = 0; i < 10; i++) {
                ticket.decr();
            }
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
}


/**
 * 创建多个线程
 */
public class SaleTicket {
    public static void main(String[] args) {
        MRunnableIncr runnableIncr = new MRunnableIncr();
        MRunnableDecr runnableDecr = new MRunnableDecr();
        Thread threadA = new Thread(runnableIncr, "incrA");
        Thread threadB = new Thread(runnableDecr, "decrB");
        Thread threadC = new Thread(runnableIncr, "incrC");
        Thread threadD = new Thread(runnableDecr, "decrD");
        threadA.start();
        threadB.start();
        threadC.start();
        threadD.start();
    }
}
```

### 线程间定制化通信

**定制化需求**

> 启动三个线程AA,BB,CC,按顺序执行, AA打印5次, BB打印10次, CC打印15次, 重复10轮

**解决方案**

<u>使用一个标志位来控制顺序执行</u>





## 集合的线程安全问题

ArrayList集合中的方法并不是同步方法

```java
import java.util.ArrayList;
import java.util.List;
import java.util.UUID;

public class ListThread {
    public static void main(String[] args) {
        List<String> list = new ArrayList<>();
        for (int i = 0; i < 30; i++) {
            new Thread(() -> {
                list.add(UUID.randomUUID().toString().substring(0, 8));
                System.out.println("list: " + list);
            }, String.valueOf(i)).start();
        }
    }
}
```

### 方式一: 使用Vector

将`new ArrayList<>()`替换为`new Vector<>()`即可

### 方式二: 使用Collections工具类转换为线程安全的集合

使用`Collections.synchronizedList()`将ArrayList转化为相应线程安全的容器

### 方式三: 使用CopeOnWriteArrayList实现<u>写时复制</u>