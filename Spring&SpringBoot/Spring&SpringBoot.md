# 一、Spring原理

## 1.1 XML配置文件

Spring通过配置bean标签来管理对象

（1）`id`：表示实例化对象的名字。

（2）`class`：对象的模板类，使用全限定名，其中该类必须具有无参构造器，否则无法通过反射机制获得类的无参构造器，实例化对象。

（3）`property`：通过property标签配置对象的属性值（成员变量值）

​			`name`：成员变量名；

​			`value`：成员变量赋值，基本数据类型和String类型可以直接赋值，引用类型需要使用`ref`注入；

​			`ref` : 将IoC中的另外一个bean赋给当前成员变量（DI依赖注入）。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:p="http://www.springframework.org/schema/p"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-			   beans.xsd">

    <bean id="student" class="com.rango.entity.Student" >
        <property name="id" value="1"/>
        <property name="name" value="li"/>
        <property name="age" value="21"/>
        <property name="address" ref="address"/>
    </bean>
    <bean id="address" class="com.rango.entity.Address">
        <property name="id" value="2"/>
        <property name="name" value="wang"/>
</beans>
```



## 1.2 IoC底层原理

传统方式建立对象：

```java
public class Test {
    public static void main(String[] args) {
        Student student = new Student();
        student.setId(1L);
        student.setAge(18);
        student.setName("li");
        System.out.println(student);
    }
}
```

IOC控制反转创建对象方式：

读取配置文件并解析XML文件

`->`通过反射机制，根据id和class属性获得类名和实例化对象名

`->`获取类的无参构造器，实例化配置文件中的每一个bean配置的对象

`->`<property>的设置，是通过将每个成员变量名首字母大写，再匹配上set（即name`->`Name`->`setName的过程），获得对象的set方法，根据配置信息来设置成员变量值。

```java
public class Test {
    public static void main(String[] args) {
        // 加载配置文件
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring.xml");
        // 实例化bean配置的对象
        Student student = (Student) applicationContext.getBean("student");
        System.out.println(student);
    }
}
```



## 1.3 通过有参构造的方式创建bean

* 要求实体类中具有对应的有参构造器
* 添加配置文件，使用标签`constructor-arg`

```xml
<bean id="student2" class="com.rango.entity.Student">
    <constructor-arg name="id" value="3"/>
    <constructor-arg name="age" value="20"/>
    <constructor-arg name="name" value="wu"/>
    <constructor-arg name="address" ref="address"/>
</bean>
```

* 可以不使用`name`进行关联，采用`index`标签进行位置索引

* ```xml
  <bean id="student3" class="com.wiggin.entity.Student">
      <constructor-arg index="0" value="3"></constructor-arg>
      <constructor-arg index="1" value="bob"></constructor-arg>
      <constructor-arg index="2" value="18"></constructor-arg>
      <constructor-arg index="3" ref="address"></constructor-arg>
  </bean>
  ```



## 1.4 为bean注入`ref`集合

```xml
<bean id="student" class="com.rango.entity.Student">
    <property name="id" value="1"></property>
    <property name="name" value="rango"></property>
    <property name="age" value="22"></property>
    <property name="addresses">
        <list>
        <ref bean="address"></ref>
        <ref bean="address2"></ref>
        </list>
	</property>
</bean>
<bean id="address" class="com.wiggin.entity.Address">
    <property name="id" value="1"></property>
    <property name="name" value="科技路"></property>
</bean>
<bean id="address2" class="com.wiggin.entity.Address">
    <property name="id" value="2"></property>
    <property name="name" value="高新区"></property>
</bean>
```



## 1.5 Scope作用域

Spring根据scope来实现对bean的管理，表示bean的作用域。包括四种scope：

* singleton：单例，在加载配置文件时（**new** ClassPathXmlApplicationContext(**"applicationContext.xml"**)）生成唯一的bean对象，后续每次调用不会创建新的对象。多个bean指向同一个地址更加节省空间。
* prototype：原型，当业务代码获取Ioc容器中的bean时(getbean)，Spring才去调用无参构造来创建对应的bean。对象不唯一，每次调用都创建一个新的对象。创建一个bean开辟一个空间，只在需要的时候才创建，浪费内存空间。
* request：请求，表示在一次HTTP请求内有效
* session： 会话，表示在一个用户会话内有效

**request**和**session**是适用于web项目，大多数使用单例和原型。



**prototype**模式当业务代码获取Ioc容器中的bean时(getbean)，Spring才去调用无参构造来创建对应的bean。创建一个bean开辟一个空间，避免bean的利用率低的问题，但是也浪费了地址空间。



## 1.6 Spring的继承

与java的继承不同，java是**类层面的继承**，子类可以继承父类的父类的内部结构信息，Spring是**对象层面的继承**，子对象可以继承父对象的属性值。

**注意**：子对象必须包含父对象的全部属性

```xml
    <bean id="student" class="com.rango.entity.Student" >
        <property name="id" value="1"/>
        <property name="name" value="li"/>
        <property name="age" value="21"/>
        <property name="address" ref="address"/>
    </bean>
    <bean id="address" class="com.rango.entity.Address">
        <property name="id" value="2"/>
        <property name="name" value="wang"/>
    </bean>
    <bean id="classmate" class="com.rango.entity.Student" parent="student">
        <property name="name" value="子对象继承父对象的属性"/>
    </bean>
</beans>
```



## 1.7 Spring的依赖

与继承类似，依赖也是描述bean和bean之间的一种关系，配置依赖之后，被依赖的bean一定要先被创建。

```xml
<bean id="student" class="com.rango.entity.Student" ></bean>

<!--  user依赖于student  -->
<bean id="user" class="com.rango.entity.User" depends-on="student"></bean>
```



## 1.8 Spring的p命名空间

p命名空间实对IoC/DI的简化操作，使用p命名空间可以更加方便的完成bean的配置以及bean之间的依赖注入

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
		xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		xmlns:p="http://www.springframework.org/schema/p"
		xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-				beans.xsd">

    
    <bean id="student" class="com.rango.entity.Student" p:id="1" p:name="张三" p:age="22" p:address-ref="address"></bean>
    <bean id="address" class="com.rango.entity.Address" p:id="2" p:name="科技路"></bean>
</beans>
```



## 1.9 Spring的工厂模式

ioC通过工厂模式创建bean的方式有两种：

* 静态工厂方法
* 实例工厂方法

静态工厂方法：直接可以通过静态方法来实例化一个对象

* 定义car类

  ```java
  @Data
  @AllArgsConstructor
  @NoArgsConstructor
  public class Car {
      private Long id;
      private String name;
  }
  ```

* 定义静态工厂

  ```java
  public class StaticCarFactory {
      private static Map<Long, Car> carMap;
      static {
          carMap = new HashMap<Long, Car>();
          carMap.put(1L,new Car(1L,"宝马"));
          carMap.put(2L,new Car(2L, "奔驰"));
      }
  
      public static Car getCar(Long id){
          return carMap.get(id);
      }
  }
  ```

* 传统方法获得对象car

  ```java
  public class Test2 {
      public static void main(String[] args) {
          Car car = new StaticCarFactory().getCar(1L);
          System.out.println(car);
      }
  }
  ```

* 静态工厂通过IoC获得静态工厂中的方法：

  ```xml
  <!--用到几个对象配置几个bean-->
  <!--  配置静态工厂  -->
  <bean id="car" class="com.rango.factory.StaticCarFactory" factory-method="getCar">
      <constructor-arg name="id" value="1"/>
  </bean>
  ```

  ```java
  public class Test2 {
      public static void main(String[] args) {
          ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-factory.xml");
          Car car = (Car) applicationContext.getBean("car");
          System.out.println(car);
      }
  }
  ```

  

