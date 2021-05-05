# 一、深浅拷贝

浅拷贝：默认的clone方法只复制了引用关系，源对象和复制对象的地址相同。

深拷贝：可以克隆所有的可变实例域。`clone`方法需要重新定义，即该类需要实现`Cloneable`接口，并调用`super.clone()`。

```java
class Employee implement Cloneable{
	public Emplyee clone() throws CloneNotSupportedException{
        //调用Object.clone()
        Employee cloned = (Employee) super.clone();
        //克隆源对象
        cloned.hireDay = (Date) hireDay.clone();
        return cloned;
    }
}
```



# 二、HashCode

如果两个对象值相同（equal()返回True），则HashCode一定相同

如果两个对象的HashCode相同，则这两个对象不一定值相同。



# 三、抽象类和接口的区别

* 抽象类中有构造方法，接口中没有构造方法

* 抽象类中可以有抽象方法和具体方法，接口中没有具体的方法

* 抽象类中的成员可以是任意类型，接口中的成员必须是public

* 抽象类中可以有静态方法，接口中不能有静态方法

* 一个类只能继承一个抽象类，却可以实现多个接口

* 有抽象方法的类必须是抽象类，而抽象类中的方法不一定是抽象方法

  

# 四、局部变量与成员变量的区别

## 4.1 声明的位置

* 局部变量：方法体｛｝，形参，代码块｛｝中

* 成员变量：类中方法外

  * 类变量：有`static`修饰

  * 实例变量：无`static`修饰

## 4.2 修饰符

* 局部变量：`final`

* 成员变量：`public、protected、private、final、static、volatile、transient`

## 4.3 值存储的位置

* 局部变量：栈
* 实例变量：堆
* 类变量：方法区

## 4.4 作用域

* 局部变量：从声明处开始，到所属方法的｝结束
* 实例变量：当前类中“`this.`”访问或者直接访问，在其他类中“`对象名.`”访问
* 类变量：通过 `类名.`或者`对象.`访问

## 4.5 生命周期

* 局部变量：每一个线程，每一次调用执行都是一次新的生命周期。
* 实例变量：随着对象的创建而初始化，随着对象的被回收而消亡，每一个对象的实例变量都是独立的。
* 类变量：随着类的初始化而初始化，随着类的卸载而消亡，每一个类的类变量都是共享的。

## 4.6 重名处理规则

当局部变量与**实例变量**重名时，在实例变量前加`this.`

当局部变量与**类变量**重名时，在类变量前加`类名.`



# 五、 序列化和反序列化

序列化是指将对象转化为字节序列实现远程通信的过程，反序列化则与之相反。

## 5.1 实现方式

 只有实现了Serializable或者Externalizable接口的类的对象才能被序列化为字节序列

* 实现Serializable接口

  使用ObjectInputStream和ObjectOutputStream两个IO工具类

  * 将对象写入文件中

    FileOutStream导入文件路径->包装到ObjectOutputStream中->调用writeObject方法将对象存入文件中

  ![img](images/20180919090545190)

  ```java
  private static void serializeAnimal() throws Exception {
      BlackCat black = new BlackCat("black", "我是黑猫");
      System.out.println("序列化前："+black.toString());
      System.out.println("=================开始序列化================");
      ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream(FILE_PATH));
      oos.writeObject(black);
      oos.flush();
      oos.close();
  ```

  

  * 将数据从文件中读取出来

    FileInputStream导入文件路径->包装到ObjectInputStream中->调用readObject方法解析文件并转化成对象

  ![img](images/20180919090621522)

  ```java
  private static void deserializeAnimal() throws Exception {
      System.out.println("=================开始反序列化================");
      ObjectInputStream ois = new ObjectInputStream(new FileInputStream(FILE_PATH));
      BlackCat black = (BlackCat) ois.readObject();
      ois.close();
      System.out.println(black);
  ```

  

* 实现Externalizable接口

参考博客：https://blog.csdn.net/mocas_wang/article/details/107621010?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_utm_term-1&spm=1001.2101.3001.4242

