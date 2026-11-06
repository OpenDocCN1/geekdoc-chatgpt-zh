
```
 # Compute the mask
        mask = bg_subtractor.apply(frame, learningRate=learning_rate) 
```

将`mask`从灰度转换为 RGB：

```
 # Convert grayscale image to RGB color image
        mask = cv2.cvtColor(mask, cv2.COLOR_GRAY2BGR) 
```

显示输入和输出图像：

```
 # Display the images
        cv2.imshow('Input', frame)
        cv2.imshow('Output', mask & frame) 
```

检查用户是否按下了`Esc`键。 如果是这样，请退出循环：

```
 # Check if the user hit the 'Esc' key
        c = cv2.waitKey(10)
        if c == 27:
            break 
```

退出循环后，请确保释放视频捕获对象并正确关闭所有窗口：

```
 # Release the video capture object 
    cap.release()
    # Close all the windows 
    cv2.destroyAllWindows() 
```

完整代码在提供给您的文件`background_subtraction.py`中给出。 如果运行代码，您将看到一个显示实时输出的窗口。 如果四处走动，您将部分看到自己，如下所示：

![](Images/6dce85f3b9d33c80619ab781aad57bda.png)

图 4：背景减影

一旦停止移动，它就会开始褪色，因为您现在是背景的一部分。 该算法会将您视为背景的一部分，并开始相应地更新模型：

![](Images/7c70773fa9a30f0451bb40375d5cfb3a.png)

图 5：背景减影

当您保持静止时，它将继续消失，如下所示：

![](Images/9e1ac72deb2efe3d031676e7dd8f07ad.png)

图 6：背景减影

淡入淡出的过程表示当前场景是，而背景模型是。

您已经可以想象到，仅在移动时才生成图像将节省大量存储空间。 一个简单的例子是使用安全摄像机。 看着一个小时或几个小时的镜头对准空旷的停车场，可能比看着油漆枯燥更无聊，但是如果安全系统足够智能，可以在车架发生运动时进行记录，我们将能够辨别出“有趣的东西”。 视频中的“”部分。

基于颜色空间的跟踪允许我们跟踪有色对象，但是我们必须首先定义颜色。 这似乎是限制性的！ 让我们看看如何在实时视频中选择一个对象，然后使用一个可以跟踪它的跟踪器。 这是，在其中 `CAMShift` 算法（代表**连续自适应均值漂移**）变得很重要。 这基本上是 `MeanShift` 算法的自适应版本。 我们将在下一节讨论 `CAMShift`。

# 使用 `CAMShift` 算法构建交互式对象跟踪器

为了理解 `CAMShift`，首先让我们了解均值漂移的工作原理。 考虑给定帧中的感兴趣区域。 我们选择该区域是因为它包含感兴趣的对象。 我们要跟踪该对象，因此在其周围绘制了一个粗糙的边界，这就是感兴趣的*区域*所指的区域。 我们希望我们的对象跟踪器在视频中四处移动时跟踪该对象。

为此，我们基于该区域的颜色直方图选择一组点，然后计算质心。 如果此质心的位置在该区域的几何中心，则我们知道该对象没有移动。 但是，如果质心的位置不在此区域的几何中心，则我们知道对象已移动。 这意味着我们还需要移动封闭边界。 质心的运动直接指示物体的运动方向。 我们需要移动边界框，以使新质心成为此边界框的几何中心。 我们对每一帧都保持这种状态，并实时跟踪对象。 因此，此算法称为均值平移，因为均值（即质心）一直在移动，我们使用此跟踪对象。

