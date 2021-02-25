# 一、Session和Cookie的区别

`session`通过服务端的web应用，将客户端发送的用户ID和密码与之前登录的信息进行匹配，以认证用户是否已经登录，称作基于表单认证。

`session`是记录在服务端，而`cookie`记录在客户端。

客户端和服务端的交互步骤如下：

* 客户端把用户 ID 和密码等登录信息放入报文的实体部分，通常是以 POST 方法把请求发送给服务器。
* 服务器会发放用以识别用户的`Session ID`。通过验证从客户端发送过来的登录信息进行身份认证，然后把用户的认证状态与
  `Session ID`绑定后记录在服务器端。
* 在请求的首部字段 `Set-Cookie` 内写入 `Session ID`，向客户端返回响应。
* 客户端接收到从服务器端发来的 `Session ID` 后，会将其作为`Cookie`保存在本地。
* 下次向服务器发送请求时，浏览器会自动发送`Cookie`，所以 `Session ID` 也随之发送到服务器。服务器端可通过验证
  接收到的 `Session ID` 识别用户和其认证状态。

# 二、HttpServletRequest详解

1. `HttpServletRequest`对象代表客户端的请求，当客户端通过HTTP协议访问服务器时，HTTP请求头中的所有信息都封装在这个对象中，通过这个对象提供的方法，可以获得客户端请求的所有信息。

2. 请求转发：

   通过request对象提供的`getRequestDispatcher(String path)`方法，该方法返回一个`RequestDispatcher`对象，调用这个对象的forward方法可以实现请求转发。

   ```java
   request.getRequestDispatcher("/index.html").forward(request,response);
   ```

3. request对象同时也是一个域对象(Map容器)，开发人员通过request对象在实现转发时，把数据通过request对象带给其它web资源处理。

   ```java
   request.setAttribute("msg", "没有权限请先登录");
   ```

   作为Map容器使用时，主要包括以下几个方法：

   * `setAttribute(String name,Object o)`方法，将数据作为request对象的一个属性存放到request对象中
   * `getAttribute(String name)`方法，获取request对象的name属性的属性值
   * `removeAttribute(String name)`方法，移除request对象的name属性
   * `getAttributeNames`方法，获取request对象的所有属性名，返回的是一个枚举类型Enumeration<String>

# 三、拦截器和过滤器的区别

3.1 过滤器：

它依赖于servlet容器。在实现上，基于函数回调，它可以对几乎所有请求进行过滤，但是缺点是一个过滤器实例只能在容器初始化时调用一次。使用过滤器的目的，是用来做一些过滤操作，获取我们想要获取的数据，比如：在Javaweb中，对传入的request、response提前过滤掉一些信息，或者提前设置一些参数，然后再传入servlet或者Controller进行业务逻辑操作。通常用的场景是：在过滤器中修改字符编码（CharacterEncodingFilter）、在过滤器中修改HttpServletRequest的一些参数（XSSFilter(自定义过滤器)），如：过滤低俗文字、危险字符等。



3.2 拦截器：

它依赖于web框架，在SpringMVC中就是依赖于SpringMVC框架。在实现上,基于Java的反射机制，属于面向切面编程（AOP）的一种运用，就是在service或者一个方法前，调用一个方法，或者在方法后，调用一个方法，比如动态代理就是拦截器的简单实现，在调用方法前打印出字符串（或者做其它业务逻辑的操作），也可以在调用方法后打印出字符串，甚至在抛出异常的时候做业务逻辑的操作。由于拦截器是基于web框架的调用，因此可以使用Spring的依赖注入（DI）进行一些业务操作，同时一个拦截器实例在一个controller生命周期之内可以多次调用。但是缺点是只能对controller请求进行拦截，对其他的一些比如直接访问静态资源的请求则没办法进行拦截处理。



3.3 两者区别：

1. 拦截器主要防止客户端未经登录直接通过URI访问页面，过滤器则主要对请求数据进行验证、过滤和筛选处理

2. 拦截器是基于java的反射机制的，而过滤器是基于函数回调。

3. 拦截器不依赖于servlet容器，过滤器依赖于servlet容器。

4. 拦截器只能对action请求起作用，而过滤器则可以对几乎所有的请求起作用。

5. 拦截器可以获取IOC容器中的各个bean，而过滤器就不行，这点很重要，在拦截器里注入一个service，可以调用业务逻辑。

6. 过滤器是在请求进入容器后，但请求进入servlet之前进行预处理的。请求结束返回也是，是在servlet处理完后，返回给前端之前。

   

   

   ![过滤器和拦截器的区别](%E8%BF%87%E6%BB%A4%E5%99%A8%E5%92%8C%E6%8B%A6%E6%88%AA%E5%99%A8%E7%9A%84%E5%8C%BA%E5%88%AB.png)

# 四、DO、DTO、BO、AO、VO、POJO的定义

分层领域模型规约：

- DO（ Data Object）：与数据库表结构一一对应，通过DAO层向上传输数据源对象。
- DTO（ Data Transfer Object）：数据传输对象，Service或Manager向外传输的对象。
- BO（ Business Object）：业务对象。 由Service层输出的封装业务逻辑的对象。
- AO（ Application Object）：应用对象。 在Web层与Service层之间抽象的复用对象模型，极为贴近展示层，复用度不高。
- VO（ Value Object）：用于业务层之间的数据传递。
- POJO（ Plain Ordinary Java Object）：在本手册中， POJO专指只有setter/getter/toString的简单类，包括DO/DTO/BO/VO等。
- Query：数据查询对象，各层接收上层的查询请求。 注意超过2个参数的查询封装，禁止使用Map类来传输。

领域模型命名规约：

- 数据对象：xxxDO，xxx即为数据表名。
- 数据传输对象：xxxDTO，xxx为业务领域相关的名称。
- 展示对象：xxxVO，xxx一般为网页名称。
- POJO是DO/DTO/BO/VO的统称，禁止命名成xxxPOJO。

# 五、