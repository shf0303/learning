# 历史回顾：jsp和servLet

## servLet简介

serveLet 是用来处理页面请求 并向数据库请求数据，处理数据，并自定义响应内容的中间站，它本身也是一个跳转的页面。servLet本身是jvm和http服务器软件进行交互的一种规范，jdbc则是jvm与数据库交互的一种规范！

ServeLet的生命周期就包含五个方法：

init()、service()、doGet()、doPost()、destroy()

初始化方法，service() 方法是执行实际任务的主要方法。Servlet 容器（即 Web 服务器）调用 service() 方法来处理来自客户端（浏览器）的请求，并把格式化的响应写回给客户端。doGet()和doPost()则是处理相应请求的具体方法，destroy()则是销毁具体线程的方法！

通过servLet，我们就能通过用户发送的请求内容，通过jdbc调取数据库调取前端所需数据，通过servLet自定义响应数据返回给前端，通过定义不同的servLet进程，我们可以实现不同的响应接口（页面）！一切就通了！

servlet实例对象是通过Http服务器软件创立和销毁的，创建的情况有两种：

1. 默认情况下是由TomCat在注册的servLet被访问时创建，而且只能创建一个，我们可以在servLet类中写一个构造函数验证！

2. 第二种则是人为干预，在TomCat启动时就创建。我们需要在web.xml文件(配置动态资源文件)中修改配置在servLet注册时添加一项：

   ```xml
       <servlet>
           <servlet-name>demo.HelloServLet</servlet-name>
           <servlet-class>demo.HelloServLet</servlet-class>
           <load-on-startup>123</load-on-startup>  //HERE!
       </servlet>
   ```

## HttpservLet中的请求和响应

响应包中的响应体是浏览器负责解析，并展示到该页面中的内容，所以我们访问任何网站所得到的页面，是浏览器向服务器发送get请求所得到的响应包，浏览器负责解析这些响应包，并将响应体中的内容展示给我们。

静态资源文件：在TomCat中，当我们在网站域名后加上对应的资源文件名，浏览器就会发送一个get请求，服务器则会把这个文件包在响应体里返回给浏览器，浏览器解析并展示到该页面上，业务人员无法干预响应的内容。

但是如果我们想根据用户请求信息的不同，自定义响应体的内容，这时候就需要一种动态资源文件了。

动态资源文件：在TomCat中，Java通过servLet来实现和http服务器的连接和交互，我们只需要在xml(动态资源文件)中配置，动态资源文件（xml）的主要目的是用来传输数据，而非展示,展示是html需要完成的事。

具体配置如下：

```xml
    <servlet>
        <servlet-name>你好</servlet-name> //别名
        <servlet-class>demo.HelloServLet</servlet-class> //访问路径
        <load-on-startup>123</load-on-startup>
    </servlet>

    <servlet-mapping> 
        <servlet-name>你好</servlet-name> //别名
        <url-pattern>/Hello</url-pattern>  //url访问路径
    </servlet-mapping>

```

### 响应头中的编码问题：

由于浏览器是按照响应头中的编码集去解析响应中的数据，所以当我们的响应头中没有规定具体的编码集时，浏览器就会按照默认的编码集(ISO-8859-1)去解析，而这是一种偏东欧的字符编码集，解析不了所有的亚洲字符，所以我们需要在响应头中设置字符编码：

```java
response.setCharacterEncoding("GBK")
```

### 响应头的内容类型设置：

浏览器是怎么判断响应数据的类型的呢?也很简单，也是通过响应头的内容类型，默认情况下是text,也就是文字内容

```java
response.setContentType("text/html")
```

### HttpServLetRequest 和 HttpServLetResponse的生命周期(同生共死)

1. 创建：由TomCat 接收到浏览器请求时创建
2. 销毁：发送响应包后销毁

### ServLetContext(全局作用域对象)

