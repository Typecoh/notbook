``

# 快捷键

## 自动补全变量名

```
ctrl + alt + v
```

## 查找类名

```
shfit + shift
```

### 查找接口的方法

鼠标点击 想查询的 接口 使用 ctrl + h进行查询  

```
ctrl+H
```



# spring



## Ioc

在书写代码的时候我们因该是追求低耦合的开发

<img src="https://i.328888.xyz/2023/04/12/iBk2Dx.png" alt="iBk2Dx.png" style="zoom:50%;" />

 

![iBk8bU.png](https://i.328888.xyz/2023/04/12/iBk8bU.png)



<img src="https://i.328888.xyz/2023/04/12/iBkRX5.png" alt="iBkRX5.png" style="zoom:50%;" />

## DI实现 和 为什么需要这个

创建对象的时候使用他的实现类

```java
private Service service = new ServiceImpl();
```

这个实现类的内部

```java
public class ServiceImpl implements Service {
    
    // private BookDao bookDao = new BookDaoImpl();
    
    private BookDao bookDao = new BookDaoImpl();
    
    public void save() {
        bookDao.save();
        System.out.println("service is running!!!");    
    }
}
```

在这个是实现类的内部 使用了另外一个接口 使用 对应的实现类 创建一个 对象

```java
   private BookDao bookDao = new BookDaoImpl();
```

**这么一来 这个耦合度就比较大**

<img src="https://i.328888.xyz/2023/04/12/iBkB3F.png" alt="iBkB3F.png" style="zoom:50%;" />

**删除new 的方法之后**

**加入bean的管理**

<img src="https://i.328888.xyz/2023/04/12/iBkxiQ.png" alt="iBkxiQ.png" style="zoom:50%;" />

<img src="https://i.328888.xyz/2023/04/12/iBk7bE.png" alt="iBk7bE.png" style="zoom:50%;" />

**这个setter方法是 IoC容器去调用的** 

**这个也就是DI 实现**

## 	bean配置



### 			bean基础配置		

```
 <bean id = "BookDao" class="com.itheima.dao.impl.BookDaoImpl"></bean>
 id 表示 bean的 id class表示 bean 的 类型
```

### 			bean别名配置

```java
bean 的别名 设置
使用name 设置

在 xml 文件中设置 name 属性
<bean id = "BookDao" class="com.itheima.dao.impl.BookDaoImpl" name="dao"></bean>

在 main 函数中 就能 使用 别名
BookDao bookDao = (BookDao) ctx.getBean("dao");
```



###             bean作用范围配置

默认设置 bean的作用范围是 singleton



```
<bean id = "BookDao" class="com.itheima.dao.impl.BookDaoImpl" name="dao" scope="singleton"></bean>
```

创建出来之后的是一个单例效果

```java
package com.itheima;

import com.itheima.dao.BookDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AppforScoped {
    public static void main(String [] args){
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
        
//        Spring 默认给我们创建的事 一个 单例
//        可以自己设定 成一个 非单例

        BookDao bookdao1 = (BookDao) ctx.getBean("BookDao");
        BookDao bookdao2 = (BookDao) ctx.getBean("BookDao");
        System.out.println(bookdao1);
        System.out.println(bookdao2);
    }
}
```

地址显示 明显是相同的

```
com.itheima.dao.impl.BookDaoImpl@2d928643
com.itheima.dao.impl.BookDaoImpl@2d928643
```

配置bean的作用范围为 可变 prototype

```
 <bean id = "BookDao" class="com.itheima.dao.impl.BookDaoImpl" name="dao" scope="prototype"></bean>
```

```java
package com.itheima;

import com.itheima.dao.BookDao;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class AppforScoped {
    public static void main(String [] args){
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
//        Spring 默认给我们创建的事 一个 单例
//        可以自己设定 成一个 非单例

        BookDao bookdao1 = (BookDao) ctx.getBean("BookDao");
        BookDao bookdao2 = (BookDao) ctx.getBean("BookDao");
        System.out.println(bookdao1);
        System.out.println(bookdao2);
    }
}
```

地址显示

```java
com.itheima.dao.impl.BookDaoImpl@2d928643
com.itheima.dao.impl.BookDaoImpl@5025a98f
```



## bean的构造方法

1. 介绍idea一个快捷键 alt + insert 生成

   ![iBkE6C.png](https://i.328888.xyz/2023/04/12/iBkE6C.png)

2. 创建出一个 无参构造函数

   ```java
       public BookDaoImpl() {
           System.out.println("bookdao constructor is running...");
       }
   ```

3. 生成结果

   ```java
   bookdao constructor is running...
   book dao save ... 
   ```

4. **注意一点的是 Spring创建构方法的时候 创建的是 无参构造方法，当使用有参构造方法的时候 会报错，底层原理是通过反射实现的**

5. **使用静态方法造Bean**

   ![iBk6CX.png](https://i.328888.xyz/2023/04/12/iBk6CX.png)

   1. 创建一个OrderDao接口 声明一个 save() 方法

      ```java
      
      package com.itheima.dao;
      
      public interface OrderDao {
      
          public void save();
      
      }
      ```

      

   2. 在impl中创建一个类继承OrderDao的接口

      ```java
      package com.itheima.dao.impl;
      
      import com.itheima.dao.OrderDao;
      
      public class OrderDaoImpl implements OrderDao {
          public void save(){
              System.out.println("order dao save ...");
          }
      }
      ```

   3. 生成一个工厂 在xml中使用

      ```java
      package com.itheima.factory;
      
      import com.itheima.dao.OrderDao;
      import com.itheima.dao.impl.OrderDaoImpl;
      
      public class OrderDaoFactory {
          public static OrderDao getOrderDao(){
              return new OrderDaoImpl();
          }
      }
      ```

      

   4. 创建一个工厂 **将原始的静态方法做对象的形式给 Ioc容器管理**

      ```Java
      package com.itheima;
      
      import com.itheima.dao.OrderDao;
      import com.itheima.factory.OrderDaoFactory;
      import org.springframework.context.ApplicationContext;
      import org.springframework.context.support.ClassPathXmlApplicationContext;
      
      public class AppForInstanceOrder {
      
          public static void main(String[] args){
      
              ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
      
              OrderDao orderDao = (OrderDao) ctx.getBean("OrderDao");
      
              orderDao.save();
      
          }
      }
      ```

   5. 在xml 中进行配置 factory-method 表示造对象的方法

      ```java
       <bean id="OrderDao" class="com.itheima.factory.OrderDaoFactory" factory-method="getOrderDao"></bean>
      ```

6. 使用实例化对象造Bean

   <img src="https://i.328888.xyz/2023/04/12/iBkGKJ.png" alt="iBkGKJ.png" style="zoom:50%;" />

   ```java
   package com.itheima;
   import com.itheima.Factory.BookServiceFactory;
   import com.itheima.dao.BookDao;
   
   /**	
    * @ClassName AppBookServiceFactory
    * @Description TODO
    * @Author Typecoh
    * @Date 2022/12/27 22:44
    * @Version 1.0
    */
   
   public class AppBookServiceFactory {
       
       public static void main(String[] args) {
       
           BookServiceFactory bookServiceFactory = new BookServiceFactory();
           
           BookDao bookDao = bookServiceFactory.bookDao();
           
           bookDao.save();
       }
   }
   ```

   ```java
   package com.itheima.Factory;
   
   import com.itheima.Service.BookService;
   import com.itheima.dao.BookDao;
   import com.itheima.dao.impl.BookDaoImpl;
   
   /**
    * @ClassName BookServiceFactory
    * @Description TODO
    * @Author Typecoh
    * @Date 2022/12/27 22:43
    * @Version 1.0
    */
   
   public class BookServiceFactory {
       
       public BookDao getBookdao(){
           return new BookDaoImpl();
       }
   }
   ```

   **交给Ioc容器管理**

   ```xml
   <bean id="bookDao" class="com.itheima.Factory.BookServiceFactory"></bean>
   
   <bean id="factory" factory-method="getBookdao" factory-bean="bookDao"></bean>
   ```

   ```java
   ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
   
   BookDao factory = (BookDao) ctx.getBean("factory");
   factory.save();
   ```

7. 第四种配置 使用factoryBean 进行配置

   1. 在xml 进行配置

      ```xml
      <bean id = "userDao" class="com.itheima.factory.UserDaoFactoryBean"></bean>
      ```

   2. 创建 UserDaoFactoryBean类

      ```java
      package com.itheima.factory;
      
      import com.itheima.dao.UserDao;
      import com.itheima.dao.impl.UserDaoImpl;
      import org.springframework.beans.factory.FactoryBean;
      
      public class UserDaoFactoryBean implements FactoryBean<UserDao> {
      
      //    代替原始实例工厂中创建对象的方法
      //    返回bean实例
          public UserDao getObject() throws Exception {
              return new UserDaoImpl();
          }
      //    返回bean指定类型
          public Class<?> getObjectType() {
      //        返回UserDao的字节码 UserDao.class
              return UserDao.class;
          }
      
          public boolean isSingleton() {
      //        返回 true 表示 单例 return false 表示 非单例
              return false;
          }
      }
      ```

   3. 创建主函数

      ```java
      package com.itheima;
      
      import com.itheima.dao.UserDao;
      import org.springframework.context.ApplicationContext;
      import org.springframework.context.support.ClassPathXmlApplicationContext;
      
      public class AppForInstanceUser {
      
          public static  void main(String[] args){
      
              ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
      
              UserDao userDao1 = (UserDao) ctx.getBean("userDao");
              UserDao userDao2 = (UserDao) ctx.getBean("userDao");
      
              System.out.println(userDao1);
              System.out.println(userDao2);
      //        userDao.save();
      
          }
      }
      
      ```

8. bean 的 声明周期

   1. 声明实现 创建和销毁的方法

      ```java
      //   对bean进行初始化操作
          public void init(){
              System.out.println("init...");
          }
      //    对bean进行销毁操作
          public void destroy(){
              System.out.println("destroy...");
          }
      ```

   2. 在xml中对 初始化方法和销毁方法进行声明定义

      ```xml
       <bean id = "bookdao" class="com.itheima.dao.impl.BookDaoImpl" init-method="init" destroy-method="destroy"></bean>
      ```

   3. 在主函数执行时可以观测到 初始化方法

      ```java
      	package com.itheima.service;
      
      import com.itheima.dao.BookDao;
      import org.springframework.context.ApplicationContext;
      import org.springframework.context.support.ClassPathXmlApplicationContext;
      
      public class BookServiceImpl {
      
          public static void main(String[] args){
      
              ClassPathXmlApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
      
              BookDao bookDao = (BookDao) ctx.getBean("bookdao");
      
              bookDao.save();
          }
      }
      ```

   4. 在虚拟机退出时关闭容器  调用close 方法 
      值得注意的一点时 使用close方法的时候 使用的接口是ClassPathXmlApplicationContext  并不是 ApplicationContext 因为
      ApplicationContext 并没用close这个方法

      ```java
        ctx.close();
      ```

   5. 设置关闭钩子

      ```java
      ctx.registerShutdownHook()
      ```

9. **使用接口**设置声明周期

   ```java
   public class BookDaoImpl implements BookDao , InitializingBean, DisposableBean {
   
   //    将这个构造方法给写出来
       public BookDaoImpl() {
           System.out.println("bookdao constructor is running...");
       }
   
       //    实现接口的关键字
       public void save(){
           System.out.println("book dao is save ... ");
       }
   
       public void destroy() throws Exception {
           System.out.println("service destroy");
       }
   //  在属性设置之后
       public void afterPropertiesSet() throws Exception {
           System.out.println("service init");
       }
   }
   ```

   

## 依赖注入方式

#### 	setter

* 引用类型：对象类型
* 传统类型：int String

1. ​	声明一个接口

   ```java
   package com.itheima.dao;
   
   //定义一个接口 BookDao
   
   public interface BookDao {
   
   //    在接口中定义一个函数 这个函数没有实现
       public void save();
   
   }
   ```

2. 声明一个类继承接口

   ```java
   package com.itheima.dao.impl;
   
   import com.itheima.dao.BookDao;
   
   public class BookDaoImpl implements BookDao  {
   
       public void save(){
           System.out.println("BookDao is running..." + databaseName + "," + connectionNum);
       }
   }
   ```

3. 使用setter方法

   ```java
   package com.itheima.dao.impl;
   
   import com.itheima.dao.BookDao;
   
   public class BookDaoImpl implements BookDao  {
   
       private int connectionNum;
       private String databaseName;
   
       public void setConnectionNum(int connectionNum) {
           this.connectionNum = connectionNum;
       }
   
       public void setDatabaseName(String databaseName) {
           this.databaseName = databaseName;
       }
   
       public void save(){
           System.out.println("BookDao is running..." + databaseName + "," + connectionNum);
       }
   }
   
   ```

4. 在其它类中调用 引入setter

   ```java
   package com.itheima.service.Impl;
   
   import com.itheima.dao.BookDao;
   import com.itheima.dao.UserDao;
   import com.itheima.service.BookService;
   
   public class BookServiceImpl implements BookService {
   
   //    定义一个接口 bookDao
   
       private BookDao bookDao;
       private UserDao userDao;
   
       public void setUserDao(UserDao userDao) {
           this.userDao = userDao;
       }
   
       public void setBookDao(BookDao bookDao){
   
           this.bookDao = bookDao;
   
       }
   
       public void save(){
   
           System.out.println("book service save...");
   
           bookDao.save();
   
           userDao.save();
       }
   
   
   }
   
   ```

5. 配置xml文件

   ```xml
   <bean id="BookDao" class="com.itheima.dao.impl.BookDaoImpl"></bean>
   ```

6. **传统类型 使用的是 name-value**

   ```xml
    <property name="connectionNum" value="10"></property>
     <property name="databaseName" value="mysql"></property>
   ```

7. **使用对象引用的是 name-ref**

8. 在相应的实现接口配置xml文件 name 来源于类 private BookDao bookDao 当中的类型 ref对应的是id

   ```xml
    <bean id="BookService" class="com.itheima.service.Impl.BookServiceImpl">
           <property name="BookDao" ref="BookDao"></property>
       </bean>
   ```

9. 在主函数中运行

   ```java
   package com.itheima;
   
   import com.itheima.service.BookService;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class App {
   
       public static void main(String[] args){
   
           System.out.println("start");
   
           ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
   
           BookService bookService = (BookService) ctx.getBean("BookService");
   
           bookService.save();
       }
   }
   ```

   

#### 	构造器注入construcor

1. 定义一个接口类

   ```java
   package com.itheima.dao;
   
   //定义一个接口 BookDao
   
   public interface BookDao {
   
   //    在接口中定义一个函数 这个函数没有实现
       public void save();
   }
   ```

2. 实现接口类

   ```java
   package com.itheima.dao.impl;
   
   import com.itheima.dao.BookDao;
   
   public class BookDaoImpl implements BookDao  {
   
       private int connectionNum;
       private String databaseName;
   //    使用构造器 实现传参
   //    最典型写法
       public BookDaoImpl(int connectionNum, String databaseName) {
           this.connectionNum = connectionNum;
           this.databaseName = databaseName;
       }
   
       public void save(){
           System.out.println("BookDao is running..." + databaseName +","+ connectionNum);
       }
   }
   ```

3. 实现传递

   ```java
   package com.itheima.service.Impl;
   
   import com.itheima.dao.BookDao;
   import com.itheima.dao.UserDao;
   import com.itheima.service.BookService;
   
   public class BookServiceImpl implements BookService {
   
   //    定义一个接口 bookDao
   
       private BookDao bookDao;
       private UserDao userDao;
   
   //    使用构造器构造
   
   
       public BookServiceImpl(BookDao bookDao, UserDao userDao) {
           this.bookDao = bookDao;
           this.userDao = userDao;
       }
   
       public void save(){
   
           System.out.println("book service save...");
           bookDao.save();
           userDao.save();
   
       }
   }
   ```

4. 主函数实现调用功能

   ```java
   package com.itheima;
   
   import com.itheima.dao.BookDao;
   import com.itheima.service.BookService;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class App {
   
       public static void main(String[] args){
   
           System.out.println("start");
   
           ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
   
           BookService bookService = (BookService) ctx.getBean("BookService");
   
           bookService.save();
       }
   }
   ```

5. xml配置

   1. 定义BookDao的bean 在bean里面定义构造器传参

      ```xml
       <bean id="BookDao" class="com.itheima.dao.impl.BookDaoImpl">
              <constructor-arg name = "databaseName" value = "mysql"></constructor-arg>
              <constructor-arg name = "connectionNum" value = "10"></constructor-arg>
          </bean>
      ```

   2. 定义UserDao的bean

      ```xml
      <bean id="UserDao" class="com.itheima.dao.impl.UserDaoImpl">
          </bean>
      ```

   3. 定义传递中的xml name 是形参名 ref 表示的 指定bean的id名

      ```xml
          <bean id="BookService" class="com.itheima.service.Impl.BookServiceImpl">
      <!--        name 是 形参的 变量名-->
             <constructor-arg name = "bookDao" ref = "BookDao"></constructor-arg>
              <constructor-arg name = "userDao" ref = "UserDao"></constructor-arg>
          </bean>
      ```

**使用setter注入和构造器注入耦合度还是很高的，当变量名变化的时候xml文件中就需要跟着变化，于是提出了 index type解决方法**

<img src="https://i.328888.xyz/2023/04/12/iBkhUc.png" alt="iBkhUc.png" style="zoom:50%;" />

#### 自动装配

自动装配的方式

* 按类型
* 按名称
* 按构造方法
* 不启用自动装配

1. 修改xml配置 原来的xml配置

   ```xml
   <bean id="BookDao" class="com.itheima.dao.impl.BookDaoImpl">
       <constructor-arg index = "1" value = "mysql"></constructor-arg>
       <constructor-arg index="0" value = "10"></constructor-arg>
   </bean>
   
   <bean id="UserDao" class="com.itheima.dao.impl.UserDaoImpl">
   </bean>
   
   <bean id="BookService" class="com.itheima.service.Impl.BookServiceImpl">
       <!--        name 是 形参的 变量名-->
       <constructor-arg name = "bookDao" ref = "BookDao"></constructor-arg>
       <constructor-arg name = "userDao" ref = "UserDao"></constructor-arg>
   </bean>
   ```

2. 修改成自动装配 name是和 private BookDao bookDao; BookDao 的首字母小写

3. autowire 主要使用byName 或者 byType

   ```xml
   <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
   </bean>
   
   <bean id="BookService" class="com.itheima.service.Impl.BookServiceImpl" autowire="byName">
   </bean>
   ```

4. 使用自动注入对象是引用类型，简单类型不推荐使用

## 集合注入

1. 创建一个BookDao接口

   ```java
   package com.itheima.dao;
   
   //定义一个接口 BookDao
   
   public interface BookDao {
   
   //    在接口中定义一个函数 这个函数没有实现
       public void save();
   
   }
   
   ```

   

2. 实现接口类

   ```java
   package com.itheima.dao.impl;
   
   import com.itheima.dao.BookDao;
   
   import java.util.*;
   
   public class BookDaoImpl implements BookDao  {
   
       private int[] array;
   
       private List<String> list;
   
       private Set<String> set;
   
       private Map<String,String> map;
   
       private Properties properties;
   
       public void setArray(int[] array) {
           this.array = array;
       }
   
       public void setList(List<String> list) {
           this.list = list;
       }
   
       public void setSet(Set<String> set) {
           this.set = set;
       }
   
       public void setMap(Map<String, String> map) {
           this.map = map;
       }
   
       public void setProperties(Properties properties) {
           this.properties = properties;
       }
   
       public void save(){
           System.out.println("BookDao is running..." );
   
           System.out.println("遍历数组: " + Arrays.toString(array));
   
           System.out.println("遍历List: " + list);
   
           System.out.println("遍历Set: " + set);
   
           System.out.println("遍历Map: " + map);
   
           System.out.println("遍历Properties: " + properties);
   
       }
   }
   
   ```

3. xml中配置 数组 List set map等

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
   <!--配置Dao-->
       <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
   <!--        表示注入一个数组-->
   <!--       以下的name 都是 和变量名匹配-->
           <property name="array">
               <array>
                   <value>100</value>
                   <value>200</value>
                   <value>300</value>
               </array>
           </property>
   
           <property name="list">
               <list>
                   <value>itheima</value>
                   <value>itcast</value>
                   <value>boxuegu</value>
               </list>
           </property>
   
           <property name="set">
               <set>
                   <value>itheima</value>
                   <value>itcast</value>
                   <value>boxuegu</value>
               </set>
           </property>
   
           <property name="map">
               <map>
                   <entry key="country" value="china"></entry>
                   <entry key="province" value="henan"></entry>
                   <entry key="city" value="kaifeng"></entry>
               </map>
           </property>
   
           <property name="properties">
               <props>
                   <prop key="country">china</prop>
                   <prop key="province">henan</prop>
                   <prop key="city">kaifeng</prop>
               </props>
           </property>
       </bean>
   </beans>
   ```

4. 主函数调用

   ```java
   package com.itheima;
   
   import com.itheima.dao.BookDao;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   public class App {
   
       public static void main(String[] args){
   
           System.out.println("start");
   
           ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
   
           BookDao bookDao = (BookDao) ctx.getBean("bookDao");
   
           bookDao.save();
   
       }
   }
   
   ```

   

## 数据源管理对象

​		idea查找当前方法的实现 快捷键 alt + 7

1. 创建一个接口类

   ```java
   package com.itheima.dao;
   
   //定义一个接口 BookDao
   
   public interface BookDao {
   
   //    在接口中定义一个函数 这个函数没有实现
       public void save();
   
   }
   ```

   

2. 继承接口并且实现类

   ```java
   package com.itheima.dao.impl;
   
   import com.itheima.dao.BookDao;
   
   import java.util.*;
   
   public class BookDaoImpl implements BookDao  {
   
       public void save(){
   
           System.out.println("BookDao is running..." );
   
       }
   }
   ```

3. 在xml文件中配置相应的坐标

   ```xml
   <!--        导入 druid 坐标-->
           <dependency>
               <groupId>com.alibaba</groupId>
               <artifactId>druid</artifactId>
               <version>1.1.16</version>
           </dependency>
           <!--导入c3p0 坐标-->
           <dependency>
               <groupId>c3p0</groupId>
               <artifactId>c3p0</artifactId>
               <version>0.9.1.2</version>
           </dependency>
   
   <!--    导入 mysql 坐标-->
                   <dependency>
                       <groupId>mysql</groupId>
                       <artifactId>mysql-connector-java</artifactId>
                       <version>5.1.16</version>
                   </dependency>	
   ```

4. 在bean中进行管理

   ```xml
   <!--   管理DruidDataSource 对象-->
       <bean class="com.alibaba.druid.pool.DruidDataSource">
   
           <property name="driverClassName" value="com.alibaba.mysql.jdbc.Driver"></property>
           <property name="url" value="jdbc:mysql://localhost:3306/spring_db"></property>
           <property name="username" value="root"></property>
           <property name="password" value="root"></property>
   
   
       </bean>
       
       <!--    管理Druid的连接池-->
       <bean id="dataSource"  class="com.mchange.v2.c3p0.ComboPooledDataSource">
           <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
           <property name="jdbcUrl" value="jdbc:mysql://localhost:3306/spring_db"></property>
           <property name="password" value="root"></property>
           <property name="user" value = "root"></property>
   		</bean>
   ```

5. 在main函数中进行测试

   ```java
   package com.itheima;
   
   import com.itheima.dao.BookDao;
   import org.springframework.context.ApplicationContext;
   import org.springframework.context.support.ClassPathXmlApplicationContext;
   
   import javax.sql.DataSource;
   
   public class App {
   
       public static void main(String[] args){
   
           System.out.println("start");
   
           ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
   
           DataSource dataSource = (DataSource) ctx.getBean("dataSource");
   
           System.out.println(dataSource);
       }
   }
   
   ```



## 加载properties文件

1. 在properties中创建一些数据变量

   ```java
   driverClass = "com.mysql.jdbc.Driver"
   jdbcUrl = "jdbc:mysql://localhost:3306/spring_db"
   password = "root"
   user = "root"
   ```

2. **在xml文件中那个配置 开辟一个新的空间**

   ```xml
    xmlns:context="http://www.springframework.org/schema/context"
   ```

3. 加入相应的https

   ```java
   http://www.springframework.org/schema/context
   http://www.springframework.org/schema/context/spring-context.xsd
   ```

4. 指定相应的内容 **classpath*表示的所有路径 ***.properties表示所有的properties的文件

5. **classpath:*.properties表示当前目录下**

   ```xml
   <context:property-placeholder location="classpath*:*.properties"></context:property-placeholder>
   ```

6. 向bookDao中传递参数 使用 ${}

   ```xml
   <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl">
       <property name="name" value="${driverClass}"></property>
   </bean>
   ```

## 容器

### 加载类型

1. 文件类型加载

   ```java
   ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
   ```

2. 加载盘符类型

   ```java
    ApplicationContext ctx = new FileSystemXmlApplicationContext("E:\\大学两年的比赛和项目\\Spring\\spring09\\src\\main\\resources\\applicationContext.xml");
   ```

### 强行转换类型

1. 实现强转

   ```java
   BookDao bookDao = (BookDao) ctx.getBean("bookDao");
   ```

2. 指定类型

   ```java
   BookDao bookDao = ctx.getBean("bookDao",BookDao.class);
   ```

3. **指定类中 这种写法要保证文件中这个类只有一个 按照类型查找（自动装配）**

   ```java
   BookDa o bookDao = ctx.getBean(BookDao.class);
   ```

   

### 查看类接口

1. 使用快捷键 ctrl+H

   ![iBk5RA.png](https://i.328888.xyz/2023/04/12/iBk5RA.png)

2. 创建一个BeanFactory对象

   ```java
   package com.itheima;
   
   import com.itheima.dao.BookDao;
   import org.springframework.beans.factory.BeanFactory;
   import org.springframework.beans.factory.xml.XmlBeanFactory;
   import org.springframework.core.io.ClassPathResource;
   import org.springframework.core.io.Resource;
   
   
   public class AppFactory {
   
       public static void main(String[] args) {
   
           Resource resources = new ClassPathResource("applicationContext.xml");
   
           BeanFactory bf = new XmlBeanFactory(resources);
   
           BookDao bookDao = (BookDao) bf.getBean("bookDao");
   
           bookDao.save();
   
       }
   }
   ```

3. BeanFactory 和 ApplicationContext 区别

   **在有构造方法的时候 BeanFactory 不会触发构造方法 但是 ApplicationContext 会触发构造方法，前者是延时加载，后者是立即加载**

4. 解决两者这件的区别 在 bean的坐标中加上一个延迟加载就行了

   ```xml
       <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl" lazy-init="true">
       </bean>
   ```

## bean的总结

**factory-method采用工厂创建对象**

![iBkSSo.png](https://i.328888.xyz/2023/04/12/iBkSSo.png)

![iBksVN.png](https://i.328888.xyz/2023/04/12/iBksVN.png)



## 注解开发定义bean

1. 注解开发提供了一个Component 注解取代 Component等价于下面代码

   ```xml
   <bean id="bookDao" class="com.itheima.dao.impl.BookDaoImpl"></bean>
   ```

   具体使用

   ```java
    package com.itheima.dao.impl;
   
       import com.itheima.dao.BookDao;
       import org.springframework.stereotype.Component;
   //  进行注解 （） 中表示名称 参数可以省去
       @Component("bookDao")
       public class BookDaoImpl implements BookDao  {
   
           public void save(){
   
               System.out.println("BookDao is running...");
           }
       }
   ```
   
2. **让 Spring 感知到这个 component 在xml中进行配置 让Spring容器感知到**

   加上component-scan是可以扫描到Bean的，使用的是context空间	

   ```xml
     <context:component-scan base-package="com.itheima"></context:component-scan>	
   ```

3. 在使用 Component 进行注解的时候

   - 进行命名

     ```java
      @Component("bookDao")
      // 调用的时候 使用名称调用
      BookDao bookDao = (BookDao) ctx.getBean("bookDao");
     
      System.out.println(bookDao);
     ```

   - 不进行命名

     ```java
    @Component
     
     // 不使用名称调用 使用默认参数的时候 就只能调用类的形式进行调用
     
     BookService bookService = ctx.getBean(BookService.class);
     
     System.out.println(bookService);
     ```
   
4. 使用衍生注解

   ![iBk96d.png](https://i.328888.xyz/2023/04/12/iBk96d.png)



## 纯注解开发bean

1. 使用**SpringConfig类**直接使用类 将上面代码替换成下面代码

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:context="http://www.springframework.org/schema/context"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="
                              http://www.springframework.org/schema/beans
                              http://www.springframework.org/schema/beans/spring-beans.xsd
   
                              http://www.springframework.org/schema/context
                              http://www.springframework.org/schema/context/spring-context.xsd
   
   ">
   </beans>
   ```
   

等价于

**@Configuration**

2. 使用**@ComponentScan** 替代 Context文件

   ```java
   @ComponentScan("com.itheima") 代替 
       <context:component-scan base-package="com.itheima"></context:component-scan>
   @ComponentScan({"com.itheima.dao","com.itheima.service"})
   ```

3. 使用类的进行 替换

   ```java
    ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationContext.xml");
   ```

   ```java
    ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
   ```

   

## bean的生命周期管理

### 控制类的单例和多例

```java
    package com.itheima.dao.impl;

    import com.itheima.dao.BookDao;
    import org.springframework.context.annotation.Scope;
    import org.springframework.stereotype.Component;

    @Component("bookDao")
//  控制单例使用
//    @Scope("singleton") // 单例
//    @Scope("prototype") // 多例
    public class BookDaoImpl implements BookDao  {

        public void save(){

            System.out.println("BookDao is running...");

        }
    }
```

![image-20220805114227887](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220805114227887.png)



### 生命周期函数 init destory

```java
//        构造方法前
        @PostConstruct
        public void init(){
            System.out.println("init...");
        }
//        销毁方法后
        @PreDestroy
        public void destory(){
            System.out.println("destory...");
        }
    }
```

主函数调用

```java
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);

        BookDao bookDao = (BookDao) ctx.getBean("bookDao");

        System.out.println(bookDao);

        BookDao bookDao1 = (BookDao) ctx.getBean("bookDao");

        System.out.println(bookDao1);

        BookService bookService = ctx.getBean(BookService.class);

        System.out.println(bookService);
//        关闭钩子
        ctx.close();
```



## 注解开发依赖注入

### Autowired和Qualifier 配置注解

引入Autowired之后 setter 方法可以不写

![image-20220805153526516](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220805153526516.png)

```java
//    使用类型装配
    @Autowired
//    指定配置 BookDao这个属性
    @Qualifier("bookDao")
    private BookDao bookDao;
//  使用了Autowired 之后 setter 方法可以不用
    public void setBookDao(BookDao bookDao) {
        this.bookDao = bookDao;
    }
```

 

### value 传递参数

```java
@Value("${name}")
private String name;

public void save(){

    System.out.println("BookDao is running...");
    System.out.println("Hello " + name);

	}
}
```

### Springconfig配置properties文件

```java
@PropertySource({"jdbc.properties"})
```

![iBkLCq.png](https://i.328888.xyz/2023/04/12/iBkLCq.png)

## 第三方bean管理 依赖注入

### 管理一个第三方的bean 

在 SpringConfig中进行配置第三方的 Bean

```java
//    1定义一个要管理的对象
//    2添加@Bean,表示当前返回值是一个bean
    @Bean("dataSource")
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();

        ds.setDriverClassName("com.mysql.jdbc.Driver");

        ds.setUrl("jdbc:mysql://localhost:3306/spring_db");

        ds.setPassword("root");

        ds.setUsername("root");
        return ds;
    }
```

但是缺点是 数据都在这个文件中进行配置 会造成数据混乱现象

### 基于对应文件开发管理第三方文件

#### 方式一 建立JdbcConfig文件

```java
package com.itheima.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import javax.sql.DataSource;

@Configuration
public class JdbcConfig {

    
//    1定义一个要管理的对象
//    2添加@Bean,表示当前返回值是一个bean
    @Bean("dataSource")
    public DataSource dataSource(){
        DruidDataSource ds = new DruidDataSource();

        ds.setDriverClassName("com.mysql.jdbc.Driver");

        ds.setUrl("jdbc:mysql://localhost:3306/spring_db");

        ds.setPassword("root");

        ds.setUsername("root");
        return ds;
    }
}
```

```java
package com.itheima.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;

import javax.sql.DataSource;

@Configuration
@ComponentScan("com.itheima.config")
public class SpringConfig {

}
```

缺点是在SpringConfig中无法观察配置文件的内容

#### 导入式import

```java
//@ComponentScan("com.itheima.config")
@Import(JdbcConfig.class)
```

#### 简单类型进一步封装 

```java
    @Value("${com.mysql.jdbc.Driver}")
    private String dirver;
    @Value("${jdbc:mysql://localhost:3306/spring_db}")
    private String url;
    @Value("${root}")
    private String name;
    @Value("${root}")
    private String password;

```

#### 引用类型

直接给一个形参 通过Bean 进行扫描得到的就行

```java
public DataSource dataSource(BookDao bookDao){

    System.out.println(bookDao);
```

![iBkPLw.png](https://i.328888.xyz/2023/04/12/iBkPLw.png)

## Spring整合Mybatis

### 加入依赖

```xml
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>5.2.10.RELEASE</version>
        </dependency>
                <!--mybatis和spring的整合坐标-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis-spring</artifactId>
            <version>1.3.0</version>
        </dependency>
```

### 1 创建JdbcConfig

#### 1 JdbcConfig中主要是给 数据库中的变量赋值

```java
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;
    @Value("${jdbc.password}")
    private String password;
```

#### 2 使用注解生成DataSource

```java
    @Bean
    public DataSource dataSource(){
    
        DruidDataSource ds = new DruidDataSource();
        ds.setDriverClassName(driver);
        ds.setUrl(url);
        ds.setUsername(username);
        ds.setPassword(password);
        return ds;
    }
```

#### 3 将这个文件导入到SpringConfig中

```java
// 为了在 SrpingConfig中使用JdbcConfig
@Import({JdbcConfig.class})   
// 使用Jdbc.properties中的数据
@PropertySource("classpath:jdbc.properties")
```

### 2 创建MybatisConfig文件

#### 1 创建SqlSessionFactoryBean对象

```java
 	@Bean
    public SqlSessionFactoryBean sqlSessionFactoryBean(DataSource dataSource){
        
        SqlSessionFactoryBean ssfb = new SqlSessionFactoryBean();
        
        ssfb.setTypeAliasesPackage("com.itheima.domain");
        
        ssfb.setDataSource(dataSource);
        
        return ssfb;
        
    }
```

##### 1 代替变量名 ssfb.setTypeAliasesPackage("com.itheima.domain");

```java
<typeAliases>
        <package name="com.itheima.domain"/>
</typeAliases>
```

##### 2 代替dataSource对象 ssfb.setDataSource(dataSource);

```java
<dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"></property>
                <property name="url" value="${jdbc.url}"></property>
                <property name="username" value="${jdbc.username}"></property>
                <property name="password" value="${jdbc.password}"></property>
</dataSource>
```

#### 2 MapperScannerConfigurer对象替代mappers

MapperScannerConfigurer扫描的包会被定义成bean的形式

![image-20230102004221089](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20230102004221089.png)

```xml
<mappers>
    <package name="com.itheima.dao"></package>
</mappers>
```

```java
    @Bean 
    public MapperScannerConfigurer mapperScannerConfigurer(){
        
        MapperScannerConfigurer mapperScannerConfigurer = new MapperScannerConfigurer();
        
        mapperScannerConfigurer.setBasePackage("com.itheima.dao");
        
        return mapperScannerConfigurer;
        
    }
```

#### 3 将MybatisConfig导入到SpringConfig中

```
@Import({MybatisConfig.class})    
```

### 3 SpringConfig文件

```java
//使用 @Configuration 代替<beans></beans>
@Configuration
// 使用@ComponentScan 进行扫描文件
@ComponentScan("com.itheima")
// 使用Jdbc.properties中的数据
@PropertySource("classpath:jdbc.properties")
// 为了在 SrpingConfig中使用JdbcConfig
@Import({JdbcConfig.class, MybatisConfig.class})    
public class SpringConfig {
}
```

### 4 创建dao文件创建AccountDao的接口

```java
public interface AccountDao {
    @Select("select * from tb_user where id = #{id}")
    Account findById(int id);  
}
```

### 5 创建domain文件创建Account类

```java
    package com.itheima.domain;
    /**
    *@ClassName Account
    *@Description  TODO
    *@Author lenovo
    *@Date 2022/8/16 11:12
    *@Version 1.0
    */
    
    public class Account {
        
        private Integer id;
        private String username;
        private String password;
        private String gender;
        private String addr;
        
        public Integer getId() {
            return id;
        }
        
        public void setId(Integer id) {
            this.id = id;
        }
        
        public String getUsename() {
            return username;
        }
        
        public void setUsename(String usename) {
            this.username = usename;
        }
        
        public String getPassword() {
            return password;
        }
        
        public void setPassword(String password) {
            this.password = password;
        }
        
        public String getGender() {
            return gender;
        }
        
        public void setGender(String gender) {
            this.gender = gender;
        }
        
        public String getAddr() {
            return addr;
        }
        
        public void setAddr(String addr) {
            this.addr = addr;
        }
        
        @Override
        public String toString() {
            return "Account{" +
                    "id=" + id +
                    ", usename='" + username + '\'' +
                    ", password='" + password + '\'' +
                    ", gender='" + gender + '\'' +
                    ", addr='" + addr + '\'' +
                    '}';
        }
    }

```

### 6 创建服务类

```java
package com.itheima.Service;

import com.itheima.domain.Account;

/**
 * @ClassName AccountService
 * @Description TODO
 * @Author lenovo
 * @Date 2023/1/1 23:52
 * @Version 1.0
 */

public interface AccountService {
    
    Account findById(Integer id);
}
```

```java
package com.itheima.Service.impl;

import com.itheima.Service.AccountService;
import com.itheima.dao.AccountDao;
import com.itheima.domain.Account;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

/**
 * @ClassName AccountServiceImpl
 * @Description TODO
 * @Author Typecoh
 * @Date 2023/1/1 23:51
 * @Version 1.0
 */

// @Service
public class AccountServiceImpl implements AccountService {
    
    // @Autowired
    // private AccountDao accountDao;
    
    public Account findById(Integer id) {
        System.out.println("test1");
        // return accountDao.findById(id);
        return null;
    }
}
```

### 7 编写测试类

```java
 @Test
    
    public void Test1() throws Exception {
    
        ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
    
        // AccountService accountService = ctx.getBean(AccountService.class);
    
        AccountDao accountDao = ctx.getBean(AccountDao.class);	
    
        Account ac = accountDao.findById(2);
    
        System.out.println(ac);
    }
```

## Junit整合

设置专用的类运行器

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
```

```java
@Autowired
private AccountService accountService
```

等价于

```java
    @Autowired
    @Qualifier("accountService")
    private AccountService accountService;

    public void setAccountService(AccountService accountService) {
        this.accountService = accountService;
    }
```



测试输出

```java
@Test
public void testFindById(){

    System.out.println(accountService.findById(2));
}
```



## AOP

AOP 面向切面编程 OOP面向对象编程

作用：在原来基础上为其增加功能



这个方法可以输出当前时间

```java
    public void save(){
        // 将时间输出单独抽出去
        System.out.println(System.currentTimeMillis());
        System.out.println("book dao save...");
        
    }
```

这个方法不能输出当前时间

```java
public void update(){

    System.out.println("book dao update...");
}
```

使用AOP方法实现并且不改边 update方法但是可是实现输出时间

```java
public void method(){
	 // 将时间输出单独抽出去
     System.out.println(System.currentTimeMillis());
}
```

将上面的方法单独抽出来

连接点：JoinPoint：方法的执行

切入点：PointCut：匹配连接点的式子

切面：Aspect：描述通知和切入点的对应关系

![iBkQok.png](https://i.328888.xyz/2023/04/12/iBkQok.png)

#### AOP入门案例



##### 1 导入坐标（pom.xml）

```xml
        <dependency>
            <groupId>org.aspectj</groupId>
            <artifactId>aspectjweaver</artifactId>
            <version>1.9.4</version>
        </dependency>
```



##### 2 制作连接点方法 (Dao接口和实现类)

```java
@Repository
public class BookDaoImpl implements BookDao {
    
    public void save(){
        // 将时间输出单独抽出去
        System.out.println(System.currentTimeMillis());
        System.out.println("book dao save...");
    }
    
    public void update(){
    
        System.out.println("book dao update...");
    }
}
```

```java
public interface BookDao {
    
    public void save();
    
    public void update();
}

```



##### 3 制作共性功能 （通知类与通知）

创建文件aop

在文件中创建类MyAdvice类实现共性方法

定义好通知

```java
public class MyAdvice {
    public void method(){
        System.out.println(System.currentTimeMillis());
    }
}
```

##### 4 定义切入点

定义一个空壳函数 public void 函数名(){}

在函数上方写入

@PointCut("execution(void (类型) com.itheima.dao.BookDao.update())")

```java
 @Pointcut("execution(void com.itheima.dao.BookDao.update())")
 private void pt(){};
```



##### 5 绑定切入点与通知关系 （切面）

```java
  @Before("pt()")
    public void method(){
        System.out.println(System.currentTimeMillis());
    }
```

##### 6 @Aspect说明AOP扫描切入点和通知 @Component生成Bean

```java
@Component
@Aspect
public class MyAdvice {
    
    @Pointcut("execution(void com.itheima.dao.BookDao.update())")
    private void pt(){};
    
    @Before("pt()")
    public void method(){
        System.out.println(System.currentTimeMillis());
    }
}
```

##### 7 在SpringConfig文件中加入@EnableAspectJAutoProxy 

@EnableAspectJAutoProxy 说明使用注解的AOP

```java
//使用 @Configuration 代替<beans></beans>
@Configuration
// 使用@ComponentScan 进行扫描文件
@ComponentScan("com.itheima")
@EnableAspectJAutoProxy
public class SpringConfig {
}
```

#### AOP工作流程

**Spring容器中的AOP是通过代理的形式做完的**

![iBkujp.png](https://i.328888.xyz/2023/04/12/iBkujp.png)

#### AOP切入点表达式

##### 切入点：要增强的方法

##### 切入点表达式：要进行增强的方法的描述方式

描述方法形式有多种：1 描述接口中的方法 2 描述实现类的方法

##### 1 语法格式

动作关键字（访问修饰符 返回值 包名.类 / 接口名.方法名（参数））

动作关键字：通常是execution

访问修饰符 public private

##### 2 通配符

#### AOP通知类型

##### 1 前置通知

```java
  // @Before("pt()")
   public void before(){
       System.out.println("before advice...");
   }
```



##### 2 后置通知

```java
 // @After("pt()")
   public void after(){
       System.out.println("after advice...");
   }
```



##### 3 环绕通知

```java
@Around("pt1()")
    public Object around_select(ProceedingJoinPoint pjp) throws Throwable {
        
        System.out.println("around before advice...");
        //表示对原始操作的调用
        Integer ret = (Integer) pjp.proceed();
    
        System.out.println("around after advice...");
        
        return ret + 566;
    }
   	
```



##### 4 afterReturning

```java
// 有类型返回就会触发
   public void afterReturning() {
       System.out.println("afterReturning advice...");
   }
```



##### 5 afterThrowing

```java
   // 这个产生异常就会触发
   public void afterThrowing() {
       System.out.println("afterThrowing advice...");
   }
   
```

![iB0ZFU.png](https://i.328888.xyz/2023/04/12/iB0ZFU.png)



#### AOP完成执行案例

```java
package com.itheima.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.Signature;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Pointcut;
import org.springframework.stereotype.Component;

/**
 * @ClassName ProjectAdvice
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/21 19:52
 * @Version 1.0
 */
@Component
@Aspect
public class ProjectAdvice {
    
    @Pointcut("execution(* com.itheima.dao.AccountDao.findById(..))")
    private void servicePt(){}
    
    @Around("ProjectAdvice.servicePt()")
    public void runSpeed(ProceedingJoinPoint pjp) throws Throwable {
        // 获取对象的方法
        Signature signature = pjp.getSignature();
        // 获得执行对象的包名
        String declaringTypeName = signature.getDeclaringTypeName();
        // 获得执行对象的方法名
        String name = signature.getName();
    
        System.out.println(declaringTypeName);
    
        System.out.println(name);
    
        long stat = System.currentTimeMillis();
        
        for(int i = 0; i < 10000; i++){
           
            pjp.proceed();
            
        }
        long end = System.currentTimeMillis();
     
        System.out.println("万次连接操作时间:" + (end - stat) + "ms");
    }
    
    
}

```



#### AOP通知获取数据

##### 获取参数 before after 使用JoinPoint

```java 
   @Before("ProjectAdvice.servicePt()")
    public void before(JoinPoint joinPoint) {
    
       Object[] args = joinPoint.getArgs();
    
       System.out.println(Arrays.toString(args));
       
       System.out.println("before...");
    }
    
    @After("ProjectAdvice.servicePt()")
    public void after(JoinPoint joinPoint) {
    
        Object[] args = joinPoint.getArgs();
    
        System.out.println(Arrays.toString(args)); 
        
        System.out.println("after...");
    }
```

##### Around 使用ProceedingJoinPoint

```java
   @Around("ProjectAdvice.servicePt()")
    public Object around(ProceedingJoinPoint pjp) throws Throwable {
    
        Object[] args = pjp.getArgs();
    
        for(int i = 0 ; i < args.length;i++){
            System.out.println(args[i]);
        }
            
        System.out.println("before around...");
    
        System.out.println("after around...");
        
        // 修改传过来的参数
        
        args[0] = 666;
        
        args[1] = "zhangsan" ;
        // 将修改获得args 传回原函数 返回回去
        Object proceed = pjp.proceed(args);
    
        return proceed;
    
    }
```

##### AfterReturning

```java
 @AfterReturning(value = "ProjectAdvice.servicePt()", returning = "ret")
    public void afterReturning(Object ret) {
        System.out.println("afterReturning..." + ret);
    }
```



## 事务处理

### 事务介绍

















































































































## SpingMVC

### 1 SpringMVC入门案例

#### 1 导入坐标 

```xml
  <dependencies>
  <!--  导入坐标 javax 和 servlet-->
      <dependency>
        <groupId>javax.servlet</groupId>
        <artifactId>javax.servlet-api</artifactId>
        <version>3.1.0</version>
        <scope>provided</scope>
      </dependency>
    
  <!--  springwebmvc-->

    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>
    


  </dependencies>
<!--  配置 tomcat 插件-->

  <build>
    <plugins>

      <!--tomcat插件-->
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
      </plugin>
    </plugins>
  </build>
```



#### 2 创建Servlet启动类 加载spring文件

```java
package com.itheima.config;

import org.springframework.web.context.WebApplicationContext;
import org.springframework.web.context.support.AnnotationConfigWebApplicationContext;
import org.springframework.web.servlet.support.AbstractDispatcherServletInitializer;

/**
 * @ClassName ServletContainerinitConfig
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/30 11:44
 * @Version 1.0
 */

// 定义一个Servlet 启动类 加载里面的spring 文件
public class ServletContainerinitConfig extends AbstractDispatcherServletInitializer {
    
    // 加载springmvc容器配置
    @Override
    protected WebApplicationContext createServletApplicationContext() {
    
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        
        ctx.register(SpringMvcConfig.class);
        
        return ctx;
    }
    
    //设置 那些请求归属springmvc处理
    @Override
    protected String[] getServletMappings() {
        // 设置所有请求归spring 请求
        return new String[]{"/"};
    }
    
    // 加载spring 容器配置
    @Override
    protected WebApplicationContext createRootApplicationContext() {
        return null;
    }
}

```



#####  方法一: 加载springmvc容器配置

创建一个web容器 

```java
 // 加载springmvc容器配置
    @Override
    protected WebApplicationContext createServletApplicationContext() {
    
        AnnotationConfigWebApplicationContext ctx = new AnnotationConfigWebApplicationContext();
        
        ctx.register(SpringMvcConfig.class);
        
        return ctx;
    }
    
```

##### 方法二: 设置请求归属

将所有的方法设置为SpringMVC形式

```java
  //设置 那些请求归属springmvc处理
    @Override
    protected String[] getServletMappings() {
        // 设置所有请求归spring 请求
        return new String[]{"/"};
    }
```

##### 方法三: 加载spring形式

```java
  // 加载spring 容器配置
    @Override
    protected WebApplicationContext createRootApplicationContext() {
        return null;
    }
```



#### 3 定义一个SpringMVC注解

扫描指定文件 将文件加载到 web容器当中去

```java
// 进行扫描
@Configuration
// 扫描 controller
@ComponentScan("com.itheima.controller")

public class SpringMvcConfig {
}
```

#### 4 定义一个controller类



```java
// 定义Controller 定义bean 这个是springmvc 特定的
@Controller
public class UserController {
    
    // 定义当前的访问路径
    @RequestMapping("/save")
    
    //设置当前操作的返回类型
    @ResponseBody
    
    // 定义一个处理请求的 方法
    public String save() {

        System.out.println(" user save ");
        
        return "'{'moudle' : 'springmvc'}'";
        
    }
}

```

@Controller 是定义bean是使用的 是 springmvc中特定的

 @RequestMapping("/save") 是同@RequestMapping 指向访问路径 以为getServletMappings()全部拦截为springmvc中

  @ResponseBody 表示返回类型 是json形式



**@ResponseBody**

**1、概念**
        **注解 @ResponseBody，使用在控制层（controller）的方法上。**

**2、作用**
        **将方法的返回值，以特定的格式写入到response的body区域，进而将数据返回给客户端。**

**当方法上面没有写ResponseBody,底层会将方法的返回值封装为ModelAndView对象。**

**如果返回值是字符串，那么直接将字符串写到客户端；如果是一个对象，会将对象转化为json串，然后写到客户端。**



#### 2 排除过滤器

```java
// 排除过滤器 排除所有的 controller 的bean
@ComponentScan(value = "com.itheima",
        excludeFilters = @ComponentScan.Filter(
            // 类型方式 使用注解的方式
                type = FilterType.ANNOTATION,
            // 所有的代码类
                classes = Controller.class
        )
)
```

```java
public class App {
    
    public static void main(String[] args) {
    
        AnnotationConfigApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
        
        System.out.println(ctx.getBean(UserController.class));
    }
}

```



#### 3  简易开发

```java
public class ServletContainerinitConfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    
    @Override
    protected Class<?>[] getRootConfigClasses() {
        //SpringMVC 开发 
        return new Class[]{SpringMvcConfig.class};
    }
    
    @Override
    protected Class<?>[] getServletConfigClasses() {
    
        //Spring 开发 
    
    
        return new Class[]{SpringConfig.class};
    }
    
    @Override
    protected String[] getServletMappings() {
        
        // 拦截所有的方法
        return new String[]{"/"};
    }
}
```



#### 4 定义整个目录的前缀

```java
@RequestMapping("/User")
```



#### 5 传参数

##### 1 简单参数

```java
public String save(String name) {

    System.out.println("user save ");

    System.out.println(name);

    return "'{'moudle' : 'springmvc'}'";

}
```



##### 2 post传参乱码处理

```java
@Override
protected Filter[] getServletFilters(){

    CharacterEncodingFilter filter = new CharacterEncodingFilter();

    // 设置编码方式
    filter.setEncoding("UTF-8");

    return new Filter[]{filter};
}
```

##### 3 传数组

当参数是同一个变量名就会将这个value放入到一个数组当中去

```java
 public String save1(String [] likes) {
        
        System.out.println("user save ");
        
        System.out.println(Arrays.toString(likes));
        
        return "'{'moudle' : 'springmvc'}'";
    }
```

##### 4 传集合

java中用的比较多的是List对象，List对象接收

```java
// 定义当前的访问路径
@RequestMapping("/save2")

//设置当前操作的返回类型
@ResponseBody

// 定义一个处理请求的 方法
// 传集合
// 加上注解 @RequestParam 说明 传递形式是 集合形式
public String save2(@RequestParam List<String> list) {
    System.out.println(list);
    System.out.println("user save ");

    return "'{'moudle' : 'springmvc'}'";
}

```

##### 5 传对象参数

创建user类 实现向user类中传入参数

**当对象的属性名和输入参数的名称一致就会被自动填充**

```java
    public String save(User user) {
    
        System.out.println("Book save");
    
        System.out.println(user.toString());
    
        System.out.println(user.getName());
    
        System.out.println(user.getAge());
        
        return "'{'moudle':'SpringMVC'}'";
        
    }
```

##### 6 传对象嵌套参数

创建Address

```java
package com.itheima.domain;

/**
 * @ClassName Address
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/30 21:28
 * @Version 1.0
 */

public class Address {
    
    String name;
    int age;
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public int getAge() {
        return age;
    }
    
    public void setAge(int age) {
        this.age = age;
    }
    
    @Override
    public String toString() {
        return "Address{" +
                "name='" + name + '\'' +
                ", age=" + age +
                '}';
    }
}
```

创建User类 加入address

```java
package com.itheima.domain;

/**
 * @ClassName User
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/30 21:03
 * @Version 1.0
 */

public class User {
    
    String name ;
    int age;
    
    // 嵌套使用类型
    private Address address;
    
    
    @Override
    public String toString() {
        return "User{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", address=" + address +
                '}';
    }
    
    public Address getAddress() {
        return address;
    }
    
    public void setAddress(Address address) {
        this.address = address;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public int getAge() {
        return age;
    }
    
    public void setAge(int age) {
        this.age = age;
    }
}
```

实现方法

```java
    // 定义当前的访问路径
    @RequestMapping("/save3")
    
    //设置当前操作的返回类型
    @ResponseBody
    
    // 定义一个处理请求的 方法
    // 传集合
    // 加上注解 @RequestParam 说明 传递形式是 集合形式
    public String save3(User user) {
        System.out.println("user save ");
        System.out.println(user);
        return "'{'moudle' : 'springmvc'}'";
        
    }
```



#### 6 传json文件

##### 1 导入json坐标

```xml
<dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
    </dependency>
```



##### 2 在config中加入识别json文件

```java
// 开启注解支持JSON格式传输

@EnableWebMvc
```



##### 3 编写传输数据

**加入@RequestBody 识别传入数据类型是 json形式**

```java
//    定义一个以json格式传输数据
    
    @RequestMapping("/listParamForJson")
    @ResponseBody
    // 传入参数是json形式 加入注解形式说明
        public String listParamForJson(@RequestBody List<String> likes){
    
        System.out.println("json...");
    
        System.out.println("likes ==>" + likes);
        
        return  "{'module':'list common for json param'}";
    
    }
    
```

![iB0fl3.png](https://i.328888.xyz/2023/04/12/iB0fl3.png)

加入对象形式传送数据

```java
  
    //    定义一个以json格式传输数据
    
    @RequestMapping("/user/listParamForJson")
    @ResponseBody
    // 传入参数是json形式 加入注解形式说明
    public String UserlistParamForJson(@RequestBody User user){
        
        System.out.println("json...");
    
        System.out.println(user.toString());
        
        return  "{'module':'list common for json param'}";
        
    }
    
```

![iB0fl3.png](https://i.328888.xyz/2023/04/12/iB0fl3.png)



传送数据为对象数组形式

```java
// 传送数组形式传递
    
    @RequestMapping("/user/ListParamForJson")
    @ResponseBody
    // 传入参数是json形式 加入注解形式说明
    public String UserlistParamForJson(@RequestBody List<User> user){
        
        System.out.println("json...");
        
        System.out.println("user == >" + user);
        
        return  "{'module':'list common for json param'}";
        
    }
    
```



```json
[{
    "name":"zhangsan",
    "age":"200",
    "address":
    {
        "name":"jack",
        "age":"100"
    }
},
{
    "name":"lisi",
    "age":"20",
    "address":
    {
        "name":"jack_json",
        "age":"10"
    }
}]
```

@EnableWebMvc 作用是 开启SpringMVC多项辅助功能

**@RequestBody与@RequestParam区别**

**@RequestBody** 更多的是用来接受json数据 或者 application

**@RequestParam区别** 更多是用来接受表单传参 【application / x-www-form-urlencoded】



#### 7 时间形式转换

##### 1 方式1  2022/08/31

```java
//     传递一个为日期的 形参
    @RequestMapping("/Data")
    @ResponseBody
    
    public String data(Date data){
        
        System.out.println("data...");
    
        System.out.println(data);
    
        return "{'module':'data param'}";
    }
```

##### 2 方式2 2022-08-31

使用 @DateTimeFormat 定义格式

定义成以 - 连接的形式 @DateTimeFormat(pattern = "yyyy-MM-dd") Date data1, 

```java
//     传递一个为日期的 形参
    @RequestMapping("/Data")
    @ResponseBody
    
    public String data(Date data, 
                     @DateTimeFormat(pattern = "yyyy-MM-dd") Date data1, 
                       @DateTimeFormat(pattern = "yyyy-MM-dd HH:MM:SS") Date data2){
        
        System.out.println("data...");
    
        System.out.println(data1);
            
        System.out.println(data);
    
        System.out.println(data2);
    
        return "{'module':'data param'}";
    }
```

注意点 ：

开启@**EnableWebMvc**

说明 ：准换原理 是 字符之间格式的互相转换



### 响应

#### 1 跳转页面

返回 page.jsp页面

```java
@Controller
public class Page {
    
    @RequestMapping("/page1")
    public String page1(){
    
        System.out.println("page is running...");
        
        return "page.jsp";
    }
}
```

#### 2 返回文本形式

```java
package com.itheima.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

/**
 * @ClassName Text
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/31 17:43
 * @Version 1.0
 */

@Controller

public class Text {
    
    @RequestMapping("/Text")
    @ResponseBody
    
    public String Text(){
    
        System.out.println("Text is runing...");
        
        return "response.text";
    }
    
}

```

#### 3 响应Json数据 和 集合 

```java
package com.itheima.controller;

import com.itheima.domain.User;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import java.util.ArrayList;
import java.util.List;

/**
 * @ClassName Json
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/31 17:47
 * @Version 1.0
 */
@Controller
@RequestMapping("/Json")
public class Json {
    
    @RequestMapping("/json")
    
    @ResponseBody
    
    public User json(){
    
        System.out.println("json is running...");
        
        User user = new User();
        
        user.setName("itheima");
        
        user.setAge(20);
        
        System.out.println(
                user.toString()
        );
        
        return user;
        
    }    
    
    
    
//    响应Pojo集合
    
    @RequestMapping("/json_collection")
    
    @ResponseBody
    
    public List<User> user(){
    
        System.out.println("user is running...");
        
        List<User> user = new ArrayList<User>();
        
        User user1  = new User();
        
        user1.setName("传智播客");
        user1.setAge(200);
        
        User user2 = new User();
        user2.setName("黑马程序员");
        user2.setAge(10);
        
        user.add(user1);
        user.add(user2);
        
        return user;
        
    }
    
}

```



### Rest风格

指定查询功能

![iB0yxy.png](https://i.328888.xyz/2023/04/12/iB0yxy.png)



注解：

**@ResponseBody @PathVariable.@RequestParam**

**@ResponseBody** 请求参数是要以json形式发送

**@PathVariable** 传一个形参的时候，使用

**@RequestParam** 使用的时候是 一个或两个参数 ，多用来 传表单数据 url地址等



**使用rest 风格编写代码的时候		RequestMapping 变化**

**@RequestMapping** 使用value 指定路径 指定使用方法 method

```java
@RequestMapping(value = "/rest_save",method = RequestMethod.POST)

@ResponseBody
```

当时用参数传递的时候 加上 @PathVariable注解

```java
@RequestMapping(value = "/delete/{id}",method = RequestMethod.DELETE)

@ResponseBody
```

```java
public String delete(@PathVariable int id)
```

```java
    
    /**
     *  使用rest风格 删除id 使用method方法 DELETE
     *  @PathVariable 定义地址值
     * 
     * 删除操作
     * @param id
     * @return
     */
    @RequestMapping(value = "/delete/{id}",method = RequestMethod.DELETE)
    
    @ResponseBody
    
        public String delete(@PathVariable int id){
        
        System.out.println("delete");
    
        System.out.println(id);
        
        return "{'module':'user save'}";
    }
    
    /**
     * 跟新操作
     * @param user
     * @return
     */
    @RequestMapping(value = "/users",method = RequestMethod.PUT)
    @ResponseBody
    
    // 以json形式发送
    public String update(@RequestBody User user){
        System.out.println("user update");
        
        return "{'module':'user update'}";
    }
    
    /**
     * 查询操作
     * @param id
     * @return
     */
        
    @RequestMapping(value = "/users/{id}",method = RequestMethod.GET)
    @ResponseBody
    
    public String get(@PathVariable int id){
        
        System.out.println("user get...");
    
        System.out.println(id);
    
        return "{'module':'user get'}";
    }
    
    /**
     * 无参传递     
     * 查询操作
     * @param 
     * @return
     */
    
    @RequestMapping(value = "/users",method = RequestMethod.GET)
    @ResponseBody
    
    public String get(){
        
        System.out.println("user getAll...");
        
        return "{'module':'user get'}";
    }
    
```

#### 简易开发

```java
package com.itheima.controller;

import com.itheima.domain.Book;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

/**
 * @ClassName SP_BookController
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/1 9:47
 * @Version 1.0
 */

// @Controller
// @ResponseBody
    
//    使用 RestController 把 @Controller 和 @ResponseBody 合并
@RestController
@RequestMapping("/SP_books")
public class SP_BookController {
    /**
     *  add
     * @param book
     * @return
     */
    // @RequestMapping(method = RequestMethod.POST)
    @PostMapping
    public String add(@RequestBody Book book){
        
        System.out.println("book add..." + book.toString()
        );
        
        return "'{moudle}:add'";
    }
    
    /**
     * delete
     * @param id
     * @return
     */
    // @RequestMapping(value = "/{id}",method = RequestMethod.DELETE)
    @DeleteMapping("/{id}")
    public String delete(@PathVariable int id){
        
        System.out.println("book delete..." + id);
        
        return "'{moudle}:delete'";
    }
    
    /**
     * update
     * @param book
     * @return
     */
    // @RequestMapping(method = RequestMethod.PUT)
    @PutMapping
    public String update(@RequestBody Book book){
        
        System.out.println("book update..." + book.toString());
        
        return "'{moudle}:update'";
    }
    
    
    /**
     * get
     * @param id
     * @return
     */
    // @RequestMapping(value = "/{id}",method = RequestMethod.GET)
    @GetMapping("/{id}")
    public String get(@PathVariable int id){
        
        System.out.println("book get..." + id);
        
        return "'{moudle}:get'";
    }
    
}

```

#### 记录@PostMapping

@PostMapping这个注解 在传递一个参数的时候不可能使用**@PathVariable** 这个是 请求的形式 /blog/1

@RequestParam 是判断的形式 键值对出现的

@PathVariable 适合用于 Get Delete 

@RequestParam 适合Post请求	

##### 传递一个参数

```java
@PostMapping("/login2")
public String login1(@RequestParam("name") String name){

    System.out.println(name);

    System.out.println("Post");

    return null;
}
```

##### 传递两个参数

```java
@PostMapping("/login1")
public String login1(@RequestParam("name") String name, @RequestParam("password") String password){

    System.out.println(name);
    System.out.println(password);

    System.out.println("Post");
    
    return null;
}
```

##### 传递一个对象

```java
package com.itheima.domain;

import lombok.Data;

/**
 * @ClassName TempUser
 * @Description TODO
 * @Author Typecoh
 * @Date 2023/1/12 17:57
 * @Version 1.0
 */

@Data
public class TempUser {
    
    private String name;
    private String password;
}
```

```java
@PostMapping("/login3")
public String login1(@RequestBody TempUser tempUser){
    
    System.out.println(tempUser.getName());
    System.out.println(tempUser.getPassword());
    
    System.out.println("Post");
    
    return null;
}
```

```json
{
        "name":"Typecoh",
        "password":"123456"
}
```

#### @GetMapping

可以使用@PathVariable注解

##### 一个参数

```java
@GetMapping("/login1/{id}")
public String login1(@PathVariable(name = "id") int id ){

    System.out.println(id);
    System.out.println("Post");
    
    return null;
}
```

##### 两个参数

```java
@GetMapping("/login2/{password}/{name}")
public String login2(@PathVariable(name = "name") String name, @PathVariable(name = "password") String password){

    System.out.println(name);
    System.out.println(password);
    
    return null;
}
```

```
http://localhost:8082/admin/login2/1/2
```



```java
@GetMapping("/login3")
public String login3(@RequestParam(name = "name") String name, @RequestParam(name = "password") String password){
    
    System.out.println(name);
    System.out.println(password);
    
    return null;
}
```

```
http://localhost:8082/admin/login3?name=12&password=1234
```

这两种形式的请求形式不一样

##### 请求一个整体

```java
@GetMapping("/login4")
public String login4(@RequestBody TempUser tempUser){

    System.out.println(tempUser.getName());
    System.out.println(tempUser.getPassword());
    
    return null;
}
```

```json
{
        "name":"Typecoh",
        "password":"123456"
}
```

**总结一下：post主要是来提交数据，好比如将前端写下的数据提交给后端，可以用于 数据验证或者将数据存入到数据库当中**

​					**get主要是根据条件去获取数据进行判断**







## SSM整合

**使用骨架创建WebApp项目的时候，创建好了只有如果没有创先src目录，就要等待一下然后断网**

### 1 文件层次目录

![iB04L5.png](https://i.328888.xyz/2023/04/12/iB04L5.png)



### 2  导入坐标

pom文件

```xml
<?xml version="1.0" encoding="UTF-8"?>

<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>org.example</groupId>
  <artifactId>ssm_01</artifactId>
  <version>1.0-SNAPSHOT</version>
  <packaging>war</packaging>


  <dependencies>
    
    <!--导入 druid 坐标-->
    <dependency>
      <groupId>com.alibaba</groupId>
      <artifactId>druid</artifactId>
      <version>1.1.16</version>
    </dependency>

    <!--导入 mybatis 坐标-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis</artifactId>
      <version>3.5.6</version>
    </dependency>

    <!--导入 mybatis-mybatis-->
    <dependency>
      <groupId>org.mybatis</groupId>
      <artifactId>mybatis-spring</artifactId>
      <version>1.3.0</version>
    </dependency>

    <!-- 导入 mysql 连接 java坐标-->
    <dependency>
      <groupId>mysql</groupId>
      <artifactId>mysql-connector-java</artifactId>
      <version>5.1.47</version>
    </dependency>

    <!-- 导入 spring-jdbc坐标-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-jdbc</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>

    <!--导入 spring-test 坐标-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-test</artifactId>
      <version>5.2.1.RELEASE</version>
      <scope>test</scope>
    </dependency>

    <!-- 导入junit坐标-->
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>4.12</version>
      <scope>test</scope>
    </dependency>

    <!--导入 spring-webmvc坐标-->
    <dependency>
      <groupId>org.springframework</groupId>
      <artifactId>spring-webmvc</artifactId>
      <version>5.2.10.RELEASE</version>
    </dependency>

    <!--  导入坐标 javax 和 servlet-->
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>

    <!--导入json坐标-->
    <dependency>
      <groupId>com.fasterxml.jackson.core</groupId>
      <artifactId>jackson-databind</artifactId>
      <version>2.9.0</version>
    </dependency>

  </dependencies>


  <build>
    <plugins>
      <!--tomcat插件-->
      <plugin>
        <groupId>org.apache.tomcat.maven</groupId>
        <artifactId>tomcat7-maven-plugin</artifactId>
        <version>2.2</version>
      </plugin>
    </plugins>
  </build>


</project>

```



###  3 resources文件中的jdbc 数据

jdbc.properties

```properties
jdbc.driver = com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/mybatis?useSSL=false
jdbc.username=root
jdbc.password=admin123	
```



### 4 创建一个config文件

#### 1 创建一个 JdbcConfig文件

##### 1 将数据库中的值赋值进去

```java
@Value("${jdbc.driver}")
private String driver;
@Value("${jdbc.url}")
private String url;
@Value("${jdbc.username}")
private String username;    
@Value("${jdbc.password}")
private String password;
```

##### 2 造一个DataSource对象 声明称bean的形式

```java
@Bean
    public DataSource dataSource(){
    
        DruidDataSource dataSource = new DruidDataSource();
        
        dataSource.setUsername(username);

        dataSource.setPassword(password);

        dataSource.setUrl(url);

        dataSource.setDriverClassName(driver);
        
        return dataSource;
    }
```

​	JdbcConfig

```java
package com.itheima.config;

import com.alibaba.druid.pool.DruidDataSource;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;

import javax.sql.DataSource;

/**
 * @ClassName JdbcConfig
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/1 11:31
 * @Version 1.0
 */

public class JdbcConfig {
    
    @Value("${jdbc.driver}")
    private String driver;
    @Value("${jdbc.url}")
    private String url;
    @Value("${jdbc.username}")
    private String username;    
    @Value("${jdbc.password}")
    private String password;
    
    @Bean
    public DataSource dataSource(){
    
        DruidDataSource dataSource = new DruidDataSource();
        
        dataSource.setUsername(username);

        dataSource.setPassword(password);

        dataSource.setUrl(url);

        dataSource.setDriverClassName(driver);
        
        return dataSource;
    }
}

```



#### 2 创建MybatisConfig文件

在这个文件中创建 sqlsessionFactoryBean对象

创建 MapperScannerConfigurer 对象代替Mapper代理

##### 1 sqlsessionFactoryBean

```java
@Bean
    public SqlSessionFactoryBean sqlSessionFactory(DataSource dataSource){
        
        SqlSessionFactoryBean factoryBean = new SqlSessionFactoryBean();
        
        factoryBean.setDataSource(dataSource);
        
        factoryBean.setTypeAliasesPackage("com.itheima.domain");
        
        return factoryBean;
    }
```

###### 1 创建一个damain 文件 Book 文件 执行 返回形式

```java
	package com.itheima.damain;

/**
 * @ClassName Bookd
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/1 21:29
 * @Version 1.0
 */

public class Book {
    
    private Integer id;
    private String type;
    private String name;
    private String description;

    @Override
    public String toString() {
        return "Book{" +
                "id=" + id +
                ", type='" + type + '\'' +
                ", name='" + name + '\'' +
                ", description='" + description + '\'' +
                '}';
    }

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getType() {
        return type;
    }

    public void setType(String type) {
        this.type = type;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public String getDescription() {
        return description;
    }

    public void setDescription(String description) {
        this.description = description;
    }
}

```



##### 2 MapperScannerConfigurer

```java
    @Bean
    public MapperScannerConfigurer mapperScannerConfigurer(){
        
        MapperScannerConfigurer msc = new MapperScannerConfigurer();
        
        msc.setBasePackage("com.itheima.dao");
        
        return msc;
        
    }    
```

###### 1 创建 dao 文件 下的 BookDao 文件执行sql语句查询的

```java
package com.itheima.dao;

import com.itheima.damain.Book;
import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import java.util.List;

/**
 * @ClassName
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/1 21:47
 * @Version 1.0
 */

public interface BookDao {
    
    @Insert("insert book values (#{id},#{type},#{name},#{description})")
    public Boolean add(Book book);
    
    @Delete("delete from book where id = #{id}")
    public Boolean delete(int id);
    
    @Update("update book set id = #{id},type = #{type},name = #{name},description= #{description} where id = #{id}")
    public Boolean update(Book book);
    
    // 查询单个
    @Select("select * from book where id = #{id}")
    public Book get(int id);
    
    //    查询全部的
    @Select("select * from book")
    public List<Book> getAll();
    
}

```



#### 3 创建SpringConfig文件

将 JdbcConfig 文件和 MybatisConfig文件 导入 到 SpringCondig文件中去

```java
@Configuration
@ComponentScan({"com.itheima.service"})
@PropertySource("classpath:jdbc.properties")
@Import({JdbcConfig.class,MybatsConfig.class})
public class SpringConfig {
}
```



#### 4 创建一个SpringMvcConfig文件

**扫描controller文件**

```java
package com.itheima.config;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.stereotype.Controller;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;

/**
 * @ClassName SpringMvcCongfig
 * @Description TODO
 * @Author lenovo   
 * @Date 2022/9/1 21:25
 * @Version 1.0
 */

@Configuration
@ComponentScan({"com.itheima.controller"})
@EnableWebMvc
public class SpringMvcCongfig {
}

```

#####  创建一个 controller文件

实现网页查询

```java
package com.itheima.controller;

import com.itheima.damain.Book;
import com.itheima.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.*;

import java.util.List;

/**
 * @ClassName BookServiceController
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/1 21:32
 * @Version 1.0
 */

@RestController
@RequestMapping("/Books")

public class BookServiceController {
    
    @Autowired
    private BookService bookService;
    
    @PostMapping
    public Boolean add(@RequestBody  Book book) {
        System.out.println("add...");
        return  bookService.add(book);

    }

    @DeleteMapping("/{id}")
    public Boolean delete(@PathVariable int id) {
        System.out.println("delete...");
        return bookService.delete(id);
    }

    @PutMapping
    public Boolean update(@RequestBody Book book) {
        System.out.println("update...");
        return bookService.update(book);
    }

    @GetMapping("/{id}")
    public Book get(@PathVariable int id) {
        System.out.println("get...");
        return bookService.get(id);
    }
    
    @GetMapping
    public List<Book> getAll() {
        System.out.println("getall...");
        return bookService.getAll();
    }
    
}

```

#### 5 整合 Spring SpringMvc文件

```java
package com.itheima.config;

import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

/**
 * @ClassName ServletCongfig
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/1 21:23
 * @Version 1.0
 */

public class ServletCongfig extends AbstractAnnotationConfigDispatcherServletInitializer {
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{SpringMvcCongfig.class};
    }

    protected String[] getServletMappings() {
        return new String[]{"/"};
    }
}

```

#### 6 实现 service 文件

```java
package com.itheima.service.impl;

import com.itheima.damain.Book;
import com.itheima.dao.BookDao;
import com.itheima.service.BookService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import java.util.List;

/**
 * @ClassName BookServiceImpl
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/1 21:31
 * @Version 1.0
 */

@Service
public class BookServiceImpl implements BookService {
    
    @Autowired
    
    private BookDao bookDao;
    
    public Boolean add(Book book) {
        
         bookDao.add(book);
         
         return true;
        
    }
    
    public Boolean delete(int id) {
    
        bookDao.delete(id);
    
        return true;
    
    }
    
    public Boolean update(Book book) {
    
        bookDao.update(book);
        
        return true;
    }
    
    public Book get(int id) {
        
        return bookDao.get(id);
    }
    
    public List<Book> getAll() {
       return bookDao.getAll();
    }
    
}
```

创建接口

```java
package com.itheima.service;

import com.itheima.damain.Book;
import org.apache.ibatis.annotations.Delete;
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Select;
import org.apache.ibatis.annotations.Update;

import java.util.List;

/**
 * @ClassName BookService
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/1 21:31
 * @Version 1.0
 */

public interface BookService {
    
    public Boolean add(Book book);
    
    public Boolean delete(int id);
    
    public Boolean update(Book book);
    
    public Book get(int id);
    
    public List<Book> getAll();
    
}
```



### 5  创建测试类

```java
package com.itheima.service;

import com.itheima.config.SpringConfig;
import com.itheima.damain.Book;
import com.itheima.dao.BookDao;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

import javax.swing.*;
import java.util.List;

/**
 * @ClassName BookServiceTest
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/2 11:05
 * @Version 1.0
 */
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
public class BookServiceTest {
    
    
    @Autowired
    private BookService bookService;
    
    @Test
    public void testGetById(){
    
        Book book = bookService.get(1);
    
        System.out.println(book);
    
    }
    
    @Test
    public void testGetAll(){
    
        List<Book> book = bookService.getAll();
    
        System.out.println(book);
        
    }
    
    @Test
    public void TestGet(){
    
        ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);
        
        BookDao bookDao = ctx.getBean(BookDao.class);
    
        Book book = bookDao.get(2);
    
        System.out.println(book);
    
    }
    
}
```

![iB0Dn8.png](https://i.328888.xyz/2023/04/12/iB0Dn8.png)

### 6 统一编码格式

#### 1 文件 目录

![iB0WfH.png](https://i.328888.xyz/2023/04/12/iB0WfH.png)

#### 2 创建code文件

**在code文件中定义 后端返回的状态**

**统一编码格式**



```java
package com.itheima.controller;

/**
 * @ClassName Code
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/3 9:19
 * @Version 1.0
 */

public class Code {
    
    // 成功标志
    public static final Integer   ADD_OK = 20011;
    public static final Integer   DELETE_OK = 20021;
    public static final Integer   UPDATE_OK = 20031;
    public static final Integer   GET_OK = 20041;
    
    // 失败标志
    public static final Integer   ADD_ERR = 20010;
    public static final Integer   DELETE_ERR = 20020;
    public static final Integer   UPDATE_ERR = 20030;
    public static final Integer   GET_ERR = 20040;
    
}
```



#### 3 创建result文件 

result 文件是 将 后端返回数据 做成一个标准的 格式

**后端返回 数据 形式化 主要包含 code （状态码） data（数据源） message（返回信息）**



返回数据格式

```json
{
    "data": {
        "id": 1,
        "type": "Spring实战 第5版",
        "name": "计算机理论",
        "description": "Spring入门经典教程，深入理解Spring原理技术内幕"
    },
    "code": 20041,
    "message": "查询成功"
}
```

**前端 发送请求之后 可以 根据 code 和  message 判定 返回的 状态**

**统一 拿数据 是从 data 中 拿取数据**



```java
package com.itheima.controller;

/**
 * @ClassName result
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/3 9:15
 * @Version 1.0
 */

public class result {
    
    // 数据返回统一格式
    private Object data;
    
    //返回状态码
    private Integer code;
    
    //返回描述信息
    private String message;
    
    public result( Integer code,Object data) {
        this.data = data;
        this.code = code;
    }
    
    public result(Integer code, Object data, String message) {
        this.data = data;
        this.code = code;
        this.message = message;
    }
    
    // setter 方法是将 数据显示到 PostMan 页面上显示
    
    public Object getData() {
        
        return data;
    }

    public void setData(Object data) {
        this.data = data;
    }

    public Integer getCode() {
        return code;
    }

    public void setCode(Integer code) {
        this.code = code;
    }

    public String getMessage() {
        return message;
    }

    public void setMessage(String message) {
        this.message = message;
    }
}
```

#### 4 修改信息返回

```java
    @GetMapping()
    public Result getAll(){
        
        System.out.println("getAll...");
    
        List<Book> list = bookService.GetAll();
    
        Integer code = list == null ? Code.GET_ERR : Code.GET_OK;
    
        String msg = list == null ? "查询失败，请重新查询" : "查询成功";
    
        return new Result(code,list,msg);
    }
```

![iB0kjQ.png](https://i.328888.xyz/2023/04/12/iB0kjQ.png)

### 7 异常 处理

每层均会可能出现异常，异常处理代码书写位置不规定，**解决办法就是将异常往外抛到表现层**统一处理

#### 1 文件目录

![iB00FE.png](https://i.328888.xyz/2023/04/12/iB00FE.png)







#### 2 在 controller 文件下 创建类 ProjectExceptionAdvice

**使用 注解 @RestControllerAdvice  @ExceptionHandler** 

所有的 异常 都会跑到 这个地方

```java
package com.itheima.controller;

import com.itheima.Expection.BusinessException;
import com.itheima.Expection.SystemException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

/**
 * @ClassName ProjectExceptionAdvice
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/3 11:11
 * @Version 1.0
 */

@RestControllerAdvice
public class ProjectExceptionAdvice {
    
    // 处理所有的类
    @ExceptionHandler(Exception.class)
    public result doException(Exception exp){
        
        System.out.println("Exception...");
        
        return new result(null,null,"error");  
    }   
}
```

#### 3 将异常分类

![iB0CYC.png](https://i.328888.xyz/2023/04/12/iB0CYC.png)

**构造方法 加上 set 和 get方法**

#### 1 BusinessException

```java
package com.itheima.Expection;

/**
 * @ClassName BusinessException
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/3 15:13
 * @Version 1.0
 */

public class BusinessException extends RuntimeException{
    
    private Integer code;
    
    public BusinessException(Integer code) {
        
        this.code = code;
    }
    
    public BusinessException(Integer code,String message) {
        
        super(message);
        this.code = code;
    }
    
    public BusinessException( Integer code,String message, Throwable cause) {
        
        super(message, cause);
        this.code = code;
    }
    
    
    public Integer getCode() {
        
        return code;
    }
    
    public void setCode(Integer code) {
        
        this.code = code;
    }
}
```

#### 2 SystemException

```java
package com.itheima.Expection;

/**
 * @ClassName SystemException
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/3 15:13
 * @Version 1.0
 */

public class SystemException extends RuntimeException{
    
    private Integer code;
    
    public SystemException(Integer code) {
        this.code = code;
    }
    
    public SystemException(Integer code,String message) {
        super(message);
        this.code = code;
    }
    
    public SystemException( Integer code,String message, Throwable cause) {
        super(message, cause);
        this.code = code;
    }
    
    
    public Integer getCode() {
        return code;
    }
    
    public void setCode(Integer code) {
        this.code = code;
    }
    
}
```



**当有异常发生时 向 两个异常类抛出异常**

```java
if(id == 1){
    throw new BusinessException(Code.BUSINESS_ERR,"请不要使用你的技术挑战我的耐性!");
}

try{
    // int i = 1 / 0;
}catch (
       Exception e 
){
    throw new SystemException(Code.SYSTEM_TIMEOUT_ERR,"服务器访问超时，请重试!",e);
}
```



**在 ProjectExceptionAdvice 中 接受 异常类 并且 向 result 返回信息**

```java
package com.itheima.controller;

import com.itheima.Expection.BusinessException;
import com.itheima.Expection.SystemException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

/**
 * @ClassName ProjectExceptionAdvice
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/3 11:11
 * @Version 1.0
 */

@RestControllerAdvice
public class ProjectExceptionAdvice {
    
    // 说明处理异常的种类
    @ExceptionHandler(SystemException.class)
    public result doSystemException(Exception exp){

        System.out.println("doSystemException...");

        return new result(null,null,exp.getMessage());
    }

    @ExceptionHandler(BusinessException.class)
    public result doBusinessException(Exception exp){

        System.out.println("doBusinessException...");

        return new result(null,null,exp.getMessage());
    }
    
    @ExceptionHandler(Exception.class)
    public result doException(Exception exp){
        
        System.out.println("Exception is running...");
        
        return new result(null,null,"error"); 
    }
}
```





### 8 拦截器

![iB0wlP.png](https://i.328888.xyz/2023/04/12/iB0wlP.png)

#### 1 目录文件

![iB0HMt.png](https://i.328888.xyz/2023/04/12/iB0HMt.png)





#### 2在 controller 文件下创建 interceptor文件



在 该文件下 创建一个拦截器 ProjectInterceptor 类

当拦截器拦截之后会调用这里的方法

```java
package com.itheima.controller.interceptor;

import org.springframework.context.annotation.ComponentScan;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * @ClassName ProjectInterceptor
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/3 16:38
 * @Version 1.0
 */

@Component
public class ProjectInterceptor implements HandlerInterceptor {
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle...");
        // return false 是结束后面的访问
        return false;
    }
    
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle...");
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...");
    }
    
}
```

#### 3 创建一个 SpringMvcSupport类

**规定拦截器触发条件**

**这个文件是加载到 SpringMvcConfig文件中去**

**配置 拦截地址**

 **registry.addInterceptor(projectInterceptor).addPathPatterns("/Books","/Books/**");**

```java
package com.itheima.config;

import com.itheima.controller.interceptor.ProjectInterceptor;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;

/**
 * @ClassName SpringvcSupport
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/3 16:41
 * @Version 1.0
 */

@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    
    @Autowired
    private ProjectInterceptor projectInterceptor;
    
    @Autowired
    // private ProjectInterceptor2 projectInterceptor2;
    
    @Override
    protected void addInterceptors(InterceptorRegistry registry) {
        
        // 配置多 拦截器
        // 当我调用/Books 或者/Books开头的文件 的时候就会进行拦截
        
        registry.addInterceptor(projectInterceptor).addPathPatterns("/Books","/Books/**");
    
        // registry.addInterceptor(projectInterceptor2).addPathPatterns("/Books","/Books/**");
    }
    
    // 过滤掉静态资源
    @Override
    protected void addResourceHandlers(ResourceHandlerRegistry registry) {
        
        registry.addResourceHandler("/pages/**").addResourceLocations("/pages/");
    }
}
```

![iB0bnJ.png](https://i.328888.xyz/2023/04/12/iB0bnJ.png)



### 9 Spring整合事务

#### 1 创建一个事务平台

将事务平台的代码加入到Jdbc中

```java
// 添加事务处理模块

@Bean
public PlatformTransactionManager transactionManager(DataSource dataSource){

    DataSourceTransactionManager dataSourceTransactionManager = new DataSourceTransactionManager();
    
    // 将数据源加入进去
    dataSourceTransactionManager.setDataSource(dataSource);
    
    return  dataSourceTransactionManager;
}
```

#### 2 开始事务

在服务层的接口上开启事务

```java
//加上事务处理
@Transactional
```

![iB0jcc.png](https://i.328888.xyz/2023/04/12/iB0jcc.png)

































## SpringBoot

### 1 创建文件

![iB08sA.png](https://i.328888.xyz/2023/04/12/iB08sA.png)



![iB0Nfo.png](https://i.328888.xyz/2023/04/12/iB0Nfo.png)



![iB0r8N.png](https://i.328888.xyz/2023/04/12/iB0r8N.png)

![iB0RYd.png](https://i.328888.xyz/2023/04/12/iB0RYd.png)







### 2 执行第一个SpringBoot文件

**到PostMan中去执行** http://localhost:8080/Books/2

```java
package com.itheima.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @ClassName controller
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/5 12:29
 * @Version 1.0
 */
@RestController
@RequestMapping("/Books")
public class controller {
    
    @GetMapping("/{id}")
    public String getById(@PathVariable int id){
    
        System.out.println("Id == >" + id);
                    
        return "hello SpringBoot!";
        
    }
    
}
```



### 3 快速运行开发

#### 1 打成jar包

![iB0mwb.png](https://i.328888.xyz/2023/04/12/iB0mwb.png)

#### 2 生成jar

![iB0pxq.png](https://i.328888.xyz/2023/04/12/iB0pxq.png)

#### 3 执行jar

![iB0BMz.png](https://i.328888.xyz/2023/04/12/iB0BMz.png)



#### 4 测试cmd执行的代码

![iB0gtw.png](https://i.328888.xyz/2023/04/12/iB0gtw.png)

### 4 配置文件格式

#### 1 properties 文件

![iB0xca.png](https://i.328888.xyz/2023/04/12/iB0xca.png)



#### 2 yaml文件

![iB07sx.png](https://i.328888.xyz/2023/04/12/iB07sx.png)

#### 3 yml文件

![iB0eyk.png](https://i.328888.xyz/2023/04/12/iB0eyk.png)



**需要注意的是这三个顺序:**

**application.properties > application.yml > application.yaml**

​	

### 5 文件数据格式配置

#### 1 使用properties文件配置数据

```properties
#设置properties文件的内容格式
enterprise.name=itcast
enterprise.age=16
enterprise.tel=4006184000
```

**注入变量**

```java
@Value("${enterprise.name}")
private String name;
@Value("${enterprise.age}")
private Integer age;
@Value("${enterprise.tel}")
private String tel;
```

**测试文件**

```java
package com.itheima.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @ClassName GetDataController
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/5 21:12
 * @Version 1.0
 */

@RestController
@RequestMapping("/getdata")
public class GetDataController {
    
    @Value("${enterprise.name}")
    private String name;
    @Value("${enterprise.age}")
    private Integer age;
    @Value("${enterprise.tel}")
    private String tel;
    
    @GetMapping
    public String GetData(){
    
        System.out.println("使用properties文件格式设置 数据格式");
        System.out.println("name ==>" + name);
        System.out.println("name ==>" + age);
        System.out.println("name ==>" + tel);
        System.out.println("-------------------------");
        return "hello SpringBoot!!!";
        
    }
}
```

**借助Environment对象**

```java
@Autowired
private Environment environment;

@GetMapping
public String GetDataEnvironment(){

    System.out.println("使用properties文件格式设置 数据格式 并且加上Environment 类");
    System.out.println(environment.getProperty("enterprise.name"));
    System.out.println(environment.getProperty("enterprise.age"));
    System.out.println(environment.getProperty("enterprise.tel"));
    System.out.println("-------------------------");
    return "hello SpringBoot!!!";

}
```

**那既然存在这个Environment 那不妨就自己在一个类**

**创建一个Enterprise**

```java
package com.itheima.domain;

import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.stereotype.Component;

import java.util.Arrays;

/**
 * @ClassName Enterprise
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/5 21:10
 * @Version 1.0
 */
@Component
@ConfigurationProperties(prefix = "enterprise")
public class Enterprise {

    private String name;
    private Integer age;
    private String tel;
    private String[] likes;

    @Override
    public String toString() {
        return "Enterprise{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", tel='" + tel + '\'' +
                ", likes=" + Arrays.toString(likes) +
                '}';
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }

    public String getTel() {
        return tel;
    }

    public void setTel(String tel) {
        this.tel = tel;
    }

    public String[] getLikes() {
        return likes;
    }

    public void setLikes(String[] likes) {
        this.likes = likes;
    }
}
```

```java
@Autowired
private Enterprise enterprise;

@GetMapping
public String getdataEnterprise(){

        System.out.println("使用properties文件格式设置 数据格式 并且加上Enterprise 类");
        System.out.println(enterprise.getName());
        System.out.println(enterprise.getAge());
        System.out.println(enterprise.getTel());
        System.out.println("-------------------------");
        return "hello SpringBoot!!!";
    
}
```



#### 2 使用yml文件

```yml
#server:
#  port: 82
#

#使用 yml 文件格式进行配置
enterprise:
  name: itcast
  age: 16
  tel: 4006184000
  subject:
    - Java
    - Web
    - Node
    - Sql
```

```java
package com.itheima.controller;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @ClassName GetDataFromYmlController
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/5 21:30
 * @Version 1.0
 */
@RestController
@RequestMapping("/getdata/yml")
public class GetDataFromYmlController {
    
    @Value("${enterprise.name}")
    private String name;
    @Value("${enterprise.age}")
    private Integer age;
    @Value("${enterprise.tel}")
    private String tel;
    @Value("${enterprise.subject[1]}")
    private String subject;
    
    @GetMapping
    public String getdata(){
    
    
        System.out.println("使用yml文件格式进行显示");
    
        System.out.println("name ==>" + name);
        System.out.println("name ==>" + age);
        System.out.println("name ==>" + tel);
        System.out.println("subject ==>" + subject);
        
        
        return "hello SpringBoot!!!";
    }
    
}
```



**yml 文件使用 自己创造的类进行数据封装**

```java
package com.itheima.controller;

import com.itheima.domain.Enterprise;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

/**
 * @ClassName GetDataFromYmlController
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/5 21:30
 * @Version 1.0
 */
@RestController
@RequestMapping("/getdata/yml")
public class GetDataFromYmlController {
    
    // @Value("${enterprise.name}")
    // private String name;
    // @Value("${enterprise.age}")
    // private Integer age;
    // @Value("${enterprise.tel}")
    // private String tel;
    // @Value("${enterprise.subject[1]}")
    // private String subject;
    //
    // @GetMapping
    // public String getdata(){
    //
    //
    //     System.out.println("使用yml文件格式进行显示");
    //
    //     System.out.println("name ==>" + name);
    //     System.out.println("name ==>" + age);
    //     System.out.println("name ==>" + tel);
    //     System.out.println("subject ==>" + subject);
    //    
    //    
    //     return "hello SpringBoot!!!";
    // }
    
    @Autowired  
    private Enterprise enterprise;
    
    @GetMapping
    public String getdata(){
        
        
        System.out.println("使用yml文件格式进行显示");
        
        System.out.println("name ==>" + enterprise.getName());
        System.out.println("name ==>" + enterprise.getAge());
        System.out.println("name ==>" + enterprise.getTel());
        // enterprise.getSubject() 返回一个数组名 + 【1】 得到对应的内容
        System.out.println("subject ==>" + enterprise.getSubject()[1]);
        
        
        return "hello SpringBoot!!!";
    }
    
}
```

### 6 多环境配置



**环境大致分为 开发环境 测试环境 生产环境**

**多环境的好处是什么呢？** 

**为了避免 在这三种环境之间相互转换，SpringBoot 给我们提供了很方便的配置，避免转换**

#### 1 在yml 和 yaml文件中

![iB0z8L.png](https://i.328888.xyz/2023/04/12/iB0z8L.png)



![iB06Gp.png](https://i.328888.xyz/2023/04/12/iB06Gp.png)





![iB0FqU.png](https://i.328888.xyz/2023/04/12/iB0FqU.png)



#### 2 在properties文件中

分别执行 dev pro test等执行命令

![iBwYW3.png](https://i.328888.xyz/2023/04/12/iBwYW3.png)



![iBwqpy.png](https://i.328888.xyz/2023/04/12/iBwqpy.png)



![iB2i48.png](https://i.328888.xyz/2023/04/12/iB2i48.png)

**文件相对位置**

![iBwuv5.png](https://i.328888.xyz/2023/04/12/iBwuv5.png)



![image-20220906142503121](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220906142503121.png)



### 7 SpringBoot整合

#### 1 SpringBoot整合Junit

##### 1 在service层造一个接口

```java
package com.itheima.service;

/**
 * @ClassName BookService
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/6 14:29
 * @Version 1.0
 */

public interface BookService {
    
    public void save();
    
}
```



在 impl 实现这个接口

```java
package com.itheima.service.impl;

import com.itheima.service.BookService;
import org.springframework.stereotype.Service;

/**
 * @ClassName BookServiceImpl
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/6 14:29
 * @Version 1.0
 */
@Service
public class BookServiceImpl implements BookService {
    
    @Override
    public void save() {
        
        System.out.println("save is running...");
        
    }
}
```

##### 2 创一个Test类

```java
package com.itheima;

import com.itheima.service.BookService;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

/**
 * @ClassName Test01
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/6 14:41
 * @Version 1.0
 */

@SpringBootTest
public class Test01 {
    
    
    @Autowired
    private BookService bookService;
    
    @Test
    public void Test(){
    
        System.out.println(1);
        
        bookService.save();
        
    }
    
}
```



####  2 SpringBoot 整合Mybatis

##### 1 文件目录

![iB2VNZ.png](https://i.328888.xyz/2023/04/12/iB2VNZ.png)

##### 2 创建一个domain文件

**这个文件放置自己的 Book类**

```java
package com.itheima06.domain;

/**
 * @ClassName Book
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/6 17:46
 * @Version 1.0
 */

public class Book {
    
    private Integer id;
    private String type;
    private String description;
    private String name;
    
    @Override
    public String toString() {
        return "Book{" +
                "id=" + id +
                ", type='" + type + '\'' +
                ", description='" + description + '\'' +
                ", name='" + name + '\'' +
                '}';
    }
    
    public Integer getId() {
        return id;
    }
    
    public void setId(Integer id) {
        this.id = id;
    }
    
    public String getType() {
        return type;
    }
    
    public void setType(String type) {
        this.type = type;
    }
    
    public String getDescription() {
        return description;
    }
    
    public void setDescription(String description) {
        this.description = description;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
}
```

##### 2 创建dao文件

**这个文件主要是放置 sql 查询语句**

```java
package com.itheima06.dao;

import com.itheima06.domain.Book;
import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Select;

/**
 * @ClassName BookDao
 * @Description TODO
 * @Author lenovo
 * @Date 2022/9/6 17:47
 * @Version 1.0
 */

@Mapper
public interface BookDao {
    
    @Select("select * from book where id = #{id}")
    public Book getById(Integer id);
    
}
```

##### 3 生成yml文件

```yml
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    username: root
    password: admin123
    url: jdbc:mysql://localhost/mybatis
    
```

##### 4 生成一个测试类进行测试

```java
package com.itheima06;

import com.itheima06.dao.BookDao;
import com.itheima06.domain.Book;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

@SpringBootTest
class SpringBoot06ApplicationTests {
    
    @Autowired
    private BookDao bookDao;
    
    @Test
    void DoGetById() {
    
        System.out.println(1);
    
        Book book = bookDao.getById(2);
        
        System.out.println(book);
    
    }
    
}
```



**至此 SpringBoot整合 Mybatis 结束**



## MybatisPlus

### 1 入门程序

**在dao的 UserDao中代码修改**

```java
package com.itheima.dao;

import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import com.itheima.domain.Book;
import org.apache.ibatis.annotations.Mapper;

/**
 * @ClassName UserDao
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/6 21:21
 * @Version 1.0
 */

@Mapper
public interface UserDao extends BaseMapper<Book> {
}
```

**在测试中代码**

```java
@Autowired
private UserDao userDao;
@Test
void TestGetAll(){

    List<Book> list = userDao.selectList(null);

    System.out.println(list);

}
```

* **刚刚认识MybatisPlus 发现没有写任何查询或者其他的sql语句 但是在调用的时候一个都不少**

**这就是惊人之处**



### 2 创建MybatisPlus步骤 

![iB2yuH.png](https://i.328888.xyz/2023/04/12/iB2yuH.png)

![iB2A2Q.png](https://i.328888.xyz/2023/04/12/iB2A2Q.png)



![iB2UTC.png](https://i.328888.xyz/2023/04/12/iB2UTC.png)



####  配置yml文件

```yml
spring:
  datasource:
    driver-class-name: com.mysql.jdbc.Driver
    username: root
    password: admin123
    url: jdbc:mysql://localhost:3306/mybatis
```

#### 使用继承BaseMapper 之后的 SQL函数

```java
package com.itheima;

import com.itheima.dao.UserDao;
import com.itheima.domain.Book;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

@SpringBootTest
class MybatisPlus01ApplicationTests {
    
    @Autowired
    private UserDao userDao;
    @Test
    void TestGetAll(){
    
        List<Book> list = userDao.selectList(null);
    
        System.out.println(list);
    
    }
    
    @Test
    void TestAdd(){
        
       Book book = new Book();
       
       book.setId(14);
       
       book.setType("Spring实战 第6版");
       
       book.setName("计算机导论");
       
       book.setDescription("十年沉淀之作，手写MybatisPlus精华思想");
       
       userDao.insert(book);
        
    }
    
    @Test
    void TestDelete(){
        
       userDao.deleteById(14);
        
    }
    
    @Test
    void TestUpdate(){
       
        Book book = new Book();
        
        book.setId(1);
        
        book.setName("MybatisPlus");
        
        book.setType("hello world");
        
        book.setDescription("深入MyabtisPlus源码剖析MyabtisPlus源码中蕴含的10大设计模式");
        
        userDao.updateById(book);
        
    }
    
    @Test
    void TestGetById(){
    
        Book book = userDao.selectById(2);
    
        System.out.println(book);
        
    }
    
    
}
```



#### 对domain中的类进行简化

**原始程序**

```java
public class Book {
    
    private Integer id;
    private String type;
    private String name;
    private String description;
    
    public Integer getId() {
        return id;
    }
    
    public void setId(Integer id) {
        this.id = id;
    }
    
    public String getType() {
        return type;
    }
    
    public void setType(String type) {
        this.type = type;
    }
    
    public String getName() {
        return name;
    }
    
    public void setName(String name) {
        this.name = name;
    }
    
    public String getDescription() {
        return description;
    }
    
    public void setDescription(String description) {
        this.description = description;
    }
    
    @Override
    public String toString() {
        return "Book{" +
                "id=" + id +
                ", type='" + type + '\'' +
                ", name='" + name + '\'' +
                ", description='" + description + '\'' +
                '}';
    }
}
```

加入依赖之后的文件

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
</dependency>
```

```java
package com.itheima.domain;

import lombok.*;

/**
 * @ClassName Book
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/6 21:15
 * @Version 1.0
 */

@Getter // 提供所有的 get方法
@Setter // 提供所有的 set方法
@ToString // 提供所有的 Tostring 方法
@EqualsAndHashCode // 为模型类的属性提供equals和hashcode方法
@Data //  将上述几个注解 合并在一起
@NoArgsConstructor // 无参构造方法
@AllArgsConstructor  // 提供包含所有参数的 构造方法

public class Book {
    
    private Integer id;
    private String type;
    private String name;
    private String description;
   
}
```

###  3 分页功能

创建一个 IPage类

```java
//在这里设定 当前页 和 每页数量
IPage iPage = new Page(1,2);
```

调用查询Page方法

```java
userDao.selectPage(iPage,null);
```

设置查询条件

```java
//这个是获得自己设定的数据
System.out.println("当前页面 ==> " + iPage.getCurrent());
System.out.println("每页显示数 ==> " + iPage.getSize());
//这里需要设置拦截器 获取数据
System.out.println("一共多少页 ==> " + iPage.getPages());
System.out.println("一共多少数据 ==> " + iPage.getTotal());
//这里是获取所有的数据源
System.out.println("数据 ==> " + iPage.getRecords());
```

```java
package com.itheima;

import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.itheima.dao.UserDao;
import com.itheima.domain.Book;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

@SpringBootTest
class MybatisPlus01ApplicationTests {

    @Autowired
    private UserDao userDao;
    
    @Test
    void TestGetPage(){

        //在这里设定 当前页 和 每页数量
        IPage iPage = new Page(1,2);
        
        userDao.selectPage(iPage,null);

        //这个是获得自己设定的数据
        System.out.println("当前页面 ==> " + iPage.getCurrent());
        System.out.println("每页显示数 ==> " + iPage.getSize());
        //这里需要设置拦截器 获取数据
        System.out.println("一共多少页 ==> " + iPage.getPages());
        System.out.println("一共多少数据 ==> " + iPage.getTotal());
        //这里是获取所有的数据源
        System.out.println("数据 ==> " + iPage.getRecords());
        

    }

}
```

设置拦截器

```java
package com.itheima.config;

import com.baomidou.mybatisplus.extension.plugins.MybatisPlusInterceptor;
import com.baomidou.mybatisplus.extension.plugins.inner.PaginationInnerInterceptor;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

/**
 * @ClassName MybatisPlusConfig
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/7 10:47
 * @Version 1.0
 */

@Configuration
public class MybatisPlusConfig {
    
    @Bean
    public MybatisPlusInterceptor mybatisPlusInterceptor(){
    
        //创建一个拦截器的 对象
        MybatisPlusInterceptor mybatisPlusInterceptor = new MybatisPlusInterceptor();
            
        //增加具体的拦截器 == 》 分页拦截器
        mybatisPlusInterceptor.addInnerInterceptor(new PaginationInnerInterceptor());
        
        return mybatisPlusInterceptor;
        
    }
    
}
```



![iB2tWP.png](https://i.328888.xyz/2023/04/12/iB2tWP.png)

### 4 DQL编程控制

#### 1 条件查询

**查询小于**

```java
@Autowired
private UserDao userDao;

@Test
public void TestGetAll(){

    // 按条件查询操作
    QueryWrapper queryWrapper = new QueryWrapper();
    // 查询大于小于操作

    //小于 &lt 
    // 查询 id < 10的
    queryWrapper.lt("id",10);

    List<Book> books = userDao.selectList(queryWrapper);
    System.out.println(books);

}
```

![iB2WpX.png](https://i.328888.xyz/2023/04/12/iB2WpX.png)



**另外一种书写形式**

```java
@Test
public void TestLambda(){

    //    查询方式二 按照lambda表达式查询

    QueryWrapper<Book> qw = new QueryWrapper<Book>();

    qw.lambda().lt(Book::getId,10);


    List<Book> books = userDao.selectList(qw);

    System.out.println(books);


}
```

**多条件查询**

```java
@Test
public void TestLambdaQW(){
    
    //    查询方式三 按照LambdaQueryWrapper表达式查询

    LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
    
    lqw.lt(Book::getId,12);
    
    // 多条件查询
    
    lqw.gt(Book::getId,10);

    List<Book> books = userDao.selectList(lqw);

    System.out.println(books);


}
```

**lambda链式编程**

```java
@Test
public void TestLambdaQW_lanshi(){
    
    //    查询方式三 按照LambdaQueryWrapper表达式查询
    
    LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
    
    // 多条件查询
    
    lqw.gt(Book::getId,10).lt(Book::getId,12);
    
    List<Book> books = userDao.selectList(lqw);
    
    System.out.println(books);
    
    
}
```

**空值判断**

```java
@Test
public  void TestNull(){
    
    //创建一个 UserQuery对象
    UserQuery uq = new UserQuery();
    
    // 将对象的值进行赋值
    uq.setId2(10);
    uq.setId(5);
    
    LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
    
    // 进行判断
    if(null != uq.getId2()){
        lqw.lt(Book::getId,uq.getId2());
    }
    
    if(null != uq.getId()){
        lqw.gt(Book::getId,uq.getId());
    }

    //输出结果
    List<Book> books = userDao.selectList(lqw);

    System.out.println(books);


}
```

```java
package com.itheima;

import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.itheima.dao.UserDao;
import com.itheima.domain.Book;
import com.itheima.query.UserQuery;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;

@SpringBootTest
class MybatisPlus01ApplicationTests {
    
    @Autowired
    private UserDao userDao;
    
    @Test
    public void TestGetAll(){
    
        // 按条件查询操作
        QueryWrapper queryWrapper = new QueryWrapper();
        // 查询大于小于操作
    
        //小于 &lt 
        // 查询 id < 10的
        queryWrapper.lt("id",10);

        List<Book> books = userDao.selectList(queryWrapper);
        System.out.println(books);
        
    
    
    }
    
    @Test
    public void TestLambda(){
    
        //    查询方式二 按照lambda表达式查询
    
        QueryWrapper<Book> qw = new QueryWrapper<Book>();
    
        qw.lambda().lt(Book::getId,10);
    
    
        List<Book> books = userDao.selectList(qw);
    
        System.out.println(books);
    
    
    }
    
    @Test
    public void TestLambdaQW(){
        
        //    查询方式三 按照LambdaQueryWrapper表达式查询
    
        LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
        
        lqw.lt(Book::getId,12);
        
        // 多条件查询
        
        lqw.gt(Book::getId,10);
    
        List<Book> books = userDao.selectList(lqw);
    
        System.out.println(books);
    
    
    }
    
    @Test
    public void TestLambdaQW_lanshi(){
        
        //    查询方式三 按照LambdaQueryWrapper表达式查询
        
        LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
        
        // 多条件查询
        
        lqw.gt(Book::getId,10).lt(Book::getId,12);
        
        List<Book> books = userDao.selectList(lqw);
        
        System.out.println(books);
        
        
    }
    
    @Test
    public  void TestNull(){
        
        //创建一个 UserQuery对象
        UserQuery uq = new UserQuery();
        
        // 将对象的值进行赋值
        uq.setId2(10);
        uq.setId(5);
        
        LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
        
        // 进行判断
        if(null != uq.getId2()){
            lqw.lt(Book::getId,uq.getId2());
        }
        
        if(null != uq.getId()){
            lqw.gt(Book::getId,uq.getId());
        }
    
        //输出结果
        List<Book> books = userDao.selectList(lqw);
    
        System.out.println(books);
    
    
    }
    
    @Test
    public  void TestNullLambda(){
        
        //创建一个 UserQuery对象
        UserQuery uq = new UserQuery();
        
        // 将对象的值进行赋值
        uq.setId2(10);
        uq.setId(5);
        
        LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
        
        lqw.lt(null != uq.getId2(),Book::getId,uq.getId2());
        lqw.gt(null != uq.getId(),Book::getId,uq.getId());
        
        List<Book> books = userDao.selectList(lqw);
        
        System.out.println(books);
    }
    
    @Test
    public  void TestNullLambdaLanshi(){
        
        //创建一个 UserQuery对象
        UserQuery uq = new UserQuery();
        
        // 将对象的值进行赋值
        uq.setId2(10);
        uq.setId(5);
        
        LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
        
        lqw.lt(null != uq.getId2(),Book::getId,uq.getId2())
           .gt(null != uq.getId(),Book::getId,uq.getId());
        
        List<Book> books = userDao.selectList(lqw);
        
        System.out.println(books);
    }
}
```



#### 2 投影查询

**通过投影的方法选着查看的字段**

```java
lqw.select(Book::getId,Book::getName);
```



```java
@Autowired
private UserDao userDao;

@Test
public void TestGet01(){
    
    LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
    
    //使用投影 显示结果
    lqw.select(Book::getId,Book::getName);

    List<Book> books = userDao.selectList(lqw);

    System.out.println(books);


}
```

以Map形式查询

```java
@Test
public void TestGet03(){
    
    QueryWrapper<Book> qw = new QueryWrapper<Book>();
    //使用Map 体现 select
    
    qw.select("count(*) as number");

    List<Map<String, Object>> maps = userDao.selectMaps(qw);

    System.out.println(maps);
    
    
}
```

**分组查询**

```java
@Test
public void TestGet04(){
    
    QueryWrapper<Book> qw = new QueryWrapper<Book>();
    // 根据 name 进行分组
    
    qw.select("count(*) as number","name");
    qw.groupBy("name");

    // List<Book> books = userDao.selectList(qw);

    List<Map<String, Object>> maps = userDao.selectMaps(qw);

    System.out.println(maps);

}
```





```java
package com.itheima;

import com.baomidou.mybatisplus.core.conditions.query.LambdaQueryWrapper;
import com.baomidou.mybatisplus.core.conditions.query.QueryWrapper;
import com.baomidou.mybatisplus.core.metadata.IPage;
import com.baomidou.mybatisplus.extension.plugins.pagination.Page;
import com.itheima.dao.UserDao;
import com.itheima.domain.Book;
import com.itheima.query.UserQuery;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;

import java.util.List;
import java.util.Map;

@SpringBootTest
class MybatisPlus01ApplicationTests {
    
    @Autowired
    private UserDao userDao;
    
    @Test
    public void TestGet01(){
        
        LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
        
        //使用投影 显示结果
        lqw.select(Book::getId,Book::getName);
    
        List<Book> books = userDao.selectList(lqw);
    
        System.out.println(books);
    
    
    }
    
    @Test
    public void TestGet02(){
        
        QueryWrapper<Book> qw = new QueryWrapper<Book>();
        //使用投影 显示结果
        qw.select("id","name");
        
        List<Book> books = userDao.selectList(qw);
        
        System.out.println(books);
        
        
    }
    
    @Test
    public void TestGet03(){
        
        QueryWrapper<Book> qw = new QueryWrapper<Book>();
        //使用Map 体现 select
        
        qw.select("count(*) as number");
    
        List<Map<String, Object>> maps = userDao.selectMaps(qw);
    
        System.out.println(maps);
        
        
    }
    
    
    @Test
    public void TestGet04(){
        
        QueryWrapper<Book> qw = new QueryWrapper<Book>();
        // 根据 name 进行分组
        
        qw.select("count(*) as number","name");
        qw.groupBy("name");
    
        // List<Book> books = userDao.selectList(qw);
    
        List<Map<String, Object>> maps = userDao.selectMaps(qw);
    
        System.out.println(maps);
    
    }
    
}
```

#### 3 查询条件

之前我们演示了 lt() gt() 方法

其他一些方法可以查看网站

[MyBatis-Plus (baomidou.com)](https://baomidou.com/)

简单演示几个函

**eq**

```java
/**
 * 等于操作 查询
 * lqw.eq 
 */

@Test
public void TestGet01(){
    
    LambdaQueryWrapper<Book> lqw = new LambdaQueryWrapper<Book>();
    
    lqw.eq(Book::getId,"10").eq(Book::getName,"市场营销");

    Book book = userDao.selectOne(lqw);

    System.out.println(book);

}
```

**between**

```java
/**
 * 范围查询
 * between
 * 
 */

@Test
public void TestGet02(){
    
   LambdaQueryWrapper<Book> lwq = new LambdaQueryWrapper<Book>();
   
   lwq.between(Book::getId,2,10);

    List<Book> books = userDao.selectList(lwq);

    System.out.println(books);

}
```

**模糊匹配like**

```java
/**
 * 范围查询
 * 模糊匹配like
 *
 */

@Test
public void TestGet03(){
    
    LambdaQueryWrapper<Book> lwq = new LambdaQueryWrapper<Book>();
    
    lwq.like(Book::getName,"论");
    
    List<Book> books = userDao.selectList(lwq);
    
    System.out.println(books);
    
}
```

**MybatisPlus还有一般内容没有学习 到这里 先停止学习 等做项目的时候在便学期**

































































# JDBC

## `JDBC创建`

### `1.注册驱动`

```java
//        1. 注册驱动
        Class.forName("com.mysql.jdbc.Driver");
```

### `2.获得连接`

```java
String url = "jdbc:mysql://localhost:3306/databases_01";
String username = "root";
String password = "admin123";

Connection coon = DriverManager.getConnection(url,username,password);
```

### `3.定义SQL语句`

```
 String sql = "UPDATE USER SET NAME = 'jac'  WHERE id = 2";
```

### `4.获取SQL对象`

```java
Statement stmt = coon.createStatement();
```



### `5.执行SQL`

```java
int count = stmt.executeUpdate(sql);
```

### `6.处理结果`

```java
System.out.println(count);
```

### `7.释放资源`

```java
stmt.close();
coon.close();
```

[JDBC文件位置]: E:\大学两年的比赛和项目\JDBC\out\production



## JDBC的API讲解

### ConnectionAPI讲解

数据库事务 利用抛出异常执行多条sql语句

```java

//        开启事务

        try{

            coon.setAutoCommit(false);
//       5.执行sql
            int count1 = stmt.executeUpdate(sql1);
//        6.处理的结果
            System.out.println(count1 + "count1");
//       5.执行sql
            int count2 = stmt.executeUpdate(sql2);
//        6.处理的结果
            System.out.println(count2 + "count2");

            coon.commit();

        } catch (Exception e){

//            回滚事务 代码中任何地方出现异常就回滚到最初地方
            coon.rollback();
            e.printStackTrace();
        }

```



### StatementAPI注解

#### DML 执行增删改查功能

##### 修改功能

```java
package com.itheima.JDBC;

import org.testng.annotations.Test;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

/**
 * JDBC 快速入门
 */

public class JDBCDemo4_Statement {

    @Test
    public void testDML() throws Exception {

        Class.forName("com.mysql.jdbc.Driver");

        String url = "jdbc:mysql://localhost:3306/databases_01";

        String username = "root";
        String password = "admin123";

        Connection coon = DriverManager.getConnection(url,username,password);

        String sql1 = "UPDATE USER SET NAME = 'j'  WHERE id = 1";

        Statement stmt = coon.createStatement();

        int count = stmt.executeUpdate(sql1);

        if(count > 0) {
            System.out.println("修改成功");
        }else {
            System.out.println("修改失败");
        }

        System.out.println(count);

        stmt.close();

        coon.close();
    }
}

```



#### DDL

##### 创建一个新的数据库 删除数据库

```java
package com.itheima.JDBC;

import org.testng.annotations.Test;

import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.Statement;

/**
 * JDBC 快速入门
 */

public class JDBCDemo4_Statement {

    @Test
    public void testDML() throws Exception {

        Class.forName("com.mysql.jdbc.Driver");

        String url = "jdbc:mysql://localhost:3306/databases_01";

        String username = "root";
        String password = "admin123";

        Connection coon = DriverManager.getConnection(url,username,password);

//        执行DML操作
//        String sql1 = "UPDATE USER SET NAME = 'j'  WHERE id = 1";

//        执行DDL操作 创建一个数据库
        String sql1 = "create database ty";

        Statement stmt = coon.createStatement();

        int count = stmt.executeUpdate(sql1);

        if(count > 0) {
            System.out.println("修改成功");
        }else {
            System.out.println("修改失败");
        }

        stmt.close();

        coon.close();
    }
}

```



### ResultSet注解

#### DQL查询

使用executeQuery执行查询sql语句 返回一个ResultSet对象

```
  ResultSet rs = stmt.executeQuery(sql);
```

##### ResultSet中的next()

进行向下检索 类型于 MySQL中的 游标

```java
        while(rs.next()){

            user us = new user();

            int id = rs.getInt("id");
            String name = rs.getString("name");
            int age = rs.getInt("age");
            int status = rs.getInt("status");
            String gender = rs.getString("gender");

            us.setId(id);
            us.setName(name);
            us.setAge(age);
            us.setGender(gender);
            us.setStatus(status);


            list.add(us);

        }
```

##### ResultSet 中的 get和set方法

```java
int id = rs.getInt("id");
String name = rs.getString("name");
int age = rs.getInt("age");
int status = rs.getInt("status");
String gender = rs.getString("gender");

us.setId(id);
us.setName(name);
us.setAge(age);
us.setGender(gender);
us.setStatus(status);
```

##### 创建user类

```java
package com.itheima.pojo;

public class user {

    int id;
    String name;
    int age;
    int status;
    String gender;

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public int getStatus() {
        return status;
    }

    public void setStatus(int status) {
        this.status = status;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    @Override
    public String toString() {
        return "user{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", age=" + age +
                ", status=" + status +
                ", gender='" + gender + '\'' +
                '}';
    }
}

```

##### 结合

###### 创建一个列表存储user对象

```
  List<user> list = new ArrayList<>();
```

###### 创建一个user对象存储rs对象

```java
user us = new user();

int id = rs.getInt("id");
String name = rs.getString("name");
int age = rs.getInt("age");
int status = rs.getInt("status");
String gender = rs.getString("gender");

us.setId(id);
us.setName(name);
us.setAge(age);
us.setGender(gender);
us.setStatus(status);

```

###### 将	user对象加到 list对象中

```java
list.add(us);
```



### PreparedStatement

##### PrepareStatement对象

```java
PreparedStatement pstmt =  coon.prepareStatement(sql);
```

##### pstmt方法

```java
pstmt.setInt(1,id);
pstmt.setString(2,name);
```

##### pstmt执行SQL

```java
//执行SQL

ResultSet rs = pstmt.executeQuery();

if(rs.next()){
    System.out.println("成功");
}else{
    System.out.println("失败");
}
```

##### 释放资源

```java
//      进行释放资源
rs.close();
pstmt.close();
coon.close();
```



### PrepareStatement好处

##### 1 预编译SQL，性能更高

###### 1.开启预编译代码

```java
useServerPrepStmts=true
```

###### 2.配置mysql配置文件（my.ini）

```java
log-output=FILE
general-log=1
general_log_file="D:\mysql.log"
slow-query-log=1
slow_query_log_file="D:\mysql_slow.log"
long_query_time=2
```



##### 2 防止SQL注入



## 数据库连接池



# MyBatis

<img src="https://i.328888.xyz/2023/04/12/iB2k9t.png" alt="iB2k9t.png" style="zoom:50%;" />

是持久性框架 简化JDBC开发的

[MyBatis官网]: https://mybatis.net.cn/

## 导入MyBatis依赖

### 写入依赖

```xml
<dependency>
    <groupId>org.mybatis</groupId>
    <artifactId>mybatis</artifactId>
    <version>3.5.5</version>
</dependency>
```

### 配置对应Maven home path文件

<img src="https://i.328888.xyz/2023/04/12/iB2C4J.png" alt="iB2C4J.png" style="zoom:67%;" />

### 数据库连接过程中字符编码出现问题

##### 错误提示

```xml
Unknown initial character set index '255' received from server. Initial client character set can be forced via the 'characterEncoding' property.
```

##### 最初代码

```xml
jdbc:mysql://localhost:3306/mybatis
```

##### 修改之后的代码

```xml
jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf8
```

### 导入logback配置文件

在main的resources文件中加入logback.xml文件 内容如下

```xml
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
    <!--
        CONSOLE ：表示当前的日志信息是可以输出到控制台的。
    -->
    <appender name="Console" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>[%level] %blue(%d{HH:mm:ss.SSS}) %cyan([%thread]) %boldGreen(%logger{15}) - %msg %n</pattern>
        </encoder>
    </appender>

    <logger name="com.itheima" level="DEBUG" additivity="false">
        <appender-ref ref="Console"/>
    </logger>


    <!--

      level:用来设置打印级别，大小写无关：TRACE, DEBUG, INFO, WARN, ERROR, ALL 和 OFF
     ， 默认debug
      <root>可以包含零个或多个<appender-ref>元素，标识这个输出位置将会被本日志级别控制。
      -->
    <root level="DEBUG">
        <appender-ref ref="Console"/>
    </root>
</configuration>
```



### 创建mybatis-config.xml文件

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <!--数据库链接信息-->
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis?useUnicode=true&amp;characterEncoding=utf8"/>
                <property name="username" value="root"/>
                <property name="password" value="admin123"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
<!--加载sql映射文件-->
        <mapper resource="com/itheima/Mapper/UserMapper.xml"/>
    </mappers>
</configuration>
```

### 编写SQL映射文件 统一管理文件 解决硬编码问题

#### 在resources文件下创建UserMapper.xml

```xml
<?xml version="1.0" encoding="UTF-8" ?><!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
// namespace 表示 作用区间名
<mapper namespace="com.itheima.Mapper.UserMapper">
    <select id="GetById" resultType="com.itheima.domain.User">
        select * from tb_user where id = 1;
    </select>
</mapper>
```

#### 编码

在com.itheima.pojo包下创建User类

```java
package com.itheima.pojo;

public class User {

    private Integer id         ;
    private String username    ;
    private String PASSWORD    ;
    private String gender      ;
    private String addr        ;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getUsername() {
        return username;
    }

    public void setUsername(String username) {
        this.username = username;
    }

    public String getPASSWORD() {
        return PASSWORD;
    }

    public void setPASSWORD(String PASSWORD) {
        this.PASSWORD = PASSWORD;
    }

    public String getGender() {
        return gender;
    }

    public void setGender(String gender) {
        this.gender = gender;
    }

    public String getAddr() {
        return addr;
    }

    public void setAddr(String addr) {
        this.addr = addr;
    }


    @Override
    public String toString() {
        return "User{" +
                "id=" + id +
                ", username='" + username + '\'' +
                ", PASSWORD='" + PASSWORD + '\'' +
                ", gender='" + gender + '\'' +
                ", addr='" + addr + '\'' +
                '}';
    }
}

```

#### 测试代码

在com.itheima中编写测试代码

```java
package com.itheima;

import com.itheima.pojo.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

/**
 * MyBatis 快速入门代码
 *
 */
public class MyBatisDemo {

    public static void main(String[] args) throws Exception {

//        1. 加载mybatis的核心配置文件，获取SqlSessionFactory

        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//        2 获取SqlSession对象 用对象执行Sql
        SqlSession sqlSession =  sqlSessionFactory.openSession();

//        3 执行sql语句 执行多条select

        // 返回一个泛型 List<User>
        List<User> users =  sqlSession.selectList("com.itheima.Mapper.UserMapper.GetAll");

//        4 执行单条sql
//        sqlSession.selectOne()


//        打印输出列表

        System.out.println(users);

//        释放资源
        sqlSession.close();
    }
}

```

### Mapper代理开发

#### 1 创建UserMapper接口

在com.itheima.Mapper中创建UserMapper接口

#### 2 UserMapper的接口和xml文件路径要一样

**将xml文件放到resources对应的com/itheima/Mapper文件目录下 切记这里使用 / 而不是 .**

#### 3 修改Mapper的namespace为接口

接口名：com.itheima.Mapper.UserMapper

#### 4 定义接口中的方法与id名一致

定义一个selectAll的方法

resultType的返回类型就是com.itheima.pojo.User

#### 5 代理对象完成之后 测试代码

```java
package com.itheima;
import com.itheima.Mapper.UserMapper;
import com.itheima.pojo.User;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;

import java.io.InputStream;
import java.util.List;

/**
 * MyBatis 快速入门代码
 *
 */
public class MyBatisDemo2 {

    public static void main(String[] args) throws Exception {

//        1. 加载mybatis的核心配置文件，获取SqlSessionFactory

        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//        2 获取SqlSession对象 用对象执行Sql
        SqlSession sqlSession =  sqlSessionFactory.openSession();

//        获取接口代理对象

       UserMapper userMapper =  sqlSession.getMapper(UserMapper.class);

//        3 执行sql语句 执行多条select

        // 返回一个泛型 List<User>
        List<User> users = userMapper.selectAll();

//        打印输出列表

        System.out.println(users);

//        释放资源
        sqlSession.close();
    }
}

```



### Mysql在IDEA中显示

#### 1 点击Database

![iB2whA.png](https://i.328888.xyz/2023/04/12/iB2whA.png)

#### 2 点击 + 

![iB2OHN.png](https://i.328888.xyz/2023/04/12/iB2OHN.png)

#### 3 选择Mysql数据库

<img src="https://i.328888.xyz/2023/04/12/iB2Ikb.png" alt="iB2Ikb.png" style="zoom:67%;" />

#### 4 勾选所有是数据库

<img src="https://i.328888.xyz/2023/04/12/iB2Npq.png" alt="iB2Npq.png" style="zoom:67%;" />	

#### 5 输入 用户名和密码

<img src="https://i.328888.xyz/2023/04/12/iB2r9z.png" alt="iB2r9z.png" style="zoom:67%;" />

#### 6 点击Test Connection 和 Apply

<img src="https://i.328888.xyz/2023/04/12/iB2cNa.png" alt="iB2cNa.png" style="zoom:67%;" />

## Mybatis的增删改查功能

### 查询所有数据selectAll

#### 1 在接口中定义方法selectAll

```java
public List<Brand> selectAll()
```

点击![iB2mhx.png](https://i.328888.xyz/2023/04/12/iB2mhx.png)

生成对应的select语句

```xml
<select id="selectAll" resultType="Brand">
#         select * from tb_brand;
</select>
```

#### 2 在test文件中写入对应的测试方法

```java
    @Test

    public  void testSelectAll() throws Exception {

//        1 获取SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//       2 获取SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();

//       3 获取Mapper代理对象

        BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

//       4. 执行方法
        List<Brand> brands = brandMapper.selectAll();

//        5 输出结果

        System.out.println(brands);

//       6 关闭资源

        sqlSession.close();


    }
```

#### 3 简化sql语句

**每次生成一个查询方法都需要写对应的select过于繁琐**

##### 1 使用sql语句块

```xml
<sql id = "brand_colunm">
	id, brand_name brandName, company_name companyName, ordered, description, status
</sql>
```

```xml
<select id="selectAll" resultType="Brand">
    select
  	  <include refid="brand_colunm"></include>  // 代替 *
    from tb_brand;
</select>
```

但是sql语句块不灵活每次更换select里面的内容需要更换一个sql文件

##### 2 使用resultMap 解决

```xml
<resultMap id="brandResultMap" type="brand">
    <!--        有两种属性 -->
    <!--
        1 id 主属性
        2 result  非主属性      
    --> 
    <result column="brand_name" property="brandName"></result>
    <result column="company_name" property="companyName"></result>

</resultMap>
```

1.id 属性 ： 是唯一标示

2 type属性 ： 是和接口类名一致

result属性中 有两种 ： 1 id 主属性 2 result 非主属性

使用的标签是： <result column = "" property = ""> column 表示列名 property 表示替换名

##### 3 参数占位符

```
1 #{}: 会将其退换成?,防止SQL注入
2 ${}: 拼SQL,会存在SQL注入问题

参数类型 parameterType 可以省略
       
特数字符处理
    1 转义处理 比如 < ==> &lt
    2 CDARA区：  处理  <![CDATA[
                            <
                         ]]> 
             
```

### 参数传递问题

#### 使用@Param

##### 1 使用 @Param("参数名称") 标记每一个参数，在映射配置文件中就需要使用 #{参数名称} 进行占位

```java
List<Brand> selectByCondition(@Param("status") int status, @Param("companyName") String companyName, @Param("brandName") String brandName);
```

##### 2 在xml内编写SQL语句

```xml
<select id="selectByCondition" resultMap="brandResultMap">  
    select *   from tb_brand   where 
    status = #{status}   
    and company_name like #{companyName}   
    and brand_name like #{brandName}
</select>
```

##### 3 在测试代码中实现

```java
@Test
    
    public  void selectByCondition() throws Exception {

//       参数设定
        
        int status = 1;
        String companyName = "华为";
        String brandName = "华为";
        
//        参数处理 将 company_name和 brand_name 进行模糊处理
    
        companyName = "%" + companyName + "%";
        brandName = "%" + brandName + "%";
        
        
//        1 获取SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//       2 获取SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();

//       3 获取Mapper代理对象
        
        BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);

//       4. 执行方法
//        1 使用传参方法进行
        List<Brand> brands = brandMapper.selectByCondition(status,companyName,brandName);

//        5 输出结果
        
        System.out.println(brands);

//       6 关闭资源
        
        sqlSession.close();
        
        
    }
```

#### 2 多个参数封装成实体对象

##### 1 创建一个对象接口

```java
List<Brand> selectByCondition(Brand brand);
```

##### 2 编写sql语句

```xml
<select id="selectByCondition" resultMap="brandResultMap">
    select * from tb_brand where status = #{status} 
    and company_name like #{companyName}
    and brand_name like #{brandName}
</select>
```

##### 3 编写测试方法

```java
 @Test
    /**
     *  将多个参数封装成实体对象
     */
    public  void selectByCondition_Obj() throws Exception {

//       参数设定
        
        int status = 1;
        String companyName = "华为";
        String brandName = "华为";

//        参数处理 将 company_name和 brand_name 进行模糊处理
        
        companyName = "%" + companyName + "%";
        brandName = "%" + brandName + "%";


//        1 获取SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//       2 获取SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();

//       3 获取Mapper代理对象
        
        BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
        
        Brand brand = new Brand();
    
        /**
         * 封装成对象形式
         */
        brand.setStatus(status);
        brand.setBrandName(brandName);
        brand.setCompanyName(companyName);

//       4. 执行方法
//        List<Brand> brands = brandMapper.selectByCondition(status,companyName,brandName);
        List<Brand> brands = brandMapper.selectByCondition(brand);
 //        5 输出结果
        
        System.out.println(brands);

//       6 关闭资源
        
        sqlSession.close();
        
        
    }
```

#### 3 使用Map

##### 1 创建map为参数的方法

```java
List<Brand> selectByCondition(Map map);
```

##### 2 编写sql语句

```xml
<select id="selectByCondition" resultMap="brandResultMap">
    select * from tb_brand where status = #{status} 
    and company_name like #{companyName}
    and brand_name like #{brandName}
</select>
```

##### 3 编写对象的test方法

```java
@Test
    /**
     *  使用Map作为接口封装对象
     */
    public  void selectByCondition_Map() throws Exception {

//       参数设定
        
        int status = 1;
        String companyName = "华为";
        String brandName = "华为";

//        参数处理 将 company_name和 brand_name 进行模糊处理
        
        companyName = "%" + companyName + "%";
        brandName = "%" + brandName + "%";


//        1 获取SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//       2 获取SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();

//       3 获取Mapper代理对象
        
        BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
        
        /**
         * 封装成Map对象形式
         */
        
        Map map = new HashMap();
        
        map.put("status",status);
        map.put("companyName",companyName);
        map.put("brandName",brandName);

//       4. 执行方法
//        List<Brand> brands = brandMapper.selectByCondition(status,companyName,brandName);
        List<Brand> brands = brandMapper.selectByCondition(map);
        //        5 输出结果
        
        System.out.println(brands);

//       6 关闭资源
        
        sqlSession.close();
        
        
    }
```

### 动态SQL

#### 使用if标签进行判断

```xml
<select id="selectByCondition" resultMap="brandResultMap"> 
    select *  from tb_brand  where 
        <if test="status != null">
       	 	status = #{status}
        </if>
        <if test="companyName != null and companyName != '' "> 
       	 	and company_name like #{companyName} 
        </if>                     
        <if test="brandName != null and brandName != '' "> 
        	and brand_name like #{brandName} 
        </if>
</select>    
```

#### 存在的问题

当将status这个条件进行去除 根据xml中SQL中的语法可以知道 where后面跟一个and 出现异常

![image-20220814154935850](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220814154935850.png)

#### 解决方法 

##### 	1 改写SQL语句

```xml
<select id="selectByCondition" resultMap="brandResultMap"> 
    select *  from tb_brand  where 1 = 1 
        <if test="status != null">
       		and status = #{status}
        </if>
        <if test="companyName != null and companyName != '' "> 
        	and company_name like #{companyName} 
        </if>                     
        <if test="brandName != null and brandName != '' "> 
        	and brand_name like #{brandName} 
        </if>
</select>    
```

##### 2 使用where<where>标签替代原来的where

```xml
<select id="selectByCondition" resultMap="brandResultMap"> 
    select *  from tb_brand  
    <where>
        <if test="status != null">
            and status = #{status}
        </if>
        <if test="companyName != null and companyName != '' ">
            and company_name like #{companyName}
        </if>
        <if test="brandName != null and brandName != '' ">
            and brand_name like #{brandName}
        </if>
    </where>
</select>
```

### 添加数据

#### 1 在接口中创建添加函数

```java
 public void add(Brand brand);
```

#### 2 在xml文件中加入对应的SQl语句

```xml
<insert id="add">
    insert into tb_brand (brand_name, company_name, ordered, description, status)
    values (#{brandName},#{companyName},#{ordered},#{description},#{status});
</insert>
```

#### 3 在test中加入测试函数

```java
    @Test
    /**
     *  add方法
     *
     */
    
    public  void add() throws Exception {

//       参数设定
        
        int status = 1;
        String companyName = "波导手机";
        String brandName = "波导手机";
        String description = "手机中的战斗机";
        int ordered = 100; 

//        1 获取SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//       2 获取SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();

//       3 获取Mapper代理对象
        
        BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
        
       Brand brand = new Brand();
       brand.setStatus(status);
       brand.setCompanyName(companyName);
       brand.setBrandName(brandName);
       brand.setDescription(description);
       brand.setOrdered(ordered);

//       4. 执行方法
       brandMapper.add(brand);

//       5. 手动提交事务
//        sqlSession.commit();
       
//       6 关闭资源
        
        sqlSession.close();
    }
```

#### 4 添加主键

useGeneratedKeys：是够获取自动增长的主键值。true表示获取

keyProperty ：指定将获取到的主键值封装到哪儿个属性里

```xml
<insert id="add" useGeneratedKeys="true" keyProperty="id">
    insert into tb_brand (brand_name, company_name, ordered, description, status)
    values (#{brandName},#{companyName},#{ordered},#{description},#{status});
</insert>
```

#### 5 开始事务

##### 1 openSession开始事务

```java
SqlSession sqlSession = sqlSessionFactory.openSession(true);
```

##### 2 手动提交

```java
 sqlSession.commit();
```

### 删除数据

#### 1 单个删除

##### 1. 编写接口方法

```java
public void delete(int id);
```

##### 2.编写SQL语句

```mysql
<delete id="deleteById">   delete from tb_brand where id = #{id};</delete>
```

##### 3. 编写测试方法

注意不提交事务数据库查询结果查询不到

提交事务的说明和上次一样

```java

    @Test
    /**
     *  add方法
     *
     */

    public  void delete() throws Exception {

//       参数设定
        int id = 6; 

//        1 获取SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//       2 获取SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();

//       3 获取Mapper代理对象
    
        BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);
    
       

//       4. 执行方法
     
        brandMapper.delete(id);

//       5. 手动提交事务
        sqlSession.commit();

//       6 关闭资源
    
        sqlSession.close();
    }
```

#### 2 多数删除

##### 1 创建一个接口函数

```java
public void deleteByids(@Param("ids") int[] ids);
```

##### 2 编写对应的SQL语句

```sql
<delete id="deleteByids">
    delete from tb_brand where id
   		 in

    <foreach collection="ids" item = "id" separator="," open="(" close=")">
   		 #{id}
    </foreach>
</delete>
```

##### 3 对应的test的方法

```java
@Test
    /**
     *  deleteByids方法
     *
     */
    
    public  void deleteByids() throws Exception {

//       参数设定
        int []ids = {3,5};

//        1 获取SqlSessionFactory
        String resource = "mybatis-config.xml";
        InputStream inputStream = Resources.getResourceAsStream(resource);
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

//       2 获取SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();

//       3 获取Mapper代理对象
        
        BrandMapper brandMapper = sqlSession.getMapper(BrandMapper.class);



//       4. 执行方法
        
        brandMapper.deleteByids(ids);

//       5. 手动提交事务
        sqlSession.commit();

//       6 关闭资源
        
        sqlSession.close();
    }
```

### 参数传递问题

#### 单个参数传递问题

##### 1 POJO类型

​		直接使用。要求 属性名 和 参数占位符名称 一致

##### 2 Map类型

​		直接使用。要求 map集合的键名 和 参数占位符名称 一致

##### 3 collection集合类型

###### 1 创建一个Collection的对象的函数

```java
 User select (Collection collection);
```

###### 2 创建出Hashset() 对象

```java
 User user = userMapper.select(new HashSet());
```

###### 3 进入ParamNameResolver

对象是collection类型的时候 map.put 会调用两次

第一个 是 map.put("collection",collection)

​				map.put("name",collection) ==> name = arg0

<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220815113220071.png" alt="image-20220815113220071" style="zoom:50%;" />



##### 4 List集合类型

###### 1 创建一个List的对象的函数

```java
 User select (List list);
```

###### 2 创建出Arraylist() 对象

```java
 User user = userMapper.select(new Arraylist());
```

###### 3 进入ParamNameResolver

对象是List类型的时候 map.put 会调用两次

第一个 是 map.put("collection",collection)

​				map.put("list",collection)

​				map.put("name",collection) ==> name = arg0

<img src="https://i.328888.xyz/2023/04/12/iB2XHL.png" alt="iB2XHL.png" style="zoom:67%;" />



##### 5 Array类型

###### 1 创建一个Array的对象的函数

```java
 User select (Array array);
```

###### 2 创建出Array() 对象

```java
 User user = userMapper.select(new array());
```

###### 3 进入ParamNameResolver

对象是Array类型的时候 map.put 会调用两次

第一个 是 map.put("arg0"，数组)

​				map.put("array"，数组);



##### 6 其他类型 

#### 多个参数传递问题

##### 1 生成对应接口函数

```java
User select (@Param("username") String username, @Param("password") String password);
```

当mybatis发现传递参数是多个的时候就会会将这些参数自动封装成Map集合，构造成对应的键值对

内部会自动使用

map.put("arg0",参数值1)

map.put("param1",参数值1)

map.put("arg1",参数值2)

map.put("param2",参数值2)

##### 2 查询的时候编写SQL语句

```sql
    <select id="select" resultMap="UserresultMapper">
        select * from tb_user where
            username = #{username}
        and password = #{password}
    </select>
```

##### 3 当没有使用@Param注注解

```sql
    <select id="select" resultMap="UserresultMapper">
        select * from tb_user where
            username = #{arg0} 
        and password = #{arg01}
    </select>
```

```sql
    <select id="select" resultMap="UserresultMapper">
        select * from tb_user where
            username = #{param1} 
        and password = #{parma2}
    </select>
```

##### 4 推荐使用@param注解 去替代Map中的键名

```java
User select (@Param("username") String username, @Param("password") String password);

  <select id="select" resultMap="UserresultMapper">
        select * from tb_user where
            username = #{username}
        and password = #{password}
    </select>
```

### 使用注解的方法

#### 当sql语句比较简单的时候使用注解

```
    @Select("select * from tb_brand where addr =  #{addr}")
    User selectByAddr(String addr);
```

#### 当sql语句比较复杂的时候使用xml文件配置

#### 注解类型

##### 1查询  @Select

##### 2 插入 @Insert

##### 3 更新 @Update

##### 4 删除 @Delete

使用注解可以不用在xml文件中继续书写sql语句



## Mybatis总结

### 创建文件summary_Mybatis

### 创建main中的java里面的文件

#### 1 创建一个类User

#### 2 创建对应的接口UserMapper

#### 3 配置pom文件各种依赖

配置依赖的时候去Maven官网搜索相对应的依赖

#### 4 修改对应的文件

![iB2gep.png](https://i.328888.xyz/2023/04/12/iB2gep.png)



#### 5 在resource文件中配置好mybatis_config.xml 和 logoback.xml

#### 6 连接数据库

#### 7 在Maven中进行Compile 和 Test

#### 8 在resource文件中创建和UserMapper对应的xml文件

至此基本文件框架写完了

### 代码书写

#### 不使用Mapper

##### 1 在 UserMapper 中创建一个方法

```java
  public User selectById(int id);
```

##### 2 使用resultType类型书SQL

```sql
    <select id="selectAll" resultType="user">
        select * from tb_user;
    </select>
```

##### 3 编写测试代码

```java
        
    //  1 加载文件获取SqlsessionFactory
        
        String resource = "mybatis_config.xml";
            
        InputStream inputStream = Resources.getResourceAsStream(resource);
        
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
        
    //    2 获取SqlSession

        SqlSession sqlsession = sqlSessionFactory.openSession();
        
    //    3 执行SQL语句
        List<User> user = sqlsession.selectList("selectAll");
    
        System.out.println(user);
        
        sqlsession.close();
```

#### 使用Mapper映射文件

首先看个错误提示：没有找到映射文件

```
Type interface com.itheima.Mapper.UserMapper_1 is not known to the MapperRegistry.
```

##### 配置映射文件步骤

###### 1 在MyBatis_config.xml中进行配配置

```xml
  <mapper resource="com/itheima/Mapper/BrandMapper.xml"/>
```

###### 2 在对应接口的Mapper中配置

namespace要和路径保持一致

```xml
<mapper namespace="com.itheima.Mapper.UserMapper_1">
    
</mapper>
```

#### 在使用Mapper进行测试

##### 重新创建一个接口

```java
User selectById(int id);
```

##### 对应的Mapper文件中书写SQL

```sql
 <select id="selectById" resultType="User">
        select * from tb_user where id = #{id};        
 </select>
```

##### 编写测试代码

获取Mapper映射对象 之后就不需要写入id名

统一使用UserMapper_1.class

```java
 		 // 纪录文件
        String resource = "mybatis_config.xml";

        // 加载文件 生成SqlSessionFactory对象
        InputStream inputStream = Resources.getResourceAsStream(resource);
    
        SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    
        // 获取 SqlSession对象
        SqlSession sqlSession = sqlSessionFactory.openSession();
        
        // 获取Mapper映射对象
        UserMapper_1 userMapper_1 = sqlSession.getMapper(UserMapper_1.class);
        
        //  执行sql
        User user = userMapper_1.selectById(2);
    
        //  打印输出
        System.out.println(user);
    
        //  释放资源
        sqlSession.close();
```

通过在mybatis_config.xml设置别名

```xml
<typeAliases>
    <package name="com.itheima.pojo"/>
</typeAliases>
```

可以将 com.itheima.pojo.User 简化

```
resultType="User"
```

至此Mapper基本功能就讲述完成

之后的增删改查功能就后续项目实战复习

### ResultType

使用resultType的场景是为了使用动态sql语句，避免命名格式不一致而导致数据读不出

### ResultMap

使用resultMap是提高resultType的类别，优化了resultType书写形式

### TypeAliases

使用resultType和resultMap的值 是包名加类名  使用TypeAliases简化



# TomCat

#### 1 解压压缩包

#### 2 修改conf文件中的server.xml中默认端口

![iB2FA5.png](https://i.328888.xyz/2023/04/12/iB2FA5.png)

#### 3 解决乱码问题

![iB2eB3.png](https://i.328888.xyz/2023/04/12/iB2eB3.png)

![iB2Gr8.png](https://i.328888.xyz/2023/04/12/iB2Gr8.png)

#### 4 配置环境

如果出现闪退的情况可能是出现了 java配配置环境出现了错误

![iB2d5Z.png](https://i.328888.xyz/2023/04/12/iB2d5Z.png)

![iB25ZF.png](https://i.328888.xyz/2023/04/12/iB25ZF.png)

#### 5 部署

将html项目放在webapps中

将项目打成一个war包会自动解压

#### 6 在IDEA中创建Web项目

双击java

![iB2oeQ.png](https://i.328888.xyz/2023/04/12/iB2oeQ.png)

![iB290C.png](https://i.328888.xyz/2023/04/12/iB290C.png)

![iB2JBP.png](https://i.328888.xyz/2023/04/12/iB2JBP.png)

创建成功



#### IDEA中使用TomCat

**如果是在服务器上启动TomCat的话需要注意pom中的packaging的类型**

* pom：父类型都为pom类型
* jar：内部调用或者是作服务使用

- war：打包项目，用于在容器（Tomcat、Jetty等）上部署

```
<packaging>war</packaging>
```

##### 1 使用插件的方法

###### 1 在pom中导入坐标

```xml

    <build>
        <plugins>
            
            <!--tomcat插件-->
            <plugin>
                <groupId>org.apache.tomcat.maven</groupId>
                <artifactId>tomcat7-maven-plugin</artifactId>
                <version>2.2</version>
            </plugin>
        </plugins>
    </build>
    
```

###### 2 运行步骤

######  <img src="https://i.328888.xyz/2023/04/12/iB2PrJ.png" alt="iB2PrJ.png" style="zoom:67%;" />



# Servlet

**创建文件项目类型是需要创建Web项目**

## 1 Servlet快速入门

### 1 导入依赖

```xml
    <dependencies>
        <dependency>
            <groupId>javax.servlet</groupId>
            <artifactId>javax.servlet-api</artifactId>
            <version>3.1.0</version>
            <scope>provided</scope>
        </dependency>
    </dependencies>
```

### 2 创建ServletDemo1类

**将ServletDemo1继承Servlet类**

指定访问路径

实现具体方法

```java
public class ServletDemo1 implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        
    }
    
    @Override
    public ServletConfig getServletConfig() {
        return null;
    }
    
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("Servlet hello world...");
    }
    
    @Override
    public String getServletInfo() {
        return null;
    }
    
    @Override
    public void destroy() {
        
    }
}
```

### 3 设置访问路径

```java
@WebServlet("/demo1")
```

### 4 运行展示结果

![iB21iA.png](https://i.328888.xyz/2023/04/12/iB21iA.png)

网页空白

输出窗口显示

```
Servlet hello world...
```



## 2 Servlet方法

### 1 init初始化方法

```java
	 **
     * 初始化方法
     * 1.调用时机：默认方法：Servlet被第一次访问时，调用
     * 2.调用次数：1次
     * @param servletConfig
     * @throws ServletException
     */
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        System.out.println("init...");
    }
```

**这个init只会执行一次**

####  loadOnStarup 控制 Servlet创建对象

```
@WebServlet(value = "/demo2",loadOnStartup = -1) // loadOnStartup = -1 是默认的
```

```
@WebServlet(value = "/demo2",loadOnStartup = 1) // loadOnStartup 改成正数的时候，没有启动Servlet服务的时候 init方法也会被调用
```

### 2 service方法	

```java
/**
     * 提供服务
     * 1 调用时间：每一次Servlet被访问时被调用
     * 2 调用次数：多次
     * @param servletRequest
     * @param servletResponse
     * @throws ServletException
     * @throws IOException
     */
    
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("Servlet hello world...");
    }
    
