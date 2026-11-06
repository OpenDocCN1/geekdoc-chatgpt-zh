
在本章中，我们将使用名为 `OpenCV` 的包。 [您可以在此处了解更多信息](http://opencv.org)。 在继续操作之前，请确保已安装。 以下是在各种操作系统上使用 `Python3` 安装 `OpenCV 3` 的链接：
* [**Windows**](https://solarianprogrammer.com/2016/09/17/install-opencv-3-with-python-3-on-windows)
* [**Ubuntu**](http://www.pyimagesearch.com/2015/07/20/install-opencv-3-0-and-python-3-4-on-ubuntu)
* [**Mac**](http://www.pyimagesearch.com/2015/06/29/install-opencv-3-0-and-python-3-4-on-osx)

现在您已经安装了它，让我们转到下一部分，我们将讨论帧差异分段。

# 帧差分

帧差分是可用于识别视频中运动部分的最简单技术之一。 直观地，在大多数应用中，这是有趣的部分所在。 如果我们有一个跑步者的视频，我们可能想分析跑步者的跑步情况，而不是背景图像。 当我们看电影时，我们主要关注最前沿的人物在说话和做事时。 我们不会倾向于关注背景中无聊的相框。
有时候，您会发现一个一次性的极客，会在隐藏在这种背景下的电影中发现问题，正如我们在《权力的游戏》的最新剧情中几次看到，有人在背景中发现一杯星巴克，但这是例外而不是规则。
当我们观看实时视频流时，从该流捕获的连续帧之间的差异为我们提供了很多信息。 让我们看看如何获取连续帧之间的差异并显示差异。 本节中的代码需要连接的摄像头，因此请确保您的计算机上装有摄像头。
创建一个新的 Python 文件并导入以下包：
```
import cv2 
```
定义一个函数来计算帧差。 首先计算当前帧和下一帧之间的差异：
```
# Compute the frame differences
def frame_diff(prev_frame, cur_frame, next_frame):
    # Difference between the current frame and the next frame
    diff_frames_1 = cv2.absdiff(next_frame, cur_frame) 
```
计算当前帧和上一帧之间的差异：
```
# Difference between the current frame and the previous frame 
    diff_frames_2 = cv2.absdiff(cur_frame, prev_frame) 
```
计算两个差异帧之间的按位与并返回：
```
return cv2.bitwise_and(diff_frames_1, diff_frames_2) 
```
定义一个函数以从网络摄像头抓取当前帧。 首先从视频捕获对象中读取它：
```
# Define a function to get the current frame from the webcam 
def get_frame(cap, scaling_factor):
    # Read the current frame from the video capture object
    _, frame = cap.read() 
```
根据缩放比例调整框架大小并返回：
```
# Resize the image
    frame = cv2.resize(frame, None, fx=scaling_factor, 
            fy=scaling_factor, interpolation=cv2.INTER_AREA) 
```
将图像转换为灰度并返回：
```
# Convert to grayscale
    gray = cv2.cvtColor(frame, cv2.COLOR_RGB2GRAY) 
    return gray 
```
定义 `main` 函数并初始化视频捕获对象：
```
if __name__=='__main__':
    # Define the video capture object 
    cap = cv2.VideoCapture(0) 
```
定义比例因子以调整图像大小：
```
# Define the scaling factor for the images 
    scaling_factor = 0.5 
```
抓取当前的帧，下一帧以及之后的帧：
```
# Grab the current frame
    prev_frame = get_frame(cap, scaling_factor)
    # Grab the next frame
    cur_frame = get_frame(cap, scaling_factor)
    # Grab the frame after that
    next_frame = get_frame(cap, scaling_factor) 
```
无限期迭代，直到用户按下 `Esc` 键。 首先计算帧差异：
```
# Keep reading the frames from the webcam 
    # until the user hits the 'Esc' key
    while True:
        # Display the frame difference
        cv2.imshow('Object Movement', frame_diff(prev_frame, 
                cur_frame, next_frame)) 
```
更新 `frame` 变量：
```
# Update the variables
        prev_frame = cur_frame
        cur_frame = next_frame 
```
从网络摄像头抓取下一帧：
```
# Grab the next frame
        next_frame = get_frame(cap, scaling_factor) 
```
检查用户是否按下了 `Esc` 键。 如果是这样，请退出循环：
```
# Check if the user hit the 'Esc' key 
        key = cv2.waitKey(10)
        if key == 27:
            break 
```
退出循环后，请确保所有窗口均已正确关闭：
```
# Close all the windows
    cv2.destroyAllWindows() 
```
完整代码在提供给您的文件 `frame_diff.py` 中给出。 如果运行代码，您将看到一个显示实时输出的输出窗口。 如果四处走动，您会在这里看到自己的轮廓在这里展示：
![](Images/ec9701c3917321e5065f692d90218101.png)
图 1：轮廓图像
前面的屏幕截图中的白线表示轮廓。 我们取得了什么成就？ 为什么这很重要？ 根据应用的不同，我们可能不需要原始图像提供的所有信息。 原始图像具有更多细节，更多对比度和更多颜色。 让我们以自动驾驶汽车为例。 我们可能不在乎我们前面的车是红色还是绿色。 我们更担心要知道汽车是否正在驶向我们并即将撞向我们。 通过过滤所有额外的不相关信息，它允许系统中的其他算法更有效地处理图像中的相关信息，因此可以更快地对任何潜在的危险做出反应。
这种过滤可能有用的另一个实例是，加载视频的空间有限或成本高昂，我们需要压缩图像并提高空间利用率。 我们将由读者提出其他可能有用的方案，但希望我们能给您足够的灵感，激发您的想象力和创造力。
通过帧差分获得的信息很有用，但是我们将无法使用它构建健壮的跟踪器。 它对噪声敏感，并且不能真正完全跟踪物体。 要构建健壮的对象跟踪器，我们需要知道可以使用对象的哪些特征来精确跟踪它。 这是色彩空间变得重要的地方，我们将在扩展部分中讨论。

# 使用颜色空间跟踪对象

可以使用各种色彩空间来表示图像。 `RGB` 颜色空间可能是最流行的颜色空间，但不适用于对象跟踪之类的应用。 因此，我们将改用 `HSV` 颜色空间。 这是一种直观的色彩空间模型，更接近于人类对色彩的感知方式。 [您可以在此处了解更多信息](https://en.wikipedia.org/wiki/HSL_and_HSV)。
我们可以将捕获的帧从 `RGB` 转换为 `HSV` 颜色空间，然后使用颜色阈值跟踪任何给定的对象。 我们应该注意，我们需要知道对象的颜色分布，以便为阈值选择合适的范围。
创建一个新的 Python 文件并导入以下包：
```
import cv2
import numpy as np 
```
定义一个函数以从网络摄像头抓取当前帧。 首先从视频捕获对象中读取它：
```
# Define a function to get the current frame from the webcam
def get_frame(cap, scaling_factor):
    # Read the current frame from the video capture object
    _, frame = cap.read() 
```
根据缩放比例调整框架大小并返回：
```
# Resize the image
    frame = cv2.resize(frame, None, fx=scaling_factor, 
            fy=scaling_factor, interpolation=cv2.INTER_AREA)
    return frame 
```
定义 `main` 函数。 首先初始化视频捕获对象：
```
if __name__=='__main__':
    # Define the video capture object
    cap = cv2.VideoCapture(0) 
```
定义用于调整捕获帧大小的比例因子：
```
# Define the scaling factor for the images
    scaling_factor = 0.5 
```
无限期地进行迭代，直到用户按下 `Esc` 键。 抓取当前帧开始：
```
# Keep reading the frames from the webcam 
    # until the user hits the 'Esc' key
    while True:
        # Grab the current frame
        frame = get_frame(cap, scaling_factor) 
```
使用 OpenCV 中可用的内置函数将图像转换为 HSV 颜色空间：
```
# Convert the image to HSV colorspace
        hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV) 
```
为人类皮肤的颜色定义近似 HSV 颜色范围：
```
# Define range of skin color in HSV
        lower = np.array([0, 70, 60])
        upper = np.array([50, 150, 255]) 
```
阈值 HSV 图像以创建掩码：
```
# Threshold the HSV image to get only skin color
        mask = cv2.inRange(hsv, lower, upper) 
```
计算遮罩和原始图像之间的按位与：
```
# Bitwise-AND between the mask and original image 
        img_bitwise_and = cv2.bitwise_and(frame, frame, mask=mask) 
```
运行中值模糊以使图像平滑：
```
# Run median blurring
        img_median_blurred = cv2.medianBlur(img_bitwise_and, 5) 
```
显示输入和输出帧：
```
# Display the input and output
        cv2.imshow('Input', frame)
        cv2.imshow('Output', img_median_blurred) 
```
检查用户是否按下了 `Esc` 键。 如果是这样，则退出循环：
```
# Check if the user hit the 'Esc' key
        c = cv2.waitKey(5)
        if c == 27:
            break 
```
退出循环后，请确保所有窗口均已正确关闭：
```
# Close all the windows
    cv2.destroyAllWindows() 
```
提供给您的文件 `colorspaces.py` 中提供了完整的代码。 如果运行代码，您将获得两个屏幕截图。 标题为 **输入** 的窗口是捕获的帧：
![](Images/e403cd66253280e04b4635011d99639d.png)
图 2：捕获的帧
标题为 **输出** 的第二个窗口显示皮肤遮罩：
![](Images/7bd2114b5b115c45873f29440adf97a6.png)
图 3：输出框架
如您在中看到的输出帧，我们现在只在图像中看到一种颜色，它对应于任何皮肤。 其他一切都是黑色的。 与上一节中看到的类似，我们对图像进行了过滤，以仅包含我们感兴趣的信息。在这种情况下，过滤是不同的，但结果是事实是我们现在仅具有进一步处理图像所需的信息。 我想到了一些应用：
* 检测异常皮肤状况或变色。
* 仅在看到人的肤色时才会打开的安全系统。 这可用于人类可能藏在容器中的港口。
您还能想到其他一些应用吗？ 在下一节中，我们将学习另一种称为背景减法的图像转换技术。

# 使用背景减法的对象跟踪

背景减法是一种技术，它对给定视频中的背景进行建模，然后使用该模型来检测运动对象。 该技术在视频压缩和视频监视中大量使用。 在必须检测静态场景中的移动物体的情况下，它的表现很好。 该算法的基本原理是检测背景，为其建立模型，然后从当前帧中减去背景以获得前景。 该前景对应于移动的对象。
这里的主要步骤之一是建立背景的模型。 它与帧差分不同，因为我们不差分连续帧。 我们正在对背景进行建模并实时更新，这使其成为一种自适应算法，可以适应不断变化的基线。 因此，它的表现要比帧差分好得多。
创建一个新的 Python 文件并导入以下包：
```
import cv2
import numpy as np 
```
定义一个函数以获取当前帧：
```
# Define a function to get the current frame from the webcam
def get_frame(cap, scaling_factor):
    # Read the current frame from the video capture object
    _, frame = cap.read() 
```
调整框架大小并返回：
```
# Resize the image
    frame = cv2.resize(frame, None, fx=scaling_factor, 
            fy=scaling_factor, interpolation=cv2.INTER_AREA)
    return frame 
```
定义 `main` 函数并初始化视频捕获对象：
```
if __name__=='__main__':
    # Define the video capture object
    cap = cv2.VideoCapture(0) 
```
定义背景减法器对象：
```
# Define the background subtractor object
    bg_subtractor = cv2.createBackgroundSubtractorMOG2() 
```
定义历史记录和学习率。 关于 `history` 的全部内容，以下注释不言自明：
```
# Define the number of previous frames to use to learn.
    # This factor controls the learning rate of the algorithm.
    # The learning rate refers to the rate at which your model 
    # will learn about the background. Higher value for
    # 'history' indicates a slower learning rate. You can
    # play with this parameter to see how it affects the output.
    history = 100
    # Define the learning rate
    learning_rate = 1.0/history 
```
无限期迭代，直到用户按下 `Esc` 键。 首先抓取当前帧：
```
# Keep reading the frames from the webcam
    # until the user hits the 'Esc' key
    while True:
        # Grab the current frame 
        frame = get_frame(cap, 0.5) 
```
使用先前定义的背景减法器对象计算 `mask`：
