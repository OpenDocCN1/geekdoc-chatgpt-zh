块的集合，而且在根目录中包含 Python 文件__init__.py（说明：在程序中实际输入时 init 两边各两个底线，中间没有空格，此处分开只为方便理解）。

为了方便说明，当本书的 Python 程序中导入 Python 模块与包后，无论调用模块的对象方法还是函数，都会统一使用函数来表示。

# 1. 导入模块或包

Python 程序中使用 import 关键字导入模块或包。例如，导入名为 random 的模块，然后直接调用此模块的函数来产生随机数值（Python 程序：Ch2_4_2.py），如下所示：

```
import random
```

上述程序代码导入名为 random 的模块后，就可以调用模块中的 randint() 函数，产生指定范围内的整数随机数值，如下所示：

```
target = random.randint(1, 100)
```

上述程序代码产生 1~100 的整数随机数值。

# 2. 模块或包的别名

在 Python 程序文件导入模块或包，除了使用模块或包名称来调用函数外，也可以使用 as 关键字为模块取一个别名，然后使用别名来调用函数（Python 程序：Ch2_4_2a.py），如下所示：

```
import random as R
```

```
target = R.randint(1, 100)
```

上述程序代码在导入 random 模块时，使用 as 关键字为 random 模块取了别名 R，所以可以使用别名 R 来调用 randint() 函数。

# 3. 导入模块或包的部分名称

当 Python 程序使用 import 关键字导入模块后，导入的模块默认是全部内容。但实际上，可能只会使用到模块的 1 或 2 个函数或对象，此时可使用 form/import 程序语句导入模块的部分名称。例如，在 Python 程序导入 BeautifulSoup 模块（Python 程序：Ch2_4_2b.py），如下所示：

```
from bs4 import BeautifulSoup
```

上述程序代码导入 BeautifulSoup 模块后，就可以创建 BeautifulSoup 对象，如下所示：

```
html_str = "<p>Hello World!</p>"
soup = BeautifulSoup(html_str, "lxml")
print(soup)
```

Tip form/import 程序语句导入的变量、函数或对象是导入目前的程序文件，成为目前程序文件的范围，所以使用时并不需要使用模块名称来指定所属的模块，直接使用 BeautifulSoup 即可。