在 JSX 写法里常要自检三件事：
- 是否把命令式的 `for...of` 换成了声明式的 `array.forEach` 这类写法；
- 组件函数里是否遗漏了 return；
- 返回的值能否合法地作为 JSX 的属性或子节点使用。

JSX 标签的属性赋值有明确规则。纯字符串类型的属性才可以直接用引号包裹；一旦属性是布尔、数值或函数，就必须用大括号 `{}` 传入。即使属性本身预期是字符串，只要值需要通过表达式计算出来，也要套在大括号里。示例见：
[PRE0]

布尔类型属性还有一个简写习惯。当布尔属性值为 true 时，可以只写属性名，不写等号和值，例如：
[PRE1]

JSX 对 HTML 标签属性做了统一的标准化处理，这正是 React 在浏览器 DOM 之上做的封装。比如 HTML 里容易混淆的 `readonly="true"`，按 W3C 规范应写成 `readonly="readonly"`，而 `readonly="false"` 实际上仍然生效；在 JSX 中统一写作 `readOnly={true}` 或 `readOnly={false}`，更贴合 JavaScript 的表达方式。`className="top-nav"` 的出现也是为了避开 HTML 中 `class` 与 JavaScript 保留字冲突的问题。原生 HTML 的事件属性 `onclick`、`onchange` 这类写法，在 JSX 里基本都改为驼峰并接收函数，如 `onClick`、`onChange`，具体细节在 7.1 节展开。

另外，JSX 中常见的双层大括号 `{{}}`，像下面 style 属性的例子：
[PRE2]
外层一对大括号负责开启表达式，内层一对大括号则是用来创建一个 JavaScript 对象。整体效果是把一个对象传给属性。这也说明 JSX 的 `style` 和 HTML 的 `style` 不同：HTML 接受 CSS 文本字符串，JSX 接受描述样式的对象，等价于操作 `ulElement.style.listStyle = 'none'`。

### 2.1.4 JSX 与 HTML 的区别对照
JSX 和 HTML 的差异汇总在表 2-1。

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

## 2.2 深入认识 JSX
本部分会拆解 JSX 背后的声明式思想，以及与之对应的声明式接口。

### 2.2.1 声明式与命令式的编程对照
开篇已经提到 React 属于声明式且组件化的前端方案。既然有声明式，就离不开相对的命令式。两种范式在软件工程里长期并存。表 2-2 从生活例子、技术代表到具体代码，对两者做了系统梳理。

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

React 作为声明式技术的典型代表，核心体现在视图的描述方式上。无论用 JSX 还是直接调用 `React.createElement()`，开发者描述的是期望的最终界面，渲染过程和 DOM 变更由框架自行完成。

除了 jQuery，还有没有命令式的前端方案？当然有，但整体趋势已转向声明式。React、Vue、Angular 三大主流框架都是声明式的，新兴跨端方案 Flutter 也遵循这一思路，其官方示例呈现出明显的声明式 API 风格：
[PRE3]

这种写法让人会心一笑。自 2017 年起，Flutter 社区里每年都有声音呼吁引入 JSX。愿望虽未实现，却侧面印证了 JSX 语法糖的吸引力。

弄清 React 的声明式本质，有助于明确目标，写出更好的 JSX。

### 2.2.2 JSX 生成的产物：React 元素
从前面示例可看出，组件函数返回的就是一段完整的 JSX。JSX 编译后每个节点都称为 React 元素，是应用最基础的构造单元。按类型可分为三类。

- React 封装的 HTML 或 SVG 元素，如 `<div></div>`、`<img />`，最终映射为真实 DOM 节点。
- React 组件渲染的元素，如 `<ThreadsPane />`，会触发对应组件的渲染。
- React Fragment 元素，`<React.Fragment></React.Fragment>` 或简写 `<></>`，不产生额外 DOM，仅用于分组。

还有 Portal、Suspense 等类型，这里暂不展开。

为区分元素可为 JSX 元素添加 props，这里特意沿用英文强调接口概念。

