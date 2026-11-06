[PRE0]

运行后即可看到图 6-8 的呈现效果。

# 图6-8 水果增添效果展示

[PRE1]

[PRE2]

# 6.8 雷电对战

本小节演示如何借助 ChatGPT 快速生成基于 pygame 的射击小游戏。游戏包含两类载具：玩家操控的 spaceship 与不断来袭的 UFO。spaceship 只有一架，初始停在窗口底部，可用上下左右方向键调整位置，按空格键向上发射导弹。UFO 数量无限，从屏幕顶部随机位置持续向下移动。若导弹命中 UFO，两者同时消失并触发音效，同时左上角计分器累计，每摧毁一架 UFO 分数加 1。

下面是可以直接交给 ChatGPT 的需求说明：

请用 pygame 开发一款射击游戏，窗口背景为黑色。包含两类飞船，分别是 images/spaceship.png 和 images/ufo.png。spaceship 仅有一艘，位于窗口底部，可通过上下左右键移动，并按空格键向上发射导弹 images/missile.png。UFO 无限生成，不断从屏幕上方随机位置向下方移动；当导弹碰到 UFO 时，两者均消失并播放音效 explose.mp3。左上角显示得分，每击毁一艘 UFO 分数加 1，例如 Score:20。若 spaceship 与 UFO 碰撞，游戏结束，屏幕显示 Game Over，且键盘无法再控制 spaceship。请输出完整代码并附带详细中文注释。

ChatGPT 会据此自动生成完整实现，文件存放于 src/game/shooter.py

[PRE3]

# 6.8 雷电代码示例

os.chdir(os.path.dirname(__file__))

# 设置游戏窗口的大小

SCREEN_WIDTH, SCREEN_HEIGHT = 800, 600

screen = pygame.display.set_mode((SCREEN_WIDTH, SCREEN_HEIGHT))

# 加载游戏图像

spaceship = pygame.image.load('images/spaceship.png')

ufo = pygame.image.load('images/ufo.png')

missiles = [pygame.image.load('images/missile.png') ]

# 设置游戏对象

spaceship_rect = spaceship.get_rect(midbottom=(SCREEN_WIDTH//2, SCREEN_HEIGHT))

ufo_rects = []

missile_rects = []

score = 0

font = pygame.font.Font(None, 36) # 设置得分的字体

def create_ufo():

    ``生成一个新的UFO``

    new_ufo_rect = ufo.get_rect(midtop=(random.randint(0, SCREEN_WIDTH), 0))

    return new_ufo_rect

# 设置速度，用每秒下移的像素表示

UFO_SPEED_PX_PER_SECOND = SCREEN_HEIGHT / 10

# 每帧下移的像素，这里假设帧率是每秒60帧

vfo_speed_per_frame = UFO_SPEED_PX_PER_SECOND / 60

def move_ufos(rects):

    ``移动所有的UFO！``

    for rect in rects:

        rect.move_ip(0, vfo_speed_per_frame)

    return rects

def remove_ufo(rects):

    ``删除出界的UFO!``

    for rect in rects:

        if rect.bottom > SCREEN_HEIGHT:

            rects.remove(rect)

def move_missiles(rects):

    ``移动所有的导弹``

    for rect in rects:

        rect.move_ip(0, -5)

# 第6章 游戏自动化实践

[PRE4]

[PRE5]

[PRE6]

[PRE7]

[PRE8]

[PRE9]

[PRE10]

[PRE11]

[PRE12]

[PRE13]

[PRE14]

[PRE15]

[PRE16]

[PRE17]

[PRE18]

[PRE19]

[PRE20]

[PRE21]

[PRE22]

[PRE23]

[PRE24]

[PRE25]

[PRE26]

[PRE27]

[PRE28]

[PRE29]

[PRE30]

[PRE31]

# 第6章 游戏自动化实战

[PRE32]

启动后即可看到图 6-10 的雷电画面，按空格键可以让 spaceship 向上发射导弹。

该程序逻辑偏复杂，ChatGPT 一次性生成的版本往往存在小瑕疵，比如 UFO 下落过快、spaceship 移动迟缓或缺少音效反馈等，需要通过多次对话逐步完善，示例如下。

第一次沟通示例。

UFO 下降速度太快，请放慢，让它从顶部到底部至少花费 10 秒

第二次沟通示例。

长按上下左右键时 spaceship 应该持续移动，但现在太慢，请提高移动速度

第三次沟通示例。

导弹命中 UFO 后需要播放音效，请补充这段逻辑并给出修改内容

[PRE33]

图 6-10 雷电效果

# 6.9 本章小结

本章核心是用 ChatGPT 与 GitHub Copilot 驱动 pygame 自动生成游戏代码，实际上两者可以适配多种主流游戏引擎，这里以 pygame 为例说明。面对大型项目时，一次性生成往往不可行，需要先拆分模块、定义好接口，再分块让 AI 编写，最后汇总成完整作品。总的来看，这些 AI 工具大幅降低了游戏开发门槛，即便对游戏技术不熟也能快速做出原型。

# 第7章 办公自动化实战

本章将利用 ChatGPT 与 GitHub Copilot 自动编写办公自动化相关代码。办公自动化涵盖面广，本章聚焦 Office 相关的自动化任务。Office 是当前最流行的办公套件之一，因其内置 VBA，程序即可自由控制 Office。Python、Java、JavaScript 等语言也提供了大量读写 Office 文档的模块与库。本章以 VBA、Python、Java 和 JavaScript 四种语言为基础，通过 AI 自动生成读写 Office 文档及相关操作的代码。即使不熟悉这些语言，也能借助 AI 完成复杂任务。

# 7.1 Excel 读写实战

本节展示如何借助 ChatGPT 与 GitHub Copilot 自动编写用于读写 Excel 文档的代码，涉及 VBA、Python、Java、JavaScript 等多种语言。AI 工具并不限于这几种语言，本节仅作示例。有了 AI 辅助，即便不会目标语言也能轻松读写 Excel 文档，只要会输入文字即可编程。若不想打字，还可使用语音输入。

# 7.1.1 用 VBA 新建保存 Excel 文档并写入表格

VBA 内嵌于 Office 软件中，是 Office 脱颖而出的关键。VBA 语法类似 Visual Basic 6，属于传统 Basic 的变种。对现在的开发者而言，VBA 看似陈旧，但它并非通用语言，而是一种领域特定语言，仅用于操作 Office 文档。

[PRE34]

Sub CreateAndSaveWorkbook()

    Dim wb As Workbook

    ' 创建新的工作簿

    Set wb = Application.Workbooks.Add

    ' 在此处，你可以对新的工作簿进行操作，例如添加数据，创建表格等

    ' 保存工作簿，替换文件名和路径为你需要的

    wb.SaveAs "C:\path\to\your\directory\data.xlsx"

    ' 关闭新创建的工作簿

    wb.Close

End Sub

[PRE35]

pip3 install openpyxl