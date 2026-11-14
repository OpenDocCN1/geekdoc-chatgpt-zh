# 15.3 自定义Anaconda环境

在执行本章的示例之前，需要安装库。在本章示例中，我们将使用TensorFlow深度学习库的Keras版本。在撰写本文时，TensorFlow尚不支持Python 3.7，因此，需要使用Python 3.6.x来执行本章的示例。本章将展示如何设置自定义环境以使用Keras和TensorFlow。

# Anaconda环境

Anaconda Python发行版可以轻松创建自定义环境。自定义环境是单独的配置，可以在其中安装不同的库和同一个库的不同版本。如果代码依赖于特定的Python或特定版本的库，可以利用它进行复现。

Anaconda中的默认环境称为基础环境，这是在安装Anaconda时创建的。Anaconda附带的所有Python库都安装在基础环境中，除非另有指定，否则后来安装的任何其他库也会放在基础环境中，可以通过自定义环境为特定任务安装特定的库。

# 创建一个Anaconda环境

conda create命令可以创建一个环境，下面创建一个TensorFlow环境，并将其命名为tf_env（可以随意命名），在Terminal、shell或Anaconda命令提示符中运行以下命令：

```
conda create -n tf_env tensorflow anaconda ipython jupyterlab
scikit-learn matplotlib seaborn h5py pydot graphviz
```

可以确定列出的库之间的依赖关系，然后显示出将在新环境中安装的所有库。这个过程有许多依赖项，因此可能需要几分钟时间。看到提示

```
Proceed ([y]/n)?
```

时，就可以按Enter键来创建环境并安装库。