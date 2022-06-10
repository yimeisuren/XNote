# 实现CRUD操作

## 使用Statement操作数据表的弊端

+ 需要拼接字符串
+ 存在sql注入的问题

## 使用PreparedStatement操作

```java
    @Test
    void test5() throws Exception {
        //        读取文件创建流, 这里需要注意文件的存放路径
        InputStream inputStream = ConnectTest.class.getClassLoader().getResourceAsStream("jdbc.properties");
        Properties properties = new Properties();
//        加载流对象, 获取键值对
        properties.load(inputStream);
        String driverClass = properties.getProperty("driverClass");
        String username = properties.getProperty("username");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
//      进行数据库连接配置
        Class.forName(driverClass);
        Connection connection = DriverManager.getConnection(url, username, password);

//        4. 预编译sql语句, 返回PreparedStatement实例
        String sql = "insert into customers(name,email,birth) values (?,?,?)";
        PreparedStatement statement = connection.prepareStatement(sql);

        statement.setString(1, "哪吒");
        statement.setString(2, "neza@gmail.com");
        SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
        Date date = simpleDateFormat.parse("1998-02-01");
//        这里需要注意new java.sql.Date()对象, 因为上面的Date导入后Date表示java.util.Date, 并且无法强制类型转换
        statement.setDate(3, new java.sql.Date(date.getTime()));
//        5. 执行操作
        statement.execute();
//        6. 关闭连接
        statement.close();
        connection.close();
    }
```

### 封装工具类

```java
import java.io.InputStream;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
import java.sql.Statement;
import java.util.Properties;

public class JDBCUtils {
    public static Connection getConnection() throws Exception {
        //        读取文件创建流, 这里需要注意文件的存放路径
        InputStream inputStream = ClassLoader.getSystemClassLoader().getResourceAsStream("jdbc.properties");
        Properties properties = new Properties();
//        加载流对象, 获取键值对
        properties.load(inputStream);
        String driverClass = properties.getProperty("driverClass");
        String username = properties.getProperty("username");
        String password = properties.getProperty("password");
        String url = properties.getProperty("url");
//      进行数据库连接配置
        Class.forName(driverClass);
        return DriverManager.getConnection(url, username, password);
    }

    public static void closeResource(Connection connection, Statement statement) throws SQLException {
        statement.close();
        connection.close();
    }
}
```



```java
    @Test
    void test6() throws Exception {
        Connection connection = JDBCUtils.getConnection();
        String sql = "update customers set name = ? where id = ?";
//        此时ps知道sql要做什么事情, 因此能够解决sql注入问题
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
//        parameterIndex表示上面的占位符的序号, 默认从1开始
        preparedStatement.setString(1, "莫扎特");
        preparedStatement.setString(2, "18");
        preparedStatement.execute();
        JDBCUtils.closeResource(connection, preparedStatement);
    }
```

### 封装通用update(增删改)操作

```java
    void update(String sql, Object... args) throws Exception {
        Connection connection = JDBCUtils.getConnection();
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        for (int i = 0; i < args.length; i++) {
            preparedStatement.setObject(i + 1, args[i]);
        }
        preparedStatement.execute();
        JDBCUtils.closeResource(connection, preparedStatement);
    }

    @Test
    void updateTest() throws Exception {
        String sql = "update customers set name = ? where id = ?";
        update(sql, "韩寒", 18);
    }
```

#### 插入Blob数据(视频,图片等)

> 重点是将获取到的二进制数据通过`toBinaryStream`转化为流, 在通过输入流, 输出流和字节数组的配合写入到文件中

```java
```





### 查询操作(重点)

#### 实现方式一

> 通过属性的顺序进行对应, 关联不够紧密, 容易被忽视而产生`bug`
>
> 此外如果将`sql`语句中的`id`和`name`的顺序互换, 或者是查找`name`和`email`而没有查找`id`则均会报错, **需要对其进行改进**

```java
    /**
     * 通过反射进行赋值, 属性的顺序和表的列的顺序需要对应, 顺序对应不够通用, 通过名称相互对应关联更强
     *
     * @param sql
     * @param args
     * @return
     * @throws Exception
     */
    ArrayList<Customer> queryForCustomers(String sql, Object... args) throws Exception {
        Connection connection = JDBCUtils.getConnection();
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        for (int i = 0; i < args.length; i++) {
            preparedStatement.setObject(i + 1, args[i]);
        }

//        根据结果集的列数来进行填充
        ResultSet resultSet = preparedStatement.executeQuery();
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();
        ArrayList<Customer> customers = new ArrayList<>();

        Class<?> clazz = Class.forName("Customer");
        Field[] fields = clazz.getDeclaredFields();
        while (resultSet.next()) {
            Customer customer = (Customer) clazz.newInstance();
            for (int i = 0; i < columnCount; i++) {
                Object object = resultSet.getObject(i + 1);
                fields[i].setAccessible(true);
                fields[i].set(customer, object);
            }
            System.out.println(customer);
            customers.add(customer);
        }
        JDBCUtils.closeResource(connection, preparedStatement, resultSet);
        return customers;
    }

    @Test
    void queryTest2() throws Exception {
        String sql = "select `id`,`name` from customers where `id` <= ?";
        queryForCustomers(sql, 10);
    }
```