```

![iB2QOo.png](https://i.328888.xyz/2023/04/12/iB2QOo.png)

手动运行tomcat命令

```
mvn tomcat7:run
```

### 3 destroy方法

```java
 
    /**
     * 销毁方法
     * 1 调用时机： 内存释放或者服务器关闭的时候，Servlet对象会被销毁，调用
     * 2 调用次数： 只调用一次
     */
    @Override
    public void destroy() {
        System.out.println("destroy...");
    }
```

手动关闭时可以触发这个函数执行

```
ctrl + c
```

### 4 ServletConfig 方法

```java
    private ServletConfig config;
    
    /**
     * 初始化方法
     * 1.调用时机：默认方法：Servlet被第一次访问时，调用
     * 2.调用次数：1次
     * @param servletConfig
     * @throws ServletException
     */
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        this.config = servletConfig;
        System.out.println("init...");
    }
    
    /**
     * 返回Config对象
     * 可以将init的形参的作用域提升
     * 创建一个对象进行传递
     * @return
     */
    
```



## 3 Servlet体系结构

自己实现分装doget dopost方法

```java
@Override
public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {

    // 强转 servletRequest 成 HttpServlet
    HttpServletRequest request = (HttpServletRequest) servletRequest;

    // 获取 访问方法
    String method = request.getMethod();
    
    // 对不同方法进行不同操作
    if(method.equals("GET")){
        doGet(servletRequest,servletResponse);
    }
    else if(method.equals("POST")){
        doPost(servletRequest,servletResponse);
    }
}
```

**后面不同的用户要求完成不同的行为**

**创建新任务实现功能**

```java
@WebServlet("/demo2")
public class demo2 extends MyServlet{
    
