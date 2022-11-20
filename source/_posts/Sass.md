---
title: Sass
tag: [Frontend, CSS, Sass]
---


## 一、快速入门

Sass 是一款强化 CSS 的辅助工具，它在 CSS 语法的基础上增加了变量 (variables)、嵌套 (nested rules)、混合 (mixins)、导入 (inline imports) 等高级功能，这些拓展令 CSS 更加强大与优雅。使用 Sass 以及 Sass 的样式库（如 Compass）有助于更好地组织管理样式文件，以及更高效地开发项目。

<!-- more -->

### 1. 变量

~~~scss
$highlight-color: #f90;
$highlight-border: 1px solid $highlight-color;
nav {
  $width: 100px;
  width: $width;
  color: $highlight-color;
  border: $highlight-border;
}

//编译后
nav {
  width: 100px;
  color: #F90;
  border: 1px solid #F90;
}
~~~

### 2. 嵌套CSS规则

~~~scss
#content {
  article {
    h1 { color: #333 }
    p { margin-bottom: 1.4em }
  }
  aside { background-color: #EEE }
}
/* 编译后 */
#content article h1 { color: #333 }
#content article p { margin-bottom: 1.4em }
#content aside { background-color: #EEE }
~~~

#### 2.1 父选择器标识符 `&`

~~~scss
article a {
  color: blue;
  &:hover { color: red }
}
// 编译后
article a { color: blue }
article a:hover { color: red }
~~~

#### 2.2 群组选择器的嵌套

~~~scss
.container {
  h1, h2, h3 {margin-bottom: .8em}
}
// 编译后
.container h1, .container h2, .container h3 { margin-bottom: .8em }
~~~

#### 2.3 子组合选择器和同层组合选择器：`>`、`+` 和 `~`

1. 直接子元素选择器 `>`

   ~~~scss
   // 选择article直接子元素中的section
   article > section {
     border: 1px solid #ccc
   }
   ~~~

2. 同层相邻组合选择器  `+`

   ~~~scss
   // 选择header元素后紧跟的p元素
   header + p { 
     font-size: 1.1em 
   }
   ~~~

3. 同层全体组合选择器 `~`

   ~~~scss
   // 选择所有跟在article后的同层article元素，不管它们之间隔了多少其他元素
   article ~ article {
     border-top: 1px dashed #ccc
   }
   ~~~

#### 2.4 属性嵌套

~~~scss
nav {
  border: {
    style: solid;
    width: 1px;
    color: #ccc;
  }
}
// 编译后
nav {
  border-style: solid;
  border-width: 1px;
  border-color: #ccc;
}

// 还可以这样
nav {
  border: 1px solid #ccc {
    left: 0px;
    right: 0px;
  }
}
// 编译后
nav {
  border: 1px solid #ccc;
  border-left: 0px;
  border-right: 0px;
}
~~~

### 3. 导入 SASS 文件

`css`有一个特别不常用的特性，即`@import`规则，它允许在一个`css`文件中导入其他`css`文件。然而，后果是只有执行到`@import`时，浏览器才会去下载其他`css`文件，这**导致页面加载起来特别慢**。

`sass`也有一个`@import`规则，但不同的是，`sass`的`@import`规则**在生成`css`文件时就把相关文件导入进来**。这意味着所有相关的样式被归纳到了同一个`css`文件中，而无需发起额外的下载请求。

#### 3.1 使用 SASS 部分文件

专门为`@import`命令而编写，不需要生成对应的独立的 `css` 文件，这样的 sass 文件称为 **局部文件**。对此，`sass`有一个特殊的约定来命名这些文件。

**`sass`局部文件的文件名以下划线开头。**这样，`sass`就不会在编译时单独编译这个文件输出`css`。

~~~scss
// 导入一个名为 themes/_night-sky.scss 文件，可以省略下划线前缀
@import "themes/night-sky"
~~~

#### 3.2 默认变量值

假如你写了一个可被他人通过`@import`导入的`sass`库文件，你可能希望导入者可以定制修改`sass`库文件中的某些值。如果这个变量被声明赋值了，那就用它声明的值，否则就用这个默认值。

~~~scss
$fancybox-width: 400px !default;
.fancybox {
  width: $fancybox-width;
}
~~~

#### 3.3 嵌套导入

`sass`允许`@import`命令写在`css`规则内。这种导入方式下，生成对应的`css`文件时，局部文件会被直接插入到`css`规则内导入它的地方。

