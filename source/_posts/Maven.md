---
title: Maven
tag: [Backend, Java, Maven]
---

### 一、 环境的搭建

Maven 翻译为"专家"、"内行"，是 Apache 下的一个纯 Java 开发的开源项目。基于项目对象模型（缩写：POM）概念，Maven利用一个中央信息片断能管理一个项目的构建、报告和文档等步骤。Maven 是一个项目管理工具，可以对 Java 项目进行构建、依赖管理。Maven 也可被用于构建和管理各种项目，例如 C#，Ruby，Scala 和其他语言编写的项目。Maven 曾是 Jakarta 项目的子项目，现为由 Apache 软件基金会主持的独立 Apache 项目。

<!-- more -->

#### 1.1  检查 JDK 的安装

​ *Maven 可运行在 JDK 1.4 及以上的版本。*

​ Windows 环境下，打开 cmd 命令窗口，键入

​ `java -version`

​ 结果如下

![1552449175(1)](C:\Users\ZNV\Desktop\Notes\images\1552449175(1).jpg)

#### 1. 2  下载Maven

- 从[Maven官网](http://maven.apache.org/download.cgi)下载压缩包，然后解压

![download](C:\Users\ZNV\Desktop\Notes\images\download.png)

- 添加系统环境变量

![system_path0](C:\Users\ZNV\Desktop\Notes\images\system_path0.png)

![system_path1](C:\Users\ZNV\Desktop\Notes\images\system_path1.png)

- 检查 Maven 是否安装成功

  Windows 环境下，打开 cmd 命令窗口，键入

  `mvn -v`

  结果如下

  ![mvn-v](C:\Users\ZNV\Desktop\Notes\images\mvn-v.png)

- 至此，完成对 Maven 的环境配置。

### 二、 使用入门

#### 2.1  认识 Maven 目录

```
project_name
    |-- src
        |—— main
            |-- java
            |-- resources
        |-- test
            |-- java
            |-- resources
    |-- target
    |-- pom.xml
```

- ##### Maven 项目标准目录结构[（官方文档）](http://maven.apache.org/guides/introduction/introduction-to-the-standard-directory-layout.html)

| Directory            | Role                                                         |
| -------------------- | ------------------------------------------------------------ |
| `src/main/java`      | Application/Library sources                                  |
| `src/main/resources` | Application/Library resources                                |
| `src/main/filters`   | Resource filter files                                        |
| `src/main/webapp`    | Web application sources                                      |
| `src/test/java`      | Test sources                                                 |
| `src/test/resources` | Test resources                                               |
| `src/test/filters`   | Test resource filter files                                   |
| `src/it`             | Integration Tests (primarily for plugins)                    |
| `src/assembly`       | Assembly descriptors                                         |
| `src/site`           | Site                                                         |
| `LICENSE.txt`        | Project's license                                            |
| `NOTICE.txt`         | Notices and attributions required by libraries that the project depends on |
| `README.txt`         | Project's readme                                             |

#### 2.2  编写 POM

​ POM（Project  Object  Model，项目对象模型）定义了项目的基本信息，用于描述项目如何构建，声明项目依赖等。pom.xml 文件是 Maven 项目的核心。

~~~xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation="http://maven.apache.org/POM/4.0.0    http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <!-- 当前pom的版本,对于 Maven 2 及 Maven 3 来说，它只能是 4.0.0 -->
    <modelVersion>4.0.0</modelVersion>

    <!-- GAV坐标，必需 -->
    <groupId>cn.edu.myapp</groupId>
    <artifactId>app-parent</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <!-- 默认是jar，可选值有war，zip，pom等 -->
    <!-- 所有带有子模块的项目的packaging都为pom。
    packaging如果不进行配置，它的默认值是jar，代表Maven会将项目打成一个jar包。 -->
    <packaging>pom</packaging>

    <!-- 项目描述名 -->
    <name>maven-study</name>
    <!-- 项目地址 -->
    <url>http://maven.apache.org</url>
    <!-- 开发人员信息 -->
    <developers></developers>
    <!-- 项目描述 -->
    <description></description>
    <!-- 许可信息 -->
    <licenses></licenses>
    <!-- 组织信息 -->
    <organization></organization>

    <!-- 配置参数 -->
    <properties>
        <!-- 配置项目编译编码为UTF-8 -->
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    </properties>
    
    <!-- 依赖集，用于配置依赖 -->
    <dependencies>
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>3.8.1</version>
            
            <!-- 依赖范围：这个jar包只在范围内生效，范围外引用会报错，这里让junit只在test时被依赖。
                 其他一些情况，如：servlet-api.jar，在编译阶段需要引用，而在服务器运行阶段则不需要引				   用，就可以使用scope
			-->
            <scope>test</scope>

            <!-- 默认为false，即子项目默认都继承；true时子项目必须显式引用 -->
            <optional>false</optional>

            <!-- 排除依赖列表：用于去除传递依赖等 -->
            <exclusions>
                <exclusion></exclusion>
            </exclusions>
        </dependency>
    </dependencies>
    
    <!-- 
		依赖管理：用于帮助管理chidren的dependencies。
		例如如果parent使用dependencyManagement定义了一个dependency on junit:junit4.0,那么它的children就可以只引用 groupId和artifactId,而version就可以通过parent来设置。
     -->     
    <dependencyManagement>
        <dependencies>
            <dependency></dependency>
        </dependencies>
    </dependencyManagement> 
    
    <!-------------------------------- 构建项目需要的信息 ---------------------------> 
    <build>
        <!-- 该元素设置了项目源码目录，当构建项目的时候，构建系统会编译目录里的源码。该路径是相对
             于pom.xml的相对路径。 --> 
        <sourceDirectory></sourceDirectory> 
        
        <!-- 该元素设置了项目单元测试使用的源码目录，当测试项目的时候，构建系统会编译目录里的源码。
             该路径是相对于pom.xml的相对路径。 --> 
        <testSourceDirectory></testSourceDirectory> 

        <!-- 被编译过的应用程序class文件存放的目录。 --> 
        <outputDirectory></outputDirectory> 

        <!-- 被编译过的测试class文件存放的目录。 --> 
        <testOutputDirectory></testOutputDirectory>
        
        <!-- 这个元素描述了项目相关的所有资源路径列表，例如和项目相关的属性文件，这些资源被包含在
             最终的打包文件里。 --> 
        <resources> 
            <!-- 这个元素描述了项目相关或测试相关的所有资源路径 --> 
            <resource> 
                <!-- 描述了资源的目标路径。该路径相对target/classes目录（例如	  								 ${project.build.outputDirectory}）。
                     举个例子，如果你想资源在特定的包里(org.apache.maven.messages)，你就必须该元素 					   设置为org/apache/maven/messages。然而，如果你只是想把资源放到源码目录结构里，					   就不需要该配置。 --> 
                <targetPath></targetPath> 

                <!-- 描述存放资源的目录，该路径相对POM路径 --> 
                <directory></directory>

                <!-- 包含的模式列表，例如**/*.xml. --> 
                <includes>
                    <include></include>
                </includes>

                <!-- 排除的模式列表，例如**/*.xml -->
                <excludes>
                    <exclude></exclude>
                </excludes>
            </resource> 
        </resources>
        
        <!--------- 该项目使用的插件列表。---------> 
        <plugins> 
            <!-- plugin元素包含描述插件所需要的信息。 --> 
            <plugin> 
                <!-- 插件在仓库里的group ID --> 
                <groupId></groupId> 

                <!-- 插件在仓库里的artifact ID --> 
                <artifactId></artifactId> 

                <!-- 被使用的插件的版本（或版本范围） --> 
                <version></version> 
                
                <!-- 作为DOM对象的配置 --> 
                <configuration></configuration> 
            </plugin> 
        </plugins>
    </build>
    
    <!-- 指定父模块 -->
    <parent>
     <groupId></groupId>
        <artifactId></artifactId>
        <version></version>
        <!-- relativePath: 可选，父项目的pom.xml文件的相对路径。默认值是 ../pom.xml 。
             Maven首先在构建当前项目的地方寻找父项目的pom，其次在文件系统的这个位置（relativePath位
			 置），然后在本地仓库，最后在远程仓库寻找父项目的pom。 -->
        <relativePath></relativePath>
    </parent>

    <!-- 用于聚合编译多个maven模块 -->
    <modules>
        <module>app-util</module>
        <module>app-dao</module>
        <module>app-service</module>
        <module>app-web</module>
    </modules>
</project>
~~~

**点[这里](https://www.cnblogs.com/hafiz/p/5360195.html)，参阅更详细的pom.xml配置**。

[**官方文档**](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)
