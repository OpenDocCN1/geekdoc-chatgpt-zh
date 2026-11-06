# 2.1 代码逻辑的灵魂——ECMAScript6 基础

ECMAScript6 简称 ES6，严格来说，其并不是编程语言，而是一种规范。ECMAScript6 定义了一套脚本编程语言的语法规则，JavaScript 就是 ECMAScript 的一种实现。ECMAScript6 是 ECMAScript 的第 6 代版本，其相比较之前的版本有了非常大的设计升级，支持更加强大且现代的语法特性。

ECMAScript 是由 ECMA 通过的一种标准化的脚本程序设计语言规范。JavaScript 和 JScript 都是 ECMAScript 的一种实现与扩展。ECMAScript6 是 ECMAScript 的一次重要更新，新增了许多强大的特性，如模块和类、字典和集合数据类型、Promises 和 Generators 相关特性等。在小程序中，支持使用 ECMAScript6 的语法规范进行 JavaScript 代码的编写。

# 2.1.1 测试 JavaScript 代码

JavaScript 用来编写小程序中的核心逻辑。要学习 JavaScript 编程语言的基本语法，首先需要构建运行 JavaScript 的工作环境。互联网上提供了许多基于 NodeJS 平台的在线编辑和运行 JavaScript 代码的工具，具体如下：

https://tool.lu/coderunner/

上述网站提供了许多编程语言的在线编辑，并且可以将执行效果实时展示出来。本节学习的 JavaScript 语言大多可以在上面进行演示，读者也可以跟随本书一起测试自己编写代码的运行效果。

上述网站支持许多编程语言在线运行，如 PHP、C、C++、Python、NodeJS 等，在使用之前，需要将编程语言的类型修改为 NodeJS，页面左侧部分为在线编码区域，页面右侧部分为运行结果区域，如图 2-1 所示。

![](bbox=[0.13, 0.65, 0.84, 0.735])

上面示例代码中的 console.log()的作用是在控制台输出信息，上面代码就是 JavaScript 版本的 HelloWorld 程序。