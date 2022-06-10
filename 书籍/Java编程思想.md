# 图形化用户界面

## Swing基础

<u>Swing</u>是一组易于使用的JavaBeans, 它能够通过拖放操作, 也可以通过手工编写来创建合理的GUI程序.

```java
import javax.swing.*;
import java.util.concurrent.TimeUnit;

public class HelloSwing {
    public static void main(String[] args) throws InterruptedException {
//        创建窗口
        JFrame jFrame = new JFrame("Hello Swing");
        JLabel jLabel = new JLabel("A label");
        jFrame.add(jLabel);
//        setDefaultCloseOperation: 用户执行关闭操作时程序执行的操作
//        JFrame.EXIT_ON_CLOSE表示退出程序
        jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
//        设置窗口大小
        jFrame.setSize(300, 100);
//        使得窗口可视化
        jFrame.setVisible(true);
        TimeUnit.SECONDS.sleep(1);
//        修改JLabel容器中的值
        jLabel.setText("Hello label");
    }
}
```

对于<u>main线程</u>来说直接对GUI组件编写代码并非是一种好的想法, Swing有它自己的专用线程来接收UI事件并更新屏幕, 如果使用其它线程对屏幕进行操作, 可能会产生冲突和死锁.

通过`SwingUtilities.invokeLater()`来执行UI操作

```java
import javax.swing.*;
import java.util.concurrent.TimeUnit;

class MRunnable implements Runnable {
    JLabel jLabel;

    public MRunnable setjLabel(JLabel jLabel) {
        this.jLabel = jLabel;
        return this;
    }

    @Override
    public void run() {
        jLabel.setText("hello label");
    }
}

public class HelloSwing {
    public static void main(String[] args) throws InterruptedException {
        JFrame frame = new JFrame("frame");
        JLabel label = new JLabel("label");
        frame.add(label);
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(300, 100);
        frame.setVisible(true);
        TimeUnit.SECONDS.sleep(3);
//        对UI的操作
        SwingUtilities.invokeLater((new MRunnable()).setjLabel(label));
    }
}
```

主线程中的工作也应该通过`SwingUtilities.invokeLater()`的方式进行调用, 注意对`sleep()`的调用不能放入到构造器中或任何UI操作中, <u>否则会造成在sleep()期间将中止事件分发线程</u>, 修改后的程序如下

```java
package mSwing;

import javax.swing.*;
import java.util.concurrent.TimeUnit;

public class SubmitSwingProgram extends JFrame {
    JLabel jLabel;
    static SubmitSwingProgram swing;

    public SubmitSwingProgram() {
        super("frame");
        jLabel = new JLabel("label");
        this.add(jLabel);
        this.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        this.setSize(300, 100);
        this.setVisible(true);
    }

    public static void main(String[] args) throws InterruptedException {
//        初始化类变量, 即该类的实例化对象
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                swing = new SubmitSwingProgram();
            }
        });
        TimeUnit.SECONDS.sleep(3);
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                swing.jLabel.setText("hello label");
            }
        });
    }
}

```

### 创建显示框架

要想使用该框架, 需要创建一个JFrame类或其子类(通过继承JFrame实现), 之后调用run()并传入该类的对象

```java
package net.mindview.util;

import javax.swing.*;

public class SwingConsole {

    /**
     * 为窗口设置标题名, 标题名为类的简单名
     * @param jFrame
     * @param width
     * @param height
     */
    public static void run(final JFrame jFrame, final int width, final int height) {

        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                jFrame.setTitle(jFrame.getClass().getSimpleName());
                jFrame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
                jFrame.setSize(width, height);
                jFrame.setVisible(true);
            }
        });
    }
}
```

### 创建按钮

```java
package mSwing;

import net.mindview.util.SwingConsole;

import javax.swing.*;
import java.awt.*;

public class MButton extends JFrame {
    private JButton btn01 = new JButton();
    private JButton btn02 = new JButton();

    public MButton() {
//        如果没有设置,那么就只会有一个按钮出现在所产生的程序中
        setLayout(new FlowLayout());
        add(btn01);
        add(btn02);
    }

    public static void main(String[] args) {
        MButton mButton = new MButton();
        SwingConsole.run(mButton, 100, 100);
    }
}
```



### 练习

> **练习1**: 修改HelloSwing.java, 验证==如果没有对setDefaultCloseOperation()的调用, 应用程序就不会关闭==
>
> 
>
> 注意点: 程序窗口会被关闭,即变为不可见状态, 但是在控制台窗口可以看到程序没有退出

```java
package mSwing.practice;

import javax.swing.*;
import java.util.concurrent.TimeUnit;

public class HelloSwing extends JFrame {
    JLabel jLabel;
    static HelloSwing program;

    public HelloSwing() {
//        初始化继承的JFrame
        super("frame");

        jLabel = new JLabel("label");
        add(jLabel);
        setSize(300, 100);
        setVisible(true);
    }

    public static void main(String[] args) throws InterruptedException {
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                program = new HelloSwing();
            }
        });
        TimeUnit.SECONDS.sleep(3);
        SwingUtilities.invokeLater(new Runnable() {
            @Override
            public void run() {
                program.jLabel.setText("hello label");
            }
        });
    }
}
```

> **练习2**: 修改HelloSwing.java, 通过==添加随机数量的标签, 说明标签的添加是动态的==

> **练习3**: 修改SubmitSwingProgram.java, 让它使用SwingConsole

```java
package mSwing;

import net.mindview.util.SwingConsole;

import javax.swing.*;
import java.util.concurrent.TimeUnit;

public class SubmitSwingProgram2 extends JFrame {
    JLabel jLabel = new JLabel();
    static SubmitSwingProgram2 swing = new SubmitSwingProgram2();


    public static void main(String[] args) throws InterruptedException {
        SwingConsole.run(swing, 300, 300);
        TimeUnit.SECONDS.sleep(3);
        SwingUtilities.invokeLater(() -> {
            swing.add(swing.jLabel);
            swing.jLabel.setText("hello label");
        });
    }
}
```