HTML 元素的 props 与原生 attributes 基本对应，差异前面已对比过。

组件元素的 props 要与组件定义中的参数对应；若组件未处理，多余的 props 会被忽略。常见错误是改了组件内部参数名，却忘了同步更新 JSX 中的传值，导致子组件拿不到预期数据。

Fragment 不支持额外 props。

除显式 props 外，只要闭合标签内有子节点，React 会自动生成一个隐式的 `children` prop。

谈到元素类型，也要区分子元素。JSX 元素可指定子元素。后续会频繁提到“子组件”，需注意：子组件一定是子元素，子元素不一定是子组件。

子元素可以是：
- 字符串，最终渲染为标签内的文本；
- 另一段 JSX，会被嵌套渲染；
- JavaScript 表达式，渲染时执行并参与输出；
- 布尔值、null、undefined，不会渲染；
- 上述类型的数组。

子元素还能是字符串与表达式的混合，JSX 编译时会把它们分组，作为数组传给父元素的 `children` prop，如：
[PRE4]

数组作为子元素是允许的，例如：
[PRE5]
这段代码能正常运行，但在 IDE 或浏览器控制台会提示警告：Warning: Each child in a list should have a unique "key" prop。这是 React 对数组子元素的特殊要求，key 的作用与规范将在 2.3 节详述。

### 2.2.3 没有 JSX 还能写 React 吗
2.1.1 节提到 JSX 是 `jsx()` 函数的语法糖，功能上等同于 `React.createElement()`。不使用 JSX 时，可手动调用该函数创建元素。

`React.createElement()` 的签名如下：
[PRE6]
`type` 必填，表示元素类型；`props` 可选，用来定义属性；当参数个数 ≥3 时，后续参数作为子元素 `children`。

以如下 JSX 为例：
[PRE7]
改写为 `React.createElement` 的形式：
[PRE8]
效果与编译后的 `jsx()` 版本几乎一致。

早期 JSX 编译目标是 `React.createElement()`，从 React 17 RC 起，JSX 编译目标改为 `react/jsx-runtime` 中的 `jsx()` 函数，以便框架获得更多优化空间。

日常开发推荐 JSX，但在开发组件库或较为复杂的复用组件时，`React.createElement()` 仍有价值。

## 2.3 JSX 常用编写模式
本节介绍 JSX 开发中的常见模式。结合实践，将在第 1 章的聊天应用 oh-my-chat 里实现联系人视图。

### 2.3.1 条件渲染
在页面模板里，条件判断是核心需求，JSX 也不例外。

以 oh-my-chat 的代码为例：
[PRE9]
这是一个典型的条件表达式：当 `activeView` 为 `'chat'` 时返回后面的 JSX 渲染消息列表，否则返回 false。根据子元素规则，false 不会被渲染，消息列表因此隐藏。

常见的 JavaScript 条件语句有 if、if-else、switch-case。但 JSX 内部只能嵌入表达式，可用短路与、三元运算符等技巧替代传统语句。表 2-3 给出了对应写法。

表 2-3 各种 JavaScript 条件语句对应的 JSX 表达式写法

| JavaScript 条件语句 | JSX 条件渲染 |
| :--- | :--- |
| if ... | `<div>{condition && <CompA />}</div>`<br>`<div>{condition || <CompB />}</div>` |
| if ... else ... | `<div>{condition ? <CompA /> : <CompB />}</div>` |
| switch ... case ... | `<div><br>{conditionVar === 'case1' && <CompA />}<br>{conditionVar === 'case2' && <CompB />}<br>{/* 也可以嵌套使用三元运算符，但可读性较差，故不推荐 */}<br></div>` |

为标签属性赋值时同样可以嵌入表达式实现条件逻辑，如 oh-my-chat 导航栏的样式通过三元运算符根据 `activeView` 决定是否高亮：
[PRE10]

虽然 JSX 代码块内部要求使用表达式，但这个限制只在块内生效。React 组件本身是一个 JavaScript 函数，函数体外可自由使用各种语句，比如在返回 JSX 前用 if-else 做前置处理。