让我们看看这与 `CAMShift` 有何关系。 均值平移的问题之一是不允许对象的大小随时间变化。 绘制边界框后，无论物体离相机有多近，它都将保持不变。 因此，我们需要使用 `CAMShift`，因为它可以使边界框的大小适应于对象的大小。 如果您想进一步探索，[可以查看以下链接](http://docs.opencv.org/3.1.0/db/df8/tutorial_py_meanshift.html)。

让我们看看如何构建跟踪器。

创建一个新的 Python 文件并导入以下包：

```
import cv2
import numpy as np 
```

定义一个类来处理与对象跟踪有关的所有功能：

```
# Define a class to handle object tracking related functionality 
class ObjectTracker(object):
    def __init__(self, scaling_factor=0.5):
        # Initialize the video capture object
        self.cap = cv2.VideoCapture(0) 
```

捕获当前帧：

```
 # Capture the frame from the webcam
        _, self.frame = self.cap.read() 
```

设置比例因子：

```
 # Scaling factor for the captured frame 
        self.scaling_factor = scaling_factor 
```

调整框架大小：

```
 # Resize the frame
        self.frame = cv2.resize(self.frame, None, 
                fx=self.scaling_factor, fy=self.scaling_factor, 
                interpolation=cv2.INTER_AREA) 
```

创建一个窗口以显示输出：

```
 # Create a window to display the frame
        cv2.namedWindow('Object Tracker') 
```

设置鼠标回调函数以从鼠标获取输入：

```
 # Set the mouse callback function to track the mouse 
        cv2.setMouseCallback('Object Tracker', self.mouse_event) 
```

初始化变量以跟踪矩形选择：

```
 # Initialize variable related to rectangular region selection
        self.selection = None
        # Initialize variable related to starting position
        self.drag_start = None
        # Initialize variable related to the state of tracking
        self.tracking_state = 0 
```

定义一个函数来跟踪鼠标事件：

```
 # Define a method to track the mouse events
    def mouse_event(self, event, x, y, flags, param):
        # Convert x and y coordinates into 16-bit numpy integers
        x, y = np.int16([x, y]) 
```

当鼠标左键按下时，表明用户已经开始绘制矩形：

```
 # Check if a mouse button down event has occurred 
        if event == cv2.EVENT_LBUTTONDOWN:
            self.drag_start = (x, y)
            self.tracking_state = 0 
```

如果用户当前正在拖动鼠标以设置矩形选区的大小，请跟踪宽度和高度：

```
 # Check if the user has started selecting the region
        if self.drag_start:
            if flags & cv2.EVENT_FLAG_LBUTTON:
                # Extract the dimensions of the frame
                h, w = self.frame.shape[:2] 
```

设置矩形的`X`和`Y`坐标的：

```
 # Get the initial position
                xi, yi = self.drag_start 
```

获取坐标的最大值和最小值，以使其与拖动鼠标以绘制矩形的方向无关：

```
 # Get the max and min values
                x0, y0 = np.maximum(0, np.minimum([xi, yi], [x, y]))
                x1, y1 = np.minimum([w, h], np.maximum([xi, yi], [x, y])) 
```

重置选择变量：

```
 # Reset the selection variable
                self.selection = None 
```

完成矩形选择：

```
 # Finalize the rectangular selection
                if x1-x0 > 0 and y1-y0 > 0:
                    self.selection = (x0, y0, x1, y1) 
```

如果选择完成，则设置标志，指​​示我们应该开始跟踪矩形区域内的对象：

```
 else:
                # If the selection is done, start tracking
                self.drag_start = None
                if self.selection is not None:
                    self.tracking_state = 1 
```

定义跟踪对象的方法：

```
 # Method to start tracking the object
    def start_tracking(self):
        # Iterate until the user presses the Esc key
        while True:
            # Capture the frame from webcam
            _, self.frame = self.cap.read() 
```

调整框架大小：

```
 # Resize the input frame
            self.frame = cv2.resize(self.frame, None, 
                    fx=self.scaling_factor, fy=self.scaling_factor, 
                    interpolation=cv2.INTER_AREA) 
```

创建框架的副本。 我们稍后将需要它：

```
 # Create a copy of the frame
            vis = self.frame.copy() 
```

将帧的颜色空间从 RGB 转换为 HSV：

```
 # Convert the frame to HSV colorspace
            hsv = cv2.cvtColor(self.frame, cv2.COLOR_BGR2HSV) 
```

根据预定义的阈值创建掩码：

```
 # Create the mask based on predefined thresholds 
            mask = cv2.inRange(hsv, np.array((0., 60., 32.)), 
            np.array((180., 255., 255.))) 
```

检查用户是否选择了区域：

```
 # Check if the user has selected the region 
            if self.selection:
                # Extract the coordinates of the selected rectangle 
                x0, y0, x1, y1 = self.selection
                # Extract the tracking window 
                self.track_window = (x0, y0, x1-x0, y1-y0) 
```

从 HSV 图像以及遮罩中提取感兴趣的区域。 根据以下这些计算感兴趣区域的直方图：

```
 # Extract the regions of interest
                hsv_roi = hsv[y0:y1, x0:x1]
                mask_roi = mask[y0:y1, x0:x1]
                # Compute the histogram of the region of
                # interest in the HSV image using the mask
                hist = cv2.calcHist( [hsv_roi], [0], mask_roi, 
                        [16], [0, 180] ) 
```

标准化直方图：

```
 # Normalize and reshape the histogram 
                cv2.normalize(hist, hist, 0, 255, cv2.NORM_MINMAX)
                self.hist = hist.reshape(-1) 
```

从原始帧中提取感兴趣的区域：

```
 # Extract the region of interest from the frame
                vis_roi = vis[y0:y1, x0:x1] 
```

计算感兴趣区域的按位非。 这仅用于显示目的：

```
 # Compute the image negative (for display only)
                cv2.bitwise_not(vis_roi, vis_roi)
                vis[mask == 0] = 0 
```

检查系统是否处于跟踪模式：

```
 # Check if the system in the "tracking" mode
            if self.tracking_state == 1:
                # Reset the selection variable
                self.selection = None 
```

计算直方图的反向投影：

```
 # Compute the histogram back projection
                hsv_backproj = cv2.calcBackProject([hsv], [0], 
                        self.hist, [0, 180], 1) 
```

计算直方图和掩码之间的按位与：

```
 # Compute bitwise AND between histogram
                # backprojection and the mask
                hsv_backproj &= mask 
```

定义跟踪器的终止条件：

```
 # Define termination criteria for the tracker
                term_crit = (cv2.TERM_CRITERIA_EPS | cv2.TERM_CRITERIA_COUNT, 10, 1) 
```

将 `CAMShift` 算法应用于反投影直方图：

```
 # Apply CAMShift on 'hsv_backproj'
                track_box, self.track_window = cv2.CamShift(hsv_backproj, self.track_window, term_crit) 
```

在对象周围绘制一个椭圆并显示它：

```
 # Draw an ellipse around the object
                cv2.ellipse(vis, track_box, (0, 255, 0), 2)
            # Show the output live video
            cv2.imshow('Object Tracker', vis) 
```

如果用户按下`Esc`，则退出循环：

```
 # Stop if the user hits the 'Esc' key
            c = cv2.waitKey(5)
            if c == 27:
                break 
```
