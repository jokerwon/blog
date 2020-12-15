---
title: Spring Boot 学习
tag: [Backend, Java, Spring, Spring Boot]
---

**学学Spring Boot**

<!-- more -->

### 一、 IOC

#### 1.1  使用属性的 setter 方法注入

代码1-1

~~~xml
<!-- xml配置文件 -->
<bean id="car" class="com.znv.study.pojo.Car">  
    <property name="brand" value="Benz"/>  
    <property name="price" value="600000.0"/>
    <property name="maxSpeed" value="240"/>
    
    <!-- 引用其他 bean -->
    <!-- 1. 通过 ref 属性或者 <ref> 标签引用 -->
    <property name="master" ref="master"/>
    <property name="master">
        <ref bean="master"></ref>
    </property>
    <!-- 2. 通过内部 bean -->
    <property name="master">
        <bean class="com.znv.study.pojo.Master">
      <property name="name" value="joker"/>
      <property name="age" value="22"/>
  </bean>
    </property>
</bean>
    
<bean id="master" class="com.znv.study.pojo.Master">
    <property name="name" value="joker"/>
    <property name="age" value="22"/>
</bean>
~~~

~~~java
package com.znv.study.pojo

class Car{
    private String brand;
    private double price;
    private int maxSpeed;
    private Master master;  //拥有者 Bean
    
    //无参构造方法，required
 public Car(){}
    
 /*  setter(required)  */
}

package com.znv.study.pojo
class Master{
    private String name;
    private int age;
    
    public Master(){}
    /*  setter(required)  */
}
~~~

*使用 setter 注入，Bean 中必需有无参的构造方法和 setter 方法*

#### 1.2  使用构造函数注入

代码1-2

~~~xml
<!-- xml配置文件 -->
<bean id="car" class="com.znv.study.Car">  
    <contructor-arg value="Benz"></contructor-arg>
    <!-- 通过指定类型 type 来区分不同的参数 -->
    <contructor-arg value="600000.0" type="double"></contructor-arg>
    <contructor-arg value="240" type="int"></contructor-arg>
</bean>  
~~~

~~~java
package com.znv.study.pojo
class Car{
    private String brand;
    private double price;
    private int maxSpeed;
    
    //参数对应的构造方法(required)
 public Car(String brand, double price, int maxSpeed){
     this.brand = brand;
     this.price = price;
     this.maxSpeed = maxSpeed;
 }
}
~~~

#### 1.3  使用注解注入

代码1-3

~~~java
package com.znv.study.pojo
import org.springframework.stereotype.Component;
@Component("car")  //注册 Bean，且命名为car
@Scope(ConfigurableBeanFactory.SCOPE_PROTOTYPE)  //修改 Bean 的作用域为原型，默认为单例
class Car{
    @Value("Benz")
    private String brand;
    @Value("600000.0")
    private double price;
    @Value("240")
    private int maxSpeed;
    @Autowired
    private Master master;
    
    /*  setter and getter  */
}
~~~

​ 假如代码 1-3 中的Master指代不明确，可以用 @Primary 为希望使用的 Bean 注解，或者用 @Qualifier("...") 在成员上注解，以用来消除歧义性。

配置类AppConfig

代码1-4

~~~java
package com.znv.study.configuration

import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;

@Configuration  //定义该类为配置类
@ComponentScan  //定义扫描的包，默认为当前包及子包
class AppConfig{
}
~~~

#### 1.4  从 IOC 容器中获得 Bean