实例工厂方法：先创建类对象，通过对象来调用创建实例对象的方法

* 定义实例工厂：

  ```java
  public class InstanceCarFactory {
      private Map<Long, Car> carMap;
      // 无参构造器中创建carMap
      public InstanceCarFactory(){
          carMap = new HashMap<Long, Car>();
          carMap.put(1L,new Car(1L,"宝马"));
          carMap.put(2L,new Car(2L,"法拉利"));
      }
  
      public Car getCar(Long id){
          return carMap.get(id);
      }
  }
  ```

* ```xml
  <!--  配置实例工厂  -->
  <bean id="carFactory" class="com.rango.factory.InstanceCarFactory"></bean>
  
  <!--  依赖实例工厂，创建car，调用getCar  -->
  <bean id="car2" factory-bean="carFactory" factory-method="getCar">
      <constructor-arg name="id" value="2"/>
  </bean>
  ```

* ```java
  public class TestInstanceFactory {
      public static void main(String[] args) {
          ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring-factory.xml");
          Car car = (Car) applicationContext.getBean("car2");
          System.out.println(car);
      }
  }
  ```

  





## 1.10 IoC自动装载(Autowire)

IoC负责创建对象，DI负责完成对象的依赖注入，通过配置Property标签的ref属性来完成，同时Spring提供了另一种更加间接的依赖注入方法，即自动装载，不用手动配置property,IoC容器会自动选择bean完成注入。

自动装载有两种方式：

​	1）byname:通过属性名自动装载

```xml
<bean id="car" class="com.wiggin.entity.Car">
	<property name="id" value="1"></property>
	<property name="name" value="宝马"></property>
</bean>
<bean id="person" class="com.wiggin.entity.Person" autowire="byName">
    <property name="id" value="1"></property>
    <property name="name" value="wiggin"></property>
</bean>
```

​	2）bytype:通过属性类型装载

```xml
<bean id="person2" class="com.wiggin.entity.Person" autowire="byType">
    <property name="id" value="1"></property>
    <property name="name" value="wiggin"></property>
</bean>
```



## 1.11 AOP面向切面编程

**1.11.1 JDK动态代理：**

* 通过动态代理，将共同业务代码集成为一个代理类，该类动态生成，每次使用时调用代理类。

  动态代理是指利用Java的反射技术(Java Reflection)，在运行时创建一个实现某些给定接口的新类（也称“动态代理类”）及其实例（对象）,代理的是接口(Interfaces)，不是类(Class)，也不是抽象类。在运行时才知道具体的实现。

  **AOP通常使用动态代理，在执行业务方法或出现异常前后时加入相关逻辑。**

* 定义四则运算接口

  ```java
  public interface Cal {
      public int add(int num1,int num2);
      public int sub(int num1,int num2);
      public int mul(int num1,int num2);
      public int div(int num1,int num2);
  }
  ```

* 实现接口

  ```java
  public class CalImpl implements Cal{
      public int add(int num1, int num2) {
  //        System.out.println("add方法参数是["+num1+","+num2+"]");
          int result = num1+num2;
  //        System.out.println("add方法的结果是："+result);
          return result;
      }
  
      public int sub(int num1, int num2) {
  //        System.out.println("sub方法参数是["+num1+","+num2+"]");
          int result = num1-num2;
  //        System.out.println("sub方法结果是："+result);
          return result;
      }
  
      public int mul(int num1, int num2) {
  //        System.out.println("mul方法参数是["+num1+","+num2+"]");
          int result = num1*num2;
  //        System.out.println("mul方法结果是："+result);
          return result;
      }
  
      public int div(int num1, int num2) {
  //        System.out.println("div方法参数是["+num1+","+num2+"]");
          int result = num1/num2;
  //        System.out.println("div方法结果是："+result);
          return result;
      }
  }
  ```

* 将输出参数和结果的非业务代码块使用AOP处理

  ```java
  public class MyInvocationHandler implements InvocationHandler {
      //接收委托对象；
      private Object object = null;
  
      // 将委托对象传入代理对象，用这个类创建动态代理类
      public Object bind(Object object){
          this.object = object;
          //object.getClass()获得委托对象的运行时类，
          //.getClassLoader()获取类加载器
          //object.getClass().getInterfaces()代表：委托对象的功能代理类必须全部拥有，根据它创建功能一样的代理类
          //(InvocationHandler) this：动态代理方法在执行时，会调用this里面的invoke方法去执行
          return Proxy.newProxyInstance(object.getClass().getClassLoader(),object.getClass().getInterfaces(),                        		   (InvocationHandler) this);
      }
  
      public Object invoke(Object proxy, Method method,Object[] args) throws Throwable {
          System.out.println(method.getName()+"方法的参数是"+ Arrays.toString(args));
          //通过委托对象自己传入参数，调用对应方法产生结果
          Object result = method.invoke(this.object, args);
          System.out.println(method.getName()+"的结果是"+result);
          return result;
      }
  }
  ```

  ```java
  public class Test {
      public static void main(String[] args) {
          Cal cal = new CalImpl();
          MyInvocationHandler myInvocationHandler = new MyInvocationHandler();
          Cal cal1 = (Cal) myInvocationHandler.bind(cal);
          cal1.add(1,1);
          cal1.sub(2,1);
          cal1.div(6,2);
          cal1.mul(3,2);
      }
  }
  ```

  以上是JDK动态代理通过反射来接收被代理的类，并且要求被代理的类必须实现一个接口(`public class CalImpl implements Cal`)，核心是`InvocationHandler`接口和`Proxy`类。

  动态代理来实现AOP的过程，过程复杂，不好理解，Spring框架对AOP进行了封装，使用Spring框架可以用面向对象的思想来实现AOP。

  Spring框架种不需要创建InvocationHandler,只需要创建一个切面对象，将所有的非业务代码在切面对象种完成即可，Spring框架底层会自动根据切面类以及目标类生成代理对象。

  

**1.11.2  Spring封装后的AOP实现方法**

* 将CalImpl类注册为组件，交给IoC管理。

  ```java
  @Component
  public class CalImpl implements Cal{
      public int add(int num1, int num2) {
  //        System.out.println("add方法参数是["+num1+","+num2+"]");
          int result = num1+num2;
  //        System.out.println("add方法的结果是："+result);
          return result;
      }
  
      public int sub(int num1, int num2) {
  //        System.out.println("sub方法参数是["+num1+","+num2+"]");
          int result = num1-num2;
  //        System.out.println("sub方法结果是："+result);
          return result;
      }
  
      public int mul(int num1, int num2) {
  //        System.out.println("mul方法参数是["+num1+","+num2+"]");
          int result = num1*num2;
  //        System.out.println("mul方法结果是："+result);
          return result;
      }
  
      public int div(int num1, int num2) {
  //        System.out.println("div方法参数是["+num1+","+num2+"]");
          int result = num1/num2;
  //        System.out.println("div方法结果是："+result);
          return result;
      }
  }
  ```

  