#### 实现方式二

> 通过属性的名称和表的列名相对应, 表的列名在结果集中可以获得
>
> 此时没有处理JavaBean类的属性和数据库中表的列名**自定义映射**的问题, 如果二者不一致将会导致`bug`

```java
    ArrayList<Customer> queryForCustomersv(String sql, Object... args) throws Exception {
        Connection connection = JDBCUtils.getConnection();
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        for (int i = 0; i < args.length; i++) {
            preparedStatement.setObject(i + 1, args[i]);
        }

//        根据结果集的列数来进行填充
        ResultSet resultSet = preparedStatement.executeQuery();
//        结果集的元数据
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();
        ArrayList<Customer> customers = new ArrayList<>();

        Class<?> clazz = Class.forName("Customer");
        //resultSet.next()判断是否有下一个item, 如果有返回true并向后移动
        while (resultSet.next()) {
            Customer customer = (Customer) clazz.newInstance();
            for (int i = 0; i < columnCount; i++) {
                Object columnValue = resultSet.getObject(i + 1);
//                获取结果集中的列名, 即获取数据库中表的列名
                String columnName = metaData.getColumnName(i + 1);
//                获取JavaBean类中相对应的属性, 这里默认完全对应, 暂时不考虑数据库表中的命名和JavaBean类中不一致的情况
                Field field = clazz.getDeclaredField(columnName);
                field.setAccessible(true);
                field.set(customer, columnValue);
            }
            System.out.println(customer);
            customers.add(customer);
        }
        JDBCUtils.closeResource(connection, preparedStatement, resultSet);
        return customers;
    }
```

#### 实现方式三

> 解决映射问题的两个关键步骤
>
> + 执行`sql`语句时为查询结果集**取别名**, 别名与JavaBean类相对应
> + 结果集`resultSet`中使用`getColumnLabel()`来**获取别名**

```java
    ArrayList<Order> queryForOrderv2(String sql, Object... args) throws Exception {
        Connection connection = JDBCUtils.getConnection();
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        for (int i = 0; i < args.length; i++) {
            preparedStatement.setObject(i + 1, args[i]);
        }

        ResultSet resultSet = preparedStatement.executeQuery();
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();
        ArrayList<Order> orders = new ArrayList<>();

        Class<?> clazz = Class.forName("Order");
        while (resultSet.next()) {
            Order Order = (Order) clazz.newInstance();
            for (int i = 0; i < columnCount; i++) {
                Object columnValue = resultSet.getObject(i + 1);
                //获取sql语句中的别名, 没有设置别名时默认取原来的列名
                String columnName = metaData.getColumnLabel(i + 1);
                Field field = clazz.getDeclaredField(columnName);
                field.setAccessible(true);
                field.set(Order, columnValue);
            }
            System.out.println(Order);
            orders.add(Order);
        }
        JDBCUtils.closeResource(connection, preparedStatement, resultSet);
        return orders;
    }
```

```java
    @Test
    void queryTest3() throws Exception {
    //	sql语句中取别名
        String sql = "select `order_name` orderName ,`order_id` orderId from `order` where `order_id` <= ?";
        queryForOrderv2(sql, 10);
    }
```

#### 实现方式四

> 针对不同表的通用查询功能

```java
    public <T> ArrayList<T> query(Class<T> clazz, String sql, Object... args) throws Exception {
        Connection connection = JDBCUtils.getConnection();
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        for (int i = 0; i < args.length; i++) {
            preparedStatement.setObject(i + 1, args[i]);
        }

        ResultSet resultSet = preparedStatement.executeQuery();
        ResultSetMetaData metaData = resultSet.getMetaData();
        int columnCount = metaData.getColumnCount();
        ArrayList<T> ts = new ArrayList<>();

//        不够通用, 让外部调用传入则更加泛用
//        Class<?> clazz = Class.forName("Order");
        while (resultSet.next()) {
            T t = clazz.newInstance();
            for (int i = 0; i < columnCount; i++) {
                Object columnValue = resultSet.getObject(i + 1);
                String columnName = metaData.getColumnLabel(i + 1);
//
                Field field = clazz.getDeclaredField(columnName);
                field.setAccessible(true);
                field.set(t, columnValue);
            }
            System.out.println(t);
            ts.add(t);
        }
        JDBCUtils.closeResource(connection, preparedStatement, resultSet);
        return ts;
    }
```

### 批量插入数据(批处理优化)

+ **使用批处理技术**来"攒"sql, 然后一次性处理
+ **关闭自动提交**, 防止每次处理都会提交一次, 等所有数据处理完再一次性提交