    @Override
    protected void doGet(ServletRequest servletRequest, ServletResponse servletResponse) {
        System.out.println("get...");
    }
    
    @Override
    protected void doPost(ServletRequest servletRequest, ServletResponse servletResponse) {
        System.out.println("post...");
    }
}
```

#### 1 继承HttpServlet

内部函数

```java
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("get...");
    }
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("post...");
    }
```

**doGet() doPost() 是自己封装出来的方法**

#### 2 urlPattern配置

##### 1 一个Servlet可以配置多个路径

```java
/**
 * 
 * 配置多个路径
 * 
 * @ClassName ServletDemo5
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/18 22:19
 * @Version 1.0
 */
@WebServlet(urlPatterns =  {"/demo5","/demo6"})
public class ServletDemo5 extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("get...");
        System.out.println("/demo7");
    }
    
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("post...");
    }
}

```

##### 2 精确匹配路径

```java
/**
 * 
 * 精确匹配
 * 
 * @ClassName ServletDemo5
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/18 22:19
 * @Version 1.0
 */
@WebServlet("user/demo6")
```

##### 3 目录匹配

```java
/**
 * 
 * 精确匹配
 * 
 * @ClassName ServletDemo5
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/18 22:19
 * @Version 1.0
 */
@WebServlet("user/*")
```

##### 4 扩展名匹配

```java