* 创建切面，并注册为组件，交给IoC管理。

  ```java
  //成为切面对象
  @Aspect
  //标记为组件，交给IoC管理
  @Component
  public class LoggerAspect {
      // 传入实体类，切割实体类种的所有方法，在执行业务之前，*为方法的通配符，(..)为参数的通配符
      // @Before表示执行的具体时机
      @Before(value = "execution(public int com.rango.utils.impl.CalImpl.*(..))")
      // JoinPoint joinPoint（连接点）获得目标方法的相关信息
      public void before(JoinPoint joinPoint){
          //获取方法名
          String name = joinPoint.getSignature().getName();
          //获取参数
          String args = Arrays.toString(joinPoint.getArgs());
          System.out.println(name+"方法的参数是："+args);
      }
  
      @After(value = "execution(public int com.rango.utils.impl.CalImpl.*(..))")
      public void after(JoinPoint joinPoint){
          //获取方法名
          String name = joinPoint.getSignature().getName();
          System.out.println(name+"方法执行了");
      }
  
      // 将result交给 @AfterReturning，然后给了public void afterReturning
      //在返回结果之后建立连接点
      @AfterReturning(value = "execution(public int com.rango.utils.impl.CalImpl.*(..))", returning = "result")
      public void afterReturning(JoinPoint joinPoint, Object result){
          String name = joinPoint.getSignature().getName();
          System.out.println(name+"方法执行结果为"+result);
      }
  
      // 将result交给 @AfterThrowing，然后给了public void afterThrowing
      @AfterThrowing(value = "execution(public int com.rango.utils.impl.CalImpl.*(..))", throwing = "exception")
      public void afterThrowing(JoinPoint joinPoint, Exception exception){
          String name = joinPoint.getSignature().getName();
          System.out.println(name+"方法执行异常"+exception);
      }
  }
  ```

  

* 修改xml配置文件

  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans xmlns="http://www.springframework.org/schema/beans"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns:aop="http://www.springframework.org/schema/aop"
         xmlns:context="http://www.springframework.org/schema/context"
         xsi:schemaLocation="http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd
           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
           http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop-2.5.xsd">
  
      <!-- 自动扫描 在指定包下寻找带有component注解的组件-->
      <context:component-scan base-package="com.rango"></context:component-scan>
  
      <!-- aspect注解生效，为目标类自动生成代理对象-->
      <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
  </beans>
  ```

  

* 测试AOP执行效果

  ```java
  public class Test {
      public static void main(String[] args) {
          // 创建委托对象
          Cal cal = new CalImpl();
  
          MyInvocationHandler myInvocationHandler = new MyInvocationHandler();
          //得到代理对象，代理对象具有委托对象的全部功能
          Cal cal1 = (Cal) myInvocationHandler.bind(cal);
          cal1.add(1,1);
          cal1.sub(2,1);
          cal1.div(6,2);
          cal1.mul(3,2);
      }
  }
  ```

# 二、SpringBoot

## 2.1 SpringBoot

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.3.5.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
</parent>
```

`springboot`具有一个版本控制中心，因此大多数依赖导入时都不需要配置版本号。

`spring-boot-starter`：场景启动器，可以自动帮我们导入相关场景的依赖。

## 2.2 Spring常用注解

1、`@EnableDiscoveryClient`：

* 将微服务注册到服务发现组件上，使得注册中心能够发现该服务。

2、`@RequestBody`: 

* 获取请求体，只支持post请求。

3、`@SpringBootApplication`: 

* 标记主配置类，通过运行该主配置类的main方法开启Springboot。

4、`@controller` :（注入服务）

* 用于标注控制层，相当于struts中的action层。

5、`@service` : （注入dao）

- 用于标注服务层，主要用来进行业务的逻辑处理。

6、`@repository`（实现dao访问）

- 用于标注数据访问层，也可以说用于标注数据访问组件，即DAO组件。

7、`@Component`：

* 把普通pojo实例化到spring容器中，相当于`<bean id = "" class = "">`；`@component(“abc”)`相当于配置了`id = abc`

8、`@ComponentScan(value="packageName")`:

* 扫描包路径下的所有`@component`标记的类，自动装载到bean容器中，value值表示包名，没有则默认扫描该类所在的包下所有的配置类

9、`@Configuration`：

* 也属于组件component，主要目的是表明当前类是一个配置类（替代配置文件），作为bean定义的源，所标记的类允许通过调用同一类中的其他@Bean方法来定义bean之间的依赖关系

10、`@Bean `：给IoC容器中注册一个bean，类型就是返回值的类型，将方法的返回值添加到容器中，该组件的id默认使用方法名。

```java
@Bean
// 返回值类型为Person类， id为person01
public Person person01() {
    // 返回实例化对象
	return new Person("liayun", 20);
}
```

11、`@ConfigurationProperties`:

* 配置属性映射，properties文件中的属性与类中的属性对应

  ```java
  @ConfigurationProperties(prefix = "mycar")
  ```

12、`@EnableConfigurationProperties`:

* ```java
  @EnableConfigurationProperties(Car.class)
  ```

  * 开启Car配置绑定，使得properties文件中的属性与类中的属性对应
  * 将这个Car类组件自动注册到容器中

13、`@import`：

* 表示给容器中导入某个class。

  ```java
  @import({User.class, Student.class})
  ```

  在容器中自动创建上述两个类的组件，默认组件名为类的全限定名。

14、`@EnableAutoConfiguration`：

* `-> `@AutoConfigurationPackage

  ```java
  @Target({ElementType.TYPE})
  @Retention(RetentionPolicy.RUNTIME)
  @Documented
  @Inherited
  @Import({Registrar.class})
  public @interface AutoConfigurationPackage {
      String[] basePackages() default {};
  
      Class<?>[] basePackageClasses() default {};
  }
  ```

  作用：给容器中导入组件AutoConfigurationPackages.Registrar.class，利用Registrar将主配置类（MainApplication）所在的包及其子包的所有组件扫描到容器中

  * `@Target`:注解的作用目标
  
    **`@Target(ElementType.TYPE)`——接口、类、枚举、注解
    `@Target(ElementType.FIELD)`——字段、枚举的常量
    `@Target(ElementType.METHOD)`——方法
    `@Target(ElementType.PARAMETER)`——方法参数
    `@Target(ElementType.CONSTRUCTOR)` ——构造函数
    `@Target(ElementType.LOCAL_VARIABLE)`——局部变量
    `@Target(ElementType.ANNOTATION_TYPE)`——注解
    `@Target(ElementType.PACKAGE)`——包**
  
  * `@Retention`：注解的保留位置
  
    **`RetentionPolicy.SOURCE`**:	这种类型的`Annotations`只在源代码级别保留,编译时就会被忽略,在`class`字节码文件中不包含。
    **`RetentionPolicy.CLASS`:**	这种类型的`Annotations`编译时被保留,默认的保留策略,在`class`文件中存在,但`JVM`将会忽略,运行时无法获得。
    **`RetentionPolicy.RUNTIME`:**	这种类型的`Annotations`将被`JVM`保留,所以他们能在运行时被`JVM`或其他使用反射机制的代码所读取和使用。
    **`@Document`：**	说明该注解将被包含在`javadoc`中
    **`@Inherited`：**	说明子类可以继承父类中的该注解**
  
  

<u>**@Value和@ConfigurationProperties的区别**</u>

`@value` : 

* 在属性类中逐个添加该注解，可以在加载配置文件（外部xml文件配置好属性值）时，将属性的值动态注入Bean中。

  ```java
  public class Person {
      @Value("${person.last-name}")
      private String lastName;
  ```

`@ConfigurationProperties`：

* 不需要像 @value 逐个添加注解，而是将该属性类中的所有属性和配置文件中的属性映射，一一对应。

