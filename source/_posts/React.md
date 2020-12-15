---
title: React
tag: [Frontend, JavaScript, React]
---
注意：

1. render函数return前不能使用 setState()，会造成死循环。
2. 构造函数是唯一可以给 this.state 赋值的地方。

<!-- more -->

+ State 的更新可能是异步的

~~~js
// Wrong，无法更新计数器
this.setState({
  counter: this.state.counter + this.props.increment,
});

// Correct
// setState() 接收一个函数而非对象,用上一个 state 作为第一个参数，将此次更新被应用时的 props 做为第二个参数
this.setState((state, props) => ({
  counter: state.counter + props.increment
}));
~~~

+ State 的更新会被合并