/**
 * 
 * 扩展名匹配
 * 
 * @ClassName ServletDemo5
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/18 22:19
 * @Version 1.0
 */
@WebServlet("*.demo6")
```

##### 5 任意匹配

```java

/**
 * 
 * 任意匹配
 * 
 * @ClassName ServletDemo5
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/18 22:19
 * @Version 1.0
 */
@WebServlet("/")
```

### xml文件配置Servlet

```xml
<!--  名称配置  -->
    <servlet>
        <servlet-name>demo3</servlet-name>
        <servlet-class>demo3</servlet-class>
    </servlet>
    
<!--    映射-->
    <servlet-mapping>
        <servlet-name>demo3</servlet-name>
        <url-pattern>/demo3</url-pattern>
    </servlet-mapping>
```



### Request and Respondse

request：请求

respondse：响应

![iB2q1V.png](https://i.328888.xyz/2023/04/12/iB2q1V.png)

#### Request Get函数

![iBHOS8.png](https://i.328888.xyz/2023/04/12/iBHOS8.png)

```java
// String getMethod()：获取请求方式： GET
String method = req.getMethod();
System.out.println(method);
```

```java
// String getContextPath()：获取虚拟目录(项目访问路径)： /MavenWeb

String contextPath = req.getContextPath();
System.out.println(contextPath);
```

```java
// String getRequestURL(): 获取URL http://localhost:8080/MavenWeb/req
StringBuffer requestURL = req.getRequestURL();
System.out.println(requestURL);
```

```java
// String getRequestURI()：获取URI(统一资源标识符)： /MavenWeb/req
String requestURI = req.getRequestURI();
System.out.println(requestURI);
```

```java
// String getQueryString()：获取请求参数（GET方式）：?username=zhangsan&password=123
String queryString = req.getQueryString();
System.out.println(queryString);
```

```java
 @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // String getMethod()：获取请求方式： GET
        String method = req.getMethod();
        System.out.println(method);
    
        // String getContextPath()：获取虚拟目录(项目访问路径)： /MavenWeb
    
        String contextPath = req.getContextPath();
        System.out.println(contextPath);
    
        // String getRequestURL(): 获取URL http://localhost:8080/MavenWeb/req
        StringBuffer requestURL = req.getRequestURL();
        System.out.println(requestURL);
        
        // String getRequestURI()：获取URI(统一资源标识符)： /MavenWeb/req
        String requestURI = req.getRequestURI();
        System.out.println(requestURI);
    
        // String getQueryString()：获取请求参数（GET方式）：?username=zhangsan&password=123
        String queryString = req.getQueryString();
        System.out.println(queryString);
    }
