```
simulate
```

用户可以在 Mujoco 仿真器官方的 Mujoco Menagerie 仓库（https://github.com/google-deepmind/mujoco_menagerie.git）下载已经搭建好的机器人场景，并使用如下命令来加载所需的机器人：

```
simulate path_to_xml_file/robot.xml
```

其中的 robot.xml 文件是 Mujoco 仿真器专用的机器人场景描述文件。

此外，Mujoco 仿真器官方还基于 Pybind11，实现了 Python 版本的仿真器应用开发包，并发布在 PyPI 上。用户可以通过 Python 的 Pip 软件包管理工具直接安装 Python 版本的 Mujoco 仿真器。

```
pip3 install mujoco
```

安装好 Mujoco 仿真器后，可以使用其提供的函数接口，来加载模型并进行物理仿真。下面给出了一个基于 Mujoco C/C++ 接口实现物理仿真的简单例子。

```
#include "mujoco.h"
#include "stdio.h"
char error [1000];
mjModel* mj_model;
mjData* mj_data;
int main ( void ) {
    // 加载模型
    mj_model = mj_loadXML ( "hello.xml", NULL, error, 1000 );
    // 如果模型为空，退出程序
    if ( !mj_model ) {
        printf ( "%s\n", error );
        return 1;
    }
    // 创建一个 mjdata 数据结构类型用于存储仿真过程中的数据
    mj_data = mj_makeData ( m );
    // 让仿真持续 10s
    while ( mj_data ->time <10) {
        // 进行一步仿真计算，计算结果会存入 mj_data
        mj_step ( mj_model, mj_data );
    }
    // 释放内存
    mj_deleteData ( mj_data );
    mj_deleteModel ( mj_model );
    return 0;
}
```

其中的 hello.xml 场景文件的内容如下：

```
<mujoco >
<asset >
```