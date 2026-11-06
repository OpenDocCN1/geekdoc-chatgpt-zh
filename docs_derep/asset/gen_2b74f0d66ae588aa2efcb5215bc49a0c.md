我们可以用 6.3 节 oh-my-chat 中新加入的副作用来验证这个插件是否生效。用 VS Code 打开 MessagesPane.jsx，把 MessagesPane 组件里 useEffect 依赖数组中的 selectedThreadId 移除，这时依赖数组上就会出现 ESLint 警告，提示 React Hook useEffect 缺少依赖项 “selectedThreadId”。

> React Hook useEffect has a missing dependency: 'selectedThreadId'. Either include it or remove the dependency array.

如果再把整个依赖数组删掉，useEffect 上会出现另一条 ESLint 警告，指出该副作用操作了 state，若不提供依赖列表会引发更新死循环。

> React Hook useEffect contains a call to 'setIsLoading'. Without a list of dependencies, this can lead to an infinite chain of updates. To fix this, pass [selectedThreadId] as a second argument to the useEffect Hook.

IDE 没有装 ESLint 插件时，也可以在命令行执行 npm run lint，看到同样的 warning。

ESLint 只在开发阶段起作用，不会影响生产环境的运行行为。除 useEffect 依赖数组外，静态代码检查还能帮助开发者规避大量常见编程错误。

### 6.4.2 副作用为何在开发时执行两次

在 6.2.2 节里，为避免 NewMessageForm 组件的副作用随每个字符输入而触发，我们给它加上了空依赖数组。即便如此，首次渲染时仍然能观察到它被触发了两次，这是正常现象。

开发模式下 React 对副作用采用特殊执行策略：在组件挂载阶段，先执行副作用回调，紧接着立刻调用该副作用的清理函数，再次执行副作用回调。这模拟了组件先卸载后重新挂载的情形，或依赖项多次变更时的副作用行为，有助于开发者核查清理函数是否写对。生产模式下 React 不会这样做，只会执行一次副作用。开发者无需为开发模式的执行次数做特殊优化，只要保证副作用在组件反复挂载时能正常工作即可。

## 6.5 本章要点回顾

本章从前端领域的副作用概念切入，引入 React 中副作用的定义，即由 React 渲染触发、主要用于与外部系统交互的副作用。随后详细介绍了 React 核心 Hooks 之一的 useEffect，包括基本用法、基于依赖数组的条件执行以及清理函数。接着以聊天应用 oh-my-chat 为例，用 useEffect 实现了与消息列表相关的三个新功能：拉取远程数据、展示消息发送的相对时间、自动滚动到列表末尾。为了降低出错率，章节末尾介绍了依赖数组的静态检查工具，并指出 React 在开发模式下会强制让副作用执行两次。

# 第7章 事件交互机制

本章的知识导图与项目实现见图 7-1。

[PRE0]

如第 6 章所述，React 与外部系统的交互主要通过副作用处理和事件处理两种方式实现。事件处理不仅服务于系统间交互，还能实现与用户的双向互动，为应用带来更好的体验。本章将深入探讨 React 的事件处理机制。

在 Web 开发里，事件处理体系已相当成熟。标准 DOM API 提供了完整的 DOM 事件体系，开发者可以借助事件监听及其冒泡与捕获机制构建复杂交互逻辑。

React 在此基础上构建了自己的合成事件 Synthetic Event 系统，与原生 DOM 事件有显著差异。初次接触合成事件的开发者常会有以下疑问：

- 什么是 React 合成事件？
- 为什么不用直接原生 DOM 事件而使用合成事件？
- 合成事件的典型应用场景有哪些？
- 哪些场景下需要使用原生 DOM 事件？

本章将介绍 React 合成事件与原生 DOM 事件的异同及内在联系。oh-my-chat 消息列表中的受控组件是合成事件的重要使用场景之一。但合成事件并非 React 事件处理的全部，部分特殊场景仍需原生 DOM 事件。同时，我们将在 oh-my-chat 中加入一些小功能来展示合成事件和原生 DOM 事件的处理方式。

