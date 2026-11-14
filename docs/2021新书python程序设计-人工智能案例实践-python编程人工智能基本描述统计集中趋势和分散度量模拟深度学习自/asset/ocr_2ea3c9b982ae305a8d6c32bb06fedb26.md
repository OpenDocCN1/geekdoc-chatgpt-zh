第一个条件grade>=90为False，因此跳过语句print('A')。第二个条件grade>=80也是False，语句print('B')也被跳过。第三个条件grade>=70为True，因此执行语句print('C')。然后跳过if...elif...else语句中的所有剩余代码。if...elif...else语句的执行速度比单独执行多个if语句的速度更快，因为当条件为True时，会立即终止测试。

# else是可选项

if...elif...else语句中的else是可选项。包含else可以使代码处理不满足任何条件的值。当没有else的if...elif语句中的条件全部都为False时，程序不会执行任何语句套件，而是直接执行if...elif语句后的下一条语句。如果在if...elif语句中包含else，则必须把它放在最后一个elif之后；否则，会引发SyntaxError。

# 逻辑错误

代码段[16]中错误地使用缩进是一个非致命逻辑错误的示例。这个代码段可以执行，但会产生错误的结果。如果脚本中存在致命的逻辑错误，会引发异常（例如尝试除以0会引发ZeroDivisionError），Python会显示回溯，然后终止脚本。交互模式中的致命错误仅终止当前代码段，然后IPython会等待下一个输入。