两者的区别：

|                      | @ConfigurationProperties | @Value     |
| :------------------- | :----------------------- | :--------- |
| 功能                 | 批量注入配置文件中的属性 | 一个个指定 |
| 松散绑定（松散语法） | 支持                     | 不支持     |
| SpEL                 | 不支持                   | 支持       |
| JSR303数据校验       | 支持                     | 不支持     |
| 复杂类型封装         | 支持                     | 不支持     |

- 松散绑定：属性匹配规则

   标准方式 person.firstName

   方式一： person.first-name

   方式二 ：person.first_name

- SpEL: `#{表达式}` 表达式内可以使用各类运算符

- JSR303数据校验： 对属性的格式校验，校验时在类上要标注@Validated，对字段的校验常见的有@Email(是否是合法的邮件地址)、@NotNull(是否非空)等

- 复杂类型：比如map、类等

配置文件yml还是properties他们都能获取到值；

如果只是在某个业务逻辑中需要获取一下配置文件中的某项值，使用@Value；

如果专门编写了一个javaBean来和配置文件进行映射，我们就直接使用@ConfigurationProperties；



`@Conditional`：

* 必须是@Conditional指定的条件成立，才给容器中添加组件，配置配里面的所有内容才生效；

* ```java
    @Bean
    //条件注解，只有TestConditional返回为true时，才能实例化Fox
    @Conditional``(value = TestConditional.class)
    public Fox fox(){
      	  //假如主类上使用了 @Configuration注解，cat()方法不会每次都返回一个新的cat对象，而是返回一个公共的代理对象
        System.out.println("test conditional");
        return new Fox(cat());
    }
  ```

| @Conditional扩展注解            | 作用（判断是否满足当前指定条件）                 |
| :------------------------------ | :----------------------------------------------- |
| @ConditionalOnJava              | 系统的java版本是否符合要求                       |
| @ConditionalOnBean              | 容器中存在指定Bean；                             |
| @ConditionalOnMissingBean       | 容器中不存在指定Bean；                           |
| @ConditionalOnExpression        | 满足SpEL表达式指定                               |
| @ConditionalOnClass             | 系统中有指定的类                                 |
| @ConditionalOnMissingClass      | 系统中没有指定的类                               |
| @ConditionalOnSingleCandidate   | 容器中只有一个指定的Bean，或者这个Bean是首选Bean |
| @ConditionalOnProperty          | 系统中指定的属性是否有指定的值                   |
| @ConditionalOnResource          | 类路径下是否存在指定资源文件                     |
| @ConditionalOnWebApplication    | 当前是web环境                                    |
| @ConditionalOnNotWebApplication | 当前不是web环境                                  |
| @ConditionalOnJndi              | JNDI存在指定项                                   |



**<u>@PropertySource、@ImportResource和@Bean的区别</u>**

1、`@PropertySource`：

* 加载指定路径的属性配置文件，可以指定多个配置文件

  ```java
  @PropertySource(value = {"classpath:person.properties"})
  ```

2、`@ImportResource`：

* 由于SpringBoot不支持Bean配置文件，编写的Bean配置也无法自动识别。所以想要加载Bean配置文件，需要在配置类上使用**@ImportResource**注解。
* 并且在beans.xml中配置的类不能再用properties和yml进行注入

* 导入Spring的Bean配置文件，并让配置文件里面的内容生效。

  ```java
  @ImportResource(locations = {"classpath:beans.xml"})
  ```

3、SpringBoot推荐使用全注解的方式进行Bean配置

* 首先添加@Configuration注解，指明当前类是一个配置类；来替代之前的Spring配置文件
* 使用@Bean注解给容器中添加组件



## 2.3 文件目录结构

`resources`目录下的文件：

* `static`: 保存所有的静态资源，如 js、css、images等。

* `templates`：保存所有的模板页面，默认不支持jsp页面，需要使用模板引擎。

* `application.properties`：应用配置文件。优先加载properties结尾的配置文件，然后再加载yml结尾的配置文件。

  * 开发中多采用application-{profile}.properties/yml的形式提供不同的配置文件，适应不同的开发场景。

  * 如application-dev.properties、application-prod.properties.
  * 通过在配置文件中添加spring.profiles.active=dev，可以激活不同环境的配置文件。

  

## 2.4 配置文件加载位置优先级

springboot 启动会扫描以下位置的application.properties或者application.yml文件作为Spring boot的默认配置文件

* –file:./config/
* –file:./
* –classpath:/config/
* –classpath:/

优先级由高到底，高优先级的配置会覆盖低优先级的配置，并与其他位置的配置形成**互补配置**；



## 2.5 自动配置原理

1、 SpringBoot启动时在主配置类时，开启了`@EnableAutoConfiguration`

2、 @EnableAutoConfiguration导入了`AutoConfigurationImportSelector.class `

3、 查看`getCandidateConfigurations`方法：

* ```java
  List<String> configurations = 
  SpringFactoriesLoader.loadFactoryNames(this.getSpringFactoriesLoaderFactoryClass(), this.getBeanClassLoader());
  ```

  其中`getSpringFactoriesLoaderFactoryClass() `返回 `EnableAutoConfiguration.class`：

  ```java
      protected Class<?> getSpringFactoriesLoaderFactoryClass() {
          return EnableAutoConfiguration.class;
  ```

* 进入`loadFactoryNames`方法可以发现，会在`META-INF/spring.factories`文件下搜索 EnableAutoConfiguration，然后将类路径下`META-INF/spring.factories` 里面配置的EnableAutoConfiguration中所有的值加入到了容器中,进行相关组件的自动配置。

  ```
  # Auto Configure
  org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
  org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
  org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
  org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
  org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
  org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
  org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
  org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
  org.springframework.boot.autoconfigure.context.LifecycleAutoConfiguration,\
  org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
  org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
  org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.elasticsearch.ReactiveElasticsearchRestClientAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.jdbc.JdbcRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.mongo.MongoReactiveDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.mongo.MongoReactiveRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.r2dbc.R2dbcDataAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.r2dbc.R2dbcRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.r2dbc.R2dbcTransactionManagerAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
  org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
  org.springframework.boot.autoconfigure.elasticsearch.ElasticsearchRestClientAutoConfiguration,\
  org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
  org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
  org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
  org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
  org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
  org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
  org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
  org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
  org.springframework.boot.autoconfigure.http.HttpMessageConvertersAutoConfiguration,\
  org.springframework.boot.autoconfigure.http.codec.CodecsAutoConfiguration,\
  org.springframework.boot.autoconfigure.influx.InfluxDbAutoConfiguration,\
  org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
  org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
  org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
  org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
  org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
  org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
  org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
  org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
  org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
  org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
  org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
  org.springframework.boot.autoconfigure.jsonb.JsonbAutoConfiguration,\
  org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
  org.springframework.boot.autoconfigure.availability.ApplicationAvailabilityAutoConfiguration,\
  org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
  org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
  org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
  org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
  org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
  org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
  org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
  org.springframework.boot.autoconfigure.mongo.MongoReactiveAutoConfiguration,\
  org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
  org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
  org.springframework.boot.autoconfigure.quartz.QuartzAutoConfiguration,\
  org.springframework.boot.autoconfigure.r2dbc.R2dbcAutoConfiguration,\
  org.springframework.boot.autoconfigure.rsocket.RSocketMessagingAutoConfiguration,\
  org.springframework.boot.autoconfigure.rsocket.RSocketRequesterAutoConfiguration,\
  org.springframework.boot.autoconfigure.rsocket.RSocketServerAutoConfiguration,\
  org.springframework.boot.autoconfigure.rsocket.RSocketStrategiesAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.rsocket.RSocketSecurityAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.saml2.Saml2RelyingPartyAutoConfiguration,\
  org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
  org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.oauth2.client.servlet.OAuth2ClientAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.oauth2.client.reactive.ReactiveOAuth2ClientAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.oauth2.resource.servlet.OAuth2ResourceServerAutoConfiguration,\
  org.springframework.boot.autoconfigure.security.oauth2.resource.reactive.ReactiveOAuth2ResourceServerAutoConfiguration,\
  org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
  org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration,\
  org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration,\
  org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
  org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
  org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
  org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.client.RestTemplateAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.embedded.EmbeddedWebServerFactoryCustomizerAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.reactive.HttpHandlerAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.reactive.ReactiveWebServerFactoryAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.reactive.WebFluxAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.reactive.error.ErrorWebFluxAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.reactive.function.client.ClientHttpConnectorAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.reactive.function.client.WebClientAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.servlet.MultipartAutoConfiguration,\
  org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
  org.springframework.boot.autoconfigure.websocket.reactive.WebSocketReactiveAutoConfiguration,\
  org.springframework.boot.autoconfigure.websocket.servlet.WebSocketServletAutoConfiguration,\
  org.springframework.boot.autoconfigure.websocket.servlet.WebSocketMessagingAutoConfiguration,\
  org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration,\
  org.springframework.boot.autoconfigure.webservices.client.WebServiceTemplateAutoConfiguration
  ```