```

#### 请求头

```java
@WebServlet("/demo4")
public class demo4 extends HttpServlet {
    
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    
        String header = req.getHeader("user-agent");
    
        System.out.println(header);
    }
}
```



#### Request Post函数

```java
//  获取post 请求体：请求参数
//    1. 获取字符输入流
// 通过字符输入流读取数据给到response处理
BufferedReader reader = req.getReader();

String line = reader.readLine();

System.out.println(line);
```

#### Request通过请求方式请求参数

![iBHN1Q.png](https://i.328888.xyz/2023/04/12/iBHN1Q.png)

##### 1 req.getParameterMap()

```java
 Map<String, String[]> map = req.getParameterMap();
        
        for(String key : map.keySet()){
            System.out.print(key + ":");
    
            String[] values = map.get(key);
            
            for(String value : values){
                System.out.print(value + " ");
            }
    
            System.out.println();
        }
```

##### 2 req.getParameterValues

```java
String[] hobbies = req.getParameterValues("hobby");

    for (String hobby : hobbies) {
        System.out.println(hobby);
    }
```

##### 3 req.getParameter

```java
String username = req.getParameter("username");
String password = req.getParameter("password");
System.out.println(username);
System.out.println(password);
```

当请求时是post的形式

```java
// 调用 doPost请求的时候

