为避免每次用鼠标右键启动命令行窗口，可固定到左侧启动栏。在左侧启动栏选择 Terminal 终端的图标，并右击，选择“Lock to Launcher”，即可将命令行窗口固定到启动栏，下次再启动命令行窗口时，只需要单击启动栏的命令行窗口图标即可，如图 3.22 所示。

![](bbox=[0.14, 0.17, 0.91, 0.53])

图 3.22 固定命令行窗口

下载 Linux 版本的 Python 集成开发环境 Anaconda。首先,访问 Anaconda 首页(https://www.anaconda.com/download/#linux)，下载 Python 3.6 版本的 Anaconda，如图 3.23 所示。

下载完成的 Anaconda 在 Downloads 文件夹下，如图 3.24 所示。

打开命令行窗口(Terminal)，用 ls 命令查看当前目录下的所有文件和文件夹，并使用 cd 命令进入 Downloads 文件夹。用 bash 命令安装下载的 Anaconda，如图 3.25 所示。在开始安装 Anaconda 之前，需要多次按回车键，以浏览 License。

输入 yes 并按回车，再次按回车后开始安装 Anaconda，如图 3.26 所示。

将 Anaconda 的启动路径添加到系统路径 PATH 中，以便 Anaconda 命令行的应用和 Spyder 的启动。如图 3.27 所示，输入 yes 并按回车。

Anaconda 安装完成后，需要首先关闭命令行窗口(Terminal)，再重新打开后，才能使用命令行启动 Spyder。

安装深度学习 Python 库 Keras。重新启动命令行窗口后，直接输入“conda intall keras”并按回车键后，即可进行 Keras 安装。输入 y,继续安装 Keras，如图 3.28 所示。