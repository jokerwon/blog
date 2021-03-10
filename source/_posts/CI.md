---
title: CI/CD
tag: [Devops, Jenkins, CI/CD]
---

## 前言

CI/CD 是一种通过在应用开发阶段引入自动化来频繁向客户交付应用的方法。CI/CD 的核心概念是持续集成、持续交付和持续部署。CI/CD 可让持续自动化和持续监控贯穿于应用的整个生命周期（从集成和测试阶段，到交付和部署）。这些关联的事务通常被统称为“CI/CD 管道”，由开发和运维团队以敏捷方式协同支持。

<!-- more -->

## Jenkins

### 简介

- Jenkins 是一款使用 Java 语言开发的开源的自动化服务器。我们通过界面或 Jenkinsfile 告诉它执行什么任务，何时执行。理论上，我们可以让它执行任何任务，但是通常只应用于持续集成和持续交付。
- Jenkins 通常作为一个独立的应用程序在其自己的流程中运行， 内置 Java servlet 容器/应用程序服务器（[Jetty](http://www.eclipse.org/jetty/)）。

### 安装部署

**前置条件**

- 系统已存在 Java 8 环境

#### 基于 Docker 安装

// TODO

#### 基于 War 包安装

1. [下载最新版的 War 文件](http://mirrors.jenkins.io/war-stable/latest/jenkins.war) ，并将其放置在合适的目录下。

   > 也可在 [此处](http://mirrors.jenkins.io/war-stable/) 查找想要的稳定版本。

2. 运行命令 `java -jar <war_filename> [--httpPort=<port>]` 。*推荐使用守护进程运行 war 文件*。

3. 开始 **[设置向导](#设置向导)** 。

#### 基于 Mac 安装

1. 使用 homebrew 安装。

   ~~~sh
   # 安装最新版本
   brew install jenkins
   # 或安装 LTS 发行版
   brew install jenkins-lts
   
   # 启动服务
   brew services start jenkins
   ~~~

2. 开始 **[设置向导](#设置向导)** 。

#### 基于 Linux 安装

1. 以 ubuntu 为例，使用 apt-get 命令安装。

   ~~~sh
   wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
   # 更新数据源
   sudo sh -c 'echo deb http://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
   sudo apt-get update
   # 执行安装
   sudo apt-get install jenkins
   
   # 启动服务
   service jenkins start
   ~~~

2. 开始 **[设置向导](#设置向导)** 。

**提示**

> Jenkins 工作目录 `/var/lib/jenkins`
>
> 日志存放目录 `/var/log/jenkins`
>
> 默认配置文件 `/etc/default/jenkins`
>
> *以上仅供参考*

#### 设置向导

*部分引导基于 ubuntu 18.04.1 LTS 64位*

1. 安装运行后访问 <http://localhost:8080> ，等待 **解锁 Jenkins** 的页面出现。(未改变运行默认端口的前提下)

   ![Getting Start](https://gitee.com/jokerwon/images/raw/master/img/20201216093734.jpg)

2. 从 Jenkins 控制台日志输出中，寻找并复制自动生成的默认密码。

   ![Default Password](https://gitee.com/jokerwon/images/raw/master/img/20201216095410.png)

   > - 此次安装的默认日志路径为 `/var/log/jenkins/jenkins.log` ，如果日志遗失，可以从 `/var/lib/jenkins/secrets/initialAdminPassword` 获取初始密码
   > - 必须在新Jenkins安装中的安装向导中输入此密码才能访问Jenkins的主UI。 如果您在设置向导中跳过了后续的用户创建步骤， 则此密码还可用作默认admininstrator帐户的密码（使用用户名“admin”）

3. 在  **解锁 Jenkins** 的页面输入刚刚复制的密码来解锁 Jenkins，而后，开始你的自定义 Jenkins 设置，后期亦可在 `系统管理` 中进行系统配置和插件管理。

#### 安装时问题

1. ##### 启动 Jenkins 时遇到 ERROR: No Java executable found in current PATH: /bin:/usr/bin:/sbin:/usr/sbin的问题

   检查环境变量，执行 `echo $PATH` ，查看是否含存在 jdk；

   ~~~sh
   echo $PATH
   /usr/lib/jvm/jdk1.8.0_271/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin
   ~~~

    如果不存在则需要去检查 Java 环境；存在的话为其建立软链接：

   ~~~sh
   ln -s /usr/lib/jvm/jdk1.8.0_271/bin/java /usr/bin/java
   ~~~

   重新启动服务。

2. ##### 下载插件太慢

   1）进入 **系统管理 => 插件管理 => 高级** ，修改选项 **升级站点(Update Site)** 为国内镜像，此处推荐 `https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json` 。

   但是我们打开该文件，发现文件中的更新地址仍然是官网的更新地址，所以此处修改配置只能在获取插件更新地址文件时加速。

   ![UpdateCenterJSON](https://gitee.com/jokerwon/images/raw/master/img/20201216101356.png)

   2）由于 Jenkins 常常不会去更新该文件，我们可以**将 `update-center.json` 文件中的更新地址替换为国内镜像源** ，即使用 `sed` 命令手动替换文件内容，将 `updates.jenkins-ci.org(旧)` 或 `updates.jenkins.io(新)` 替换为 `mirrors.tuna.tsinghua.edu.cn`，`www.google.com` 替换为 `www.baidu.com` 。需要注意的是，默认的官方更新地址可能由于版本不同而改变，所以你应该检查文件中的地址来确认应该修改的地址内容。此处推荐典型的两种替换方法。

   ~~~sh
   # 进入Jenkins工作目录
   cd /var/lib/jenkins/updates
   
   # 备份
   cp default.json default.json.bak
   
   # 替换URL
   # 老版本
   sed -i 's#http://updates.jenkins-ci.org/download#https://mirrors.tuna.tsinghua.edu.cn/jenkins#g' default.json && sed -i 's#http://www.google.com#https://www.baidu.com#g' default.json
   # 新版本
   sed -i 's#https://updates.jenkins.io/download#https://mirrors.tuna.tsinghua.edu.cn/jenkins#g' default.json && sed -i 's#http://www.google.com#https://www.baidu.com#g' default.json
   ~~~

   3）重启 Jenkins。

3. ##### Jenkins 系统权限问题

   1） 为 Jenkins 用户添加相应文件的权限。

   ~~~sh
   chown -R jenkins <path>
   ~~~

   2）暴力点，使 Jenkins 以 root 用户执行。

   ~~~sh
   # 打开配置文件
   vi /etc/default/jenkins
   # 找不到的话尝试这个
   vi /etc/sysconfig/jenkins
   
   # 找到JENKINS_USER和JENKINS_GROUP，修改为root
   JENKINS_USER=root
   JENKINS_GROUP=root
   ~~~

   3）重启 Jenkins 服务。

### Pipeline

#### 概念

Jenkins 流水线 (或简单的带有大写 "P" 的 "Pipeline" ) 是一套插件，它支持实现和集成 *continuous delivery pipelines* 到 Jenkins。

~~~groovy
pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        echo 'Hello World'
      }
    }
  }
}
~~~



#### 意义

- **更好的自动化** : 自动地为所有分支创建流水线构建过程并拉取请求。
- **更好地版本化** : 将 pipeline 提交到软件版本库中进行版本控制。（通过 Jenkinsfile）
- **更好地协作** : pipeline的每次修改对所有人都是可见的。除此之外，还可以对pipeline进行代码审查。
- **更好的重用性** : 手动操作没法重用，但是代码可以重用。

#### 语法

Jenkins pipeline其实就是基于Groovy语言实现的一种DSL（领域特定语言），用于描述整条流水线是如何进行的。流水线的内容包括执行编译、打包、测试、输出测试报告等步骤。

##### 必要的 Groovy 语法

学习 Jenkins pipeline 可以不需要任何Groovy知识，但是了解 Groovy，写 pipeline 会如虎添翼。

- 定义变量。

  ~~~groovy
  def x = 'abc'
  def y = 2
  def slot = "hello ${x}" // Hello abc
  // 单引号和三单引号不支持插值
  def linebreak = '''
   line 0
   line 1
   Hello ${y}
  ''' // Hello ${y}
  // 没想到吧，还支持三双引号，支持插值
  def multiLine = """
  	x = ${x}
  	"hi ${y}"
  """ // x = abc \n "hi 2"
  ~~~

- 调用方法可以省略括号

  ~~~groovy
  System.out.println "Hello World"
  ~~~

- 支持命名参数和参数默认值

  ~~~groovy
  def createName(String firstName = "Micro", String lastName = "soft") {
    return "${firstName} ${lastName}"
  }
  
  createName lastName = "gle", firstName = "Goo"
  createName() // 此时不可省略括号
  ~~~

- 支持闭包

  ~~~groovy
  // 定义闭包
  def codeBlock = { echo 'Hello' }
  // 可以被当成函数直接调用
  codeBlock()
  
  // 还可以将闭包当作参数传递给函数
  def pipeline(closure) {
    closure()
  }
  
  pipeline({ print "Hello Closure" })
  // 省略括号
  pipeline {
    print "Hello Closure"
  }
  ~~~

- 闭包的另类用法

  ~~~groovy
  def stage(String name, closure) {
    println name
    closure()
  }
  
  // 正常情况下
  stage("stageName", { println "closure" })
  
  // Groovy 提供了一种写法
  stage("stageName") {
    println "closure"
  }
  ~~~

##### pipeline 的最简结构

~~~groovy
pipeline {
  agent any
  stages {
    stage('build') {
      steps {
        echo 'Hello World'
      }
    }
    
    stage('build1') {
      steps {
        echo 'Hello World'
      }
    }
  }
  
  post {
    always {
      
    }
  }
}
~~~

以下每个部分是必需的，否则会报错。

- `pipeline` : 代表整条流水线，包含整条流水线的逻辑。
- `stage` : 流水线的阶段。每个阶段都必须有名称。本例中，build 就是此阶段的名称。
- `stages` : 流水线中多个stage的容器。**`stages` 部分至少包含一个 `stage`**。
- `steps` : 代表阶段中的一个或多个具体步骤（step）的容器。`steps` 部分至少包含一个步骤，本例中，`echo` 就是一个步骤。**在一个 `stage` 中有且只有一个 `steps`**。
- `agent` : 指定流水线的执行位置（Jenkins agent）。流水线中的每个阶段都必须在某个地方（物理机、虚拟机或Docker容器）执行， `agent` 部分即指定具体在哪里执行。

##### post

post 部分包含的是在整个 pipeline 或阶段完成后一些附加的步骤。post 部分是可选的。

根据pipeline或阶段的完成状态，post部分分成多种条件块：

- `always` : 不论当前完成状态是什么，都执行。相当于 `finally` 代码块。
- `changed` : 只要此次任务的完成状态和上一次不同则执行。
- `fixed` : 上一次完成状态为失败或者不稳定（unstable），此次完成状态为成功时则执行。
- `regression` : 上一次完成状态为成功，当前完成状态为失败、不稳定或中止（aborted）时执行。
- `aborted` : 当前执行结果是中止状态时（一般为人为中止）执行。
- `failure` : 当前完成状态为失败时执行。
- `success` : 当前完成状态为成功时执行。
- `unstable` : 当前完成状态为不稳定时执行。
- `cleanup` : 清理条件块。不论当前完成状态是什么，在其他所有条件块执行完成后都执行。

##### options

用于配置整个 Jenkins pipeline 本身的选项。根据具体的选项不同，可以将其放在 `pipeline` 块或 `stage` 块中。

以下是常用的选项：

- `buildDiscarder` : 保存最近历史构建记录的数量。当pipeline执行完成后，会在硬盘上保存制品和构建执行日志，如果长时间不清理会占用大量空间，设置此选项后会自动清理。此选项**只能在 `pipeline` 下的 `options` 中使用**。

  ~~~groovy
  options {
    buildDiscarder(logRotator(numToKeepStr: '10'))
  }
  ~~~

- `checkoutToSubdirectory` : Jenkins从版本控制库拉取源码时，默认检出到工作空间的根目录中，此选项可以指定检出到工作空间的子目录中。

  ~~~groovy
  options {
    checkoutToSubdirectory('subdir')
  }
  ~~~

- `disableConcurrentBuilds` : 同一个 pipeline，Jenkins 默认是可以同时执行多次的。此选项是为了禁止pipeline同时执行。

  ~~~groovy
  options {
    disableConcurrentBuilds()
  }
  ~~~

- `newContainerPerStage` : 当agent为docker或dockerfile时，指定在同一个Jenkins节点上，每个stage都分别运行在一个新的容器中，而不是所有stage都运行在同一个容器中。

  ~~~groovy
  options {
    newContainerPerStage()
  }
  ~~~

- `retry` : 当发生失败时进行重试，可以指定整个 pipeline 的重试次数。需要注意的是，**这个次数是指总次数，包括第1次失败**。当使用 `retry` 选项时，`options` 可以被放在 `stage` 块中。

  ~~~groovy
  options {
    retry(4)
  }
  ~~~

- `timeout` : 如果 pipeline 执行时间过长，超出了我们设置的 timeout 时间，Jenkins 将中止 pipeline。

  ~~~groovy
  options {
    timeout(time: 1, unit: 'HOURS')
  }
  ~~~

  此外，job 依赖的一些插件的选项也可以在 `options` 中配置。

##### 在声明式 pipeline 中使用脚本

Jenkins pipeline 专门提供了一个 `script` 步骤，你能在 `script` 步骤中像写代码一样写 pipeline 逻辑。

~~~groovy
pipeline {
  agent any
  
  stages {
    stage('building') {
      steps {
        script {
          def persons = ['Bob', 'Jest', 'Tim']
          for (int i = 0; i < persons.size(); ++i) {
            echo persons[i]
          }
        }
      }
    }
  }
}
~~~

在 `script` 块中的其实就是 Groovy 代码。大多数时候，我们是不需要使用 `script` 步骤的。如果在 `script` 步骤中写了大量的逻辑，则说明你应该把这些逻辑拆分到不同的阶段，或者放到**共享库**中。

##### when

`when`指令允许`Pipeline`根据给定条件确定是否应执行该阶段。该`when`指令必须至少包含一个条件。如果`when`指令包含多个条件，则所有子条件必须返回`true`才能执行该阶段。这与子条件嵌套在`allOf`条件中的情况相同（参见下面的示例）。如果使用`anyOf`条件，请注意一旦找到第一个`true`条件，条件就会跳过剩余的测试条件。

更复杂的条件结构可使用嵌套条件建：`not`，`allOf`或`anyOf`。嵌套条件可以嵌套到任意深度。

- 是否必须： 非必须
- 参数： 没有
- 所在位置: 在`stage`指令内

```
pipeline {
    agent none
    stages {
        stage('Example Build') {
        		when {
        				branch 'production'
                environment name: 'DEPLOY_TO', value: 'production'
                anyOf {
                    environment name: 'DEPLOY_TO', value: 'dev'
                    environment name: 'DEPLOY_TO', value: 'staging'
                }
        		}
            steps {
                echo 'Hello World'
            }
        }
        stage('Example Deploy') {
            agent {
                label "some-label"
            }
            when {
                beforeAgent true
                branch 'production'
            }
            steps {
                echo 'Deploying'
            }
        }
    }
}
```

###### branch

当正在构建的分支与给定的分支模式匹配时执行阶段。如 `when { branch 'master' }` . 这仅在多分支`Pipeline`中有效。

###### buildingTag

构建构建标记时执行阶段。例: `when { buildingTag() }`

###### changelog

如果构建的SCM更新日志包含给定的正则表达式模式，则执行该阶段 例如: `when { changelog '.*^\[DEPENDENCY\] .+$' }`

###### changeset

如果构建的SCM变更集包含与给定字符串或glob匹配的一个或多个文件，则执行该阶段。 例如: `when { changeset "**/*.js" }`

默认情况下，路径匹配不区分大小写，可以使用caseSensitive参数关闭, 例如: `when { changeset glob: "ReadMe.*", caseSensitive: true }`

###### changeRequest

如果当前构建用于“更改请求”（GitHub和Bitbucket上的`Pull Request`，GitLab上的`Merge`请求或Gerrit中的`change`等），则执行该阶段。 如果没有传递任何参数，则每个更改请求都会运行该阶段， 例如: `when { changeRequest() }`.

通过向变更请求添加带参数的过滤器属性，可以使阶段仅在匹配的变更请求上运行。 可能的属性是`id，target，branch，fork，url，title，author，authorDisplayName和authorEmail`。 其中每个对应一个`CHANGE_*`环境变量， 例如: `when { changeRequest target: 'master' }`.

可以在属性之后添加可选参数比较器，以指定如何评估匹配的任何模式：`EQUALS`用于简单字符串比较（默认值），`GLOB`用于ANT样式路径glob（与例如变更集相同）或`REGEXP`用于常规 表达匹配。例如: `when { changeRequest authorEmail: "[\w_-.]+@example.com", comparator: 'REGEXP' }`

###### environment

当指定的环境变量设置为给定值时执行阶段， 例如: `when { environment name: 'DEPLOY_TO', value: 'production' }`

###### equals

当期望值等于实际值时执行阶段， 例如: `when { equals expected: 2, actual: currentBuild.number }`

###### expression

当指定的Groovy表达式求值为`true`时执行阶段，例如: `when { expression { return params.DEBUG_BUILD } }` .

请注意，从表达式返回字符串时，必须将它们转换为布尔值或返回`null`表示为`false`。 简单地返回`0`或`false`仍将评估为`true`。

###### tag

如果`TAG_NAME`变量与给定模式匹配，则执行该阶段。例如: `when { tag "release-*" }`.

如果提供了空模式，则如果`TAG_NAME`变量存在，则将执行该阶段（与buildingTag（）相同）。

可以在属性之后添加可选参数比较器，以指定如何评估匹配的任何模式：`EQUALS`用于简单字符串比较（默认值），`GLOB`用于ANT样式路径glob（与例如变更集相同）或`REGEXP`用于常规 表达匹配。 例如: `when { tag pattern: "release-\d+", comparator: "REGEXP"}`

###### not

嵌套条件为false时执行阶段。 必须包含一个条件。 例如: `when { not { branch 'master' } }`

###### allOf

当所有嵌套条件都为真时执行阶段。 必须至少包含一个条件。例如: `when { allOf { branch 'master'; environment name: 'DEPLOY_TO', value: 'production' } }`

###### anyOf

至少有一个嵌套条件为真时执行阶段。 必须至少包含一个条件。例如: `when { anyOf { branch 'master'; branch 'staging' } }`

##### tools

// TODO

##### enviroment

// TODO

##### parameters

// TODO

##### triggers

// TODO

### 环境变量

环境变量可以被看作是pipeline与Jenkins交互的媒介。可分为 Jenkins 内置变量和自定义变量。

#### Jenkins 内置变量

Jenkins 通过 `env` 的全局变量，将 Jenkins 内置环境变量暴露出来。

~~~groovy
pipeline {
  agent any
  stages {
    stage('example') {
      steps {
        echo "Running ${env.BUILD_NUMBER} on ${env.JENKINS_URL}" // 方法一
        echo "Running $env.BUILD_NUMBER on $env.JENKINS_URL" // 方法二
        echo "Running ${BUILD_NUMBER} on ${JENKINS_URL}" // 方法三，不推荐，变量冲突时难以排查问题
      }
    }
  }
}
~~~

常用内置变量

- `BUILD_NUMBER` : 构建号，累加的数字。
- `BRACH_NAME` : 多分支 pipeline 项目支持。当需要根据不同的分支做不同的事情时就会用到，比如通过代码将 release 分支发布到生产环境中、master分支发布到测试环境中。
- `BUILD_URL` : 当前构建的页面 URL。如果构建失败，则需要将失败的构建链接放在邮件通知中，这个链接就可以是 BUILD_URL。
- `GIT_BRANCH` : 通过 git 拉取的源码构建的项目才会有此变量。

#### 自定义 pipeline 环境变量

声明式 pipeline 提供了 `environment` 指令，方便自定义变量。

`environment` 指令可以在 `pipeline` 中定义，代表变量作用域为整个 pipeline；也可以在 `stage` 中定义，代表变量只在该阶段有效。

~~~groovy
pipeline {
  agent any
  environment {
    OUTER = "entire job"
  }
  stages {
    stage('example') {
      environment {
        INNER = "current stage"
      }
      steps {
        echo "${OUTER} and ${INNER}"
      }
    }
  }
}
~~~

但是这些**自定义变量只能在当前 pipeline 中使用**，比如 pipeline a 访问不到 pipeline b 的变量。在 pipeline 之间共享变量可以通过参数化 pipeline 来实现。

如果在 `environment` 中定义的变量与 `env` 中的变量重名，那么被重名的变量的值会被覆盖掉。

#### 自定义全局变量

Jenkins 2.269 版本中，可以在 **系统管理 => 系统配置 => 全局属性** 中添加。使用时无需前缀，直接引用。

### 触发器

#### 触发条件

基于前面的知识，每当我们推送代码后，需要到 Jenkins 系统中手动触发构建。显然，这不够自动化。自动化是指 pipeline 按照一定的规则自动执行。而这些规则被称为 pipeline 触发条件。

分析目前常用的触发器，大概可以分为两类：**时间触发** 和 **事件触发** 。

***下文基于 pipeline 语法来展开介绍触发器，但依然可以在 配置=>构建触发器 选项中创建触发器***

#### 时间触发

时间触发是指定义一个时刻，每到这个时刻就会触发 pipeline 执行。在 Jenkins pipeline 中使用 `trigger` 指令来定义时间触发。`tigger` 指令只能被定义在 `pipeline` 块内，Jenkins 内置支持 cron、pollSCM，upstream 三种方式。其他方式可以通过插件来实现。

#####  定时执行 cron

~~~groovy
pipleline {
  agent any
  triggers {
    cron('0 0 * * *')
  }
  stages {
    stage('Nightly build') {
      steps {
        echo '这是一个耗时的构建，每天凌晨构建'
      }
    }
  }
}
~~~

Jenkins trigger cron 语法采用的是UNIX cron语法（有些细微的区别）。一条 cron 包含 5 个字段，使用空格或 Tab 分隔，格式为：MINUTE HOUR DOM MONTH DOW(星期几)。也可以使用正则。

##### 轮询代码仓库：pollSCM

这种方式比较耗用资源，在一些特殊情况下，比如外网的代码仓库无法调用内网的 Jenkins，或者反过来，才会采用这种方式。

~~~groovy
pipeline {
  agent any
  triggers {
    // 每分钟判断一次代码是否有变化
    pollSCM('H/1 * * * *')
  }
}
~~~

#### 事件触发

事件触发就是发生了某个事件就触发 pipeline 执行。这个事件可以是你能想到的任何事件。比如手动在界面上触发、其他 job 主动触发、HTTP API Webhook 触发等。

##### 由上游任务触发：upstream

当 B 任务的执行依赖 A 任务的执行结果时，A 就被称为 B 的上游任务。在 Jenkins2.22 及以上版本中，`trigger` 指令开始支持 upstream类型的触发条件。upstream 的作用就是能让 B pipeline自行决定依赖哪些上游任务。

~~~groovy
// job1, job2 为任务名
triggers {
  upstream(upstreamProjects: 'job1,job2', threshold: hudson.model.Result.SUCCESS)
}
~~~

当upstreamProjects参数接收多个任务时，使用，分隔。threshold参数是指上游任务的执行结果是什么值时触发。

##### *WebHook 触发

当代码仓库的某些事件（如Push、Merge等）被触发时，仓库主动去触发 Jenkins 执行构建。

![webhook](https://gitee.com/jokerwon/images/raw/master/img/20201216151639.png)

下面以 GitLab 为例。

*假设已存在 Gitlab项目*

1. 为 Jenkins 安装 GitLab 插件。

2. 创建全局凭证。

   1）进入 **系统管理 => Manage Credentials => 添加凭据** 。

   2）添加 ssh 凭证。

   ![create Credentials](https://gitee.com/jokerwon/images/raw/master/img/20201216152732.png)

3. 创建单分支 pipeline 项目。

4. 配置触发器。

   ![trigger](https://gitee.com/jokerwon/images/raw/master/img/20201216154442.png)

5. 进入 Gitlab 的项目，进入 **Setting => Integrations**，添加 Hook。

   ![gitlabSetting](https://gitee.com/jokerwon/images/raw/master/img/20201216154553.png)

6. 添加 Hook 后，可以点击 Test 按钮测试相关事件。





### 权限

// TODO

## 基于 Docker

## Drone