定义局部文件 `_blue-theme.scss`

~~~scss
aside {
  background: blue;
  color: white;
}
~~~

将其导入到一个 CSS 规则内

~~~scss
.blue-theme {@import "blue-theme"}

//生成的结果跟你直接在.blue-theme选择器内写_blue-theme.scss文件的内容完全一样。

.blue-theme {
  aside {
    background: blue;
    color: #fff;
  }
}
~~~

被导入的局部文件中定义的所有变量和混合器，也会在这个规则范围内生效。这些变量和混合器不会全局有效，这样我们就可以通过嵌套导入只对站点中某一特定区域运用某种颜色主题或其他通过变量配置的样式。

#### 3.4 原生的 CSS 导入

在下列三种情况下会生成原生的`CSS@import`，尽管这会造成浏览器解析`css`时的额外下载：

- 被导入文件的名字以`.css`结尾；
- 被导入文件的名字是一个URL地址（比如<http://www.sass.hk/css/css.css），由此可用谷歌字体API>提供的相应服务；
- 被导入文件的名字是`CSS`的url()值。

### 4. 静默注释

`css`中注释的作用包括帮助你组织样式、以后你看自己的代码时明白为什么这样写，以及简单的样式说明。但是，你并不希望每个浏览网站源码的人都能看到所有注释。

`sass`另外提供了一种不同于`css`标准注释格式`/* ... */`的注释语法，即静默注释，其内容不会出现在生成的`css`文件中。静默注释的语法跟`JavaScript``Java`等类`C`的语言中单行注释的语法相同，它们以`//`开头，注释内容直到行末。

```scss
body {
  color: #333; // 这种注释内容不会出现在生成的css文件中
  padding: 0; /* 这种注释内容会出现在生成的css文件中 */
}
```

实际上，`css`的标准注释格式`/* ... */`内的注释内容亦可在生成的`css`文件中抹去。当注释出现在原生`css`不允许的地方，如在`css`属性或选择器中，`sass`将不知如何将其生成到对应`css`文件中的相应位置，于是这些注释被抹掉。

```scss
body {
  color /* 这块注释内容不会出现在生成的css中 */: #333;
  padding: 1; /* 这块注释内容也不会出现在生成的css中 */ 0;
}
```

### 5. 混合器

#### 5.1 基本用法

混合器使用`@mixin`标识符定义。

```scss
@mixin rounded-corners {
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

通过`@include`来使用这个混合器。

```scss
notice {
  background-color: green;
  border: 2px solid #00aa00;
  @include rounded-corners;
}

//sass最终生成：
.notice {
  background-color: green;
  border: 2px solid #00aa00;
  -moz-border-radius: 5px;
  -webkit-border-radius: 5px;
  border-radius: 5px;
}
```

#### 5.2 给混合器传参

```scss
@mixin link-colors($normal, $hover, $visited) {
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```

```scss
a {
  @include link-colors(blue, red, green);
}

//Sass最终生成的是：

a { color: blue; }
a:hover { color: red; }
a:visited { color: green; }
```

当你@include混合器时，有时候可能会很难区分每个参数是什么意思，参数之间是一个什么样的顺序。为了解决这个问题，`sass`允许通过语法`$name: value`的形式指定每个参数的值。这种形式的传参，参数顺序就不必再在乎了，只需要保证没有漏掉参数即可：

```scss
a {
    @include link-colors(
      $normal: blue,
      $visited: green,
      $hover: red
  );
}
```

#### 5.3 默认参数值

为了在`@include`混合器时不必传入所有的参数，我们可以给参数指定一个默认值。参数默认值使用`$name: default-value`的声明形式，默认值可以是任何有效的`css`属性值，甚至是其他参数的引用，如下代码：

```scss
@mixin link-colors(
    $normal,
    $hover: $normal,
    $visited: $normal
  )
{
  color: $normal;
  &:hover { color: $hover; }
  &:visited { color: $visited; }
}
```

~~~scss
a {
  // $hover和$visited也会被自动赋值为red。
  @include link-colors(red);
}
~~~

#### 5.4 参数变量

有时，不能确定混合指令需要使用多少个参数，比如一个关于 `box-shadow` 的混合指令不能确定有多少个 'shadow' 会被用到。这时，可以使用参数变量 `…` 声明（写在参数的最后方）告诉 Sass 将这些参数视为值列表处理：

```scss
@mixin box-shadow($shadows...) {
  -moz-box-shadow: $shadows;
  -webkit-box-shadow: $shadows;
  box-shadow: $shadows;
}
.shadows {
  @include box-shadow(0px 4px 5px #666, 2px 6px 10px #999);
}

// 编译后
.shadows {
  -moz-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
  -webkit-box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
  box-shadow: 0px 4px 5px #666, 2px 6px 10px #999;
}
```

参数变量也可以用在引用混合指令的时候 (`@include`)，与平时用法一样，将一串值列表中的值逐条作为参数引用：

```scss
@mixin colors($text, $background, $border) {
  color: $text;
  background-color: $background;
  border-color: $border;
}
$values: #ff0000, #00ff00, #0000ff;
.primary {
  @include colors($values...);
}
```

编译为

```css
.primary {
  color: #ff0000;
  background-color: #00ff00;
  border-color: #0000ff;
}
```

#### 5.5 向混合样式中导入内容

在引用混合样式的时候，可以先将一段代码导入到混合指令中，然后再输出混合样式，额外导入的部分将出现在 `@content` 标志的地方（作用类似于插槽）：

```scss
@mixin apply-to-ie6-only {
  * html {
    @content;
  }
}
@include apply-to-ie6-only {
  #logo {
    background-image: url(/logo.gif);
  }
}
```

编译为

```css
* html #logo {
  background-image: url(/logo.gif);
}
```

**为便于书写，@mixin 可以用 = 表示，而 @include 可以用 + 表示**，所以上面的例子可以写成：

```scss
=apply-to-ie6-only
  * html
    @content

