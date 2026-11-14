```
bash ~/Downloads/Anaconda3-2020.11-Linux-x86_64.sh
```

在安装过程中，需要按回车键（Enter）来阅读并确认同意 Anaconda 的服务条款，该过程中还要手动输入 “yes”，明确表示同意该条款之后，Anaconda 才能正式进入安装过程。

Anaconda 的默认安装路径是 “/home/<username>/anaconda3”。这里的 <username>表示用户名，不同的 Linux 用户名，安装路径稍有不同。

在安装尾声，程序会询问是否通过 conda 来初始化 Anaconda 3，实际上就是将 Anaconda 的环境变量导入到 PATH 中，输入 “yes”。这样一来，以后就可以直接在终端使用诸如 ipython、spyder 等命令了（这些好用的命令，均来自 Anaconda 环境）。

最后，当屏幕输出 “Thank you for installing Anaconda 3!” 字样时，就表明 Anaconda 安装完毕了。macOS 版本的 Anaconda 与 Linux 版本的 Anaconda 的安装流程类似，这里不再赘述。

由于 Windows 发行版用户量较大，因此下面对 Windows 发行版的 Anaconda 安装过程，也给予简单介绍。

# 二、Windows 环境下的 Anaconda 下载与安装

下面我们简单介绍一下在 Windows 系统如何安装 Anaconda。在图 3-8 所示的下载界面中，选择 Windows 版本，下载与自己所用操作系统位数相适配的发行版。

![](bbox=[0.021, 0.622, 0.453, 0.847])

若用户使用的操作系统是 64 位的，则选择下载 “64-Bit Graphical Installer”；否则，下载 “32-Bit Graphical Installer”。假设我们下载的是 64 位的 Anaconda 安装包，待下载完毕后，双击已下载的安装包 “Anaconda3-2020.11-Windows-x86_64.exe”，即可进入安装流程。对 Anaconda 的条款单击 “I Agree” 按钮，进入下一步，如图 3-8 所示。

图 3-8 Anaconda 的同意协议与条款

若用户安装 Anaconda 的目的仅是为自己服务，则选择 “Just Me” 选项。若用户想让 Anaconda 可以为当前计算机的所有用户服务，则

选择 “All Users” 选项，这时候操作系统会请求管理员权限。选择完毕后，单击 “Next>” 按钮，进入正式安装程序，如图 3-9 所示。