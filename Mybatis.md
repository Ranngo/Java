## 一、Mybatis主要流程

1. 构建工具类，通过输入流导入全局配置(mybtis-config)

   ```java
   public class MybatisUtils {
   
       private static SqlSessionFactory sqlSessionFactory;
   
       static {
           try {
               String resource = "mybatis-config.xml";
               InputStream inputStream = Resources.getResourceAsStream(resource);
               sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
           } catch (IOException e) {
               e.printStackTrace();
           }
       }
   
       public static SqlSession getSqlSession() {
           // 传递true可以使得SqlSession自动提交
           return sqlSessionFactory.openSession(true);
   
       }
   }
   
   ```

   

2. 配置mybatis-config

   详细配置可以查看mybatis官方文档

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE configuration
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-config.dtd">
   <configuration>
       <!--  配置外部数据源  -->
       <properties resource="db.properties"/>
   
       <!--  配置调整  -->
       <settings>
           <setting name="logImpl" value="SLF4J"/>
           <setting name="mapUnderscoreToCamelCase" value="true"/>
       </settings>
   
       <!--  设置别名  -->
   
       <!--  指定一个包名，MyBatis 会在包名下面搜索需要的 Java Bean  -->
       <typeAliases>
           <package name="com.kkp.pojo"/>
       </typeAliases>
   
       
       <environments default="development">
           <environment id="development">
               <transactionManager type="JDBC"/>
               <dataSource type="POOLED">
                   <property name="driver" value="${driver}"/>
                   <property name="url" value="${url}"/>
                   <property name="username" value="${username}"/>
                   <property name="password" value="${pwd}"/>
               </dataSource>
           </environment>
       </environments>
       <mappers>
           <!-- 映射单个mapper时使用class标签-->
           <!--  <mapper class="com.kkp.dao.TeacherMapper"></mapper>  -->
   
           <!--   映射整个文件夹下的所有mapper文件     -->
           <package name="com.kkp.dao"/>
   
       </mappers>
   </configuration>
   ```

   外部配置文件db.properties如下

   ```java
   driver=com.mysql.cj.jdbc.Driver
   url=jdbc:mysql://47.98.118.170:3306/mybatis?useSSL=false&useUnicode=true&characterEncoding=utf-8
   username=root
   pwd=root
   ```

3. 定义实体类

   ```java
   @Data
   public class Teacher {
       private int id;
       private String name;
       private List<Student> students;
   }
   ```

4. 定义接口

   ```java
   public interface TeacherMapper {
   //    List<Teacher> getTeacher();
       Teacher getTeacher(@Param("tid") int id);
   }
   ```

   

5. 配置XML映射，实现具体业务操作

   ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE mapper
           PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
           "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
   <mapper namespace="com.kkp.dao.TeacherMapper">
       <select id="getTeacher" resultMap="TeacherStudent">
           select s.id sid, s.name sname, t.id tid, t.name tname
           from student s, teacher t
           where s.tid=t.id and t.id=#{tid}
       </select>
   
       <resultMap id="TeacherStudent" type="com.kkp.pojo.Teacher">
           <result property="id" column="tid"/>
           <result property="name" column="tname"/>
           <collection property="students" ofType="com.kkp.pojo.Student">
               <result property="id" column="sid"/>
               <result property="name" column="sname"/>
               <result property="tid" column="tid"/>
           </collection>
       </resultMap>
   
   </mapper>
   ```

6. 测试业务

   ```java
   public class MyTest {
       @Test
       public void getTeacherTest(){
           SqlSession sqlSession = MybatisUtils.getSqlSession();
           TeacherMapper mapper = sqlSession.getMapper(TeacherMapper.class);
           Teacher teacher = mapper.getTeacher(2);
           System.out.println(teacher);
           sqlSession.close();
       }
   }
   ```

   

