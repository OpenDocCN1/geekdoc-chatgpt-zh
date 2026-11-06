直接子组件 MyComponent 能直接从父级拿到 props；如果要穿过层级，也可以靠 props drilling 把数据一路带到第二层、第三层的 MyChildComponent 和 MyGrandchildComponent；想要一次性喂给整棵子树，则用 context 更省事。

MyChildComponent 自己维护的 state B 作用域远比 state A 小，但二者并非完全互斥，存在重叠区。这样一来 MyChildComponent 以及它的后代 MyGrandchildComponent 就能同时消费 state A 和 state B。

当 ParentComponent 就是应用的根节点时，state A 基本可以当成全局状态来用，而 state B 则是局部状态。全局与局部并非绝对，相对看待即可。举例来说，根节点本身不提供状态，而它唯一的子节点提供了状态，这份状态在实际使用中同样具有全局意义。局部状态也可以通过状态提升按需升级为全局状态。

全局状态的波及面更广，初次定义和后续修改都要更谨慎，尤其是某个组件去改动全局状态时可能触发多处重渲染。没有明确需求就把局部状态提升为全局，是应该避免的习惯。区分全局与局部，也是在阅读和维护 React 代码时必备的能力。实践中，业务相关的状态更容易走向全局。

### 10.4.3 状态钩子 useReducer
10.4.2 里提到的全局状态会带来新问题，随着状态覆盖面扩大，更新函数往往会被一路传下去，子孙组件怎么用完全看自觉。尤其是当状态是对象或数组时，更新方式多种多样。定义状态的根组件能否给更新函数加上约束，来提升状态逻辑的内聚性？这里就引出了处理不可变数据的常见做法——**reducer**，也就是归约器函数。

在该模式下，开发者把数据变更逻辑收敛成一个或多个 reducer，并让它们对应不同的动作。调用方只需用调度器派发动作，就能触发对应的状态更新。

React 提供了 `useReducer` 这个 Hook 来以 reducer 的方式描述状态更新。可以把它看成 `useState` 的另一种写法。以 oh-my-chat 中的 `NavigationCtxProvider` 为例，用 `useReducer` 重写后的代码如下：

[PRE0]

代码量看上去变多了，因为 `useReducer` 相比 `useState` 多了一层抽象，引入了三个概念。

- 动作 action：一个带 `type` 字段的普通 JS 对象，用来表达意图或事件，可选地携带 `payload`。
- 归约器 reducer：纯函数，接收当前状态和动作，根据 `type` 返回新的状态，常定义在组件外部。
- 调度器 dispatch：派发动作以触发 reducer 执行。

即便抽象变多，基于 `useReducer` 的数据流动依然是单向的。上面的例子很简单，只是处理字符串类型的 state。真实项目里状态多为对象、数组甚至多层嵌套，reducer 的编写难度也会上升。值得一提的是，`useState` 底层其实就是基于 `useReducer` 实现的。

另外 `useReducer` 中的 action、reducer、dispatch 与 Redux 的设计几乎一致。提到 React 生态的状态管理，Redux 总是第一个被提及。Redux 是面向 JavaScript 应用的可预测状态容器。它包含 store 这个概念，store 由 reducer 给出的初始值创建，可通过 `store.getState()` 读取最新状态，通过 `store.dispatch()` 派发 action，并允许外部订阅变化。Redux 在 React 之外建立了自己的单向数据流，也能与 React 结合。简化版的 Redux 示例如下：

[PRE1]

受限于篇幅，这里不展开 Redux 的细节，只列出它的三条核心原则。

- 单一事实来源。Redux 全局只有一个 store，保存唯一的状态树。
- 状态只读。状态不可变，只能通过派发 action 让 reducer 返回包含变更的新状态。
- 状态变更无副作用。store 里的 reducer 必须是纯函数。

这三条原则共同保证了 Redux 管理的状态高度可预测，也为选型其他状态管理方案提供了参考。

### 10.5 状态管理方案 Zustand
本节先讨论引入独立状态管理框架的时机，再介绍近两年流行起来的 Zustand，并尝试用它重构 oh-my-chat 部分逻辑，实现聊天视图与联系人视图之间的状态共享，最后探讨项目中能否同时使用 Zustand 与 React 内置 state。

虽然本节示例基于 oh-my-chat 使用了 Zustand，但思路同样适用于 Redux 等框架。