~~~java
package com.znv.study
import com.znv.study.pojo.Car
public class Main {
    public static void main(String[] args) {
        ApplicationContext ctx = new ClassPathXmlApplicationContext("spring.xml");
        Car car = (Car) ctx.getBean("car");
        System.out.println(car);
~~~

### 二、 AOP

#### 2.1  相关概念

* 连接点（joint  point）: 对应的是具体被拦截的对象。因为 Spring 只能支持方法，所以被拦截的对象往往指特定的方法。
* 切点（point  cut）： 目标对象，*已经增强的方法*。
* 通知（advice）： 增强的代码。分为前置通知（before advice）、后置通知（after advice）、环绕通知（around advice）、事后返回通知（after-returning advice）和异常通知（after-throwing advice）。会根据约定织入流程中。
* 目标对象（target）： 被代理的对象。
* 织入（weaving）： 将通知应用到切入点的过程。
* 引入（introduction）： 引入新的类和其方法，增强现有 Bean 的功能。
* 切面（aspect）： 一个可以定义切点、各类通知和引入的内容。

#### 2.2  确定连接点

代码2-1  用户服务接口

~~~java
package com.znv.study.service
import com.znv.study.pojo.User
public interface UserService{
 public void printUser(User user);
}
~~~

代码2-2  用户服务接口实现类

~~~java
package com.znv.study.service.impl

@Service
public class UserServiceImpl implements UserService{
 @Override
    public void printUser(User user){
     if(User == null){
      throw new RuntimeException("检查用户参数是否为空");
     }
     System.out.println(user);
    }
}
~~~

#### 2.3  开发切面

代码2-3  定义切面和切点

~~~java
package com.znv.study

@Aspect  //需要手动导入 aspectj.jar 和 aspectj-weave.jar
public class MyAspect{
 //定义切点
 /*
  * @Param execution： 在执行的方法，拦截括号中正则匹配的方法
  * @Param *： 返回值类型
  * @Param ...printUser()： 方法的全限定名称
  * @Param .. ： 表示任意参数进行匹配
  */
 @Pointcut("execution(* com.znv.study.service.impl.UserServiceImpl.printUser(..))")
 public void pointCut(){}
 
 @Before("pointCut()")
 public void before(){...}
 ...
}
~~~

#### 2.4  测试 AOP

代码2-4  用户控制器

~~~java
package com.znv.study.controller
//定义控制器
@Controller
//定义类请求路径
@RequestMapping("/user")
public class UserController{
    //注入用户服务
    @Autowired
 private UserService userService = null;
    
    //定义请求
    @RequestMapping("/print")
    //转换为JSON
    @ResponseBody
    public User printUser(Long id, String userName, String note){
        User user = new User(id, userName, note);
        userService.printUser(user);
        return user;
    }
}
~~~

代码2-5  Spring Boot 配置启动文件

~~~java
package com.znv.study

@SpringBootApplication
public class Demo1Application {
    //定义切面
    @Bean(name = "myAspect")
    public MyAspect initMyAspect(){
        return new MyAspect();
    }
    //启动切面
    public static void main(String[] args) {
        SpringApplication.run(Demo1Application.class, args);
    }

}
~~~

浏览器访问 <http://localhost:8080/user/print?id=1&userName=username1&note=232>，如果显示正确的 JSON 对象，则测试通过。

#### 2.5  环绕通知

在代码 2-3 中加入以下代码

~~~java
@Around("pointCut()")
public void around(ProceedingJoinPoint jp) throws Throwable {
    ...
    //回调目标对象的原有方法
    jp.proceed();
    ...
}
~~~

#### 2.6  通知获取参数

代码 2-6  在前置通知中获取参数

~~~java
@Before("pointCut() && args(user)")  //将连接点的名称为 user 的参数传递进来
public void beforeWithParam(JoinPoint point, User user){
    Object[] args = point.getArgs();
    System.out.println(before...);
}
~~~

#### 2.7  多个切面

定义多个切面后，只要在 Spring  Boot 配置启动文件（代码2-5）中加入以下代码

~~~java
@Bean(name = "myAspect1")
    public MyAspect initMyAspect1(){
        return new MyAspect1();
    }
@Bean(name = "myAspect2")
    public MyAspect initMyAspect2(){
        return new MyAspect2();
    }
...
~~~

### 三、 MyBatis 操作数据库

#### 3.1  配置自定义数据源

* 增加依赖（pom.xml）

代码 3-1

~~~xml
<dependency>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
<dependency>
 <groupId>mysql</groupId>
 <artifactId>mysql-connector-java</artifactId>
</dependency>
~~~

- 配置数据源（application.properties）

代码 3-2

~~~properties
spring.datasource.url=jdbc:mysql://localhost:3306/test
spring.datasource.username=root
spring.datasource.password=123456
#*spring.datasource.driver-class-name=com.mysql.jdbcDriver*
#数据库连接池初始化数据库
spring.datasource.tomcat.initial-size=5
#最大等待毫秒数，超出时间会报错
spring.datasource.tomcat.max-wait=10000
#最大等待连接的数量，设 0 为没有限制
spring.datasource.tomcat.max-idle=10
#最大连接活动数
spring.datasource.tomcat.max-active=50
~~~

注释中加 * 的地方：虽然注释了驱动类的配置，但依旧可以连接数据源，因为 Spring Boot 会尽可能地判断数据源是什么类型的，然后根据默认的情况去匹配驱动类。

#### 3.2  MyBatis 基础操作

代码 3-3  定义 User 类 pojo

~~~java
package com.znv.database.demodatabase.pojo;

import org.apache.ibatis.type.Alias;

@Alias("user")  //MyBatis指定别名
public class User {
    private int id;
    private String username;
    private String password;
    /* setters and getters */
}
~~~

代码 3-4  定义数据持久层接口，无需实现类

~~~java
package com.znv.database.demodatabase.dao

@Repository
public interface MyBatisUserDao {
    //增加用户，返回增加结果，1 代表成功，其他代表失败，下同
    public int insertUser(User user);
    //删除用户
    public int deleteUser(String username);
    //更新用户
    public int updateUser(User user);
    //通过用户名查找用户信息，返回符合条件的对象
    public User getUserByUsername(String username);
    //返回所有用户的列表
    public List<User> findAllUsers();
}
~~~

代码 3-5  定义 MyBatis 映射配置文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "mybatis-3-mapper.dtd" >

<!-- namespace 指定 dao 的一个接口 -->
<mapper namespace="com.znv.database.demodatabase.dao.MyBatisUserDao">
    <!-- id 指代此条 SQL，resultType 指定返回值类型 -->
    <!-- 使用 user 是因为在 User类中指代了别名，也可以用全限定名com.znv.database.demodatabase.pojo.User -->
    <!--通过 username 获取用户信息-->
    <select id="getUserByUsername" parameterType="String" resultType="user">
        SELECT id,username,password FROM user WHERE username = #{username}
    </select>
    <!-- 获取所有用户信息 -->
    <select id="findAllUsers" resultType="user">
        SELECT * FROM user
    </select>

    <!-- 插入用户信息 -->
    <insert id="insertUser" parameterType="user">
        INSERT INTO User (id,username,password) values(#{id},#{username},#{password})
    </insert>

    <!--修改用户信息-->
    <update id="updateUser" parameterType="user">
        UPDATE user SET username=#{username},password=#{password} WHERE id=#{id}
    </update>

    <delete id="deleteUser" parameterType="String">
        DELETE FROM user WHERE username=#{username}
    </delete>
</mapper>
~~~

代码 3-6  调用数据持久层接口

~~~java
@RequestMapping("/dologin")
    public String doLogin(String username, String password, Model model) {
        User user = (User) myBatisUserDao.getUserByUsername(username);
        boolean flag = password.equals(user.getPassword());
        if (user == null ) {
            return "login";
        } else if(!flag){
            return "login";
        }else {
            List<User> list = (List<User>) myBatisUserDao.findAllUsers();
            model.addAttribute("user",user);  //保存user，可由thymeleaf访问，下同
            model.addAttribute("list",list);
            return "index";
        }
    }//doLogin end
~~~

#### 3.3  MyBatis  进阶

##### 3.3.1  resultMap 表关联

代码 3-7  创建 Post 类 pojo

~~~java
package com.znv.database.demodatabase.pojo;

@Alias("post")  //定义 Post 别名
public class Post {
    private int id;
    private User user;
    private String title;
    private String content;
 /* setters and getters */
}
~~~

在代码 3-3 加入 Post 关联

`private List<Post> posts;`

代码 3-8  新建映射文件

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" 
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">

<mapper namespace="com.znv.joker.userMaper">
 <!-- User 级联文章查询 方法配置 (一个用户对多个文章)  -->
 <resultMap type="user" id="resultUserMap">
        <!--
		   - id: 数据库记录的主键
           - result： 数据库记录的其他字段
           - column： 数据库记录字段名
           - property： bean 对象中的属性名
		-->
  <id property="id" column="user_id" />
  <result property="username" column="username" />
  <result property="mobile" column="mobile" />
  <collection property="posts" ofType="post" column="userid">
   <id property="id" column="post_id" javaType="int" jdbcType="INTEGER"/>   
            <result property="title" column="title"/>
            <result property="content" column="content"/> 
  </collection>
 </resultMap>

    <!-- resultMap 与 resultType 不可共存 -->
    <!-- resultMap 指向对应的自定义映射规则的id -->
 <select id="getUser" resultMap="resultUserMap" parameterType="int">
  SELECT u.*,p.*
  FROM user u, post p
  WHERE u.id=p.userid AND id=#{user_id} 
 </select>
    
    <!-- User 级联文章查询 方法配置 (多个文章对一个用户)  -->
 <resultMap type="Post" id="resultPostsMap">
  <id property="id" column="post_id" />
  <result property="title" column="title" />
  <result property="content" column="content" />
  <association property="user" javaType="User">  
         <id property="id" column="userid"/>   
         <result property="username" column="username"/>   
         <result property="mobile" column="mobile"/>   
        </association> 
 </resultMap>

 <select id="getPosts" resultMap="resultPostsMap" parameterType="int">
  SELECT u.*,p.*
  FROM user u, post p
  WHERE u.id=p.userid AND p.post_id=#{post_id} 
  </select>
</mapper>
~~~

##### 3.3.2  动态 SQL

1. if 标签

   在 if 标签中先进行判断，如果值为 null 或等于空字符串，就不在 SQL 语句中进行此条件的判断。

   这里的 1=1 是为了避免 if 标签都未匹配从而导致 SQL 语法错误

   ~~~xml
   <select id="dynamicIfTest" parameterType="Blog" resultType="Blog">
    select * from t_blog where 1=1
    <if test="title != null">
     and title = #{title}
    </if>
    <if test="content != null">
     and content = #{content}
    </if>
    <if test="owner != null">
     and owner = #{owner}
    </if>
   </select>
   ~~~

2. where 标签

   where 主要是用来简化 SQL语句中 where 条件判断，自动处理（前置）AND/OR 条件。

   如果将 AND/OR 写在 if 标签中 SQL 语句后在某些情况下还是会出现语法错误。

   ~~~xml
   <select id="dynamicWhereTest" parameterType="Blog" resultType="Blog">
    select * from t_blog 
    <where>
     <if test="title != null">
      title = #{title}
     </if>
     <if test="content != null">
      and content = #{content}
     </if>
     <if test="owner != null">
      and owner = #{owner}
     </if>
    </where>
   </select>
   ~~~

3. set 标签

   set 标签元素主要用在更新操作的时候，在包含的语句前输出一个 set，然后如果包含的语句是以逗号结束的话将会把该逗号忽略，如果 set 包含的内容为空的话则会出错。

   ~~~xml
   <update id="dynamicSetTest" parameterType="Blog">
    update t_blog
    <set>
     <if test="title != null">
               title = #{title},
     </if>
     <if test="content != null">
               content = #{content},
     </if>
     <if test="owner != null">
               owner = #{owner}
     </if>
    </set>
    where id = #{id}
   </update>
   ~~~

4. trim 标签

   trim 是为了更灵活地去除多余关键字的标签，可以用来实现 where 和 set 的效果。

   |        属性        | 描述                                                         |
   | :----------------: | :----------------------------------------------------------- |
   |       prefix       | 给sql语句拼接的前缀                                          |
   |       suffix       | 给sql语句拼接的后缀                                          |
   | prefixesToOverride | 去除sql语句前面的关键字或者字符，该关键字或者字符由prefixesToOverride属性指定，假设该属性指定为”AND”，当sql语句的开头为”AND”，trim标签将会去除该”AND” |
   | suffixesToOverride | 去除sql语句后面的关键字或者字符，该关键字或者字符由suffixesToOverride属性指定 |

   * 使用 if/trim 代替 where 标签

     ~~~xml
     <!-- 使用 if/trim 代替 where(判断参数) - 将 User 类不为空的属性作为 where 条件 -->  
     <select id="getUsertList_if_trim" resultMap="resultMap_User">  
         SELECT * 
           FROM user u
         <trim prefix="WHERE" prefixToOverride="AND|OR">  
             <if test="username !=null ">  
                 u.username LIKE CONCAT(CONCAT('%', #{username, jdbcType=VARCHAR}),'%')  
             </if>  
             <if test="sex != null and sex != '' ">  
                 AND u.sex = #{sex, jdbcType=INTEGER}  
             </if>  
             <if test="birthday != null ">  
                 AND u.birthday = #{birthday, jdbcType=DATE}  
             </if>
         </trim>     
     </select>
     ~~~

   * 使用 trim 代替 set 标签

     ~~~xml
     <!-- if/trim代替set(判断参数) - 将 User 类不为空的属性更新 -->  
     <update id="updateUser_if_trim" parameterType="com.yiibai.pojo.User">  
         UPDATE user  
         <trim prefix="SET" suffixToOverride=",">  
             <if test="username != null and username != '' ">  
                 username = #{username},  
             </if>  
             <if test="sex != null and sex != '' ">  
                 sex = #{sex},  
             </if>  
             <if test="birthday != null ">  
                 birthday = #{birthday},  
             </if>  
         </trim>  
         WHERE user_id = #{user_id}  
     </update>
     ~~~

5. choose( when, otherwise ) 标签

   用法与 switch 相似。

   ~~~xml
   <select id="dynamicChooseTest" parameterType="Blog" resultType="Blog">
    select * from t_blog where 1 = 1 
    <choose>
           <when test="title != null">
               and title = #{title}
           </when>
           <when test="content != null">
               and content = #{content}
           </when>
           <otherwise>
               and owner = "owner1"
           </otherwise>
    </choose>
   </select>
   ~~~

### 四、 Web 相关

#### 4.1  Controller 接收参数

##### 4.1.1  请求路径参数

1. @PathVariable

   获取路径参数。即 *url/{id}*的形式。

2. @RequestParam

   获取查询参数。即 *url?username=xxx&age=xx* 的 形式。

代码 4-1

~~~java
//url: 127.0.0.1:8080/demo/123?username=hah
@GetMapping("demo/{id}")
public void demo(@PathVariable(name="id") String id,
                 @RequestParam(name="username") String username){
    System.out.println("id: " + id);  //id: 123
    System.out.println("username: " + username);  //username: hah
}
~~~

##### 4.1.2  Body 参数

1. @RequestBody

   url:  127.0.0.1:8080/demo1

   body:

   ~~~json
   {
       "id": 1,
       "username": "joker",
       "password": "123"
   }
   ~~~

   代码 4-2

   ~~~java
   @PostMapping(value = "/demo1")
   public void demo1(@RequestBody User user){
       System.out.println(user.toString());  //User[id=1,username="joker",password="123"]
   }
   
   //或者
   
   @PostMapping(path = "/demo1")
   public void demo1(@RequestBody Map<String, Object> user) {
       System.out.println(person.get("username"));  //joker
   }
   ~~~

##### 4.1.3  请求头参数及 Cookie

1. @RequestHeader

2. @CookieValue

   代码 4-3

   ~~~java
   @GetMapping("/demo2")
   public void demo3(@RequestHeader(name = "myHeader") String myHeader,
           @CookieValue(name = "myCookie") String myCookie) {
       System.out.println("myHeader=" + myHeader);
       System.out.println("myCookie=" + myCookie);
   }
   
   //或者
   
   @GetMapping("/demo2")
   public void demo3(HttpServletRequest request) {
       System.out.println(request.getHeader("myHeader"));
       for (Cookie cookie : request.getCookies()) {
           if ("myCookie".equals(cookie.getName())) {
               System.out.println(cookie.getValue());
           }
       }
   }
   ~~~

#### 4.2  返回 JSON 数据格式

##### 4.2.1  通过@ResponseBody

通过@ResponseBody 方式，需要在@RequestMapping 中，添加`produces = "application/json;charset=UTF-8"`,设定返回值的类型。

先导入 net.sf.json-lib 的依赖

代码 4-4

~~~xml
<dependency>
 <groupId>net.sf.json-lib</groupId>
 <artifactId>json-lib</artifactId>
 <version>2.4</version>
 <classifier>jdk15</classifier>
</dependency>
~~~

~~~java
import net.sf.json.JSONObject;
import com.znv.database.demodatabase.dao.MyBatisUserDao;

@RequestMapping(value = "/insert", produces = "application/json;charset=UTF-8")
@ResponseBody
public String insertUser(int id, String username, String password){
        User user = new User(id, username, password);
        int flag = myBatisUserDao.insertUser(user);
        JSONObject result = new JSONObject();
        result.put("success",flag);
        return result.toString();  //插入成功后会向客户端返回 { "success": 1 }
    }
~~~

##### 4.2.2  通过HttpServletResponse来返回

通过HttpServletResponse 获取到输出流后，写出数据到客户端。

~~~java
@RequestMapping(value = "/resp/data", method = RequestMethod.POST)
public void writeByResp(@RequestBody JSONObject jsonParam,HttpServletResponse resp) {

    // 将获取的json数据封装一层，然后在给返回
    JSONObject result = new JSONObject();
    result.put("msg", "ok");
    result.put("method", "HttpServletResponse");
    result.put("data", jsonParam);

    //写json数据到客户端
    this.writeJson(resp, result);
}

public void writeJson(HttpServletResponse resp ,JSONObject json ){
    PrintWriter out = null;
    try {
        //设定类容为json的格式
        resp.setContentType("application/json;charset=UTF-8");
        out = resp.getWriter();
        //写到客户端
        out.write(json.toJSONString());
        out.flush();
    } catch (IOException e) {
        e.printStackTrace();
    }finally{
        if(out != null){
            out.close();
        }
    }
}
~~~