ServLetContext为所有的HttpServLet实例对象提供了一个公共的大仓库，里面存储了共享的数据！这就避免了冗余的代码，降低维护成本！

使用方式：

```
ServletContext app =  request.getServletContext(); 
```

共享数据来源：

1. 将共享数据添加到web.xml中

   ```xml
   	<context-param>
           <param-name>user</param-name>
           <param-value>jack</param-value>
       </context-param>
   
   String share = app.getInitParameter("user")  //获取共享数据
   ```

2. 由某一个servLet提供，其他servLet获取

   ```
   A servLet
   app.setAttribute(s:,o:)//共享数据为object;
   B servLet
   app.getAttribute(s:)  //可变数据
   ```

### Http状态码

1. 简介：

   1.1  是由http服务器软件创建并发送到响应行中的状态行中的。

   1.2  是由三位数字组成

   1.3  作用是来指示浏览器收到响应包后的行为和此次响应各种状态的原因

2. 5类状态码

   1.  1xx 通知浏览器此次响应的资源文件并不完整，需要浏览器继续发起请求获取资源（浏览器自动发起请求获取后续资源）

   2. 2xx 资源完整，例如：200代表一次完美通信

   3. 3xx 服务器推送给客户端是一个网址，指示用户去重定向，向这个网址发起请求，该网址一搬被放在响应头的location属性中，命令为`response.sendRedirect("地址")`

   4. 4xx 服务端没有对应的资源文件。

      404： 网址写错，服务端没有对应文件

      405：网址没错，但是请求方法有误，该servLet没有重写浏览器所请求的方法，比如说servLet没有写get方法，那所有get方法都会报405！

      ```
      Type Status Report
      
      消息 此URL不支持Http方法GET
      
      描述 请求行中接收的方法由源服务器知道，但目标资源不支持
      ```

   5. 5xx 服务端servLet执行时抛出了异常！执行错误！

### 一个网站的默认欢迎资源文件

当我们向一个网站发起请求，而没有指定后面的任何资源文件时，TomCat会寻找一个默认资源文件，配置默认资源文件有两种方式。

1. 在该网站的web.xml中配置

   ```xml
   <welcome-file-list>
   	<welcome-file>xxx.html</welcome-file>	
   </welcome-file-list>
   ```

2. 当TomCat在该网站的web.xml文件中未找到如上设置时，它会去自己的web.xml中寻找默认欢迎资源文件，如下：

   ```xml
   	<welcome-file-list>
           <welcome-file>index.html</welcome-file>
           <welcome-file>index.htm</welcome-file>
           <welcome-file>index.jsp</welcome-file>
       </welcome-file-list>
   ```

所以我们可以发现，index.*便是默认的欢迎文件！

### 重定向和请求转发

#### 重定向（使用转查询接口页面）

1. 定义： 重定向是指在处理完一次servLet动态资源后，指示浏览器跳转到另一个页面，此时状态码时302。

2. 特征：

   1. 浏览器将发送多次不同的请求

                2. 重定向发生在浏览器客户端
      3. 地址栏会发生改变
      4. 由于是通过地址栏location访问，所以第二次浏览器的自动请求方式必然是get
      5. 重定向的网址可以是任意网址，并不局限该网站

3. 实现方式：`response.sendRedirect("地址")`在响应头中加一个属性Location

#### 请求转发（适用jsp）

1. 定义： 请求转发是指在处理完一次servLet动态资源后，指示Http服务器软件，将此次请求交给另一个接口处理

2. 特征：

   1. 浏览器发送一次请求

   2. 重定向发生在服务器端
   3. 地址栏不会发生改变
   4. 请求方式为浏览器第一次请求的接口的方式
   5. 请求转发只局限该网站，而且不需要写域名

