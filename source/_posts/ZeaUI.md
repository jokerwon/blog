---
title: ZeaUI
tag: [Frontend, React, UI]
---

## Styled-component

### 安装

~~~sh
npm install --save styled-components
// 或
yarn add styled-components
~~~

### 开始

#### 编写第一个组件

~~~react
const Title = styled.h1`
  font-size: 1.5em;
  text-align: center;
  color: palevioletred;
`;

const Wrapper = styled.section`
  padding: 4em;
  background: papayawhip;
`;

render(
  <Wrapper>
    <Title>
      Hello World!
    </Title>
  </Wrapper>
);
~~~

#### 基于属性渲染

~~~react
const Button = styled.button`
  /* Adapt the colors based on primary prop */
  background: ${({primary}) => primary ? "palevioletred" : "white"};
  color: ${({primary}) => primary ? "white" : "palevioletred"};

  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

render(
  <div>
    <Button>Normal</Button>
    <Button primary>Primary</Button>
  </div>
);
~~~

#### 继承样式

~~~react
// 相当于 styled('button')
const Button = styled.button`
  color: palevioletred;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

// 使用圆括号包裹自定义组件，继承和重写样式
const TomatoButton = styled(Button)`
  color: tomato;
  border-color: tomato;
`;

render(
  <div>
    <Button>Normal Button</Button>
    <TomatoButton>Tomato Button</TomatoButton>
  </div>
);
~~~

#### 替换标签

~~~react
const Button = styled.button`
  display: inline-block;
  color: palevioletred;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
  display: block;
`;

render(
  <div>
    <Button>Normal Button</Button>
    <!-- 替换成a标签 -->
    <Button as="a" href="/">Link with Button styles</Button>
  </div>
);
~~~

替换自定义组件

~~~react
const Button = styled.button`
  display: inline-block;
  color: palevioletred;
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border: 2px solid palevioletred;
  border-radius: 3px;
  display: block;
`;

const ReversedButton = props => <Button {...props} children={props.children.split('').reverse()} />

render(
  <div>
    <Button>Normal Button</Button>
    <Button as={ReversedButton}>Custom Button with Normal Button styles</Button>
  </div>
);
~~~

#### 传递属性

~~~react
const Input = styled.input`
  padding: 0.5em;
  margin: 0.5em;
  color: ${props => props.inputColor || "palevioletred"};
  background: papayawhip;
  border: none;
  border-radius: 3px;
`;

// 可以将defaultValue和type传递给input标签，并自动过滤非标准属性inputColor
render(
  <div>
    <Input defaultValue="@probablyup" type="text" />
    <Input defaultValue="@geelen" type="text" inputColor="rebeccapurple" />
  </div>
);
~~~

> Note how the inputColor prop is not passed to the DOM, but type and defaultValue are. That is styled-components being smart enough to filter non-standard attributes automatically for you.

#### 基于预处理器 `stylis`

~~~react
const Thing = styled.div`
  color: blue;
 &:hover {
    color: red; // <Thing> when hovered
  }

  & ~ & {
    background: tomato;
  }

  .something {
    border: 1px solid; // an element labeled ".something" inside <Thing>
    display: block;
  }
`;

render(
  <>
   <Thing>Hello world!</Thing>
    <Thing>
      <label htmlFor="foo-button" className="something">Mystery button</label>
      <button id="foo-button">What do I do?</button>
    </Thing>
  </>
)
~~~

#### 附加属性

~~~react
const Input = styled.input.attrs(props => ({
  type: "text",
  size: props.size || "1em",
}))`
  border: 2px solid palevioletred;
  margin: ${props => props.size};
  padding: ${props => props.size};
`;

// Input's attrs will be applied first, and then this attrs obj
const PasswordInput = styled(Input).attrs({
  type: "password",
})`
  // similarly, border will override Input's border
  border: 2px solid aqua;
`;

render(
  <div>
    <Input placeholder="A bigger text input" size="2em" />
    <br />
    {/* Notice we can still use the size attr from Input */}
    <PasswordInput placeholder="A bigger password input" size="2em" />
  </div>
);
~~~

#### 动画

~~~react
const rotate = keyframes`
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  }
`;

// Here we create a component that will rotate everything we pass in over two seconds
const Rotate = styled.div`
  display: inline-block;
  animation: ${rotate} 2s linear infinite;
  padding: 2rem 1rem;
  font-size: 1.2rem;
`;

render(
  <Rotate>&lt; 💅🏾 &gt;</Rotate>
);
~~~

#### 引用其他组件

~~~react
const Link = styled.a`
  display: flex;
  align-items: center;
  padding: 5px 10px;
  background: papayawhip;
  color: palevioletred;
`;

