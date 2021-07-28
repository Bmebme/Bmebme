# Java EE

## Java EE 分层模型

### Java EE 核心技术

**Java 数据库连接 ( Java Database Connectivity，JDBC ) **，在 Java 语言中用来规范客户端程序如何访问数据库的应用程序接口，提供了诸如查询和更新数据的方法。

**Java 命名和目录接口 ( Java Naming and Directory Interface，JNDI ) **，是 Java 的一个目录服务应用程序界面，他提供了一个目录系统，并将服务名称与对象关联起来，使开发人员在开发过程中可以使用名称来访问对象。

**企业级 JavaBean ( Enterprise JavaBean，EJB ) **是一个用来构筑企业级应用的，在服务器端可被管理的组件。

**远程方法调用 ( Remote Method Invocation，RMI ) **是 Java 的一组用来开发分布式应用程序的 API，大大增强了 Java 开发分布式应用程序的能力。

**Servlet ( Server Applet ) **是使用 Java 编写的服务器端程序。狭义的 Servlet 是指 Java 语言实现的一个接口，广义的 实现该 Servlet 接口的类。其主要功能在于交互式的浏览和修改数据，生成动态的 Web 内容。

**JSP ( JavaServer Pages ) **是一种动态网页技术标准。JSP 部署于网络服务器之上，可以响应客户端发送的请求，并根据请求内容动态生成 HTML、XML 或其他格式的 Web 网页，然后返回给请求者。

**可扩展标记语言 ( eXtensible Markup Language，XML ) **是被设计用于传输和存储数据的语言。

**Java 消息服务 ( Java Message Service，JMS ) **是一个 Java 平台中关于面向消息中间件 ( MOM ) 的 API，用于在两个应用程序之间或分布式系统中发送消息，进行异步通信。

### Java EE 分层模型

**Domain Object ( 领域对象 ) 层：**本层由一系列 POJO ( Plain Old Java Object，普通的传统的 Java 对象 ) 组成，这些对象是该系统的 Domain Object，通常包含各自所需实现的业务逻辑方法。

**DAO ( Data Access Object，数据访问对象 ) 层：**本层由一系列 DAO 组件组成，这些 DAO 实现了对数据库的构建、查询、更新和删除等操作。

**Service ( 业务逻辑 ) 层：**本层由一系列的业务逻辑对象组成，这些业务逻辑对象实现了系统所需要的业务逻辑方法。