this.doGet(req,resp);
```

#### IDEA模板创建Servlet

![iBHnbk.png](https://i.328888.xyz/2023/04/12/iBHnbk.png)

![iBHpUX.png](https://i.328888.xyz/2023/04/12/iBHpUX.png)

#### 请求参数乱码问题

##### 1 post请求

```java
 // post 请求解决乱码问题
 // 设置字符输入流的编码
 // 乱码原因是POST.getReader() 使用的是默认的是ISO-8859-1
 request.setCharacterEncoding("UTF-8");
```

##### 2 get请求

![iBHBRt.png](https://i.328888.xyz/2023/04/12/iBHBRt.png)

```java
// get 获取参数列表是
// getQueryString()
因为网页编码和Tomcat解码是不一致的，所以导致最终乱码
```

##### 	

出错问题

```java
package com.itheima.web;

import java.io.UnsupportedEncodingException;
import java.net.URLDecoder;
import java.net.URLEncoder;

/**
 * @ClassName URLDemo
 * @Description TODO
 * @Author lenovo
 * @Date 2022/8/20 14:45
 * @Version 1.0
 */

public class URLDemo {
    
    public static void main(String[] args) throws UnsupportedEncodingException {
        
        String username = "张三";
        
        //    url编码
    
        String encode = URLEncoder.encode(username, "utf-8");
        
        // ISO-8859-1 解码
    
        String decode = URLDecoder.decode(encode, "ISO-8859-1");
        
        // 获得字节数组
        
        byte[] bytes = decode.getBytes("ISO-8859-1");
    
        for (byte aByte : bytes) {
            System.out.print(aByte + " ");
        }
    
        System.out.println();
    
        System.out.println(encode);
    
        System.out.println(decode);
        
    //    将字节数组转换成字符串
    
        String s = new String(bytes, "utf-8");
    
        System.out.println(s);
    }
}
```

```java
//    解决get乱码问题 先编码后解码
    
 String newUsername = new String(username.getBytes(StandardCharsets.ISO_8859_1),StandardCharsets.UTF_8);                             
 System.out.println(newUsername);