4、 每一个自动配置类进行自动配置功能；

* 以**HttpEncodingAutoConfiguration（Http编码自动配置）**为例解释自动配置原理

  ```java
  //表示这是一个配置类，以前编写的配置文件一样，也可以给容器中添加组件
  @Configuration   
  //启动指定类的ConfigurationProperties功能；将配置文件中对应的值和HttpEncodingProperties绑定起来；并把HttpEncodingProperties加入到ioc容器中
  @EnableConfigurationProperties(HttpEncodingProperties.class)  
  //Spring底层@Conditional注解（Spring注解版），根据不同的条件，如果满足指定的条件，整个配置类里面的配置就会生效；    判断当前应用是否是web应用，如果是，当前配置类生效
  @ConditionalOnWebApplication 
  //判断当前项目有没有这个类CharacterEncodingFilter；SpringMVC中进行乱码解决的过滤器；
  @ConditionalOnClass(CharacterEncodingFilter.class)  
  //判断配置文件中是否存在某个配置spring.http.encoding.enabled；即使不存在spring.http.encoding.enabled=true，也是默认为true
  @ConditionalOnProperty(prefix = "spring.http.encoding", value = "enabled", matchIfMissing = true)  
  public class HttpEncodingAutoConfiguration {
    
    	//该属性已经和SpringBoot的配置文件映射了
    	private final HttpEncodingProperties properties;
    
     //只有一个有参构造器的情况下，参数的值就会根据配置文件的映射从容器中取值
    	public HttpEncodingAutoConfiguration(HttpEncodingProperties properties) {
  		this.properties = properties;
  	}
      
    	//给容器中添加一个组件，这个组件的某些值需要从properties中获取
      @Bean   
      //判断容器没有这个组件
  	@ConditionalOnMissingBean(CharacterEncodingFilter.class) 
  	public CharacterEncodingFilter characterEncodingFilter() {
  		CharacterEncodingFilter filter = new OrderedCharacterEncodingFilter();
  		filter.setEncoding(this.properties.getCharset().name());
  		filter.setForceRequestEncoding(this.properties.shouldForce(Type.REQUEST));
  		filter.setForceResponseEncoding(this.properties.shouldForce(Type.RESPONSE));
  		return filter;
  	}
  
  ```

  一但这个配置类生效；这个配置类就会给容器中添加各种组件；这些组件的属性是从对应的properties类中获取的，这些类里面的每一个属性又是和配置文件绑定的。

**精髓：**

 **1）、SpringBoot启动会加载大量的自动配置类**

 **2）、我们看我们需要的功能有没有SpringBoot默认写好的自动配置类；**

 **3）、我们再来看这个自动配置类中到底配置了哪些组件；（只要我们要用的组件有，我们就不需要再来配置了）**

 **4）、给容器中自动配置类添加组件的时候，会从properties类中获取某些属性。我们就可以在配置文件中指定这些属性的值；**



小技巧：

**可以通过在配置文件中配置debug=true属性；来让控制台打印自动配置报告**



## 2.6 引入静态资源

**<u>2.6.1 使用webjars以jar包的方式</u>**

​		https://www.webjars.org/

* 访问方式：

  所有`/webjars/**`，都可以在calsspath:/META-INF/resources/webjars/内找到资源。例如：**localhost:8080/webjars/jquery/3.3.1/jquery.js**

* 引入后静态资源管理目录：	

![webjars](E:\Down\文献阅读\JavaNote\Spring&SpringBoot\Pictures\webjars.jpg)



* jar包引入方式：

  在pom文件中添加相应版本的依赖：

  ```xml
  <dependency>
      <groupId>org.webjars</groupId>
      <artifactId>jquery</artifactId>
      <version>3.3.1</version>
  </dependency>
  ```

 <u>`/**`**路径下访问当前项目的任何资源，都会去下列路径下寻找静态资源:**</u>

```java
"classpath:/META-INF/resources/"
"classpath:/resources/"
"classpath:/static/"
"classpath:/public/
"/":当前项目的根路径
```

例如：访问`localhost:8080/objectFile` -> 如果没有得到相应处理，都会去上述路径下寻找objectFile这个静态资源

<u>**欢迎页：**</u> 

/**请求都会被映射到静态资源文件夹下的所有index.html页面

例如：访问localhost：8080/，自动会跳转到静态资源文件夹下的index.html页面。 

**<u>图标文件</u>**:

所有的**/favico.ico 都会在静态资源文件下搜索：

<u>如果想要修改静态资源文件夹路径，可以在properties文件中配置`spring.resources.static-locations`属性指定，指定之后默认文件夹会失效。</u>

<u>**静态资源访问前缀**</u>：

对静态资源的访问都有指定的前缀

```yaml
spring:
	mvc:
		static-path-pattern: /res/**
```



## 2.6 模板引擎

```java
@ConfigurationProperties(prefix = "spring.thymeleaf")
public class ThymeleafProperties {

	private static final Charset DEFAULT_ENCODING = Charset.forName("UTF-8");

	private static final MimeType DEFAULT_CONTENT_TYPE = MimeType.valueOf("text/html");

	public static final String DEFAULT_PREFIX = "classpath:/templates/";

	public static final String DEFAULT_SUFFIX = ".html";
  	//
```

只要把HTML页面放到template文件夹下，则自动会被Thymleaf渲染

使用方法：

* 在HTML页面中导入名称空间：

  ```html
  <html lang="en" xmlns:th="http://www.thymeleaf.org">
  ```

* 使用Thymleaf语法：

  ```html
  <!DOCTYPE html>
  <html lang="en" xmlns:th="http://www.thymeleaf.org">
  <head>
      <meta charset="UTF-8">
      <title>Title</title>
  </head>
  <body>
      <h1>hello to somebody</h1>
      <div th:text="${name}">显示欢迎信息</div>
  </body>
  </html>
  ```

