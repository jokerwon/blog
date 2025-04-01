---
title: react
tags:
---

## 一、环境搭建

### 1.1 Monorepo

~~~shell
mkdir mini-react

cd mini-react

pnpm init

mkdir packages

touch pnpm-workspace.yaml
~~~



~~~yaml
# pnpm-workspace.yaml
packages:
  - 'packages/*'
~~~



### 1.2 eslint

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



### 1.3 prettier

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
		"prettier/prettier": "error",
		"no-case-declarations": "off",
		"no-constant-condition": "off",
		"@typescript-eslint/ban-ts-comment": "off"
	}
}
~~~



### 1.4 husky & commitlint

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



### 1.5 typescript

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



### 1.6 打包工具 rollup

~~~shell
pnpm i -Dw rollup
~~~



## 二、JSX
