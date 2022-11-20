---
title: Interview
---

## 前言

收集前端方面高频面试题。

<!-- more -->

## HTML 和 CSS

### 1. HTML

#### 语义化

#### 多媒体

#### SEO

### 2. CSS

#### 水平垂直居中

##### 1. 定位

   ~~~css
   .father {
     position: relative;
   }
   .son {
     position: absolute;
     top: 50%;
     left: 50%;
     width: 200px;
     height: 100px;
     background-color: #2797ff;
     /* 已知宽高 */
     margin-top: -50px;
     margin-left: -100px;
     /* 未知宽高 */
     transform: translate(-50%, -50%);
   }
   ~~~

##### 2. flex

   ~~~css
   .father {
     display: flex;
     justify-content: center;
     aligh-items: center
   }
   ~~~

#### 布局

##### 双飞翼布局

~~~html
<body class="clearfix">
  <div class="container">
    <div class="center"></div>    
  </div>
  <div class="left"></div>
  <div class="right"></div>
</body>
~~~

~~~css
.container {
  float: left;
  width: 100%;
}

.center {
  height: 200px;
  margin-left: 110px;
  margin-right: 220px;
  background: green;
}

.center::after {
  content: '';
  display: block;
  font-size: 0;
  height: 0;
  zoom: 1;
  clear: both;
}

.left {
  float: left;
  height: 200px;
  width: 100px;
  margin-left: -100%;
  background: red;
}

.right {
  float: right;
  height: 200px;
  width: 200px;
  margin-left: -200px;
  background: blue;
}
~~~

##### 圣杯布局

~~~html
<div class="container clearfix">
  <div class="center"></div>
  <div class="left"></div>
  <div class="right"></div>
</div>
~~~

~~~css
.container {
  margin-left: 120px;
  margin-right: 220px;
}

.main {
  float: left;
  width: 100%;
  height: 300px;
  background: green;
}

.left {
  position: relative;
  left: -120px;
  float: left;
  height: 300px;
  width: 100px;
  margin-left: -100%;
  background: red;
}

.right {
  position: relative;
  right: -220px;
  float: right;
  height: 300px;
  width: 200px;
  margin-left: -200px;
  background: blue;
}
~~~

#### 盒子模型

##### 标准盒模型（content-box）

盒子大小 = content(width) + padding + border + margin

##### 怪异盒模型（旧版本IE）(border-box)

盒子大小 = (content + padding + border)(width) + margin

##### Flex 盒模型

#### CSS 的单位

##### px

基本单位，绝对单位。其他都是相对单位。

##### 百分比

相对于父元素的宽度比例。

##### em

相对于当前元素的 `font-size` 。

##### rem

相对于根元素的 `font-size` 。

##### vw 和 vh

屏幕宽高的 1% 。
> vmin 两者的较小值，vmax 两者的较大值。

#### BFC

- 触发条件
  - body 根元素
  - 浮动元素：float 除 none 以外的值
  - 绝对定位元素：position (absolute、fixed)
  - display 为 inline-block、table-cells、flex
  - overflow 除了 visible 以外的值 (hidden、auto、scroll)

- 特性及应用
  1. 同一个 BFC 下外边距会发生折叠。
  2. BFC 可以包含浮动元素。（应用场景：清除浮动）
  3. BFC 可以阻止元素被浮动元素覆盖。

#### Flex

##### 容器属性

- flex-direction

  主轴方向，row ｜ column ｜ row-reverse ｜ column-reverse

- flex-wrap

  换行方式，nowrap ｜ wrap ｜ wrap-reverse

- flex-flow

  <flex-direction> || <flex-wrap>，默认 row  nowrap。

- justify-content

  定义主轴的对齐方式。

  - `flex-start`（默认值）：左对齐
  - `flex-end`：右对齐
  - `center`： 居中
  - `space-between`：两端对齐，项目之间的间隔都相等。
  - `space-around`：每个项目两侧的间隔相等。所以，项目之间的间隔比项目与边框的间隔大一倍。

- align-items

  定义交叉轴的对齐方式。

  - `flex-start`：交叉轴的起点对齐。
  - `flex-end`：交叉轴的终点对齐。
  - `center`：交叉轴的中点对齐。
  - `baseline`: 项目的第一行文字的基线对齐。
  - `stretch`（默认值）：如果项目未设置高度或设为auto，将占满整个容器的高度。

- align-content

  定义多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。

  - `stretch`（默认值）：轴线占满整个交叉轴。

  - `flex-start`：与交叉轴的起点对齐。
  - `flex-end`：与交叉轴的终点对齐。
  - `center`：与交叉轴的中点对齐。
  - `space-between`：与交叉轴两端对齐，轴线之间的间隔平均分布。
  - `space-around`：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。

##### 项目属性

