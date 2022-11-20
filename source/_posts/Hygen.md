---
title: Hygen
---

<!-- more -->

[TOC]

## 起步

### 安装

```shell
npm i -g hygen
# 或
cnpm i -g hygen
```

### 初始化

```shell
# 进入项目地址
cd /projects/demo

# 初始化模板
hygen init self
```

初始化成功后，会自动在项目根目录下生成 `_templates` 文件夹，并且默认生成了一个名为 `generator` 的生成器及其工作目录。后续会依托该生成器，派生出更多的生成器来满足需求。

> **注意**
>
> 不要删除 `_templates/generator` 文件夹，否则会导致无法创建 `Generator` 。

>**提示**
>
>后续的所有命令行操作，如无特殊说明，都在项目根目录。

### 创建第一个生成器

~~~shell
hygen generator new component
~~~

执行后会生成一个生成器 `component` 及其工作目录，目录中默认存在一个示例文件 `hello.ejs.t` 。

~~~ejs
---
to: app/hello.js
---
const hello = ```
Hello!
This is your first hygen template.

Learn what it can do here:

https://github.com/jondot/hygen
```

console.log(hello)
~~~

### 使用生成器创建文件

~~~shell
hygen component new
~~~

执行后会生成文件目录 `app` 及文件 `app/hello.js` 。至此，一次最基本的 Hygen 旅程已经完成。

## 模板

Hygen 的文件模板分为两个部分，FrontMatter 和 Body（ejs）。

> **说明**
>
> 值得一提的是，虽然默认生成的模板文件扩展名都是 `.t` ，但是你可以使用任何扩展名。这样做是为了避开编辑器的智能操作。

### FrontMatter

文件最上方的描述信息。

~~~ejs
---
to: src/component/demo/index.js
// ...
---
~~~

可能用到的配置：

- `to: string<url>`

  目标路径。**当值为 `null` 时，则会跳过生成。**

  ~~~ejs
  ---
  to: "<%= message ? `where/to/render/${name}.js` : null %>"
  ---
  conditionally rendering template
  ~~~

- `force: boolean`

  默认值 `false` 。是否强制覆盖已存在的文件。

- `unless_exists: boolean`

  默认值 `false`。为 `true` 时只有当不存在重复文件时才会生成文件。

- `from: string<url>`

  加载到 Body 的文件路径。当设置了该配置后，只会从指定文件中加载内容到 Body 中，原来的 Body 会被忽视。

  ~~~ejs
  ---
  to: app/readme.md
  from: shared/docs/readme.md
  ---
  这段文字会被丢失。替代的将是指定文件的内容。
  ~~~

- `after: Regex`

  用于向已存在的文件中插入模板内容。

  ~~~ejs
  ---
  inject: true
  to: package.json
  after: dependencies
  skip_if: react-native-fs
  ---
  "react-native-fs":"*",
  ~~~

  上述模板会向 `package.json` 文件中的 `dependencies` 行后插入 `"react-native-fs":"*",` ，但是当在该位置已存在 `react-native-fs` 时则不会执行插入。

- `before: Regex` or `after: Regex`

  which contain a regular expression of text to locate. The inject line will appear `before` or `after` the located line.

- `prepend` or `append`

  when true, add a line to start or end of file respectively.

- `at_line`

  which contains a line number will add a line at this exact line number.

- `skip_if: Regex`

  which contains a regular expression / text. If exists, injection is skipped.

- `sh: string`

  执行 shell 指令。

  ~~~ejs
  ---
  inject: true
  to: package.json
  after: dependencies
  skip_if: lodash
  sh: cd <%= cwd %> && yarn install
  ---
  "lodash":"*",
  ~~~

### Body