## 7.1 React 合成事件体系

React 的事件处理主要建立在合成事件系统之上。虽然底层实现仍依赖浏览器的原生 DOM 事件，但通过封装，它有效解决了浏览器兼容性差异以及原生事件系统复杂性这两个核心问题，使开发者在 React 框架中更高效地处理事件。

对使用过原生 DOM 事件的前端开发者来说，传统事件处理的写法并不陌生：

[PRE1]

在 React JSX 中，HTML 标签同样拥有以 on* 开头的事件处理属性。明显区别在于命名规范采用 on* 开头的驼峰写法，如 onClick、onKeyDown。在 JSX 中使用这些属性时需要传入函数，而不能使用字符串：

[PRE2]

以 button 组件为例，开发者把 handleClick 函数传给 onClick 属性。浏览器中用户点击按钮时 handleClick 会被调用，无论是否需要，React 都会把一个描述点击事件的对象作为第一个参数传入，这个对象即为 React 中的合成事件。

合成事件是原生 DOM 事件的高层封装，接口与原生事件相同，遵循 W3C 规范，React 内部对不同浏览器的行为进行了规范化 Normalize，开发者无需担心事件处理的浏览器兼容问题。

## 7.2 合成事件与原生 DOM 事件的差异

合成事件与原生 DOM 事件之间存在明显区别。

### 7.2.1 监听方式各不相同

监听原生 DOM 事件基本有三种方式。

（1）与 React 合成事件类似，以内联方式写在 HTML 标签中：

[PRE3]

（2）在 JavaScript 中把事件处理函数赋值给 DOM 元素的事件处理属性：

[PRE4]

（3）在 JavaScript 中调用 DOM 元素的 addEventListener 方法，需要在合适时机调用 removeEventListener 以防内存泄漏：

[PRE5]

合成事件无法通过 addEventListener 监听，其 JSX 写法为：

[PRE6]

特定场景需要采用事件捕获机制监听，原生 DOM 事件通过在 addEventListener 中加入第三个参数实现：

[PRE7]

而在 React 合成事件中，则需在事件属性名后加上 Capture 后缀：

[PRE8]

### 7.2.2 某些事件的表现不一样

合成事件通过规范化处理解决了浏览器间事件行为以及不同元素上事件行为不一致的问题，其中最具代表性的是 onChange 事件的处理。

在原生 DOM 中，不同浏览器对表单元素 change 事件的处理差异明显：对于文本框 `<input type="text" />`，change 事件在内容被修改且失去焦点时才触发。而对于下拉框 `<select>` 的 change 事件，Chrome 会在每次按下键盘方向键时触发，Firefox v63.0 之前的版本则只在失去焦点时触发。React 通过合成事件系统统一了这些差异，对 `<input>`、`<textarea>` 和 `<select>` 三种表单元素的 onChange 事件进行了标准化处理，核心原则是在避免显示抖动的前提下尽可能实时响应表单值的变化。以文本框为例，同样输入一句话，合成 change 事件的触发频率高于原生 change 事件。

需要说明的是，原生 change 事件的行为差异只是前端兼容性问题的冰山一角。即便主流浏览器兼容性已大幅改善，React 等前端框架仍在幕后默默处理大量类似的“疑难杂症”。

### 7.2.3 实际挂载的 DOM 节点不同

在原生 DOM 事件中，event.currentTarget 会明确指向 id 为 btn 的按钮元素：

[PRE9]

合成事件的行为则不同。通过调试 oh-my-chat 项目可以观察到这一差异。具体操作：在浏览器打开应用页面并启动开发者工具，切换到 Sources 面板，在 Firefox 中为 Debugger 调试器，在资源列表中找到 localhost:5173→src→components 下的 NewMessageForm.jsx 文件。这里会出现两个同名文件，一个是被浏览器真正下载读取的编译后 JavaScript 文件，另一个是经过 source map 映射的源码，与 IDE 中的源码基本一致。选择映射后的源码文件，在 textarea 文本框的 onChange 事件处理函数 handleChange 中设置断点，页面文本框输入新消息时执行会停在断点处。开发者工具右侧可以看到传入的 evt 参数类型为合成事件基类 SyntheticBaseEvent，其中 evt.nativeEvent 属性很重要，通过它可以得到合成事件封装的原生 DOM 事件。查看以下属性值：

