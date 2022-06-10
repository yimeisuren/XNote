# 引言

## 基本规则

+ 清晰性和简洁性最为重要
+ 组件的用户永远不应该被其行为迷惑
+ 组件要尽可能小, 但又不能太小
+ 代码应该重用, 而不是拷贝复制
+ 组件之间的依赖性应该尽可能降到最小
+ 错误应该尽早检测出来, 最好是在编译时发现并解决

书中的规则不应该盲目的遵从, 在有足够的理由时可以打破这些规则

## 术语约定

**client(客户端)**: 使用API的类

**user(用户)**: 使用API编写应用程序的程序员

# 创建和销毁对象

<u>主要内容</u>

+ when和how创建对象
+ when和how避免创建对象
+ how确保适时销毁
+ how管理销毁前的各种清理动作

## <u>第01条: 静态工厂方法 > 构造器</u><a name="item01"></a>

**客户端client**想要获取**某个类A**的实例, 传统的方法是**类A提供一个public构造器**, 另外一种方法是**类A提供一个public的静态工厂方法, 即返回类实例的静态方法**

### 静态工厂方法

> 在类中提供一个公开的静态方法, 方法的返回值是该类的实例

#### 优势

+ 相较于构造器而言, 静态工厂方法<u>有名称</u>
  + 可以实现参数相同的不同构造器
  + 可读性更好
+ 不必在每次调用它们的时候都创建一个新对象
  + [不可变类](#item17)可以预先构建好实例或者缓存构建好的实例, 进行重复利用, 从而避免创建不必要的重复对象[^享元模式]
  + 将构造器私有化后, 可以使得类地实例化受控, 这种类称为<u>实例受控的类(instance-controlled)</u>. 有以下原因:
    + 可以确保是[Singleton(单例模式)](#item03)或[不可实例化的](#item04)
    + [使得不可变的值类](#item17)可以确保不会出现两个相等的实例
+ 可以返回原返回类型的任何子类型的对象
+ 所返回的对象的类可以随着每次调用而发生变化, 取决于静态工厂方法的参数值
+ 静态方法返回的对象所属的类, 在编写包含该静态方法的类时可以不存在

#### 缺点

+ 没有提供public或protect的构造器, 则无法进行子类的实例化, 即该类不能被继承

+ 需要遵守静态工厂方法相关的命名规范

  1. from----类型转换方法

     只有单个参数, 返回该类型的一个相对应的实例, 例如`Date date = Date.from(inputValue);`

  2. of----聚合方法

     带有多个参数, 返回该类型的一个实例, 把它们合并起来, 例如`Set<Card> cards = EnumSet.of(Jack, Queen, King);`

  3. valueOf----比from和of更烦琐的一种替代方案, 例如`String s = String.valueOf(Integer.MAX_VALUE);`

  4. instance或getInstance----效果类似含参构造器

  5. create或newInstance----作用和getInstance类似, 但每次返回一个新的实例

  6. get<u>Type</u>----<u>Type</u>表示该方法所返回的实例对象的类型, 例如`FileStore fs = Files.getFileStore(filepath);`

  7. new<u>Type</u>----例如`BufferedReader br = Files.newBufferedReader(path);`

  8. <u>type</u>----例如`List<Complaint> litany = Collections.list(legacyLitany);`

## <u>第02条: 构建器 > 重叠构造器模式(telescoping constructor)</u>

### 重叠构造器模式

通过提供多个含有默认值的构造器, <u>调用相应构造器直接生成对象</u>

```java
public class User {
    private String id;
    private String name;
    private Integer age;
    private Boolean gender;
    private String address;


    public User(String id, String name, Integer age, Boolean gender, String address) {
        this.id = id;
        this.name = name;
        this.age = age;
        this.gender = gender;
        this.address = address;
    }

    public User(String id, String name, Integer age, Boolean gender) {
        this(id, name, age, gender, null);
    }

    public User(String id, String name, Integer age) {
        this(id, name, age, null);
    }

    public User(String id, String name) {
        this(id, name, null);
    }

    public User(String id) {
        this(id, null);
    }

    public User() {
        this(null);
    }
}
```

### JavaBean模式

通过调用<u>无参构造器</u>来创建对象, 然后通过setter方法来设置每个必要的参数.

该模式的缺点是构造过程分配到了多个函数调用中, <u>在构造过程中JavaBean可能处于不一致状态</u>, 如果setter中忽略了给某个必要参数设置值则会导致程序错误. 此外JavaBean模式无法将类设置为[不可变类](#item17).

### 构建器(建造者模式)

利用所有必要的参数来调用构造器, 得到一个builder对象, 然后调用类似setter方法来设置相关的可选参数

```java
public class UserBuilder {
    private final String id;
    private final String name;
    private final Integer age;
    private final Boolean gender;
    private final String address;

    private UserBuilder(Builder builder) {
        this.id = builder.id;
        this.name = builder.name;
        this.age = builder.age;
        this.gender = builder.gender;
        this.address = builder.address;
    }


    /**
     * 构建器类
     */
    public static class Builder {
        //        必要参数
        private String id;
        private String name;
        private Integer age;
        //        可选参数, 设置默认值
        private Boolean gender = null;
        private String address = null;

        public Builder(String id, String name, Integer age) {
            this.id = id;
            this.name = name;
            this.age = age;
        }

        public Builder setGender(Boolean gender) {
            this.gender = gender;
            return this;
        }

        public Builder setAddress(String address) {
            this.address = address;
            return this;
        }

        public UserBuilder build() {
            return new UserBuilder(this);
        }
    }
    
    //m
    public static void main(String[] args) {
        Builder builder = new Builder("1", "root", 18);
        UserBuilder user = builder.setAddress("address").setGender(true).build();
        System.out.println(user);
    }

}
```

## <u>第03条: 用私有构造器或枚举类型强化Singleton属性</u>

实现Singleton有两种常见方法, 均需要保持构造器方法为私有的, 并导出公有的静态成员, 以便允许客户端能够访问该类的唯一实例.

### 方法一: 直接暴露静态成员

```java
public class Elvis{
    public static final Elvis INSTANCE = new Elvis();
    private Elvis(){}
}
```

### 方法二: 通过静态工厂方法暴露静态成员

```java
public class Elvis{
    private static final Elvis INSTANCE = new Elvis();
    private Elvis(){}
    public static Elvis getInstance(){
        return INSTANCE;
    }
}
```

上面的单例模式的实现还是可以通过反射方式来调用私有构造器, 如果需要防止这种情况, 可以<u>修改构造器, 在创建第二个实例时抛出异常</u>

```java
public class Elvis{
    private static final Elvis INSTANCE = new Elvis();
    private Elvis(){
        if(INSTANCE != null){
            throw new Exception();
        }
    }
    public static Elvis getInstance(){
        return INSTANCE;
    }
}
```

### 方法三: 单元素的枚举类

与公有域(即公有属性, public-field)的方法类似, 更加简洁, 同时提供了序列化机制. 该方式经常称为实现Singleton的最佳方式, 但是如果该类必须拓展成为一个超类(父类, 即必须被继承), 则不应该使用该方法.

```java
public enum Elvis{
    INSTANCE;
}
```









[^享元模式]:如果程序经常请求创建相同的对象, 并且创建对象的代价很高, 使用享元模式可以极大地提升性能