模板内容。使用模板引擎 `ejs` 。语法参见 [文档](https://ejs.bootcss.com/#docs) 。

### Helpers and Inflections

工具对象 `h`。

`// example: <%= h.inflection.pluralize(name) %>`

- `pluralize( str, plural )`
- `singularize( str, singular )`
- `inflect( str, count, singular, plural )`
- `camelize( str, low_first_letter )`
- `underscore( str, all_upper_case )`
- `humanize( str, low_first_letter )`
- `capitalize( str )`
- ``dasherize( str )`
- `titleize( str )`
- `demodulize( str )`
- `tableize( str )`
- `classify( str )`
- `foreign_key( str, drop_id_ubar )`
- `ordinalize( str )`
- `transform( str, arr )`

### Change case helpers

`// example: <%= h.changeCase.camel(name) %>`

- `camel( str )`
- `constant( str )`
- `dot( str )`
- `header( str )`
- `isLower( str )`
- `isUpper( str )`
- `lower( str )`
- `lcFirst( str )`
- `no( str )`
- `param( str )`
- `pascal( str )`
- `path( str )`
- `sentence( str )`
- `snake( str )`
- `swap( str )`
- `title( str )`
- `upper( str )`

### `locals` 对象

所有命令行参数和交互值都自动保存在 `locals` 对象中。

因此，有两种方式引用变量。

- 直接引用

  ~~~ejs
  Hello <%= message %>
  ~~~

- 通过 `locals` 引用

  ~~~ejs
  Hello <%= locals.message %>
  ~~~

### 预定义变量

对于这样一条指令

~~~shell
hygen component new:story
~~~

| Variable       | Content                   | Example                      |
| -------------- | ------------------------- | ---------------------------- |
| `templates`    | Templates path (absolute) | /User/.../project/_templates |
| `actionfolder` | Action path               | /.../component/new           |
| `generator`    | Generator name            | `component`                  |
| `action`       | Action name               | `new`                        |
| `subaction`    | Sub-action name           | `story`                      |
| `cwd`          | Process working directory | /User/.../project            |

## 生成器

### 创建

语法：`hygen generator [ACTION] [NAME]` 或 `hygen generator [ACTION] --name [NAME]`

`ACTION` 表示你想使用何种命令创建一个生成器，可选值有：

- `new`

  创建一个基本的生成器。

- `with-prompt`

  创建一个带有交互的生成器，交互逻辑在 `prompt.js` 中编写。

  > **注意**
  >
  > - 交互脚本的名称并不是固定的。只需要导出指定结构的数组或者含有 `prompt` 属性的对象。
  > - 可以通过自己在 `new` 文件夹下添加 `.js` 文件创建交互逻辑，使 `new` Action 也拥有交互能力。

- `help`

  打印 `help` 文件夹下模板 FrontMatter 部分的 `message` 信息。可以将指令用法等提示信息记录在模板中。

> **说明**
>
> 官网生成的命令解释并不明朗，目前来看，由于 `_templates/generator` 文件夹下默认存在 `new` 、`with-prompt` 和 `help` 文件夹，所以分别存在对应的 Action。

~~~shell
# 创建一个名为 component 的生成器
hygen generator new component
~~~

执行命令后，`_template` 路径下会新增 `component/new/hello.ejs.t` 。

~~~shell
# 可以继续为 component 生成器添加 with-prompt Action
hygen generator with-prompt component
~~~

执行命令后，`_template` 路径下会新增 `component/with-prompt/hello.ejs.t` 和 `component/with-prompt/prompt.js` 。

### 使用

#### 修改模板

修改 `_template/component/new/hello.ejs.t` 文件。

~~~ejs
---
to: src/components/<%= name %>.js
---
import React from 'react';

const <%= name %> = ({}) => {
    console.log('Hello Hygen');
};

export default <%= name %>;
~~~

#### 生成文件

~~~shell
hygen component new Demo
# 或
hygen component new --name Demo
~~~

> **说明**
>
> `new` 后的第一个参数默认被保存到模板文件变量 `locals.name` 中。其他显示指定的参数也会被保存到 `locals` 对象中。

查看 `src/components/Demo.js` 文件。

~~~javascript
import React from 'react';

const Demo = ({}) => {
    console.log('Hello Hygen');
};

export default Demo;
~~~

#### 命令行参数

你可以通过 `--[KEY] [VALUE]` 的方式为模版文件提供变量支持。

~~~shell
hygen component new --name Demo --description 'test file' --author JokerWon --isClass yes
~~~

你可以直接在模板中引用它们，也可以通过 `locals.[KEY]` 的方式。

~~~ejs
---
to: app/<%= name %>.js
---
/**
 * @description <%= description %>
 * @author <%= author %>
 */
import React from 'react';

<% if (locals.isClass === 'yes') { _%>
const <%= name %> = ({}) => {
    console.log('Hello Hygen');
};
<% } else { _%>
class <%= name %> extends React.Component {
    render() {

    }
}
<% } _%>

export default <%= name %>;
~~~

生成的文件是这样的：

~~~javascript
/**
 * @description test file
 * @author JokerWon
 */
import React from 'react';

class Demo extends React.Component {
    render() {

    }
}

export default Demo;
~~~

> **注意**
>
> 当你没有传递某个变量却使用了它时，会发生错误。所以当你不确定该变量是否会被提供时，使用后者去引用该变量。众所周知，访问 JavaScript 对象的不存在的变量时，并不会产生错误。

#### 交互提示

在之前，我们为生成器 `component` 添加了 `with-prompt` Action，因此可以使用该 Action 来交互式地生成文件。

查看 `component/with-prompt/prompt.js` 。

~~~javascript
module.exports = [
  {
    type: 'input',
    name: 'message',
    message: "What's your message?"
  }
];
~~~

执行 `hygen component with-prompt` ，开始和终端对话吧。

~~~shell
$ hygen test with-prompt                                                            
? What's your message? › Let's go 
~~~

#### 高级交互

简单的交互只需要提供一个包含操作行为的数组，高级交互功能允许你使用函数更灵活地定义交互逻辑。

修改 `component/with-prompt/prompt.js` 。

~~~javascript
module.exports = {
    prompt({ prompter, args }) {
        prompter
            .prompt({
                type: 'input',
                name: 'email',
                message: 'What is your email?',
            })
            .then(({ email }) =>
                prompter.prompt({
                    type: 'select',
                    name: 'type',
                    message: 'Select gender',
                    choices: ['male', 'female'],
                }),
            );
    },
};
~~~

你也可以不用交互功能，直接使用 `params` 来根据命令行参数计算或者推断出你想要的变量。

~~~javascript
module.exports = {
  params: ({ args }) => {
    return { moreConvenientName: args.foobamboozle };
  },
};
~~~

> **说明**
>
> Params and Prompts are The Same | If you think about it, prompting for variables or reshaping CLI arguments lead to the same goal: new parameters. But to make a future-proof API, we've separated the two intents to the `prompt` and `params` functions.

#### 添加说明

~~~ejs
---
message: |
  - hygen {bold mailer} new --name [NAME]
---
~~~

可选的样式：

- {bold mailer}
- {red mailer}
- {underline mailer}
- {green mailer}

> **说明**
>
> 官网对此描述很简单，而且期待的样式也没有生效。

#### 生成部分文件

可选择只使用部分模板生成文件。

~~~shell
hygen GENERATOR ACTION:SUBACTION
~~~

其中，`SUBACTION` 为正则表达式。

例如只生成 `hello.ejs.t` 文件，可以这样执行：

~~~shell
hygen component new:hello 
~~~

## 案例

### `_templates/component/new/index.ejs.t`

~~~ejs
---
to: src/components/<%= h.changeCase.pascal(componentName) %>/index.js
---
<% namePascal = h.changeCase.pascal(componentName) -%>
<% nameCamel = h.changeCase.camel(componentName) -%>
<% models = locals.models || '';
    models = models.split(',');
 -%>
import React from 'react';
<% if (locals.needRedux) { -%>
import connect from 'dva';
<% } -%>

<% if (locals.needStyle) { -%>
import styles from './index.less';
<% } -%>

<% if (type === 'Class') { -%>
<% if (needRedux) { -%>
@connect(({
    <% models.forEach(item => { -%>
        <% if (item.trim()) { -%>
            <%= item.trim() %>,
        <% } -%>
    <% }) -%>
}) => ({
    <% models.forEach(item => { -%>
        <% if (item.trim()) { -%>
            <%= item.trim() %>,
        <% } -%>
    <% }) -%>
}))
<% } -%>
class <%= namePascal %> extends React.PureComponent {

    render() {

        return (
            <div<%= needStyle ? ` className={styles.${nameCamel}}` : '' %>>
            {/* TODO */}
            </div>
        );
    }
}
<% } else if (type === 'Function') { -%>
const <%= namePascal %> = ({}) => {

    return (
        <div>
        {/* TODO */}
        </div>
    );
};
<% } -%>

export default <%= namePascal %>;
~~~

### `_templates/component/new/style.ejs.t`

~~~ejs
---
to: "<%= locals.needStyle ? `src/components/${h.changeCase.pascal(name)}/index.less` : null %>"
---
.<%= h.changeCase.camel(componentName) %> {
    // TODO
}
~~~

### `_templates/component/new/prompt.js`

~~~javascript
module.exports = {
    prompt: ({ prompter, args }) => {
        const questions = [
            {
                type: 'input',
                name: 'componentName',
                message: '输入组件/文件夹名称',
                initial: args.name,
            },
            {
                type: 'select',
                name: 'type',
                message: '选择组件类型',
                choices: ['Class', 'Function'],
            },
            {
                type: 'confirm',
                name: 'needStyle',
                message: '是否需要创建样式文件',
                initial: true,
            },
            {
                type: 'confirm',
                name: 'needService',
                message: '是否需要创建Service',
            },
        ];
        return prompter
            .prompt(questions)
            .then(lastAnswer => {
                const { type } = lastAnswer;
                if (type === 'Function') {
                    return Promise.resolve({ ...args, ...lastAnswer, needRedux: false });
                }
                return prompter
                    .prompt({
                        type: 'confirm',
                        name: 'needRedux',
                        message: '是否需要connect',
                    })
                    .then(({ needRedux }) => ({ ...args, ...lastAnswer, needRedux }));
            })
            .then(lastAnswer => {
                const { needRedux } = lastAnswer;
                if (needRedux) {
                    return prompter
                        .prompt({
                            type: 'input',
                            name: 'models',
                            message: '输入使用到的model',
                            initial: `${lastAnswer.componentName},`,
                        })
                        .then(({ models }) => ({ ...lastAnswer, models }));
                } else {
                    return Promise.resolve({ ...lastAnswer });
                }
            });
    },
};
~~~