* 结合Controller映射：

  ```java
  @RequestMapping("hello/{name}")
  public String helloWho(@PathVariable("name") String id){
      System.out.println("hello to "+id);
      // 页面名
      return "helloWho";
  }
  ```





## 2.7 SpringMvc自动配置

官方文档：https://docs.spring.io/spring-boot/docs/1.5.10.RELEASE/reference/htmlsingle/#boot-features-developing-web-applications

<u>**1）Spring MVCantoconfiguration**</u> 

Spring Boot自动配置好了Spring MVC

以下是Spring Boot对Spring MVC的默认配置：

- Inclusion of `ContentNegotiatingViewResolver` and `BeanNameViewResolver` beans.

  - 自动配置了ViewResolver(视图解析器：根据方法的返回值得到视图对象（View）,视图对象决定是否渲染（转发？重定向）)

   - `ContentNegotiatingViewResolver` 组合所有的视图解析器

     ```java
     @Nullable
     public View resolveViewName(String viewName, Locale locale) throws Exception {
         RequestAttributes attrs = RequestContextHolder.getRequestAttributes();
         Assert.state(attrs instanceof ServletRequestAttributes, "No current ServletRequestAttributes");
         List<MediaType> requestedMediaTypes = this.getMediaTypes(((ServletRequestAttributes)attrs).getRequest());
         if (requestedMediaTypes != null) {
             // 获取候选的视图
             List<View> candidateViews = this.getCandidateViews(viewName, locale, requestedMediaTypes);
             // 从候选视图中取出最合适的视图
             View bestView = this.getBestView(candidateViews, requestedMediaTypes, attrs);
             if (bestView != null) {
                 return bestView;
             }
         }
     ```

   - **如何定制**：给容器中添加一个视图解析器,  `ContentNegotiatingViewResolver` 就会将视图解析出来

     ```java
     @Bean
     public ViewResolver myViewResolver(){
         return new MyViewResolver();
     }
     
     private static class MyViewResolver implements ViewResolver{
         @Override
         public View resolveViewName(String s, Locale locale) throws Exception {
             return null;
         }
     }
     ```

     ![image-20201220123813560](C:/Users/li/AppData/Roaming/Typora/typora-user-images/image-20201220123813560.png)

- Support for serving static resources, including support for WebJars (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-static-content))).

  静态资源文件夹webjars

- Static `index.html` support.      静态首页访问

- Custom `Favicon` support (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-favicon)).     支持定制favicon.ico图标文件

  

- Automatic registration of `Converter`, `GenericConverter`, and `Formatter` beans.

  - `Converter`:转换器 -- >public String hello(User user) :自动装载后，前后端交互需要类型转换

  - `Formatter`：格式化器--> 2020/11/11===Data,按照一定的格式进行转换

    ```java
     @Bean
    public FormattingConversionService mvcConversionService() {
          Format format = this.mvcProperties.getFormat();
    	  WebConversionService conversionService = new WebConversionService((new DateTimeFormatters())
    	  .dateFormat(format.getDate())
    	  .timeFormat(format.getTime())
    	  .dateTimeFormat(format.getDateTime()));
           this.addFormatters(conversionService);
           return conversionService;
            }
    ```

    ```java
    public class DateTimeFormatters {
        private DateTimeFormatter dateFormatter;
        private String datePattern;
        private DateTimeFormatter timeFormatter;
        private DateTimeFormatter dateTimeFormatter;
    
        public DateTimeFormatters() {
        }
    
        public DateTimeFormatters dateFormat(String pattern) {
            if (isIso(pattern)) {
                this.dateFormatter = DateTimeFormatter.ISO_LOCAL_DATE;
                this.datePattern = "yyyy-MM-dd";
            } else {
                this.dateFormatter = formatter(pattern);
                this.datePattern = pattern;
            }
    
            return this;
        }
    ```

    DateTimeFormatters中封装了各种格式的时间形式

- Support for `HttpMessageConverters` (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-message-converters)).

  - `HttpMessageConverters`:SpringMVC的消息转换器用来转换Http请求和响应User-json

  - 从底层中获取所有的HttpMessageConverter

    ```java
    public class HttpMessageConverters implements Iterable<HttpMessageConverter<?>> {
        private static final List<Class<?>> NON_REPLACING_CONVERTERS;
        private final List<HttpMessageConverter<?>> converters;
    
        public HttpMessageConverters(HttpMessageConverter<?>... additionalConverters) {
            this((Collection)Arrays.asList(additionalConverters));
        }
    
        public HttpMessageConverters(Collection<HttpMessageConverter<?>> additionalConverters) {
            this(true, additionalConverters);
        }
    ```

  - 也可以**自定义**HttpMessageConverter，用bean或者component的方式注册到容器中

    ```java
    import org.springframework.boot.autoconfigure.web.HttpMessageConverters;
    import org.springframework.context.annotation.*;
    import org.springframework.http.converter.*;
    
    @Configuration
    public class MyConfiguration {
    
        @Bean
        public HttpMessageConverters customConverters() {
            HttpMessageConverter<?> additional = ...
            HttpMessageConverter<?> another = ...
            return new HttpMessageConverters(additional, another);
        }
    
    }
    ```

- Automatic registration of `MessageCodesResolver` (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-message-codes)). 定义错误代码生成规则

