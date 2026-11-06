JSX 实战速查：属性规范、元素本质与条件写法

写 JSX 组件时，先做三件事自检：命令式的 `for...of` 是否改写成声明式的 `array.forEach` 之类；函数体有没有漏掉 `return`；返回的东西能不能合法地放进属性或作为子节点。

属性赋值有明确分界。纯字符串可以直接用引号，布尔、数值、函数等非字符串类型必须用 `{}` 包裹。哪怕预期是字符串，只要需要计算，就必须放进花括号里。相关示例见：
[PRE0]

布尔属性取值为 true 时可以只写属性名，省略等号和值，写法如下：
[PRE1]

JSX 对原生 HTML 属性做了统一封装。比如 HTML 里容易写错的 `readonly="true"`，按 W3C 规范应是 `readonly="readonly"`，而 `readonly="false"` 依然会生效。JSX 里统一写成 `readOnly={true}` 或 `readOnly={false}`，更符合 JS 习惯。`className="top-nav"` 的命名是为了避开 HTML 的 `class` 与 JS 保留字的冲突。原生事件名 `onclick`、`onchange` 在 JSX 里基本都改成驼峰并接收函数，如 `onClick`、`onChange`，细节在 7.1 节展开。

JSX 里常见的双层大括号 `{{}}`，比如下面 style 的例子：
[PRE2]
外层花括号是表达式入口，内层花括号是构造对象。最终把一个对象传给属性。这也体现了 JSX 的 `style` 与 HTML 的 `style` 不同，前者要对象，后者要 CSS 文本，相当于 `ulElement.style.listStyle = 'none'`。

### 2.1.4 JSX 与 HTML 的异同
表 2-1 汇总了主要差别。

表 2-1 JSX 与 HTML 的异同

| 特征 | HTML | JSX |
| :--- | :--- | :--- |
| 标签闭合 | 不严格，如 `<p>段落` | 严格，如 `<p> 段落 </p>` |
| 空标签闭合 | 无须闭合，如 `<br>`、`<img>` | 需要闭合，如 `<br />`、`<img />` |
| 单根元素 | 不限制 | 组件需返回单根元素，如 `<div></div>`、`<></>` |
| 属性值加引号 | 可选，如 `<input type=text size=10>` | 须加引号或者大括号，如 `<input type="text" size={10} />` |
| 标签名 | 不区分大小写 | HTML 标签小写，自定义组件标签首字母需大写 |
| 属性名 | 不区分大小写，如onclick | 驼峰命名法，区分大小写，如onClick |
| 样式类属性名 | class | className |
| 样式属性值 | 字符串，如style="background-color: red;" | 对象，如style={{backgroundColor:'red'}} |
| <script>标签 | 支持 | 不支持（React 19以前版本） |
| JavaScript 表达式 | 不直接支持 | 在标签属性值或子元素位置使用大括号 { } |
| 注释 | <!-- xxx --> | {/* xxx */} |

## 2.2 进一步理解 JSX
下面拆解 JSX 背后的声明式思路，以及相关接口。

### 2.2.1 前端开发的声明式与命令式
开篇强调 React 是声明式、组件化的框架。声明式与命令式是两种对立范式。表 2-2 做系统对比。

表 2-2 前端开发中声明式与命令式的对比

| | 声明式 | 命令式 |
| --- | --- | --- |
| 现实世界用例 | 我希望大象在冰箱里。 | 一、打开冰箱门；二、把大象装到冰箱里；三、关上冰箱门。 |
| 代表性编程范式 | 函数式编程 | 面向对象编程 |
| 代表性后端技术 | SQL | Java |
| 代表性前端技术 | HTML、CSS | DOM API |
| 代表性前端框架 | React | jQuery |
| JavaScript 条件语句 | a ? b : c | if (a) { console.log(b) } else { console.log(c) } |
| JavaScript 循环语句 | array.map(item => ( item.prop )) | for (let item of array) { console.log(item) } |

React 的核心在于用描述替代操作。无论是 JSX 还是直接调 `React.createElement()`，开发者只声明想要的视图，渲染和 DOM 操作由框架完成。

除了 jQuery 之外命令式方案依然存在，但主流已转向声明式。React、Vue、Angular 都是声明式，Flutter 这类跨端方案也如此，其官方示例呈现典型的声明式风格：
[PRE3]

从 2017 年起 Flutter 社区每年都有声音要求加入 JSX，愿望未实现却说明 JSX 语法糖的吸引力。

理解 React 的声明式本质，有助于明确目标，写出更规范的 JSX。