3. 实现方式：

   ```java
   RequestDispatcher report = request.getRequestDispatcher("/other");  //获取报告，以及请求转发的地址
   report.forward(request, response); //传递请求包和响应包
   
   
   //如何实现接口间的资源共享
   
   //session实现数据共享
   HttpSession session = request.getSession();
   session.setAttribute("城市", "上海");
   
   //servLetContext实现数据共享
   ServletContext context = request.getServletContext();
   Integer age = 16;
   context.setAttribute("年龄", age);
   
   //request请求包中添加数据！
   request.setAttribute("性别", "男");  //专门用来给请求转发共享数据！
   ```

   

# cookie和session

cookie是一种会话跟踪技术！存在本地浏览器里的key,value信息键值对，可设置属性有

| 属性名            | 描述                                                         |
| ----------------- | ------------------------------------------------------------ |
| name              | Cookie的名称，Cookie一旦创建，名称便不可更改                 |
| value             | Cookie的值，如果值为Unicode字符，需要为字符编码。如果为二进制数据，则需要使用BASE64编码 |
| maxAge（expires） | Cookie失效的时间，单位秒。如果为整数，则该Cookie在maxAge秒后失效。如果为负数，该Cookie为临时Cookie，关闭浏览器即失效，浏览器也不会以任何形式保存该Cookie。如果为0，表示删除该Cookie。默认为-1。 |
| secure            | 该Cookie是否仅被使用安全协议传输。安全协议。安全协议有HTTPS，SSL等，在网络上传输数据之前先将数据加密。默认为false。 |
| domain            | 可以访问该Cookie的域名。如果设置为“.google.com”，则所有以“google.com”结尾的域名都可以访问该Cookie。注意第一个字符必须为“.”。 |
| path              | Cookie的使用路径。如果设置为“/sessionWeb/”，则只有contextPath为“/sessionWeb”的程序可以访问该Cookie。如果设置为“/”，则本域名下contextPath都可以访问该Cookie。注意最后一个字符必须为“/”。 |
| commen            | 该Cookie的用处说明，浏览器显示Cookie信息的时候显示该说明。   |
| version           | Cookie使用的版本号。0表示遵循Netscape的Cookie规范，1表示遵循W3C的RFC 2109规范 |

cookie是服务端为了识别客户端而存储的一些信息，由于http协议本身是无状态的，一次请求，一次响应，这次会话就结束了，下次会话和这次没有任何关系！那么如何实现一些跨页面，跨请求的操作呢？这就有了cookie！实现过程：当用户第一次访问该页面，没cookie,，服务端发一个（逻辑由我们设计）通过response.setCookie返回让浏览器存储起来，下次请求时，浏览器就会把cookie的信息放在请求体里发给服务端，服务端通过这个在进行一系列识别操作！

可是这样还有一个问题，他不安全啊！啥都存在客户端，也就意味这客户可以随意修改，用户信息也有可能泄露，所有出现了第二种会话跟踪技术，session,session 也是一种键值对，而且里面存储的是**对象**，但不同的是它是存在服务端的，理论上服务端会为每一次会话存一个session。（为什么？后面再说）规则：

1. 如果浏览器支持cookie，创建session的时候，会被sessionID保存再cookie里。只要允许cookie，session就不会改变，如果不允许使用cookie，每刷新一次浏览器就会换一个session（因为浏览器以为这是一个新的链接）

2. 如果不支持cookie，必须自己编程使用URL重写的方式实现session

3. Session不像cookie一样拥有路径访问的问题，同一个application下的servlet/jsp都可以共享同一个session，前提下是同一个客户端窗口。
   

当服务器接收到请求时，它会去两个地方找这个session,url和cookie里的sessionid,所以在底层里，sessionid这个cookie的本地过期时间默认是session(即会话结束时，进一步说，关了浏览器时)，（暂时不知道怎么改！）

所以说浏览器每次都会为新的会话创建新的sessionID,因为我们逻辑上请求没有就给一个，而当禁用cookie时，就使用重写url的方法，在路由后面加上;sessionid=....。





