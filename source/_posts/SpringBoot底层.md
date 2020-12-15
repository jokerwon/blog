---
title: Spring Boot 底层
tag: [Backend, Java, Spring, Spring Boot]
---

**Spring Boot 底层源码分析**

<!-- more -->
### 一、 POM 文件

* 父项目

~~~xml
<!-- 父项目 -->
<parent>
 <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
 <version>2.1.3.RELEASE</version>
</parent>
~~~

父项目的父项目

~~~xml
<parent>
 <groupId>org.springframework.boot</groupId>
 <artifactId>spring-boot-dependencies</artifactId>
 <version>2.1.3.RELEASE</version>
 <relativePath>../../spring-boot-dependencies</relativePath>
</parent>
~~~

它是真正管理Spring Boot应用里面的依赖版本，也是版本仲裁中心

子项目如需要dependencies中的依赖不再需要写版本。

- spring-boot-starter：spring boot 的场景启动器
- spring-boot-starter-web： 导入了 web 模块正常运行所依赖的组件

### 二、 主程序类

~~~java
@SpringBootApplication
public class DemoDatabaseApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoDatabaseApplication.class, args);
    }
}
~~~

@SpringBootApplication：说明这个类是Spring Boot是主配置类。

~~~java
@Target({ElementType.TYPE})
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
public @interface SpringBootApplication {...}
~~~

- **@SpringBootConfiguration**：表示这个是一个Spring Boot配置类
  - **@Configuration**：配置类注解。也是容器中的一个组件。@Component
- **@EnableAutoConfiguration**：开启自动配置功能

```java
@AutoConfigurationPackage
@Import({AutoConfigurationImportSelector.class})
public @interface EnableAutoConfiguration {...}
```

​ **@AutoConfigurationPackage**: 自动配置包

​  @**Import**({Registrar.class})：Spring底层注解，给容器导入一个组件。==将主配置类（@SpringBootApplication标注的类）所在的包及所有子包中所有的组件扫描到 Spring 容器。==

​ **@Import({AutoConfigurationImportSelector.class})** ：

​  AutoConfigurationImportSelector： 组件选择器。将所有需要导入的组件以全类名的方式返回。这些组件就会被添加到容器中。最终会在容器中导入非常多的自动配置类（xxxAutoConfiguration）: 给容器中导入这个场景需要的所有组件，并配置好这些组件。

### 三、 配置文件

#### 3.1  YAML( application.yml ) 配置文件

##### 3.1.1  语法

- key:·value  表示键值对，冒号与 value 间的空格不可缺少

- 以缩进来控制层级关系
- 大小写敏感

```yaml
server:
 port: 8081
```

##### 3.1.2  值的写法

- 字面量： 字符串默认不用加引号，如果加了，用法不同

  - “ ” 双引号： 不会转义字符串中的特殊字符。

    eg.  name: "hello \n world"   =>  hello 换行  world

  - ' ' 单引号：会转义特殊字符。

    eg.  name: ‘hello \n world’  =>  hello \n world

- 对象、Map（键值对）：

```yaml
friends:
 name: tom
 age: 22
```

​ 行内写法：

```yaml
friend: { name: tom, age: 22 }
```

- 数组： 用 - value 表示数组中的一个元素

```yaml
pets: 
 - cat
 - dog
 - pig
```

​ 行内写法：

```yaml
pets: [ cat, dog, pig ]
```

##### 3.1.3  从 yaml 配置文件中配置 bean 中的值

**需要引入依赖**

bean：

```java
/*
 * @ConfigurationProperties： 告诉SpringBoot将本类中的所有属性和全局配置文件相关的配置进行绑定。
 *  prefix = "person"： 指定配置文件中要映射的属性 
 */
@Component
@ConfigurationProperties(prefix = "person")
public class Person{
    private String name;
    private int age;
    private Map<String,Object> map;
    private List<Object> list;
    private Dog dog;
    
    /* setters and getters */ 
}

public class Dog{
    private name;  
}
```

yaml：

```yaml
person: 
 name: joe
 age: 22
 map: { k1: v1, k2: v2 }
 list: 
  - item1
  - item2
 dog: 
  name: jacky
```

#### 3.2  properties( application.properties) 配置文件

```properties
person.name=joe
person.age=22
person.map.k1=v1
person.map.k2=v2
person.list=item1,item2
person.dog.name=jacky
```

#### 3.3  @ConfigurationProperties 和 @Value 的区别

|                | @ConfigurationProperties | @Value   |
| -------------- | ------------------------ | -------- |
| 功能           | 批量注入配置文件中的属性 | 单个注入 |
| 松散语法       | 支持                     | 不支持   |
| SpEL           | 不支持                   | 支持     |
| JSR303数据校验 | 支持                     | 不支持   |
| 复杂类型封装   | 支持                     | 不支持   |

配置文件 yml 和 properties 都能获取到值。

如果只是在某个业务逻辑中需要获取文件中的某项值，使用 @Value。

如果专门编写了 bean 和配置文件映射，使用 @ConfigurationProperties。

#### 3.4  @PropertySource 和 @ImportResource

- **@PropertySource**： 加载指定的配置文件

​ eg.  @PropertySource(value = {"classpath:person.properties"})

- **@ImportResource**： 导入 Spring 配置文件，让配置文件里的内容生效。

  eg.  @ImportResource(locations = {"classpath:beans.xml"})

#### 3.5  向容器中添加组件

​ Spring Boot 推荐使用全注解的方式来给容器添加组件。

  1. 新建配置类
  2. 使用 @Bean

~~~java
@Configuration
public class MyAppConfig{
    @Bean
    public HelloService helloService(){
        return new HelloService();
    }
}
~~~