+apply-to-ie6-only
  #logo
    background-image: url(/logo.gif)
```

### 6. 选择器继承

#### 6.1 基本用法

```scss
//通过选择器继承继承样式
.error {
  border: 1px solid red;
  background-color: #fdd;
}
.seriousError {
  @extend .error;
  border-width: 3px;
}
```

不仅会继承`.error`自身的所有样式，任何跟`.error`有关的组合选择器样式也会被`.seriousError`以组合选择器的形式继承，如下代码:

```scss
//.seriousError从.error继承样式
.error a{  //应用到.seriousError a
  color: red;
  font-weight: 100;
}
h1.error { //应用到hl.seriousError
  font-size: 1.2rem;
}
```

#### 6.2 高级用法

假如一条样式规则继承了一个复杂的选择器，那么它只会继承这个复杂选择器命中的元素所应用的样式。举例来说， 如果`.seriousError``@extend``.important.error` ， 那么`.important.error` 和`h1.important.error` 的样式都会被`.seriousError`继承， 但是`.important`或者`.error下`的样式则不会被继承。这种情况下你很可能希望`.seriousError`能够分别继承`.important`或者`.error`下的样式。

如果一个选择器序列（`#main .seriousError`）`@extend`另一个选择器（`.error`），那么只有完全匹配`#main .seriousError`这个选择器的元素才会继承`.error`的样式，就像单个类 名继承那样。拥有`class="seriousError"`的`#main`元素之外的元素不会受到影响。

像`#main .error`这种选择器序列是不能被继承的。这是因为从`#main .error`中继承的样式一般情况下会跟直接从`.error`中继承的样式基本一致，细微的区别往往使人迷惑。

`@extend`背后最基本的想法是，如果`.seriousError @extend .error`， 那么样式表中的任何一处`.error`都用`.error``.seriousError`这一选择器组进行替换。这就意味着相关样式会如预期那样应用到`.error`和`.seriousError`。当`.error`出现在复杂的选择器中，比如说`h1.error``.error a`或者`#main .sidebar input.error[type="text"]`，那情况就变得复杂多了，但是不用担心，`sass`已经为你考虑到了这些。

关于`@extend`有两个要点你应该知道。

- 跟混合器相比，继承生成的`css`代码相对更少。因为继承仅仅是重复选择器，而不会重复属性，所以使用继承往往比混合器生成的`css`体积更小。如果你非常关心你站点的速度，请牢记这一点。
- 继承遵从`css`层叠的规则。当两个不同的`css`规则应用到同一个`html`元素上时，并且这两个不同的`css`规则对同一属性的修饰存在不同的值，`css`层叠规则会决定应用哪个样式。相当直观：通常权重更高的选择器胜出，如果权重相同，定义在后边的规则胜出。

*此章节是对 <https://www.sass.hk/guide/> 的精简概括，目的在于帮助自己快速使用 SASS。*