### 2.2.2 JSX 的产物：React 元素
组件函数返回完整 JSX 结构。编译后每个节点都叫 React 元素，是应用最基础的单元。按类型分三类：

- React 封装的 HTML 或 SVG 元素，如 `<div></div>`、`<img />`，最终渲染为真实 DOM 节点。
- 组件渲染的元素，如 `<ThreadsPane />`，会调用对应组件的渲染。
- React Fragment 元素，`<React.Fragment></React.Fragment>` 或简写 `<></>`，不产生额外 DOM，只做分组。

Portal、Suspense 等暂不展开。

元素可加 props 以区分类型，这里特意保留英文强调接口。

HTML 元素的 props 与原生 attributes 基本对应，差异已在前文对比。

组件元素的 props 需与组件定义对应，未处理的 props 会被丢弃。常见错误是改了组件内部参数名却未同步 JSX 传参，导致子组件拿不到预期值。

Fragment 不支持额外 props。

除显式 props 外，若标签内有子节点，React 会自动生成隐式的 `children` prop。

谈到元素类型也要区分子元素。JSX 元素可以指定子元素。后续常说的“子组件”需明确：子组件一定是子元素，子元素不一定是子组件。

子元素类型包括：
- 字符串，最终渲染为标签内文本；
- 另一段 JSX，会嵌套渲染；
- JavaScript 表达式，渲染时执行并参与输出；
- 布尔值、null、undefined，不会渲染；
- 上述类型的数组。

子元素可以是字符串与表达式的混合，编译时会分组后作为数组传给父元素的 `children` prop，如：
[PRE4]

数组作为子元素是允许的，例如：
[PRE5]
代码能正常工作，但在 IDE 或浏览器控制台会提示警告：Warning: Each child in a list should have a unique "key" prop。这是 React 对数组子元素的特殊要求，key 的作用与规范将在 2.3 节详述。

### 2.2.3 不用 JSX 还能写 React 代码吗
2.1.1 节提到 JSX 是 `jsx()` 的语法糖，功能等同于 `React.createElement()`。不使用 JSX 时，可手动调用该函数创建元素。

`React.createElement()` 的签名如下：
[PRE6]
`type` 必填，表示元素类型；`props` 可选，用来定义属性；参数数量 ≥3 时，后续参数作为子元素 `children`。

以如下 JSX 为例：
[PRE7]
改写为 `React.createElement` 的形式：
[PRE8]
效果与编译后的 `jsx()` 版本几乎一致。

早期 JSX 编译目标是 `React.createElement()`，从 React 17 RC 起改为 `react/jsx-runtime` 中的 `jsx()` 函数，为框架优化提供空间。

日常开发推荐 JSX，但在开发组件库或复杂可复用组件时，`React.createElement()` 仍有价值。

## 2.3 编写 JSX 的常用模式
本节介绍 JSX 开发中的常见模式。结合实践，将在第 1 章的聊天应用 oh-my-chat 中实现联系人视图。

### 2.3.1 条件渲染
页面模板里条件判断是核心需求，JSX 也不例外。

以 oh-my-chat 的代码为例：
[PRE9]
这是一个典型条件表达式：当 `activeView` 为 `'chat'` 时返回后面的 JSX 渲染消息列表，否则返回 false。按子元素规则，false 不会被渲染，消息列表因此隐藏。

JavaScript 的常见条件语句有 if、if-else、switch-case。JSX 内部只能嵌入表达式，可用短路与、三元运算符等技巧替代传统语句。表 2-3 给出了对应写法。

表 2-3 各种 JavaScript 条件语句对应的 JSX 表达式写法

| JavaScript 条件语句 | JSX 条件渲染 |
| :--- | :--- |
| if ... | `<div>{condition && <CompA />}</div>`<br>`<div>{condition || <CompB />}</div>` |
| if ... else ... | `<div>{condition ? <CompA /> : <CompB />}</div>` |
| switch ... case ... | `<div><br>{conditionVar === 'case1' && <CompA />}<br>{conditionVar === 'case2' && <CompB />}<br>{/* 也可以嵌套使用三元运算符，但可读性较差，故不推荐 */}<br></div>` |

为标签属性赋值时同样可以嵌入表达式实现条件逻辑，如 oh-my-chat 导航栏的样式通过三元运算符根据 `activeView` 决定是否高亮：
[PRE10]

虽然 JSX 代码块要求使用表达式，但限制仅在块内。React 组件本身是 JavaScript 函数，函数体外可自由使用各种语句，例如在返回 JSX 前用 if-else 做前置处理。