```



#### request请求转发	

![iBHXSJ.png](https://i.328888.xyz/2023/04/12/iBHXSJ.png)

```java
System.out.println("demo5...");

// 进行转发操作
request.getRequestDispatcher("/RequsetDemo5").forward(request,response);
```

###### 1 设置转发对象

```java
// 设置 转发对象 在转发中 request中加入数据

request.setAttribute("msg","hello");
```

###### 2 接受转发的请求对象

```java
// 在接收对象中将接受的数据显示
Object msg = request.getAttribute("msg");

System.out.println(msg);
```

### Response

![iBH7jA.png](https://i.328888.xyz/2023/04/12/iBH7jA.png)

#### 重定向

方法一

```java
// 设置响应号码
response.setStatus(302);

// 设置响应头 Location
response.setHeader("Location","/MavenWeb/ResponseDemo2");
```

方法二

```java
// 设置重定向的建议步骤

response.sendRedirect("/MavenWeb/ResponseDemo2");
```

#### 加不加虚拟目录

**在服务器内部之间转换不需要加上虚拟目录**

**在网页之间转变需要加上虚拟目录**

#### Response响应字符&字节数据

```java
//    1 读取文件

FileInputStream fileInputStream = new FileInputStream("E:\\大学两年的比赛和项目\\MavenWeb\\src\\main\\java\\com\\itheima\\web\\response\\2.jpg");

//    2 获得字节输出流

ServletOutputStream os = response.getOutputStream();

//    3 完成copy

byte[] bytes = new byte[1024];

int len = 0;

while((len = fileInputStream.read(bytes))!= -1){
    os.write(bytes,0,len);

    System.out.println(bytes);

    System.out.println(len);
}

```

```java
while((len = fileInputStream.read(bytes))!= -1){
    os.write(bytes,0,len);

    System.out.println(bytes);

    System.out.println(len);
}

```



# Spring二刷

## **耦合度**

在书写代码的时候我们因该是追求低耦合的开发

![image-20220908104853573](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908104853573.png)



解决方法是 : **不要自己的主动去 new 一个对象** **而是 让外部提供一个对象  这种思想是 IoC**

![image-20220908105209883](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908105209883.png)

![image-20220908105251890](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908105251890.png)

**Ioc容器给他提供对象，负责给对象创建、初始化一系列操作，被创建或者被管理的对象成为Bean**



![image-20220908105320082](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908105320082.png)

![image-20220908105416908](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908105416908.png)

![image-20220908105813523](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908105813523.png)

![image-20220908105836479](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908105836479.png)

## 接口



1、**接口的出现是因为 java类之间的继承是单一的，为了打破这个局面，后来引出了接口这个概念，类是可以继承多个接口的**

2、**接口是不能创造对象的** 使用接口创建对象直接报错了

![image-20220908111650294](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908111650294.png)

## DI实现 和 为什么需要这个

创建对象的时候使用他的实现类

```java
private Service service = new ServiceImpl();
```

这个实现类的内部

```java
public class ServiceImpl implements Service {
    
    // private BookDao bookDao = new BookDaoImpl();
    
    private BookDao bookDao = new BookDaoImpl();
    
    public void save() {
        bookDao.save();
        System.out.println("service is running!!!");    
    }
}
```

在这个是实现类的内部 使用了另外一个接口 使用 对应的实现类 创建一个 对象

```java
   private BookDao bookDao = new BookDaoImpl();
```

**这么一来 这个耦合度就比较大**

![image-20220908114723963](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908114723963.png)

**删除new 的方法之后**

**加入bean的管理**

![image-20220908201033480](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908201033480.png)

![image-20220908201200847](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908201200847.png)

**这个setter方法是 IoC容器去调用的** 

**这个也就是DI 实现**



## Bean造对象

### 1 直接使用Bean造对象

**直接通过获取bean的形式 获取一个接口 在 xml 文件中将这个接口 配置成bean的形式**

```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationcontext.xml");

//方式一

BookDao bookDao = (BookDao) ctx.getBean("BookDao");

bookDao.save();
```

```xml
<bean id="BookDao" class="com.itheima.dao.impl.BookDaoImpl">

