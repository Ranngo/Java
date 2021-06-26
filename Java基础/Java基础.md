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

# 六、VO、DO、DTO、PO的区别

**POJO**（Plain Ordinary Java Object）: 只能承载数据进行临时的传输，不具备任何业务逻辑处理能力，没有任何类继承和接口实现的简单的java对象。

**PO**（Persistent Object）: POJO的持久层对象，与持久层的数据结构（通常是关系型数据库）形成一一对应的映射关系，对应于数据库中的一条记录，每个字段对应对象的某个属性。PO是只含有get/set方法的POJO

**DAO**（Data Access Object）：数据传输对象，包含了各种数据库的操作方法，通常和PO结合使用，实现各种CRUD操作。

**DO**（Data Object）：领域对象，指业务实体对象。包含有业务方法。

**DTO**（Data Transfer Object）: 数据传输对象， 数据传输时有些属性不需要传输，则DTO中可以不包含这些属性，只将需要的数据进行传输而定义的对象即为DTO。

**VO**（View Object）：展示层对象，在视图中所需要的数据会随着需求的变化而变化，为此而定义的对象即为VO。

**BO**（Business Object）：业务对象，用于封装业务逻辑的对象，通过调用DAO中方法，结合VO和PO进行业务操作。

这个对象可以包括一个或多个其它的对象。 比如一个简历，有教育经历、工作经历、社会关系等等。 我们可以把教育经历对应一个 PO ，工作经历对应一个 PO ，社会关系对应一个 PO 。 建立一个对应简历的 BO 对象处理简历，每个 BO 包含这些 PO 。 这样处理业务逻辑时，就可以针对 BO 去处理。



**DO**与**PO**的区别：

DO和PO在绝大部分情况下是一一对应的，PO是只含有get/set方法的POJO，但某些场景还是能反映出两者在概念上存在本质的区别：

* DO在某些场景下不需要进行显式的持久化，例如利用策略模式设计的商品折扣策略，会衍生出折扣策略的接口和不同折扣策略实现类，这些折扣策略实现类可以算是DO，但它们只驻留在静态内存，不需要持久化到持久层，因此，这类DO是不存在对应的PO的。

* 同样的道理，某些场景下，PO也没有对应的DO，例如老师Teacher和学生Student存在多对多的关系，在关系数据库中，这种关系需要表现为一个中间表，也就对应有一个TeacherAndStudentPO的PO，但这个PO在业务领域没有任何现实的意义，它完全不能与任何DO对应上。这里要特别声明，并不是所有多对多关系都没有业务含义，这跟具体业务场景有关，例如：两个PO之间的关系会影响具体业务，并且这种关系存在多种类型，那么这种多对多关系也应该表现为一个DO，又如：“角色”与“资源”之间存在多对多关系，而这种关系很明显会表现为一个DO——“权限”。

* 某些情况下，为了某种持久化策略或者性能的考虑，一个PO可能对应多个DO，反之亦然。例如客户Customer有其联系信息Contacts，这里是两个一对一关系的DO，但可能出于性能的考虑（极端情况，权作举例），为了减少数据库的连接查询操作，把Customer和Contacts两个DO数据合并到一张数据表中。反过来，如果一本图书Book，有一个属性是封面cover，但该属性是一副图片的二进制数据，而某些查询操作不希望把cover一并加载，从而减轻磁盘IO开销，同时假设ORM框架不支持属性级别的延迟加载，那么就需要考虑把cover独立到一张数据表中去，这样就形成一个DO对应对个PO的情况。

* PO的某些属性值对于DO没有任何意义，这些属性值可能是为了解决某些持久化策略而存在的数据，例如为了实现“乐观锁”，PO存在一个version的属性，这个version对于DO来说是没有任何业务意义的，它不应该在DO中存在。同理，DO中也可能存在不需要持久化的属性。

参考博客：https://www.cnblogs.com/qixuejia/p/4390086.html

