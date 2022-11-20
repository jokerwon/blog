---
title: Babel
tag: [babel, AST, 编译]
---

<!-- more -->

### 作用

转译器（Transpiler）。

- 转译 `esnext`、`tyescript`、`flow` 等到目标环境支持的 js。

- 特定用途的代码转换。

  函数插桩，自动国际化，`default import` 转 `named import` 等。

- 代码的静态分析。

  - linter 工具
  - api 文档自动生成工具。提取注释，生成文档。
  - 压缩混淆工具。删除死代码，变量名混淆，常量折叠。
  - js 解释器。



### 流程

整体编译流程分为三步。

- `parse` : 通过 `parser` 把源码转成抽象语法树（AST）。

  把源码分成一个个不能细分的单词（token），例如 `let` , `name` , `=` , `JokerWon` ，这个过程是词法分析。之后把 token 进行递归组装，生成 AST，这个过程是语法分析。

- `tranform` : 遍历 AST，调用各种 `transform` 插件对 AST 进行增删改。

  对 AST 进行处理，会进行 AST 的遍历，遍历的过程中处理到不同的 AST 节点会调用注册的响应的 visitor 函数，visitor 函数里可以对 AST 节点进行增删改，返回新的 AST。

- `generate` : 把转换后的 AST 打印成目标代码，并生成 sourceMap 。

  把 AST 打印成目标代码字符串，并且会生成 sourcemap。