- order

  定义项目的排列顺序。数值越小，排列越靠前，默认为0。

- flex-grow

  定义项目的放大比例，默认为`0`，即如果存在剩余空间，也不放大。如果所有项目的`flex-grow`属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的`flex-grow`属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。

- flex-shrink

  定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。如果所有项目的`flex-shrink`属性都为1，当空间不足时，都将等比例缩小。如果一个项目的`flex-shrink`属性为0，其他项目都为1，则空间不足时，前者不缩小。

- flex-basis

  定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为`auto`，即项目的本来大小。它可以设为跟`width`或`height`属性一样的值（比如350px），则项目将占据固定空间。

- flex

  `flex`属性是`flex-grow`, `flex-shrink` 和 `flex-basis`的简写，默认值为`0 1 auto`。后两个属性可选。

  该属性有两个快捷值：`auto` (`1 1 auto`) 和 none (`0 0 auto`)。

- align-self

  `align-self`属性允许单个项目有与其他项目不一样的对齐方式，可覆盖`align-items`属性。默认值为`auto`，表示继承父元素的`align-items`属性，如果没有父元素，则等同于`stretch`。

#### Grid

##### 容器属性

- grid-template-columns & grid-template-rows

  `grid-template-columns`属性定义每一列的列宽，`grid-template-rows`属性定义每一行的行高。

  ~~~css
  .container {
    display: grid;
    grid-template-columns: 100px 100px 100px;
    grid-template-rows: 33.33% 33.33% 33.33%;
  }
  ~~~

  - repeat()

    ~~~css
    .container {
      display: grid;
      grid-template-columns: repeat(3, 100px);
      grid-template-rows: repeat(3, 33.33%);
      /* 重复某种模式 */
      grid-template-columns: repeat(2, 100px 20px 80px);
      /* 相当于 */
      grid-template-columns: 100px 20px 80px 100px 20px 80px;
    }
    ~~~

  - auto-fill 关键字

    有时，单元格的大小是固定的，但是容器的大小不确定。如果希望每一行（或每一列）容纳尽可能多的单元格，这时可以使用`auto-fill`关键字表示自动填充。

    ~~~css
    /* 表示每列宽度100px，然后自动填充，直到容器不能放置更多的列 */
    .container {
      display: grid;
      grid-template-columns: repeat(auto-fill, 100px);
    }
    ~~~

  - fr 关键字

    为了方便表示比例关系，网格布局提供了`fr`关键字（fraction 的缩写，意为"片段"）, 表示剩余空间。如果两列的宽度分别为`1fr`和`2fr`，就表示后者是前者的两倍。

    ~~~css
    .container {
      display: grid;
      grid-template-columns: 150px 1fr 2fr;
    }
    ~~~

  - minmax()

    `minmax()`函数产生一个长度范围，表示长度就在这个范围之中。它接受两个参数，分别为最小值和最大值。

    ~~~css
    /* 列宽不小于100px，不大于1fr */
    grid-template-columns: 1fr 1fr minmax(100px, 1fr);
    ~~~

  - auto 关键字

    表示由浏览器自己决定长度。

    ~~~css
    /* 第二列的宽度，基本上等于该列单元格的最大宽度，除非单元格内容设置了min-width，且这个值大于最大宽度 */
    grid-template-columns: 100px auto 100px;
    ~~~

- grid-row-gap & grid-column-gap & grid-gap

  `grid-row-gap`属性设置行与行的间隔（行间距），`grid-column-gap`属性设置列与列的间隔（列间距）。

  ~~~css
  .container {
    grid-row-gap: 20px;
    grid-column-gap: 20px;
  }
  ~~~

  `grid-gap`属性是`grid-column-gap`和`grid-row-gap`的合并简写形式，语法如下。

  ~~~css
  grid-gap: <grid-row-gap> <grid-column-gap>;
  ~~~

- grid-template-areas

  网格布局允许指定"区域"（area），一个区域由单个或多个单元格组成。`grid-template-areas`属性用于定义区域。

  ~~~css
  /* 先划分出9个单元格，然后将其定名为a到i的九个区域，分别对应这九个单元格 */
  .container {
    display: grid;
    grid-template-columns: 100px 100px 100px;
    grid-template-rows: 100px 100px 100px;
    grid-template-areas: 'a b c'
                         'd e f'
                         'g h i';
  }
  ~~~

  **重名的单元格会合并**：

  ~~~css
  /* 顶部是页眉区域header，底部是页脚区域footer，中间部分则为main和sidebar */
  grid-template-areas: "header header header"
                       "main main sidebar"
                       "footer footer footer";
  ~~~

  不需要利用的区域可以用 `.` 来表示：

  ~~~css
  /* 中间一列为点，表示没有用到该单元格，或者该单元格不属于任何区域 */
  grid-template-areas: 'a . c'
                       'd . f'
                       'g . i';
  ~~~

  ⚠️ 注意

  区域的命名会影响到网格线。每个区域的起始网格线，会自动命名为`区域名-start`，终止网格线自动命名为`区域名-end`。

  比如，区域名为`header`，则起始位置的水平网格线和垂直网格线叫做`header-start`，终止位置的水平网格线和垂直网格线叫做`header-end`。

