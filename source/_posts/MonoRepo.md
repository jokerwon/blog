---
title: MonoRepo
---

### 一、背景

#### 1.1 什么是 MonoRepo ？

MonoRepo 是一种仓库管理模式，它的理念是把多个项目放在一个仓库里面，相对立的是传统的 MultiRepo 模式，即每个项目对应一个单独的仓库来分散管理。

![mono-multi](https://doc.zeaho.com/display/RND/MonoRepo?preview=/248389371/248389458/image2022-5-18_11-13-46.png)



#### 1.2 传统的 MultiRepo 有什么好处？

- 仓库体积小，模块划分清晰。
- 权限管理方便。



#### 1.3 MultiRepo 有什么痛点？

###### 代码复用

很难做到项目层级的代码复用。

###### 版本依赖

依赖重复安装，多个依赖可能在多个仓库中存在不同的版本，npm link 时不同项目的依赖可能会存在冲突问题。

项目之间存在依赖时，版本同步会变得困难。如果一个项目的更新存在破坏性修改(break change)，则依赖于它的所有项目必须都更新对其的依赖版本。

###### 项目基建

每个项目需要单独配置工作规范以及 CI/CD 流程等。

###### 开发体验

在多个项目之间切换工作空间，导致心智负担加重。



#### 1.4 为什么要用 MonoRepo ？

###### 代码重用更简单

可以抽离各项目共同的工具和组件。

###### 依赖管理更简单

项目的公共依赖可以提取到根目录中。

###### 基础建设更统一

各项目使用同一套配置，如果有需求，可以在子目录中进行定制。

###### 工作流程更一致



#### 1.5 MonoRepo 带来了什么问题？

###### 权限管理变得困难

项目都存储在同一个 Git 仓库，没有办法对项目做权限管理。

###### 仓库体积逐渐庞大

当业务项目变得庞大，会导致代码仓库的体积也变得愈发庞大。



### 二、方案

###### 1. [Yarn Workspace](https://www.yarnpkg.cn/features/workspaces)

###### 2. [Lerna](https://www.lernajs.cn/)

###### 3. [Pnpm Workspace](https://pnpm.io/zh/workspaces)

###### 4. [Turborepo](https://turborepo.org/)



### 三、实战1

***\*基于 pnpm + turborepo***



#### 3.1 创建 monorepo

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



### 四、实战2

#### 4.1 创建 monorepo

~~~shell
mkdir mini-react

cd mini-react

pnpm init

mkdir packages

touch pnpm-workspace.yaml
~~~

创建子包

~~~shell
cd packages
mkdir dup
mkdir shared

# /packages/dup
pnpm init
~~~



~~~yaml
# pnpm-workspace.yaml
packages:
  - 'packages/*'
~~~



#### 4.2 eslint

~~~shell
pnpm i eslint -Dw

npx eslint --init

pnpm i -Dw @typescript-eslint/eslint-plugin  @typescript-eslint/parser typescript
~~~

添加脚本

~~~json
// package.json
"scripts": {
  "lint": "eslint --ext .js,.ts,.jsx,.tsx --fix --quiet ./packages"
}
~~~



#### 4.3 prettier

~~~shell
pnpm i -Dw prettier

touch .prettierrc.json
~~~

~~~json
// .prettierrc.json
{
  "printWidth": 80,
  "tabWidth": 2,
  "useTabs": true,
  "singleQuote": true,
  "semi": true,
  "trailingComma": "none",
  "bracketSpacing": true
}
~~~

`eslint` 集成 `prettier`

~~~shell
pnpm i -Dw eslint-config-prettier eslint-plugin-prettier
~~~

~~~json
// .eslintrc.json
{
	"env": {
		"browser": true,
		"es2021": true,
		"node": true
	},
	"extends": [
		"eslint:recommended",
		"plugin:@typescript-eslint/recommended",
		"prettier",
		"plugin:prettier/recommended"
	],
	"parser": "@typescript-eslint/parser",
	"parserOptions": {
		"ecmaVersion": "latest",
		"sourceType": "module"
	},
	"plugins": ["@typescript-eslint", "prettier"],
	"rules": {
		"prettier/prettier": [
			"error",
      // 这里需要将 prettier 的配置加入进来，否则会出现 eslint 和 prettier 的行为不一致的问题
			{
				"singleQuote": true,
				"trailingComma": "all",
				"printWidth": 80,
				"tabWidth": 2,
				"useTabs": false,
				"jsxBracketSameLine": false,
				"eslintIntegration": false,
				"bracketSpacing": true,
				"arrowParens": "avoid"
			}
		],
		"no-case-declarations": "off",
		"no-constant-condition": "off",
		"@typescript-eslint/ban-ts-comment": "off"
	}
}
~~~



#### 4.4 husky & commitlint

~~~shell
# husky
pnpm i -Dw husky

npx husky install

npx husky add .husky/pre-commit "pnpm lint"

# commitlint
pnpm i -Dw commitlint @commitlint/cli @commitlint/config-conventional

touch .commitlintrc.js
~~~

~~~js
// .commitlintrc.js
module.exports = {
  extends: ["@commitlint/config-conventional"]
}; 
~~~

集成到 `husky`

~~~shell
npx husky add .husky/commit-msg "npx --no-install commitlint -e $HUSKY_GIT_PARAMS"
~~~



#### 4.5 typescript

~~~json
// tsconfig.json
{
  "compileOnSave": true,
  "compilerOptions": {
    "target": "ESNext",
    "useDefineForClassFields": true,
    "module": "ESNext",
    "lib": ["ESNext", "DOM"],
    "moduleResolution": "Node",
    "strict": true,
    "sourceMap": true,
    "resolveJsonModule": true,
    "isolatedModules": true,
    "esModuleInterop": true,
    "noEmit": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noImplicitReturns": false,
    "skipLibCheck": true,
    "baseUrl": "./packages",
  }
}
~~~



#### 4.6 tsup

~~~shell
pnpm i -Dw tsup

touch tsup.config.ts
~~~



~~~typescript
// tsup.config.ts
import { defineConfig } from 'tsup';

export default defineConfig([
  {
    entry: ['packages/dup/index.ts'],
    bundle: true,
    splitting: true,
    outDir: 'packages/dup/dist',
    format: ['cjs', 'esm'],
    dts: true,
    shims: true,
  },
]);

~~~



#### 4.7 changesets

~~~shell
pnpm i @changesets/cli -Dw

pnpm changeset init
~~~

~~~json
// package.json
"scripts": {
    "change": "changeset add",
    "change:version": "changeset version",
    "release": "pnpm build && pnpm release:only",
    "release:only": "changeset publish"
}
~~~



#### 4.8 vitest

~~~shell
pnpm i vitest @vitest/coverage-c8 -Dw

touch vitest.config.ts
~~~

~~~typescript
// vitest.config.ts
import { resolve } from 'path';
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    globals: true,
  },
  resolve: {
    alias: {
      '@pursuneer/shared': resolve(__dirname, 'packages/shared/src/index'),
    },
  },
});
~~~