const Icon = styled.svg`
  flex: none;
  transition: fill 0.25s;
  width: 48px;
  height: 48px;
 
 // Link组件hover时会改变Icon的填充色
 // ${.} 此时作为组件选择器
  ${Link}:hover & {
    fill: rebeccapurple;
  }
`;

const Label = styled.span`
  display: flex;
  align-items: center;
  line-height: 1.2;

  &::before {
    content: '◀';
    margin: 0 10px;
  }
`;

render(
  <Link href="#">
    <Icon viewBox="0 0 20 20">
      <path d="M10 15h8c1 0 2-1 2-2V3c0-1-1-2-2-2H2C1 1 0 2 0 3v10c0 1 1 2 2 2h4v4l4-4zM5 7h2v2H5V7zm4 0h2v2H9V7zm4 0h2v2h-2V7z"/>
    </Icon>
    <Label>Hovering my parent changes my style!</Label>
  </Link>
);
~~~

> This behaviour is **only supported within the context of *Styled* Components**: attempting to mount B in the following example will fail because component A is an instance of React.Component not a Styled Component.

~~~react
class A extends React.Component {
  render() {
    return <div />
  }
}

// 报错
const B = styled.div`
  ${A} {
  }
`
// 通过这种方式引用
const StyledA = styled(A)``;
const B = styled.div`
  ${StyledA} {
  }
`
~~~

#### 对象式样式

~~~react
// Static object
const Box = styled.div({
  background: 'palevioletred',
  height: '50px',
  width: '50px'
});

// Adapting based on props
const PropsBox = styled.div(props => ({
  background: props.background,
  height: '50px',
  width: '50px'
}));

render(
  <div>
    <Box />
    <PropsBox background="blue" />
  </div>
);
~~~

#### 主题

~~~react
// Define our button, but with the use of props.theme this time
const Button = styled.button`
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border-radius: 3px;

  /* Color the border and text with theme.main */
  color: ${props => props.theme.main};
  border: 2px solid ${props => props.theme.main};
`;

// We are passing a default theme for Buttons that arent wrapped in the ThemeProvider
Button.defaultProps = {
  theme: {
    main: "palevioletred"
  }
}

// Define what props.theme will look like
const theme = {
  main: "mediumseagreen"
};

render(
  <div>
    <Button>Normal</Button>

    <ThemeProvider theme={theme}>
      <Button>Themed</Button>
    </ThemeProvider>
  </div>
);
~~~

## StoryBook

### 安装

在项目根目录下执行。

~~~sh
npx sb init
~~~

需要在已存在的项目中使用。

> Storybook needs to be installed into a project that is already setup with a framework. It will not work on an empty project.

### 运行

~~~sh
npm run storybook
~~~

### 开始

#### Component Story Format (CSF)

- 所有故事和组件都需要使用 ES 模块化，每个组件故事需由 `default export` 和 至少一个 `named exports`。

- 默认导出包括 `component` 、`title` 、`decorator` 和 `parameters` 等。

  其中 `title` 为必选项（需**全局唯一**），其他为可选项，但是 `component` 是推荐选项，因为 `addon` 界面能根据组件属性自动生成表单控件。

  ~~~react
  // MyComponent.story.js
  import MyComponent from './MyComponent';
  
  export default {
    title: 'Path/To/MyComponent',
    component: MyComponent,
    decorators: [ ... ],
    parameters: { ... },
    ...
  }
  ~~~

- 每个具名导出默认表示一个故事。

- 为故事函数输入参数

  ~~~react
  // Button.stories.js
  export const Text = ({ label, onClick }) => <Button label={label} onClick={onClick} />;
  Text.args = {
    label: 'Hello',
    onClick: action('clicked'),
  };
  ~~~

#### 故事写在哪里

挨着你的组件

~~~sh
Button.js | ts
Button.stories.js | ts
~~~

#### 如何去编写故事

~~~react
// Button.stories.js
import Button from './Button';

export const Primary = () => <Button background="#ff0" label="Button" />;
export const Secondary = () => <Button background="#ff0" label="😄👍😍💯" />;
export const Tertiary = () => <Button background="#ff0" label="📚📕📈🤓" />;
~~~

有什么问题？

对于较少的故事集，无伤大雅。当故事多起来后，就会变得冗余。

**使用 args**

~~~react
// Button.stories.js

// We create a “template” of how args map to rendering
const Template = (args) => <Button {...args} />;

// Each story then reuses that template
export const Primary = Template.bind({});
Primary.args = { background: '#ff0', label: 'Button' };

export const Secondary = Template.bind({});
Secondary.args = { ...Primary.args, label: '😄👍😍💯' };

export const Tertiary = Template.bind({});
Tertiary.args = { ...Primary.args, label: '📚📕📈🤓' };
~~~

## 问题

1. 样式判断是在样式组件 style.js 中编写还是在组件中编写？
2. 函数功能薄弱的问题如何解决？例如函数。