- evt.currentTarget
- evt.target
- evt.nativeEvent.currentTarget
- evt.nativeEvent.target

可以确认两种事件的 target 属性都正确指向文本框元素，合成事件的 currentTarget 同样指向文本框，完全符合 W3C 规范；但原生 DOM 事件的 currentTarget 却指向了 React 应用的根容器 DOM 元素 `<div id="root"></div>`。这是 React 事件代理机制导致。具体来说，通过 createRoot 创建根节点时，React 会在根容器上监听所有支持的原生 DOM 事件。原生事件被触发时，React 根据事件类型和目标元素定位到对应的 React 元素及其事件处理函数，然后创建相应的合成事件并调用处理函数。从表层接口看，合成事件所有公开属性都严格遵循 W3C 事件规范，这种设计既统一了不同浏览器的事件行为差异，又显著提升了开发效率和代码可维护性。

## 7.3 合成事件的冒泡与捕获

与原生 DOM 事件类似，合成事件同样支持冒泡与捕获机制。下面这个简单例子定义了两层嵌套的 `<div>` 结构：

[PRE10]

外层和内层 `<div>` 都监听了 onClick 事件，运行时点击“可点击内容”，内层 `<div>` 的 onClick 会先触发，随后通过事件冒泡机制传播到外层 `<div>`，因此 handleInnerClick 和 handleOuterClick 会依次执行，顺序为先内层后外层。

若希望改变内外处理函数的执行顺序，可使用事件捕获。对于上述示例，只需在外层事件属性名后添加 Capture 后缀即可：

[PRE11]

修改后，执行顺序变为先外层后内层。

若需要阻止事件进一步传播，无论是冒泡还是捕获，只需在事件处理函数中接收描述合成事件的 evt 参数，然后调用 evt.stopPropagation() 即可。

## 7.4 受控组件

表单处理是前端常见需求，在 React 中也很重要。目前 oh-my-chat 项目中唯一的表单代码如下：

[PRE12]

用户在文本框输入文本时，会触发 onChange 合成事件并调用 handleChange 函数。该函数通过 evt 传递的最新输入值保存在组件的 content 状态中。state 更新会触发组件重新渲染，使文本框显示值与 content 状态保持同步，形成完整的控制闭环。

这种以 React 组件 state 为唯一事实来源，并通过合成事件处理用户交互的组件，被称为受控组件。除了文本框，大部分表单元素包括单选框、多选框、下拉框等都可以做成受控组件。当这些元素组合成表单时，开发者可以随时获取表单数据进行验证、提交到服务端等操作。

虽然 oh-my-chat 示例中使用了 `<form>` 元素，受控组件的应用并不局限于表单内部。无论是文本框还是其他表单控件，都可以独立于 `<form>` 使用，只要将值与 state 绑定即可实现受控行为。

现在为 oh-my-chat 的消息发送功能增加一个便捷操作：在输入新消息的同时按下 Shift 和 Enter 键即可快速发送，无需再点击“发送”按钮。为实现此功能，可为 NewMessageForm 组件的 textarea 添加 onKeyDown 属性，并指定 handleKeyDown 事件处理函数：

[PRE13]

“提交表单”功能有三种实现方式：

- 直接复制 handleSubmit 函数代码，即调用 onSubmitMessage 并清空 state；
- 直接调用 handleSubmit 函数；
- 通过 evt.target.form.requestSubmit() 触发提交表单事件。

推荐采用第三种方式，其优势在于按键事件只触发提交动作，不直接处理提交后的具体逻辑，便于后期扩展表单验证等功能，实现了组件逻辑与表单整体逻辑的解耦。