</bean>
```

### 2 使用工厂造bean

![image-20220908210516744](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908210516744.png)

```java
OrderDao orderDao = OrderDaoFactory.getOrderDao();

orderDao.save();
```

修改成bean的形式



```java
ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationcontext.xml");

// 方式二

OrderDao orderDao = (OrderDao) ctx.getBean("OrderDao");

orderDao.save();
```

```xml
<bean id="OrderDao" class="com.itheima.Factory.OrderDaoFactory" factory-method="getOrderDao"></bean>
```

**在配置的时候 加以说明 工厂造对象的方法是 哪一个**



**补充**：

区别：

* 1、静态方法是使用static关键字修饰的方法，属于类的，不属于对象；非静态方法是不使用static关键字修饰的普通方法，属于对象，不属于类。

* **2、静态方法可以直接调用，类名调用和对象调用；非静态方法只能通过对象调用。**

* 3、生命周期不同。

### 3使用 实例化对象造对象

```java
ServiceFactory serviceFactory = new ServiceFactory();

OrderDao orderDao = serviceFactory.getOrderdao();
```

```java
public class ServiceFactory {
    
    public OrderDao getOrderdao(){
        
        return new OrderDaoImpl();
        
    }
    
}
```

![image-20220908212941519](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908212941519.png)

**改成bean的形式**

![image-20220908213227034](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908213227034.png)

### 4 使用FactoryBean简化开发

```java
package com.itheima.Factory;

import com.itheima.dao.OrderDao;
import com.itheima.dao.impl.OrderDaoImpl;
import org.springframework.beans.factory.FactoryBean;
import org.springframework.core.annotation.Order;

/**
 * @ClassName UserDaoFactoryBean
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/8 21:36
 * @Version 1.0
 */

public class UserDaoFactoryBean implements FactoryBean<OrderDao> {
   
    public OrderDao getObject() throws Exception {
        return new OrderDaoImpl();
    }
    
    public Class<?> getObjectType() {
        
        return OrderDao.class;
        
    }
}
```

```xml
<bean id="orderdao" class="com.itheima.Factory.UserDaoFactoryBean"></bean>
```

**使用FactoryBean 对象 简化很多**



## Setter注入

**第一次看你这个东西的时候 不是很清楚这里面的 setter的方法是怎么被调用的**

**第二次看的时候就是到原来执行的都是 容器进行执行**

代码 分为以下三部分

* 测试类的书写

```java
package com.itheima;

import com.itheima.dao.BookDao;
import javafx.scene.transform.Scale;
import org.junit.Test;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.Bean;
import org.springframework.context.support.ClassPathXmlApplicationContext;

/**
 * @ClassName testbean
 * @Description TODO
 * @Author Typecoh
 * @Date 2022/9/8 21:51
 * @Version 1.0
 */

public class testbean {
    
    @Test
    public void test01(){
    
        ApplicationContext ctx = new ClassPathXmlApplicationContext("applicationcontext.xml");
    
        BookDao bookDao = (BookDao) ctx.getBean("BookDao");
        
        bookDao.save();
    
    
    }
    
}
```

* 接口类的实现

  ```java
  package com.itheima.dao.impl;
  
  import com.itheima.dao.BookDao;
  import com.itheima.dao.OrderDao;
  import org.springframework.core.annotation.Order;
  
  import java.security.PrivateKey;
  
  /**
   * @ClassName BookDaoImpl
   * @Description TODO
   * @Author Typecoh
   * @Date 2022/9/8 10:01
   * @Version 1.0
   */
  
  public class BookDaoImpl implements BookDao {
      
      private Integer id;
      
      public void setId(Integer id) {
          this.id = id;
      }
      
      public void setDatabase(String database) {
          this.database = database;
      }
      
      private String database;
      
      public void save() {
          
          System.out.println("save is running!!!");
          System.out.println("id == >" + id + " database ==> " + database );
          
      }
      
  }
  ```

* bean配置 文件的书写 xml

  ```xml
  <bean id="BookDao" class="com.itheima.dao.impl.BookDaoImpl">
      <property name="id" value="10"></property>
      <property name="database" value="mysql"></property>
   </bean>
  ```

  ![image-20220908215958134](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220908215958134.png)



## 自动装配

<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909102007043.png" alt="image-20220909102007043" style="zoom:80%;" />



## 容器

### 1 创建容器

![image-20220909111355598](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909111355598.png)

### 2 获取bean形式

![image-20220909111518241](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909111518241.png)

### 3 使用 FactoryBean

![image-20220909111756033](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909111756033.png)



## 注解开发

**一开始使用注解进行开发文件**

**一步一步进行替换**



![image-20220909145101311](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909145101311.png)



**后来啊 java开发最喜欢的就是使用配置类来代替一些东西**

**于是产生了配置类 代替 xml 文件**



![image-20220909150134073](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909150134073.png)

![image-20220909150300584](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909150300584.png)





**正式使用Autowired**

![image-20220909151846259](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909151846259.png)

![image-20220909152538882](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909152538882.png)

![image-20220909152729325](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909152729325.png)

**使用value注解 或者就比较简单就不在这里说明**

## 第三方注解@Bean

![image-20220909161153568](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909161153568.png)



使自己创建的这个Bean生效的话 也就是 需要导入 到 SpringCongfig类中去

![image-20220909161311579](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909161311579.png)

**当bean需要 文件或者 类型时 直接使用形参传入格式**

![image-20220909165326995](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909165326995.png)

**流程是**



<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909165955091.png" alt="image-20220909165955091" style="zoom:80%;" />



## Jdbc 和 Mybatis和DataSource

### Jdbc

对于传统用法，使用数据驱动进行连接

<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20231009084925908.png" alt="image-20231009084925908" style="zoom:67%;" />

```java
String url = "jdbc:mysql://localhost:3306/databases_01";
String username = "root";
String password = "admin123";

Connection coon = DriverManager.getConnection(url,username,password);
```

由于连接繁琐，并且对象不是很好用，因此提出使用数据池进行连接

### DataSource

使用**第三方Bean**对数据源进行封装，当其他对象需要使用时，可以直接使用**注入**的方式使用

```java
@Bean
public DataSource dataSource(){

    DruidDataSource ds = new DruidDataSource();
    ds.setDriverClassName(driver);
    ds.setUrl(url);
    ds.setUsername(username);
    ds.setPassword(password);
    return ds;
}
```

Jdbc的创建 是 比较简单的 在 Spring文件当中有详细的介绍 在这里就不过多 阐述了

但是 为什么又有Mybatis 这个玩意出现呢 



![image-20220909211020022](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909211020022.png)

**为了简化上面的情况 产生了 mybatis技术**

![image-20220909211316850](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909211316850.png)

```java
<mapper namespace="test">
    
    <select id="selectAll" resultType="com.itheima.domain.Book">
        select * from book
    </select>
    
</mapper>
```



```java
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mybatis"/>
                <property name="username" value="root"/>
                <property name="password" value="admin123"/>
            </dataSource>
        </environment>
    </environments>
    <mappers>
        <mapper resource="BookMapper.xml"/>
    </mappers>
</configuration>
```

```java
@Test
public void test02() throws Exception {
    
    String resource = "SqlMapConfig.xml" ;

    InputStream inputStream = Resources.getResourceAsStream(resource);

    SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);

    SqlSession sqlSession = sqlSessionFactory.openSession();

    List<Book> list = sqlSession.selectList("test.selectAll");

    System.out.println(list);
    
    sqlSession.close();

}
```

<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909215100748.png" alt="image-20220909215100748" style="zoom:80%;" />

**为了解决这个办法于是采用Mapper代理的方式去处理**

![image-20220909215735162](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909215735162.png)

![image-20220909220058441](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909220058441.png)

<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220909220103914.png" alt="image-20220909220103914" style="zoom: 150%;" />







**当自己创建一个实体类，如果没有继承父类就会自动继承基类 当继承了父类 就不会继承基类**

[MyBatis 源码分析（二）：反射模块 - 简书 (get方法)](https://www.jianshu.com/p/baba62bbc107)

**在 Mybatis 中调用 数据库 中执行 Mapper中的 SQL 语句是 在 Mybatis 中使用 将数据库中的 字段名去匹配**

**setXxx 通过反射将类中的变量命名的set方法和数据库 的字段进行对应，如果说 类的变量名和数据库的字段**

**如果一致就去调取这个set的方法，否则就不去调用**





**反射是可以去拿到 类的函数 类名 等等东西，发生在比如反射就是发生在 java 运行时完成的。 在类加载阶段，虚拟机会在 java 堆中生成一个代表这个类的 java.lang.Class 对象，通过这个 Class 对象就可以访问到 jvm 中的这个类。 Class 类与 class 是不同，Class 是实实在在存在于 java.lang.Class 包中的一个类。**



<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910124532259.png" alt="image-20220910124532259" style="zoom:80%;" />



**所以为了解决这个问题**

**我们引入<resultMap> 这个标签解决**

```java
<resultMap id="UserMapper" type="User">
    
    <result column="brand_name" property="BrandName"/>
    <result column="company_name" property="CompanyName"/>
    <result column="ordered" property="ordered"/>
    <result column="description" property="description"/>
    <result column="status" property="status"/>
</resultMap>

<select id="selectAll" resultMap="UserMapper">
    select * from tb_brand
</select>
```

![image-20220910132056694](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910132056694.png)



**之后一个点就是说 使用注解 简化这个开发**

```java
@Select("select * from tb_brand where status = #{statis}")
User selectById(Integer status);
```



## Spring整合Mybatis

![image-20220910145825290](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910145825290.png)

[具体代码](E:\大学两年的比赛和项目\Spring\复习\SpringMybatis)

整合 前后差异

![image-20220910150552869](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910150552869.png)

## JUnit整合

**首先说说这个JUnit这个玩意的用处是什么，为啥总有一些稀奇古怪的东西**

**在测试类中**

**正常去写一个测试数据层代码的时候**

```java
ApplicationContext ctx = new AnnotationConfigApplicationContext(SpringConfig.class);

BookDao bean = ctx.getBean(BookDao.class);

List<Book> list = bean.SelectAll();

System.out.println(list);
```

**你总要去创建一个容器吧，要是连容器都没有那还玩个啥**

**但是，只是去测一个类的接口，你就让我写一个容器，完了之后还要拿bean，那这个就有点过分了**

**于是 JUnit说我来帮你解决这个问题**

**于是**

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(classes = SpringConfig.class)
```

```java
@Test

public void test03(){

    Book book = service.SelectById(1);

    System.out.println(book);

}
```

**至此就简单方便的执行了**



## AOP

**先来聊聊这个玩意主要是来干什么的，AOP这个好家伙是用来做增强的作用，它可以在原来的基础上不做处理但是能加入一些别的东西进去做增强**

**那具体是怎么实现的呢**？ 先来说说AOP中的几个概念

* **连接点:zipper_mouth_face:：连接点说白了就是一个函数的名称，这个函数就是想来做AOP的那个函数**
* **切入点：但是也不是所有的方法都需要做增强，需要做增强的那些方法被称为切入点**
* **通知：也就是说这个通知是个函数，这个函数是需要增强的内容**

* **通知类：封装这个通知的类被称为通知类**
* **切面：通知和切入点之间的关系描述，我们给起了个名字叫切面**

![image-20220910160025329](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910160025329.png)



**制作AOP入门案例图示结构**

<img src="C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910162211279.png" alt="image-20220910162211279" style="zoom:67%;" />



**AOP还有其他很多东西，在这里就不一一说明了 在Spring中是有说明的**

## SpringMvc

**SpringMvc这个东西就是在 Servlet技术上进行做了增强，简便操作**

先介绍两个东西

* **@Responsebody 注解一般是跟在 @RequestMapping之后，使用了@RequestMapping注解往往返回值是跳转页面；比如jsp形式，但是加上了@Responsebody 之后往往是将正文给显示出来**
* **@Requestbody 这个注解一般是跟在形参的前面，修饰形参，说明形参的那个格式 是以什么形式表示；比如 json，xml**

![image-20220910173326612](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910173326612.png)

![image-20220910173403881](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910173403881.png)



**到此SpringMvc到此时就先到一段了，在SpringMvc中其实有很多相关的知识点，这些知识点是可以结合代码+Spring开发文档+pdf进行学习**



## SSM整合

**ssm整合技术：是将Sping Mybatis SpringMvc三者整合到一起去**

先说一个熟悉的知识点

**在使用SSM技术时 统一在这个地方加上前缀 classpath 否则找不到文件**

```java
@PropertySource("classpath:Jdbc.properties")
```



简单回顾以下整合SSM的步骤

![image-20220910210738360](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910210738360.png)

**具体文件代码**

[代码位置](E:\大学两年的比赛和项目\Spring\复习\ssm)

**到此 ssm整合就结束了，同时呢一些细节东西就不过多阐述，只是将流程过了一遍**

**对于简易开发比如说 rest风格等等这一方面就结合Spring文档查看就行**



## SpringBoot

**在整合Junit中主要涉及 test包和SpringBoot自动生成的加载类的位置是否一致**

![image-20220910221155780](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220910221155780.png)

**在整合Mybatis中看看SpringBoot 和 原来之间的区别**

![image-20220911094443355](C:\Users\lenovo\AppData\Roaming\Typora\typora-user-images\image-20220911094443355.png)



# Cookie&&Session

<img src="https://i.328888.xyz/2023/04/12/iBHGLb.png" alt="iBHGLb.png" style="zoom:67%;" />

## Cookie介绍

* Cookie是在服务器所产生
* Cookie是存储客户端里的
* Cookie存储数据的形式是键值对和字典

## Cookie的分类

* 会话层：当浏览器关闭就会消失
* 持久层：最有当失效时间达到才会失效（同时用的是这一种）

## 查看自己浏览器上的Cookie

<img src="https://i.328888.xyz/2023/04/12/iBHhUq.png" alt="iBHhUq.png" style="zoom:50%;" />



## Cookie基本使用

### 对于服务端（后端人员）

解决的问题是

* 发送Cookie(这件事情有ASevlet完成)

  ```java
  @Override
  protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
          
      // 创建一个Cookie
  
      Cookie cookie = new Cookie("username", "zhangsan");
  
      // 将 cookie 加入 到服务器中
  
      resp.addCookie(cookie);
  }
  ```

  

* 获取Cookie发送(这件事情有BSevlet完成)

  ```java
  @Override
  protected void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
  
      System.out.println("启动了BServlet");
      // 获取所有的Cookie
  
      Cookie[] cookies = request.getCookies();
  
      // 从所有的 cookies中拿到我们想要的cookie
  
      for(Cookie cookie : cookies){
  
          String name = cookie.getName();
  
          if(name.equals("username")) {
              System.out.println("拿到了Cookie：" + name + " " + cookie.getValue());
              break;
          }
      }
  }
  ```

  **客户向A服务器第一次发送请求，A服务器生成一个Cookie，并且向客户发送这个Cookie,这个Cookie存储在客户中**

  **等待客服第二次向B服务器进行访问，如果Cookie相同就放行，否则认为是越权访问**，在SpringBoot中可以加在拦截器中进行拦截

  给用户做权限限制

## Cookie实现原理

### Cookie实现是基于Http协议

* 响应头：set-cookie
* 请求头：cookie

![iBHsVa.png](https://i.328888.xyz/2023/04/12/iBHsVa.png)

页面向服务器发送请求（request） 服务器给页面进行回复（response）

当客户存在cookie的时候，在请求的时候会带上自己的cookie对服务器发送请求，因此服务器可以查询到客户所有的cookie

**响应1过程**

![iBHvjx.png](https://i.328888.xyz/2023/04/12/iBHvjx.png)

**请求2的过程**

![iBH9Fk.png](https://i.328888.xyz/2023/04/12/iBH9Fk.png)

## Cookie的使用细节

### Cookie存活时间

**默认情况下，Cookie存储在内存中，当关闭浏览器这个Cookie就会被撤销**

```java
// 设置存活时间 为一周
cookie.setMaxAge(60*60*24*7); // 单位为S
```

## cookie存储中文

```java
// 使用URLEncoder进行编码
String name = URLEncoder.encode("张三","UTF-8");

 // 使用 URL解码
 System.out.println("拿到了Cookie：" + name + " " + URLDecoder.decode( cookie.getValue(),"UTF-8"));
```

## Session使用

<img src="https://i.328888.xyz/2023/04/12/iBHJQL.png" alt="iBHJQL.png" style="zoom:50%;" />

**Session的作用就是为了避免信息在客户和服务器之间传输可能导致信息泄露问题，所以在服务器中产生Session，服务器公用Session**

**服务器A将数据存入到Session，服务器B将信息从Session中取出来，进行验证**

服务器A代码

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 客服访问服务器A
    // 创建一个Session
    HttpSession session = req.getSession();

    // 存储数据
    session.setAttribute("Username","李四");
}
```

服务器B代码

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException{
   
    // 创建完了Session 之后 页面访问 服务器
    // 拿到Session 中的数据
    HttpSession session = req.getSession();
    
    // 从Session 中 获取数据
    Object username = session.getAttribute("Username");
    
    System.out.println("Username" + " " + username);
}
```

## Session原理

<img src="https://i.328888.xyz/2023/04/12/iBHMxU.png" alt="iBHMxU.png" style="zoom:67%;" />

**同一个浏览器创建的和使用的是同一个Session**，

**不同的浏览器使用的Session不是同一个**

服务器A向页面发送一个**set-cookie.JSESSIONID**,如果页面请求服务器的时候携带的**set-cookie.JSESSIONID**也是哪一个，于是使用同一个Session对象



### Session钝化和活化

![iBHQo5.png](https://i.328888.xyz/2023/04/12/iBHQo5.png)

关闭服务器之后，数据不会丢失，但是当同一个浏览器关闭之后，再访问服务器产生的Session就不是同一个

<img src="https://i.328888.xyz/2023/04/12/iBHqf8.png" alt="iBHqf8.png" style="zoom:67%;" />

# Token&&JWT

Token简称为令牌

<img src="https://i.328888.xyz/2023/04/12/iBHeQN.png" alt="iBHeQN.png" style="zoom: 33%;" />

## 工作流程

* 用户登入，向后端发送请求，后端产生Token令牌
* 后端给前端用户响应，将生成的令牌发送给用户
* 后端将生成的令牌存入到Redis数据库中
* 用户将Token存入到localStore中进行保存
* 当其他用户需求发送请求时，会携带一个令牌（Token）
* 后端向Redis中取出令牌和用户携带过来的令牌进行对比，如果一致就成功显示，否则返回登入界面

![iBH6lV.png](https://i.328888.xyz/2023/04/12/iBH6lV.png)

## 添加拦截器

除了登入请求以外所有的都需要做统一的拦截请求

```java
@Configuration
public class SpringMvcSupport extends WebMvcConfigurationSupport {
    
    @Autowired
    private ProjectInterceptor projectInterceptor;
    
    // 拦截除了登入界面以外的所有的请求
    protected void addInterceptors(InterceptorRegistry registry){
        
        registry.addInterceptor(projectInterceptor)
                .addPathPatterns("/**")
                // 对登入操作进行放行
                .excludePathPatterns("/login");
    }
}
```

## 实现拦截器



## Token工具类

主要是将登入生成的Token和登入信息加入

将获得的信息进行解析

```java
public String getToken(String userId, String userRole){

    System.out.println(System.currentTimeMillis());

    String token = JWT.create()
        .withClaim("userId",userId)
        .withClaim("userRole",userRole)
        .withClaim("timeStamp",System.currentTimeMillis())
        .sign(Algorithm.HMAC256(privateKey));

    return token;
}
```

```java
public Map<String,String> parseToken(String token){

    Map<String, String> map = new HashMap<>();
    DecodedJWT decodedjwt;
    try{
        // decodedjwt = JWT.require(Algorithm.HMAC256(privateKey)).build().verify(token);
        //
        // Claim userId = decodedjwt.getClaim("userId");
        // Claim userRole = decodedjwt.getClaim("userRole");
        // Claim timeStamp = decodedjwt.getClaim("timeStamp");
        //
        // map.put("userId",userId.asString());
        // map.put("userRole",userRole.asString());
        // map.put("timeStamp",timeStamp.asLong().toString());

    }catch (Exception e){
        System.out.println(e);
    }finally {

    }

    return map;
}
```

## 登入和测试

```java
@RestController
public class TokenTestController {
    
    @Autowired
    private TokenUtils tokenUtils;
    
    @PostMapping("/login")
    public String login(){
        
        return tokenUtils.getToken("靓仔","admin");
    }
    
    @PostMapping("/test-token")
    public Map testToken(HttpServletRequest request){
        
        String token = request.getHeader("token");

        System.out.println(token);

        return tokenUtils.parseToken(token);
    }
}

```

## 实现拦截器功能

```java
package com.example.controller.interceptor;

import com.example.Utils.TokenUtils;
import com.sun.javaws.IconUtil;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.HashMap;
import java.util.Map;

/**
 * @ClassName ProjectInterceptor
 * @Description TODO
 * @Author Typecoh
 * @Date 2023/3/2 23:43
 * @Version 1.0
 */

@Component
public class ProjectInterceptor implements HandlerInterceptor {
    
    @Autowired
    TokenUtils tokenUtils;
    
    @Value("${token.privateKey}")
    private String privateKey;
    @Value("${token.yangToken}")
    private Long yangToken;
    @Value("${token.oldToken}")
    private Long oldToken;
    
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
       
        System.out.println("preHandle");
    
        // 获取 header
        String token = request.getHeader("token");
    
        if(token == null) {
            System.out.println("token为空");
            return false;
        }
    
        Map<String,String> map = tokenUtils.parseToken(token);
        
        String userId = map.get("userId");
        String userRole = map.get("userRole");
    
        long timeStamp = 0;
        
        if(!map.isEmpty()){
            timeStamp = System.currentTimeMillis() - Long.parseLong(map.get("timeStamp"));
        }
        
        // 判断 Token 是否过期
        if(timeStamp < yangToken){
            System.out.println("年轻 token");
        }

        // 刷新 token
        else if(timeStamp > yangToken && timeStamp < oldToken){
            response.setHeader("token",tokenUtils.getToken(userId,userRole));
        }
        else {
            System.out.println("过期");
        }

        if("user".equals(userRole)){
            System.out.println(userRole + "账号");
            return true;
        }

        if("admin".equals(userRole)){
            System.out.println(userRole + "账号");
            return true;
        }
        return false;
    }
    
    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
    
        System.out.println("postHandle...");
    }
    
    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion...");
    }
}

```

简单的拦截器 拦截功能实现

在拦截器内部可以实现用户信息检测，实现权限访问控制



# SpringBoot整合Excel

导入依赖

```xml
<!--导入excel-->
<dependency>
    <groupId>com.alibaba</groupId>
	<artifactId>easyexcel</artifactId>
</dependency>
```





# 端口指令

查看端口的PID

```c
netstat -ano|findstr 69
```

查看对应PID的进程

```c
tasklist|findstr 5678
```

关闭某个端口

```c
taskkill /pid 10564 -t -f
```

