# Spring

## 目录

* [1. 什么是 Spring?](#1-----spring-)
  + [1.1 Spring 核心](#11-spring---)
  + [1.2 Spring 发展历程](#12-spring-----)
  + [1.3 Spring 优势](#13-spring---)
  + [1.4 Spring 体系结构](#14-spring-----)
* [2. 程序的耦合和解耦](#2---------)
  + [2.1 问题回顾](#21-----)
  + [2.2 工厂模式解耦](#22-------)
* [3. IOC](#3-ioc)
  + [3.1 什么是 IOC？](#31-----ioc-)
  + [3.2 Spring 中的 IOC](#32-spring----ioc)
  + [3.3 Spring 基于 XML 的 IOC 环境搭建](#33-spring----xml---ioc-----)
* [4. 依赖注入](#4-----)



## 1. 什么是 Spring?

Spring 框架是一个开源的 [J2EE](https://baike.baidu.com/item/J2EE/110838) 应用程序框架，针对bean的生命周期进行管理的轻量级容器



<div align="center"> <img src="image-20200513102726539.png" width="100%"/> </div><br>


### 1.1 Spring 核心

1. `IOC` 控制反转
2. `AOP` 面向切片

### 1.2 Spring 优势

1. 方便解耦，简化开发

   通过 Spring 提供的 `IoC` 容器，我们可以将对象之间的依赖关系交由 Spring 进行控制，避免硬编码所造成的过度程序耦合。有了 Spring，用户不必再为单实例模式类、属性文件解析等这些很底层的需求编写代码，可以更专注于上层的应用。

2. `AOP` 编程支持

   通过Spring提供的[AOP](https://baike.baidu.com/item/AOP)功能，方便进行面向切面的编程，许多不容易用传统OOP实现的功能可以通过AOP轻松应付。

3. 声明式事务支持

   在 Spring 中，我们可以从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活地进行事务的管理，提高开发效率和质量。

4. 方便程序测试

   Spring 对 `Junit4` 支持，可以通过注解方便的测试 Spring 程序。

5. 方便集成各种优秀框架

   Spring 不排斥各种优秀的开源框架，相反，Spring 可以降低各种框架的使用难度，Spring 提供了对各种优秀框架等的直接支持。

6. Java 源码经典学习范例

   Spring 的源码设计精妙、结构清晰、匠心独运，处处体现着大师对 [Java设计模式](https://baike.baidu.com/item/Java设计模式)灵活运用以及对 Java 技术的高深造诣。Spring 框架源码无疑是 Java 技术的最佳实践范例。如果想在短时间内迅速提高自己的 Java 技术水平和应用开发水平，学习和研究 Spring 源码将会使你收到意想不到的效果。



### 1.3 Spring 体系结构

 





## 2. 程序的耦合和解耦

### 2.1 什么是耦合

耦合指的是程序之间的依赖关系，分为：

1. 类之间依赖
2. 函数方法依赖



### 2.2 什么是解耦

降低程序的耦合性，使得 **编译期不依赖，运行时才依赖**

方法：

1. 使用反射创建对象，避免使用 `new` 
2. 读取配置文件获取创建对象全限定类命



### 2.3 问题回顾

在之前我们保存用户信息都是这么做的：

**View.java**

```java
package com.ceezyyy.view;

import com.ceezyyy.service.UserService;
import com.ceezyyy.service.impl.UserServiceImpl;

public class View {
    public static void main(String[] args) {
        UserService userService = new UserServiceImpl();
        userService.save();
    }
}

```



**UserService.java**

```java
package com.ceezyyy.service;

public interface UserService {
    void save();
}

```



**UserServiceImpl.java**

```java
package com.ceezyyy.service.impl;

import com.ceezyyy.dao.UserDao;
import com.ceezyyy.dao.impl.UserDaoImpl;
import com.ceezyyy.service.UserService;

public class UserServiceImpl implements UserService {
    private UserDao userDao = new UserDaoImpl();

    public void save() {
        userDao.save();
    }
}

```



这样程序依赖很强，当我们移除了 `userDaoImpl.java` ，程序在编译期间就无法通过

 

### 2.4 工厂模式解耦

**预备知识：**

`Bean`：可重用组件

`Java Bean`：用 Java 编写的可重用组件（`Java Bean` 的范围要大于实体类）



**bean.properties**

```properties
UserDaoImpl=com.ceezyyy.dao.impl.UserDaoImpl
UserServiceImpl=com.ceezyyy.service.impl.UserServiceImpl
```

`bean` 配置文件：

`id`=`全限定类名`

根据 `id` 找到响应的类



**BeanFactory.java**

```java
public class BeanFactory {

    private static Properties properties;

    static {
        try {
            properties = new Properties();
            InputStream inputStream = BeanFactory.class.getClassLoader().getResourceAsStream("bean.properties");
            properties.load(inputStream);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    // get bean
    public static Object getBean(String beanName) {
        Object bean = null;
        try {
            String beanPath = properties.getProperty(beanName);
            bean = Class.forName(beanPath).newInstance();
        } catch (InstantiationException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        }
        return bean;
    }
}

```



**View.java**

```java
public class View {
    public static void main(String[] args) {
//        UserService userService = new UserServiceImpl();
        UserService userService = (UserService) BeanFactory.getBean("UserServiceImpl");
        userService.save();
    }
}

```



**UserServiceImpl.java**

```java
public class UserServiceImpl implements UserService {
//    private UserDao userDao = new UserDaoImpl();

    private UserDao userDao = (UserDao) BeanFactory.getBean("UserDaoImpl");
    public void save() {
        userDao.save();
    }
}

```



:heavy_check_mark:Succeeded!

<div align="center"> <img src="image-20200514142934472.png" width="100%"/> </div><br>


**:bulb:TIPS**

1. 使用加载配置文件利用反射来创建实体类对象，减少类之间的强耦合关系

   



### 2.5 工厂模式解耦 Pro



**LET'S GO MORE IN DEPTH**



**View.java**

```java
    public static void main(String[] args) {
//        UserService userService = new UserServiceImpl();
        for (int i = 0; i < 10; i++) {
            UserService userService = (UserService) BeanFactory.getBean("UserServiceImpl");
            System.out.println(userService);
//            userService.save();
        }
    }
```



我们这样的写法存在一个问题：`bean` 是多例的



**:bulb:什么是单例和多例？**

**单例：**只有一个共享的实例存在，所有对这个 `bean` 的请求都会返回这个唯一的实例。不管 `new` 多少次，只生成一个对象。

**多例：**每次请求都会创建一个新的对象，类似于 `new`



**:bulb:为什么使用单例/多例？**


<div align="center"> <img src="image-20200514144623179.png" width="80%"/> </div><br>

<div align="center"> <img src="image-20200514143504505.png" width="80%"/> </div><br>



**工厂模式解耦 Pro**

下面进行升级，使得每次调用 `getBean` 所获得的对象都是单例的（`service` 和 `dao` 的属性没有频繁改变的需求，显然单例更合适，减少资源消耗）

 **BeanFactory.java**

```java
public class BeanFactory {

    private static Properties properties;
    // store id-object
    private static Map<String, Object> map = new HashMap<String, Object>();

    static {
        try {
            properties = new Properties();
            // load properties
            InputStream inputStream = BeanFactory.class.getClassLoader().getResourceAsStream("bean.properties");
            properties.load(inputStream);

            // get keys from properties
            Enumeration<Object> keys = properties.keys();
            while (keys.hasMoreElements()) {
                String id = String.valueOf(keys.nextElement());
                // get beanPath by key
                String beanPath = properties.getProperty(id);
                // create a object by reflection
                Object value = Class.forName(beanPath).newInstance();
                // put id-value into our map
                map.put(id, value);
            }
        } catch (IOException | ClassNotFoundException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        } catch (InstantiationException e) {
            e.printStackTrace();
        }
    }

    public static Object getBean(String beanName) {
        Object value = null;
        try {
            value = map.get(beanName);
        } catch (Exception e) {
            e.printStackTrace();
        }
        return value;
    }

}

```

**:bulb:注意：**

1. `map` 是我们用来存储 `id` 和 `bean` 的容器，这样可以确保 `bean` 是单例



这样我们获得的 `bean` 对象就是单例的

<div align="center"> <img src="image-20200514153500054.png" width="70%"/> </div><br>




## 3. IOC

### 3.1 什么是 IOC？

<div align="center"> <img src="ioc.png" width="60%"/> </div><br>



`IOC` 是 `inverse of control` 的缩写，控制反转

<div align="center"> <img src="image-20200514204134781.png" width="60%"/> </div><br>

以前在 `new` 实体类的时候，选择权在我们自己手上，而现在选择权交给了工厂，由工厂帮我们决定新建的对象，称控制反转



### 3.2 Spring 中的 IOC

**Spring 体系结构**

<div align="center"> <img src="arch1.png" width="50%"/> </div><br>




### 3.3 Spring 基于 XML 的 IOC 环境搭建

1. 引入 `maven` 坐标

2. 定义 `bean.xml` （此时我们不用自己创建 `beanFactory`，交给 `Spring` 帮我们实现）

   

   **bean.xml**

   ```xml
   <?xml version="1.0" encoding="UTF-8"?>
   <beans xmlns="http://www.springframework.org/schema/beans"
          xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
          xsi:schemaLocation="http://www.springframework.org/schema/beans
           https://www.springframework.org/schema/beans/spring-beans.xsd">
   
       <bean id="UserServiceImpl" class="com.ceezyyy.service.impl.UserServiceImpl">
           <!-- collaborators and configuration for this bean go here -->
       </bean>
   
       <bean id="UserDaoImpl" class="com.ceezyyy.dao.impl.UserDaoImpl">
           <!-- collaborators and configuration for this bean go here -->
       </bean>
   
       <!-- more bean definitions go here -->
   
   </beans>
   ```

   

3. 利用 `ApplicationContext` 读取 `bean.xml` 中的内容，创建具体对象的事就交给 `Spring` 来完成

   

   **View.java**

   ```java
   public class View {
       public static void main(String[] args) {
           ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean.xml");
           UserService userServiceImpl = applicationContext.getBean("UserServiceImpl", UserServiceImpl.class);
           UserDao userDaoImpl = applicationContext.getBean("UserDaoImpl", UserDaoImpl.class);
   
           // result
           System.out.println(userServiceImpl);
           System.out.println(userDaoImpl);
       }
   }
   ```

   

   :heavy_check_mark:Succeeded!

   <div align="center"> <img src="image-20200514210633613.png" width="70%"/> </div><br>



### 3.4 BeanFactory 接口与 ApplicationContext 的区别

![image-20200514211910676](image-20200514211910676.png)



`BeanFactory`：延迟加载，适用于多例

`ApplicationContext`：立即加载，适用于单例





### 3.5 Spring 对 Bean 的管理细节

#### 3.5.1 创建 bean 的三种方式

1. 指定 `id` 和 `class` 属性

   ```xml
   <bean id="UserServiceImpl" class="com.ceezyyy.service.impl.UserServiceImpl">
           <!-- collaborators and configuration for this bean go here -->
   </bean>
   ```

   `id`：唯一标识

   `class`：实现类全限定类名

   若没有指定方法，则是默认选择实现类的（默认）`constructor`，若修改该 `constructor`，则会报错：

<div align="center"> <img src="image-20200514223717734.png" width="80%"/> </div><br>

2. 









#### 3.5.2 bean 对象的作用范围













#### 3.5.3 bean 对象的生命周期








## 4. 依赖注入