- justify-items & align-items & place-items （**设置单元格对齐方式**）

  `justify-items` 属性设置单元格内容的水平位置（左中右），`align-items` 属性设置单元格内容的垂直位置（上中下）。

  - `start`：对齐单元格的起始边缘。
  - `end`：对齐单元格的结束边缘。
  - `center`：单元格内部居中。
  - `stretch`：拉伸，占满单元格的整个宽度（默认值）。

  `place-items` 属性是 `align-items` 属性和 `justify-items` 属性的合并简写形式。

  ```css
  place-items: <align-items> <justify-items>;
  ```

- justify-content & align-content & place-content

  `justify-content` 属性是整个内容区域在容器里面的水平位置（左中右），`align-content` 属性是整个内容区域的垂直位置（上中下）。

  - `start` : 对齐容器的起始边框。
  - `end` : 对齐容器的结束边框。
  - `center` : 容器内部居中。
  - `stretch` : 项目大小没有指定时，拉伸占据整个网格容器。
  - `space-around` : 每个项目两侧的间隔相等。所以，项目之间的间隔比项目与容器边框的间隔大一倍。
  - `space-between` : 项目与项目的间隔相等，项目与容器边框之间没有间隔。
  - `space-evenly` : 项目与项目的间隔相等，项目与容器边框之间也是同样长度的间隔。

  `place-content` 属性是 `align-content` 属性和 `justify-content` 属性的合并简写形式。

  ```css
  place-content: <align-content> <justify-content>
  ```

##### 项目属性

- grid-column-start：左边框所在的垂直网格线
  grid-column-end：右边框所在的垂直网格线
  grid-row-start：上边框所在的水平网格线
  grid-row-end：下边框所在的水平网格线

  ```css
  /* 左边框是第二根垂直网格线，右边框是第四根垂直网格线，上下边框使用默认配置 */
  .item-1 {
    grid-column-start: 2;
    grid-column-end: 4;
  }
  ```

  `grid-column`属性是`grid-column-start`和`grid-column-end`的合并简写形式，`grid-row`属性是`grid-row-start`属性和`grid-row-end`的合并简写形式。

  ```css
  .item {
    grid-column: <start-line> / <end-line>;
    grid-row: <start-line> / <end-line>;
  }
  
  .item-1 {
    grid-column: 1 / 3;
    grid-row: 1 / 2;
  }
  /* 等同于 */
  .item-1 {
    grid-column-start: 1;
    grid-column-end: 3;
    grid-row-start: 1;
    grid-row-end: 2;
  }
  ```

  斜杠以及后面的部分可以省略，默认跨越一个网格。

- grid-area

  指定项目放在哪一个区域。

  ```css
  .item-1 {
    grid-area: e;
  }
  ```

  `grid-area`属性还可用作`grid-row-start`、`grid-column-start`、`grid-row-end`、`grid-column-end`的合并简写形式，直接指定项目的位置。

  ```css
  .item {
    grid-area: <row-start> / <column-start> / <row-end> / <column-end>;
  }
  
  .item-1 {
    grid-area: 1 / 1 / 3 / 3;
  }
  ```

- justify-self & align-self & place-self

  `justify-self`属性设置单元格内容的水平位置（左中右），跟`justify-items`属性的用法完全一致，但只作用于单个项目。

  `align-self`属性设置单元格内容的垂直位置（上中下），跟`align-items`属性的用法完全一致，也是只作用于单个项目。

  ```css
  .item {
    justify-self: start | end | center | stretch;
    align-self: start | end | center | stretch;
  }
  ```

  `place-self`属性是`align-self`属性和`justify-self`属性的合并简写形式。

  ```css
  place-self: <align-self> <justify-self>;
  ```

#### 响应式布局

##### 1. @media

##### 2. rem

##### 3. flex

##### 4. vh/vw

## JavaScript


## Vue

## React

## Babel

## Webpack

## HTTP

> 网络连接是基于 TCP 协议，传输内容基于 HTTP。

### TCP 的三次握手和四次挥手

#### 三次握手

~~~text
Client   ====SYN====>   Server
Client <====SYN+ACK==== Server
Client   ====ACK====>   Server
~~~

#### 四次挥手

~~~text
Client   ====FIN====>   Server
Client   <====ACK====   Server
Client   <====FIN====   Server
Client   ====ACK====>   Server
~~~