**Controller ( 控制器 ）层：**本层由一系列控制器组成，这些控制器用于拦截用户的请求，并调用业务逻辑组件的业务逻辑方法去处理用户请求，然后根据处理结果向不同的 View 组件转发。

**View ( 表现 ) 层：**本层由一系列的页面及视图组件组成，负责收集用户的请求，并显示处理后的结果。

## MVC 模式与 MVC 框架

### Java MVC 模式

**模型 ( Model )：**表示携带数据的对象或 Java POJO。即使模型内数据改变，也具有逻辑来更新控制器。

**控制器 ( Controller )：**表示逻辑控制，控制器对模型和视图都有作用，控制数据流进入模型对象，并在数据更改时更新视图，是视图和模型的中间层。

**视图 ( View )：**表示模型包含的数据的可视化层。

### Java MVC 框架

- Struct 1 框架
- Struct 2 框架
- Spring MVC 框架
- JSF 框架
- Tapestry 框架

## Servlet

### Servlet 配置

#### 基于 web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml">
  <display-name>manage</display-name>
  <welcome-file-list>
      <welcome-file>index.html</welcome-file>
      <welcome-file>index.htm</welcome-file>
      <welcome-file>index.jsp</welcome-file>
      <welcome-file>default.html</welcome-file>
      <welcome-file>default.htm</welcome-file>
      <welcome-file>default.jsp</welcome-file>
  </welcome-file-list>
  <servlet>
      <discription></discription>
      <display-name>user</display-name>
      <servlet-name>user</servlet-name>
      <servlet-class>com.sec.servlet.UserServlet</servlet-class>
  </servlet>
  <servlet-mapping>
      <servlet-name>user</servlet-name>
      <url-pattern>/user</url-pattern>
  </servlet-mapping>
</web-app>
```

标签含义如下

|        标签         |                  含义                   |
| :-----------------: | :-------------------------------------: |
|     `<servlet>`     |         声明 Servlet 的配置入口         |
|   `<description>`   |          声明 Servlet 描述信息          |
|  `<display-name>`   |           定义 Web 应用的名字           |
|  `<servlet-name>`   | 声明 Servlet 名称以便再后面的映射时使用 |
|  `<servlet-class>`  |     指定当前 Servlet 对应的类的路径     |
| `<servlet-mapping>` |       注册组件访问配置的路径入口        |
|  `<servlet-name>`   |     指定上文的配置的 Servlet 的名称     |
|   `<url-pattern>`   |       指定配置这个组件的访问路径        |

#### 基于注解方式

Servlet 3.0 以上版本，可以 `@WebServlet`注释修改 Servlet 属性，如下

```java
@WebServlet(description = "this is a test for java servlet", urlPatterns = { "/test" })
```
其他参数如下

|     属性名      |       类型       |                             描述                             |
| :-------------: | :--------------: | :----------------------------------------------------------: |
|     `name`      |     `String`     |    指定 `Servlet` 的 `name` 属性，等价于 `<servlet-name>`    |
|     `value`     |    `String[]`    |                  等价于 `urlPatterns` 属性                   |
|  `urlPatterns`  |    `String[]`    |  指定一组 Servlet 的 URL 的匹配模式，等价于`<url-pattern>`   |
| `loadOnStartup` |      `int`       |      指定 Servlet 的加载顺序，等价于`<load-on-startup>`      |
|  `initParams`   | `WebInitParam[]` |     指定一组 Servlet 的初始化参数，等价于`<init-param>`      |
|  `asyncParams`  |    `boolean`     | 声明 Servlet 是否支持异步操作模式，等价于`<async-supported>` |
|  `description`  |     `String`     |          Servlet 的描述信息，等价于`<description>`           |
|  `displayName`  |     `String`     |  Servlet 的显示名，通常配合工具使用，等价于`<display-name>`  |

### Servlet 的访问流程

以该[配置](#基于 web.xml)为例，首先在浏览器输入`user`，如`192.168.0.1/manage/user`，即访问 `url-pattern`标签中的值；然后浏览器发起请求，服务器通过`servlet-mapping`标签中找到文件名为`user`的`url-pattern`，通过其对应的`servlet-name`寻找`servlet`标签中`servlet-name`相同的`servlet`；在通过`servlet`标签中的`servlet-name`，获取`servlet-class`参数；最后得到具体的`class`文件路径，执行代码

### Servlet 的接口方法

`HTTP`有8种请求方法，分别为`GET`，`POST`，`HEAD`，`OPTIONS`，`PUT`，`DELETE`，`TRACE`，`CONNECT`。与此类似，`Servlet`中也存在对应的请求接口。

#### `init()`

在 `Servlet`实例化后，`Servlet`容器会调用`init`接口来初始化该对象，该方法只会被调用一次

```java
public void init() throws ServletException{
    // 初始化代码
}
```

#### `service()`

`service()`是实际执行任务的主要方法，`Servlet`容器调用`service`方法来处理客户端的请求，并将格式化的响应返回给客户端。每次收到一个请求，服务器都会产生一个新的线程并调用服务

```java
public void service(ServletRequest request, ServletResponse response)
    			throws ServletException, IOException{
    // 处理请求代码
}
```

#### `doGet()/doPost()`

如果接收到的是`GET`请求，会调用`doGet`方法，如果是`POST`请求，则调用`doPost`请求

```java
public void doGet(HttpServletRequest request, HttpServletResponse response)
    			throws ServletException, IOException{
    // 处理 GET 请求
    // 若为 POST 请求，调用 public void doPost 方法
}
```

#### `destroy()`

当一个 `Servlet`对象被销毁时，就会调用该方法，释放对应的资源，该方法也只会被调用一次

```java
public void destroy(){
	// 终止代码
}
```

#### `getServletConfig()`

当`servlet`配置了初始化参数后，服务器在创建`servlet`实例对象时，会自动将这些初始化参数封装到`ServletConfig`对象中，并在调用`servlet`的`init`方法时，将`ServletConfig`对象传递给`servlet`。进而，我们通过`ServletConfig`对象就可以得到当前`servlet`的初始化参数信息。

#### `getServletInfo()`

`getServletInfo`方法会返回一个`String`类型的字符串，包括关于`Servlet`的信息，如作者、版本以及版权等。

## Java Web 过滤器 —— filter

### filter配置

#### 基于 web.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml"> 
	<!-- 其他省略-->
    <filter>
        <display-name>test</display-name>
        <filter-name>test</filter-name>
        <filter-class>com.sec.test.test</filter-class>
    </filter>
    <filter-mapping>
        <filter-name>test</filter-name>
        <url-pattern>/test</url-pattern>
    </filter-mapping>
</web-app>
```

标签含义如下

|        标签        |                             含义                             |
| :----------------: | :----------------------------------------------------------: |
|     `<filter>`     |                        指定一个过滤器                        |
|  `<filter-name>`   |             为过滤器指定一个名称，该元素不能为空             |
|  `<filter-class>`  |                用于指定过滤器的完整的限定类名                |
|   `<init-param>`   |                  用于为过滤器指定初始化参数                  |
|   `<param-name>`   |           `<init-param>`的子参数，用于指定参数名称           |
|  `<param-value>`   |           `<init-param>`的子参数，用于指定参数的值           |
| `<filter-mapping>` |             用于设置一个`filter`所负责拦截的资源             |
|  `<filter-name>`   | `<filter-mapping>`的子元素，用于设置`filter`的注册名称。该值必须是在`<filter>`元素中声明过的过滤器的名称 |
|  `<url-pattern>`   |                用于设置`filter`拦截的请求路径                |
|  `<servlet-name>`  |             用于指定过滤器所拦截的`servlet名称`              |

#### 基于注解方式

方法基本等同于`Servlet`，参数如下，指定注意的是采用注解方式无法确定执行顺序

|       属性名        |        类型        |                 描述                 |
| :-----------------: | :----------------: | :----------------------------------: |
|  `asyncSupported`   |     `boolean`      |   指定 `servlet` 是否支持异步模式    |
|  `dispatcherTypes`  | `DispatcherType[]` | 指定`filter`对哪种方式的请求进行过滤 |
|    `filterName`     |      `String`      |             `filter`名称             |
|    `initParams`     |  `WebInitParam[]`  |               配置参数               |
|    `displayName`    |      `String`      |            `filter`显示名            |
|    `servletName`    |     `String[]`     |      指定对哪些`filter`进行过滤      |
| `urlPatterns/value` |     `String[]`     |            指定拦截的路径            |

### filter 的使用流程及实现方式

当用户向服务器发送 request 请求是，服务器接受该请求，并将其发送到过滤器处理。如果有多个过滤器，则会依次执行`filter 1`，`filter 2`......`filter n`，接着调用`service()`方法，调用完毕后，按照与之前的相反的顺序，从`filter n`开始执行

```java
public class TestFilter1 implements Filter {  
		@Override
  	    protected void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {  
        //在DispatcherServlet之前执行  
		System.out.println("##########TestFilter1 doFilterInternal executed##########");  
        filterChain.doFilter(request, response);  
        //在视图页面返回给客户端之前执行，但是执行顺序在Interceptor之后  
        System.out.println("##########TestFilter1 doFilter after##########");  
    }  
} 
```

 在`HTTPServletRequest`到达`Servlet`之前，`filter`拦截客户端的`HTTPServletRequest`，根据需要检查或者修改数据。在`HTTPServletResponse`到达客户端之前，拦截`HTTPServletResponse`，检查或者修改数据。

### filter 的接口方法

与`Servlet`不同的是，`filter`接口在创建时就默认创建了所有方法。

#### `init()`

`filter`会调用`init`接口来初始化，如果初始化代码中要用到`FilterConfig`对象，只能在`init`方法中编写

```java
public void init(FilterConfig fConfig) throws ServletException{
    // 初始化代码
}
```

#### `doFilter()`

当客户端请求目标资源的时候，容器会筛选出符合`<filter-mapping>`标签中的`<url-pattern>`的`filter`，并按照`<filter-mapping>`声明的顺序依次调用`filter`中的`doFilter()`方法。需要注意的是，`doFilter`方法有许多参数，其中`request`和`response`为服务器或上一个`filter`中传过来的请求和响应对象。`chain`代表当前`filter`链对象，只有当前`filter`对象中的`doFilter`方法内部需要调用`FilterChain`对象的`doFilter`方法时，才能把请求交付给`filter`链中下一个`filter`或者目标程序处理

```java
public void doFilter(HttpServletRequest request, HttpServletResponse response, 
                     FilterChain chain)
    			throws ServletException, IOException{
    // 过滤代码
    // ...
    // 传递 filter 链
    chain.doFilter(request, response);
}
```

#### `destroy()`

当需要销毁`filter`时会调用该方法，释放对应的资源

```java
public void destroy(){
	// 终止代码
}
```

## Java 反射机制

