<!--
 * @Author: your name
 * @Date: 2020-06-10 22:34:54
 * @LastEditTime: 2020-06-11 16:14:48
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \GitHubnotebook\面试题笔记\spring.md
--> 

## bean的作用域

默认情况下，spring只为每个在IOC容器里声明的bean创建唯一一个实例，整个IOC容器范围内都能共享该实例：所有后续的getBean()调用和bean引用都将返回这个唯一的bean实例。该作用域被称为singleton，是所有Bean的默认作用域

|类别|说明|
|--|--|
|singleton|在SpringIOC容器中仅存在一bean实例，Bean以单实例的方式存在|
|prototype|IOC容器不在事先实例化该Bean,每次调用getBean()都会返回一个新的实例|
|request|每次HTTP请求都会常见一个新的Bean.该作用域仅适合用于WebApplicationContex环境|
|session|同一个HTTP Session共享一个Bean,不同HTTP Session使用不同的Bean.该作用域仅适合用于WebApplicationContex环境|

## 事务的传播行为

事务具有ACID特性。是指作为单个逻辑工作单元执行的一系列操作，要么完全地执行，要么完全地不执行。

当事务方法被另外一个事务方法调用时，必须指定事务该如何传播

注解@Transactional

|传播属性|描述|
|--|--|
|REQUIRED|默认的spring事务传播级别，使用该级别的特点是，如果上下文中已经存在事务，那么就加入到事务中执行，如果当前上下文中不存在事务，则新建事务执行。所以这个级别通常能满足处理大多数的业务场景。|
|REQUIRES_NEW|每次都会新建一个事务，并且同时将上下文中的事务挂起，执行当前新建事务完成以后，上下文事务恢复再执行。|
|SUPPORTS|如果上下文存在事务，则支持事务加入事务，如果没有事务，则使用非事务的方式执行。所以说，并非所有的包在transactionTemplate.execute中的代码都会有事务支持。这个通常是用来处理那些并非原子性的非核心业务逻辑操作。应用场景较少|
|NOT_SUPPORTED|上下文中存在事务，则挂起事务，执行当前逻辑，结束后恢复上下文的事务。|
|MANDATORY|该级别的事务要求上下文中必须要存在事务，否则就会抛出异常！配置该方式的传播级别是有效的控制上下文调用代码遗漏添加事务控制的保证手段。比如一段代码不能单独被调用执行，但是一旦被调用，就必须有事务包含的情况，就可以使用这个传播级别|
|NEVER|上下文中不能存在事务，一旦有事务，就抛出runtime异常，强制停止执行！|
|NESTED|如果上下文中存在事务，则嵌套事务执行，如果不存在事务，则新建事务|

## 事务的隔离级别

1. 数据库事务并发问题
    - 脏读
    - 不可重复读
    - 幻读
2. 隔离级别 Mysql默认可重复读 Oracle默认读已提交
   一个事务与其他事务隔离的具体程度成为隔离级别
   - 读未提交
   - 读已提交
   - 可重复读
   - 串行化

## SpringMvc中如何解决POST请求中文乱码的问题，GET又是如何处理

```xml
<!--解决POST请求的请求乱码问题-->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>foreEnconding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncondingFilter</filter-name>
    <url-pattern>/*</url-pattetrn>
</filter-mapping>
```

```xml
<!--get请求乱码-->
<!--tomcat中server.xml文件中-->
<Connector connectionTimeout="2000" port="8080" ...>
更改为
<Connector URIEncoding="UTF" connectionTimeout="2000" port="8080" ...>
```

## springMVC的工作流程

```java
//处理模型数据方式1：将方法的返回值设置为ModelAndView
@RequestMapping("/testModelAndView")
public ModelAndView testModelAndView(){
    //1. 创建ModelAndView对象
    ModelAnView mav = new ModelAndView();
    //2. 设置模型数据，最终会放到request域中
    mav.addObject("user","admin");
    //3. 设置视图
    mav.setViewName("success");
    return mav;
}
```

```java
//处理模型数据方式2: 方法的返回值仍然是String类型，在方法中传入Map,Model,或者ModelMap
//不管将处理器方法的返回值设置为ModelAndView还是在方法中传入Map,Model,或者ModelMap，SpringMVC都会转换为一个ModelAndView对象
@RequestMapping("/testModelAndView")
public String testMap(Map<String,Object> map){
    //向map中添加模型数据最终会自动放到request域中
    map.put("user",new Object());
    return SUCCESS;
}
```

## Mybatis中实体类中的属性名和表中的字段名不一样，怎么办？

1. 写sql语句时起别名
2. 在Mybatis的全局配置文件开启驼峰命名规则

  ```xml
  <settings>
    <setting name="mapUnderscoreToCamelCase" value="true"/>
   </settings>
  ```

3. 在Mapper映射文件中使用resultMap来自定义映射规则
