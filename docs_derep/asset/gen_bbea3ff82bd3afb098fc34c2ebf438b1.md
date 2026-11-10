[PRE0]

第 14 行，这里声明了`MainActivity`这个类，它继承自`Activity`，也就是代表 Android 应用里的一块页面。
第 15～17 行，放了一个静态初始化块，里面执行了`loadLibrary`，目的就是从 JVM 指定的加载目录里把`nn_sample`的动态库拉进来。
第 22 行，出现了`initModel`方法，方法上标注了`native`，意味着真实的实现在前面加载进来的动态库里，Java 端调用时会被转发到库中的 C 函数。
第 24～26 行，依次声明了`startCompute`和`detroyModel`两个方法。
第 29 行，是`onCreate`生命周期回调，用来做`Activity`的启动初始化。
第 30 行，先调用父类的`onCreate`完成基础初始化。
第 31 行，通过`setContentView`把`activity_main`布局文件设为页面的主视图。
第 33 行，新建一个`InitModelTask`实例并立刻`execute`，让模型初始化在后台跑起来。
第 35 行，用`findViewById`找到布局里的`button`控件，并强转为`Button`类型。
第 36 行，给该按钮绑定`setOnClickListener`，注册点击监听。
第 40～44 行，在点击回调里，先分别从`inputValue1`和`inputValue2`两个输入框读取内容，转成字符串。
第 45～55 行，判断两个输入都不为空时，把字符串转成浮点数，随后启动`ComputeTask`的`execute`去做前向推理，同时用`Toast.makeText`弹出“正在计算中”的提示。
第 69 行，定义了一个内部类`InitModelTask`，专门负责模型初始化，它继承`AsyncTask`，用异步机制避免阻塞 UI 主线程。
第 71 行，类里实现了`doInBackground`，这是后台执行入口，主要工作就是调用外部类的`initModel`完成模型加载。
第 81 行，定义了`onPostExecute`，任务结束后会把结果切回主线程处理，这里直接把返回的`result`赋给`modelHandle`，不会有并发冲突。
第 86 行，又定义了一个`ComputeTask`类，同样继承`AsyncTask`，职责是执行模型计算。
第 88 行，其`doInBackground`里调用外部类的`startCompute`完成前向计算。
第 81 行，`onPostExecute`里先拿到页面上的结果文本控件，再用`setText`把处理好的字符串写到界面上。

到这里，App 的 Java 主体逻辑已经搭完。

不过还不够，项目里还含有 C++ 源文件，Android Studio 不会自动编译这部分，必须借助外部构建工具。本例选用 CMake 来构建 C++ 部分。下面给出`CMakeLists.txt`的配置，见代码清单 12-10。

### 代码清单 12-10　CMakeLists.txt 构建要点

[PRE1]

第 1 行，通过`cmake_mininum_required`声明 CMake 的最低版本要求。
第 3 行，调用`add_library`把`nn_sample.cpp`、`simpe_model.cpp`打包成名为`nn_sample`的共享库目标。
第 8 行，使用`target_link_libraries`把`neuralnetworks`、`android`、`log`三个系统库链接到该共享库。

接着要把 CMake 规则接入 Gradle 构建流程，修改后的`build.gralde`文件参考代码清单 12-11。

### 代码清单 12-11　改造后的 build.gralde 配置

[PRE2]

第 11 行，用`externalNativeBuild`开启外部原生构建的配置入口。
第 12 行，明确指定构建工具为`cmake`。
第 13 行，设置`cppFlags`，把 C++ 编译标准固定为`C++11`，以便使用较新的语法特性。
第 23～27 行，进一步指定`externalNativeBuild`的构建参数，声明 CMake 版本并把`src/main/cpp/CMakeLists.txt`作为输入的构建脚本，也就是上面编写的那一份。

完成上述配置后执行构建，就能把 App 安装到手机上运行了。

### 12.3　实战：Android 图像分类器落地

本节计划用裁剪版的`TensorFlow Lite`实现一个图像分类器，并保证可以在搭载 Android 的树莓派上正常运行。

#### 12.3.1　JNI 封装

第 11 章提到`TensorFlow Lite`是用 C++ 实现的，若要在 Android App 中调用，就必须做 JNI 封装。

因此我们先准备与`nativeinterpreterwrapper.cpp`对应的 Java 端类——`NativeInterpreterWrapper`，其实现示例如代码清单 12-12。

### 代码清单 12-12　NativeInterpreterWrapper 类实现

# `NativeInterpreterWrapper` 实现