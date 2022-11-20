---
title: StoryBook
tag: [Frontend]
---

### 安装

在项目根目录下执行。

<!-- more -->

```sh
npx sb init
```

需要在已存在的项目中使用。

> Storybook needs to be installed into a project that is already setup with a framework. It will not work on an empty project.

### 运行

```sh
npm run storybook
```

### 开始

#### Component Story Format (CSF)

- 所有故事和组件都需要使用 ES 模块化，每个组件故事需由 `default export` 和 至少一个 `named exports`。

- 默认导出包括 `component` 、`title` 、`decorator` 和 `parameters` 等。

  其中 `title` 为必选项（需**全局唯一**），其他为可选项，但是 `component` 是推荐选项，因为 `addon` 界面能根据组件属性自动生成表单控件。

  ```react
  // MyComponent.story.js
  import MyComponent from './MyComponent';

  export default {
    title: 'Path/To/MyComponent',
    component: MyComponent,
    decorators: [ ... ],
    parameters: { ... },
    ...
  }
  ```

- 每个具名导出默认表示一个故事。

- 为故事函数输入参数

  ```react
  // Button.stories.js
  export const Text = ({ label, onClick }) => <Button label={label} onClick={onClick} />;
  Text.args = {
    label: 'Hello',
    onClick: action('clicked'),
  };
  ```

#### 故事写在哪里

挨着你的组件

```sh
Button.js | ts
Button.stories.js | ts
```

#### 如何去编写故事

```react
// Button.stories.js
import Button from './Button';

export const Primary = () => <Button background="#ff0" label="Button" />;
export const Secondary = () => <Button background="#ff0" label="😄👍😍💯" />;
export const Tertiary = () => <Button background="#ff0" label="📚📕📈🤓" />;
```

有什么问题？

对于较少的故事集，无伤大雅。当故事多起来后，就会变得冗余。

**使用 args**

```react
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
```