- Automatic use of a `ConfigurableWebBindingInitializer` bean (covered [later in this document](https://docs.spring.io/spring-boot/docs/current/reference/html/spring-boot-features.html#boot-features-spring-mvc-web-binding-initializer)).

  同样的，也可以自定义`ConfigurableWebBindingInitializer` ，再注册到容器中即可

  ```
  初始化WebDataBinder;
  请求数据绑定到====>JavaBean中
  ```

**org.springframework.boot.autoconfigure.web:web中的所有场景**

If you want to keep those Spring Boot MVC customizations and make more [MVC customizations](https://docs.spring.io/spring/docs/5.3.1/reference/html/web.html#mvc) (interceptors, formatters, view controllers, and other features), you can add your own `@Configuration` class of type `WebMvcConfigurer` but **without** `@EnableWebMvc`.

* **自定义Mvc(如interceptors, formatters, view controllers, and other features等）**

  ```xml
    <mvc:view-controller path="/hello" view-name="success">
          <mvc:interceptors>
              <mvc:mapping path="/hello">
              <bean></bean>
          </mvc:interceptors>
      </mvc:view-controller>
  ```

  编写一个配置类（@Configuration），是`WebMvcConfigurer`类型而不是 `@EnableWebMvc`.

  既保留了所有的自动配置，也能使用扩展配置

  ```java
  package com.kkp.springboot04.config;
  
  import org.springframework.context.annotation.Configuration;
  import org.springframework.web.servlet.config.annotation.ViewControllerRegistry;
  import org.springframework.web.servlet.config.annotation.WebMvcConfigurationSupport;
  
  
  
  @Configuration
  public class MyMvcConfig extends WebMvcConfigurationSupport {
  
      @Override
      protected void addViewControllers(ViewControllerRegistry registry) {
          // super.addViewControllers(registry);
          // 效果，浏览器发送/wiggin请求，也来到success页面
          registry.addViewController("/kkp").setViewName("success");
      }
  }
  
  ```

  原理：

    - WebMvcAutoConfiguration是SpringMVC的自动配置类

    - 在做其他自动配置时会导入@Import({WebMvcAutoConfiguration.EnableWebMvcConfiguration.class})

      ```java
      @Configuration(
          proxyBeanMethods = false
      )
      public static class EnableWebMvcConfiguration extends DelegatingWebMvcConfiguration implements ResourceLoaderAware {
          
      	// 获取所有的WebMvcConfigurer
          @Autowired(
              required = false
          )
          public void setConfigurers(List<WebMvcConfigurer> configurers) {
              if (!CollectionUtils.isEmpty(configurers)) {
                  this.configurers.addWebMvcConfigurers(configurers);
              }
              // 一个实例：将WebMvcConfigurer的所有相关配置一起调用
             /*
             // 可以看到addViewControllers方法通过遍历把所有配置加载到delegate
             public void addViewControllers(ViewControllerRegistry registry) {
              Iterator var2 = this.delegates.iterator();
      
              while(var2.hasNext()) {
                  WebMvcConfigurer delegate = (WebMvcConfigurer)var2.next();
                  delegate.addViewControllers(registry);
              }
      
          }
          */
      
          }
      ```

  - 容器中所有的WebMvcConfigurer都会一起起作用

  - 自定的config配置类也会被调用

    效果：SpringMVC和我们的扩展配置都会器作用

    

If you want to provide custom instances of `RequestMappingHandlerMapping`, `RequestMappingHandlerAdapter`, or `ExceptionHandlerExceptionResolver`, and still keep the Spring Boot MVC customizations, you can declare a bean of type `WebMvcRegistrations` and use it to provide custom instances of those components.



**如何修改SpringBoot默认配置：**

* SpringBoot在自动配置很多组件的时候，先看容器中有没有用户自己配置的（@Bean、@Component）如果有就用用户配置的，如果没有，才自动配置；如果有些组件可以有多个（ViewResolver）将用户配置的和自己默认的组合起来；

* 在SpringBoot中会有非常多的xxxConfigurer帮助我们进行扩展配置 

* 在SpringBoot中会有很多的xxxCustomizer帮助我们进行定制配置



**<u>全面接管Spring MVC</u>**

If you want to take complete control of Spring MVC, you can add your own `@Configuration` annotated with `@EnableWebMvc`, or alternatively add your own `@Configuration`-annotated `DelegatingWebMvcConfiguration` as described in the Javadoc of `@EnableWebMvc`.



SpringBoot对SpringMVC的自动配置不需要s's's了，所有都是自定义配置，但需要在配置类中添加@EnableWebMvc

```java
@EnableWebMvc
@Configuration
public class MyMvcConfig extends WebMvcConfigurer {

    @Override
    protected void addViewControllers(ViewControllerRegistry registry) {
        // super.addViewControllers(registry);
        // 效果，浏览器发送/wiggin请求，也来到success页面
        registry.addViewController("/wiggin").setViewName("success");
    }
}
```

全面接管后所有的SpringMVC默认配置就失效了，方法需要重新进行书写。

失效的原理：

  - @EnableWebMvc的核心

    ```java
    @Retention(RetentionPolicy.RUNTIME)
    @Target({ElementType.TYPE})
    @Documented
    @Import({DelegatingWebMvcConfiguration.class})
    public @interface EnableWebMvc {
    }
    
    ```

- 而DelegatingWebMvcConfiguration的主要实现：

  ```java
  @Configuration(
      proxyBeanMethods = false
  )
  public class DelegatingWebMvcConfiguration extends WebMvcConfigurationSupport {
  ```

- 而在基本配置类中

  ```java
  // 之前的@EnableWebMvc已经导入了WebMvcConfigurationSupport这个类，所以这个判断失效了，下面的自动WebMvcAutoConfiguration也就失效了
  @ConditionalOnMissingBean({WebMvcConfigurationSupport.class})
  @AutoConfigureOrder(-2147483638)
  @AutoConfigureAfter({DispatcherServletAutoConfiguration.class, TaskExecutionAutoConfiguration.class, ValidationAutoConfiguration.class})
  public class WebMvcAutoConfiguration {
  ```

- @EnableWebMvc全面接管了SpringMVC，但它只是最基本的功能，需要继续书写。

## 2.8 国际化

1. 配置需要国际化的元素：

   ```xml
   login.btn=登陆~
   login.password=密码~
   login.remember=记住我~
   login.tip=请登陆~
   login.username=用户名~
   ```

   ![image-20201220154919946](C:/Users/li/AppData/Roaming/Typora/typora-user-images/image-20201220154919946.png)

2. SpringBoot自动配置好了管理国际化资源文件的组件，需要在spring配置文件中指定国际化配置文件的位置：

   ```xml
   spring.messages.basename=i18n/login
   ```

3. 使用#{}语法，修改HTML页面，获取国际化值

   ```html
   <!DOCTYPE html>
   <html lang="en"  xmlns:th="http://www.thymeleaf.org">
   	<head>
   		<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
   		<meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
   		<meta name="description" content="">
   		<meta name="author" content="">
   		<title>Signin Template for Bootstrap</title>
   		<!-- Bootstrap core CSS -->
   		<link href="asserts/css/bootstrap.min.css" th:href="@{/webjars/bootstrap/4.0.0/css/bootstrap.css}" rel="stylesheet">
   		<!-- Custom styles for this template -->
   		<link href="asserts/css/signin.css" th:href="@{/asserts/css/signin.css}" rel="stylesheet">
   	</head>
   	<body class="text-center">
   		<form class="form-signin" action="dashboard.html" th:action="@{/user/login}" method="post">
   			<img class="mb-4" th:src="@{/asserts/img/bootstrap-solid.svg}" src="asserts/img/bootstrap-solid.svg" alt="" width="72" height="72">
   			<h1 class="h3 mb-3 font-weight-normal" th:text="#{login.tip}">Please sign in</h1>
   			<!--判断-->
   			<p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
   			<label class="sr-only" th:text="#{login.username}">Username</label>
   			<input type="text"  name="username" class="form-control" placeholder="Username" th:placeholder="#{login.username}" required="" autofocus="">
   			<label class="sr-only" th:text="#{login.password}">Password</label>
   			<input type="password" name="password" class="form-control" placeholder="Password" th:placeholder="#{login.password}" required="">
   			<div class="checkbox mb-3">
   				<label>
   					<!-- 行内写法[[]] -->
             			<input type="checkbox" value="remember-me"/> [[#{login.remember}]]
           		</label>
   			</div>
   			<button class="btn btn-lg btn-primary btn-block" type="submit" th:text="#{login.btn}">Sign in</button>
   			<p class="mt-5 mb-3 text-muted">© 2017-2018</p>
   			<!--在访问按钮上添加超链接并且携带区域信息，通过信息来判断使用何种语言-->
   			<a class="btn btn-sm" th:href="@{/index.html(l='zh_CN')}">中文</a>
   			<a class="btn btn-sm" th:href="@{/index.html(l='en_US')}">English</a>
   		</form>
   	</body>
   
   </html>
   ```

   

4. 自定义LocaleResolver

   ```java
   /**
    * 可以在连接上携带区域信息
    */
   public class MyLocaleResolver implements LocaleResolver {
       
       @Override
       public Locale resolveLocale(HttpServletRequest request) {
           // 获取包含"l"的请求参数
           String l = request.getParameter("l");
           // 获得默认的区域信息
           Locale locale = Locale.getDefault();
           // 将参数分隔得到区域信息
           if(!StringUtils.isEmpty(l)){
               String[] split = l.split("_");
               locale = new Locale(split[0],split[1]);
           }
           return locale;
       }
   
       @Override
       public void setLocale(HttpServletRequest request, HttpServletResponse response, Locale locale) {
   
       }
   }
   ```

5. 在SpringMvc配置文件添加自定义的LocaleResolver到容器中

   ```java
   @Bean
   public LocaleResolver localeResolver(){
   
       return new MyLocaleResolver();
   }
   ```

   

## 2.9 使用addViewController实现无业务逻辑跳转



```java
@Configuration
public class MyMvcConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
       // super.addViewControllers(registry);
        //浏览器发送 /index 请求来到 index
        registry.addViewController("/index").setViewName("index");
    }
```

等价于

```java
@Controller
public class HelloController {

    @RequestMapping({"/index"})
    public String index(){
        return "index";
    }
```



## 2.10 登录界面

* 开发期间对模板引擎的修改，如需要立即生效，应禁用缓存，并对修改后的模板ctrl+F9进行重新编译。

  ```java
  # 禁用缓存,页面修改后立即同步
  spring.thymeleaf.cache=false 
  ```

* 用户名和密码校验

  ```java
  @Controller
  public class LoginController {
      @PostMapping(value = "/user/login")
      public String login(@RequestParam("username") String username,
                          @RequestParam("password") String password,
                          Map<String,Object> map, HttpSession session){
          if(!StringUtils.isEmpty(username) && "123456".equals(password)){
              // 设置loginUser属性对应的对象为username
              session.setAttribute("loginUser",username);
              //登陆成功，防止表单重复提交，可以重定向到主页
              return "redirect:/main.html";
          }else{
              //登陆失败
              map.put("msg","用户名密码错误");
              return  "login";
          }
      }
  }
  ```

  如果登录错误，msg对应于模板引擎:

  ```html
  <!--判断-->
  <p style="color: red" th:text="${msg}" th:if="${not #strings.isEmpty(msg)}"></p>
  ```

* 配置拦截器

  ```java
  public class LoginHandlerInterceptor implements HandlerInterceptor {
      //目标方法执行之前
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          // 获取loginUser属性对应的对象
          Object user = request.getSession().getAttribute("loginUser");
          if(user == null){
              //未登陆，返回登陆页面
              request.setAttribute("msg","没有权限请先登陆");
              request.getRequestDispatcher("/index.html").forward(request,response);
              return false;
          }else{
              //已登陆，放行请求
              return true;
          }
  
      }
  
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
  
      }
  
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
  
      }
  }
  ```

* 注册拦截器

  ```java
  @Bean //将组件注册在容器
  public WebMvcConfigurerAdapter webMvcConfigurerAdapter(){
      WebMvcConfigurerAdapter adapter = new WebMvcConfigurerAdapter() {
          @Override
          public void addViewControllers(ViewControllerRegistry registry) {
              registry.addViewController("/").setViewName("login");
              registry.addViewController("/index.html").setViewName("login");
              registry.addViewController("/main.html").setViewName("dashboard");
          }
  
          //注册拦截器
          @Override
          public void addInterceptors(InterceptorRegistry registry) {
              //super.addInterceptors(registry);
              //静态资源；  *.css , *.js
              //SpringBoot已经做好了静态资源映射
              registry.addInterceptor(new LoginHandlerInterceptor()).addPathPatterns("/**")
                      .excludePathPatterns("/index.html","/","/user/login");
          }
      };
      return adapter;
  }
  ```

## 2.11 ModelAndView和ModelMap详解

ModelAndView包含Model属性和View属性；

* Model：本质上是一个ModelMap类型，而ModelMap是LinkedHashMap的一个子类
* View：包含一些视图信息

**当添加数据时，采用ModelAndView传入数据，返回结果为对象**

```java
//使用ModelAndView传入数据，返回对象
@RequestMapping("/test")
public ModelAndView modelTest(String name) {
    //构建ModelAndView实例，并设置跳转的页面路径
    /*ModelAndView mv = new ModelAndView();
    mv.setViewName("hello");*/
    ModelAndView mv = new ModelAndView("hello");
    mv.addObject("name",name);
    return mv;
}
```

**当添加数据时，采用ModelMap的方式传入数据，返回结果为指定的页面路径**

```java
//使用ModelMap的接口方法传入键值对，返回指定的页面路径
@RequestMapping(value = "/test")
public String modelTest(ModelMap model, String name) {
    model.addAttribute("name", name);
    return "hello";
}
```



## 2.12 Maven中dependency的optional选项的意义

```java
<dependency>
   <groupId>org.springframework.boot</groupId>
   <artifactId>spring-boot-devtools</artifactId>
   <optional>true</optional>
</dependency>
```

默认为false，即该依赖可以被继承传递；

当optional为true时，表示该依赖只在当前项目中生效。子项目中如果需要使用该依赖，需要另行添加。



## 2.13 注册三大组件（servlet、filter、listener）

* 注册自定义`servlet`

  * 自定义servlet

    ```java
    public class MyServlet extends HttpServlet {
    
        //处理get请求
        @Override
        protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            doPost(req,resp);
        }
        
        //处理post请求
        @Override
        protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
            resp.getWriter().write("Hello MyServlet");
        }
    }
    ```

  * 注册servlet

    ```java
    @Bean
    public ServletRegistrationBean myServlet(){
        ServletRegistrationBean registrationBean = new ServletRegistrationBean(new MyServlet(),"/myServlet");
        registrationBean.setLoadOnStartup(1);
        return registrationBean;
    }
    ```

* 注册自定义`filter`

  * 自定义filter

    ```java
    public class MyFilter implements Filter {
    
        @Override
        public void init(FilterConfig filterConfig) throws ServletException {
    
        }
    
        @Override
        public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            System.out.println("MyFilter process...");
            chain.doFilter(request,response);
    
        }
    
        @Override
        public void destroy() {
    
        }
    }
    ```

  * 注册filter

    ```java
    @Bean
    public FilterRegistrationBean myFilter(){
        FilterRegistrationBean registrationBean = new FilterRegistrationBean();
        registrationBean.setFilter(new MyFilter());
        registrationBean.setUrlPatterns(Arrays.asList("/hello","/myServlet"));
        return registrationBean;
    }
    ```

* 注册自定义`listener`

  * 自定义listener

    ```java
    public class MyListener implements ServletContextListener {
        @Override
        public void contextInitialized(ServletContextEvent sce) {
            System.out.println("contextInitialized...web应用启动");
        }
    
        @Override
        public void contextDestroyed(ServletContextEvent sce) {
            System.out.println("contextDestroyed...当前web项目销毁");
        }
    }
    ```

  * 注册listener

    ```java
    @Bean
    public ServletListenerRegistrationBean myListener(){
        ServletListenerRegistrationBean<MyListener> registrationBean = new ServletListenerRegistrationBean<>(new MyListener());
        return registrationBean;
    }
    ```

    

## 2.14 注册handlerInterceptor拦截器

* 自定义handlerInterceptor

  ```java
  public class LoginHandlerInterceptor implements HandlerInterceptor {
      //目标方法执行之前
      @Override
      public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          Object user = request.getSession().getAttribute("loginUser");
          if(user == null){
              //未登陆，返回登陆页面
              request.setAttribute("msg","没有权限请先登陆");
              request.getRequestDispatcher("/index.html").forward(request,response);
              return false;
          }else{
              //已登陆，放行请求
              return true;
          }
  
      }
  
      @Override
      public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler,
                             ModelAndView 	modelAndView) throws Exception {
      }
  
      @Override
      public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, 
                                  Exception ex) throws Exception {
      }
  }
  ```

* 注册handlerInterceptor



