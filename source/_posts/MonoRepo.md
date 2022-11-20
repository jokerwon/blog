---
title: MonoRepo
---

### 一、背景

#### 1. 什么是 MonoRepo ？

MonoRepo 是一种仓库管理模式，它的理念是把多个项目放在一个仓库里面，相对立的是传统的 MultiRepo 模式，即每个项目对应一个单独的仓库来分散管理。

![mono-multi](https://doc.zeaho.com/display/RND/MonoRepo?preview=/248389371/248389458/image2022-5-18_11-13-46.png)



#### 2. 传统的 MultiRepo 有什么好处？

- 仓库体积小，模块划分清晰。
- 权限管理方便。



#### 3. MultiRepo 有什么痛点？

###### 代码复用

很难做到项目层级的代码复用。

###### 版本依赖

依赖重复安装，多个依赖可能在多个仓库中存在不同的版本，npm link 时不同项目的依赖可能会存在冲突问题。

项目之间存在依赖时，版本同步会变得困难。如果一个项目的更新存在破坏性修改(break change)，则依赖于它的所有项目必须都更新对其的依赖版本。

###### 项目基建

每个项目需要单独配置工作规范以及 CI/CD 流程等。

###### 开发体验

在多个项目之间切换工作空间，导致心智负担加重。



#### 4. 为什么要用 MonoRepo ？

###### 代码重用更简单

可以抽离各项目共同的工具和组件。

###### 依赖管理更简单

项目的公共依赖可以提取到根目录中。

###### 基础建设更统一

各项目使用同一套配置，如果有需求，可以在子目录中进行定制。

###### 工作流程更一致



#### 5. MonoRepo 带来了什么问题？

###### 权限管理变得困难

项目都存储在同一个 Git 仓库，没有办法对项目做权限管理。

###### 仓库体积逐渐庞大

当业务项目变得庞大，会导致代码仓库的体积也变得愈发庞大。



### 二、方案

###### 1. [Yarn Workspace](https://www.yarnpkg.cn/features/workspaces)

###### 2. [Lerna](https://www.lernajs.cn/)

###### 3. [Pnpm Workspace](https://pnpm.io/zh/workspaces)

###### 4. [Turborepo](https://turborepo.org/)



### 三、实战

***\*基于 pnpm + turborepo***



#### 1. 创建 monorepo

创建一个 `monorepo` 仓库可以使用以下两种方式。

- 使用 `turborepo` 的初始化命令。`npx create-turbo@latest`
- 手动创建。



为了使步骤更加清晰，此处选择后者。手动创建 `monorepo` ，再引入  `turborepo` 。

~~~shell
# 创建根目录
mkdir zhgcloud && cd zhgcloud
 
# 初始化
npm init -y
~~~



针对功能进行分层后，创建 `app` 和 `shared` 文件夹，并分别在其下创建对应的子包。操作完成后目录如下。

~~~shell
├── app
│   ├── cm
│   │   └── package.json
│   └── cp
│       └── package.json
├── package.json
└── shared
    ├── components
    │   └── package.json
    └── utils
        └── package.json
~~~



定义工作区，先修改 `package.json` 。

~~~json
// package.json
{
    // ...
    "workspaces": [
        "app/*",
        "shared/*"
    ]
}
~~~



并且在根目录中创建 `pnpm-workspace.yaml` 。

~~~yaml
# pnpm-workspace.yaml
packages:
  - 'app/**'
  - 'shared/**'
  # 排除所有 test
  - '!**/test/**'
~~~