#### 10.5.1 何时引入独立的状态管理框架
对小规模 React 项目来说，加一个状态管理框架往往得不偿失。但当项目会持续变大，或已经是中大型规模时，就值得考虑独立方案。尤其是当大量组件的状态都被提升到根节点，全局状态不断膨胀时，就需要引入专门的状态管理。Zustand、Redux 都倡导全局单一 store 的理念，正适合管理全局状态。

正如 10.4.1 所说，业务状态常被多个组件共享且需要保持一致性，这正是状态管理框架擅长的场景。发现某些状态需要在项目内高度复用且保持一致，就该考虑引入框架。把状态管理从组件树外化后，能摆脱层级限制，让开发者专注状态本身。

尽管理想化一些，可以把 React 的状态管理框架看作 React 单向数据流的一层抽象。基于框架写的代码与 React 解耦，开发和测试更独立。抽象数据流后，组件 state 的复杂度会下降，并实现关注点分离。部分框架甚至完全独立于 React，例如 Redux，其逻辑可平滑迁移到其他 Web 框架。

#### 10.5.2 Zustand 概览
React 早期，Flux 代表了单向数据流的独立方案。Redux 在此基础上发展，并在很长一段时间内成为首选。10.4.3 提到，Redux 的三原则以及 store、action、reducer 等概念深刻影响了后续方案。Redux 流行之余也被诟病代码冗长，后来官方推出的 Redux Toolkit 大幅减少样板代码。

本章为 oh-my-chat 选择的并非 Redux，而是近年兴起的 **Zustand**。相比 Redux，Zustand 设计更轻巧，API 更偏向 Hooks，贴合现代 React 开发。简单示例：

[PRE2]

代码用 Zustand 核心 API `create` 生成了一个状态存储，里面只有一个 `threads` 状态，并附带 `addThread`、`removeThread` 两个动作方法。在 Zustand 里，动作方法等价于 `useReducer` 或 Redux 的 dispatch + action + reducer 组合，用来变更 state。reducer 部分依然遵循不可变写法。`create` 返回的函数本质是一个自定义 Hook，可直接在组件中使用：

[PRE3]

调用 store Hook 时通常会传一个选择器 selector，用来从 store 中挑出需要的 state 或 action。上面的例子调用了三次 useStore，得到的 threads 相当于过去用 useState 定义的 state，addThread 和 removeThread 则相当于更新函数，能直接在 onClick 中调用。点击“添加”后 threads 更新，列表随之重新渲染。底层 Zustand 调用了 React 的 `useSyncExternalStore`，更具体是 `use-sync-external-store` 中的 `useSyncExternalStoreWithSelector`，只要合理设计状态、遵守不可变规则并配合 selector，性能是有保障的。

useStore 还可以抽到独立的 .js 文件导出，多处 import 调用即可跨组件共享状态，无需再靠提升状态、props 或 context 传导。

从这些角度看，Zustand 用起来更简便，也具备不错的扩展性。例如 Zustand 的 devtools 中间件可直接接入 Redux DevTools。Redux DevTools 能跟踪 state 和 action，还提供时间旅行功能。加上以下代码，就能在 Redux DevTools 里观察 Zustand 的状态：

[PRE4]

#### 10.5.3 在 Zustand 中借助 Immer 操作不可变数据
从 10.5.2 的示例可见，Zustand 仍依赖不可变数据。10.3 节提到可以用 Immer 库更直观地更新不可变数据。Zustand 内置了与 Immer 集成的中间件。启用 Immer 后，10.5.2 的代码可改写为：

[PRE5]

#### 10.5.4 实践：用 Zustand + Immer 共享联系人状态
回顾 10.4.1 的需求：在对话视图里，所有展示联系人名称和头像的位置，都应按联系人 ID 从 contacts 数据中读取。这涉及两个视图间的数据共享，为保证一致性，全局应只保留一份联系人名称和头像。为展示状态关联，还会额外实现修改联系人名称与删除联系人。考虑到后续章节仍需改动，本节只限于联系人视图和聊天视图的对话列表。

首先安装依赖：

[PRE6]

在 src 目录下新建 stores 目录，并在其中创建 chatStore.js，使用 Zustand 的 create 接口配合 immer 中间件创建状态存储，命名为 useChatStore 并导出。具体代码如下：