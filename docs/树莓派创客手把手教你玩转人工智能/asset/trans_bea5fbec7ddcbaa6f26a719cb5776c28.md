

# 5.3.3 White Balance

Certain lighting sources (such as incandescent bulbs) emit a slightly reddish light, while others (such as fluorescent lights) have a bluish tint. These lighting conditions affect color signatures. For example, if you teach Pixy2 an object under incandescent light and then move it to a fluorescent environment, the color signature may fail. At this point, you can only re-teach all color signatures or adjust the white balance.

When Pixy2 is first powered on, it spends about 5 seconds determining the correct white balance to use, then disables auto white balance. If you want to readjust the white balance, press and hold the button until the LED turns white, then release. This process is quick, so be prepared. Pixy2 is now in auto white balance mode; you can place a sheet of white paper in front of Pixy2 to let it adjust the white balance. The adjustment takes only 2 or 3 seconds, after which you can press and release the button (like a mouse click). The LED flashing indicates success, and Pixy2 enters the "re-balance" mode.

# 5.3.4 Teaching via PixyMon

You can also teach Pixy2 objects via PixyMon. Teaching via PixyMon is useful when the object to be taught is small, or when you want better control over which pixels on the screen are used for teaching. Connect Pixy2 to your computer via a USB data cable, then run PixyMon.

Next, place the object to be taught directly in front of Pixy2, and select `Action → Set signature 1` from the dropdown menu bar, as shown in Figure 5-37.

![](EPUB/images/image_242.jpg)

**Figure 5-37**

Drag the mouse on the screen to select the area of the object; Pixy2 will use the selected area to learn, as shown in Figure 5-38.

![](EPUB/images/image_243.jpg)

**Figure 5-38**

Once the area is selected, Pixy2 has "learned" the object and will automatically start tracking it, as shown in Figure 5-39.

![](EPUB/images/image_244.jpg)

**Figure 5-39**

# 5.3.5 Signature Tuning

Sometimes the color signatures you teach Pixy2 need "tuning," meaning there might be false positives (Pixy2 incorrectly identifies other objects as the one you taught) or false negatives (Pixy2 fails to detect the taught object, or only detects it intermittently). In this case, you can open the Configure dialog (click the gear icon or select `File → Configure`) to adjust, then select the `Tuning` tab under `Pixy2 Parameters`, as shown in Figure 5-40.

![](EPUB/images/image_245.jpg)

**Figure 5-40**

If you need to adjust Signature 1, you can use the `Signature 1 range` slider to adjust its inclusion range. If you want to make the range of Signature 1 smaller (e.g., when false positives occur as in Figure 5-41), slide the slider to the left.

![](EPUB/images/image_246.jpg)

**Figure 5-41**

If you want to make the range of Signature 1 larger (when false negatives occur), or if the object is only detected intermittently, or the detection range is discontinuous, slide the slider to the right, as shown in Figure 5-42.

![](EPUB/images/image_247.jpg)

**Figure 5-42**

Slide the slider to select a value until the detection range perfectly matches the learned object, as shown in Figure 5-43.

![](EPUB/images/image_248.jpg)

**Figure 5-43**

You can use this method to tune all 7 color signatures to maximize detection accuracy. Be sure to click the `Apply` or `OK` button to save the adjusted range; clicking `Cancel` or closing the dialog will not save changes.

For more tips on improving color signature accuracy, please refer to the documentation here.

# 5.4 Line Tracking Feature Details

This program executes the line tracking algorithm, suitable for line-following robots, serving as the core algorithm for line tracking.

## 5.4.1 PixyMon "View" and "Tuning" Tabs

The "View" tab contains different types of views for the line tracking program in PixyMon. To open this menu, you must first click to run the line tracking program in the "Program" menu, then click the "View" menu in the menu bar, as shown in Figure 5-44.

![](EPUB/images/image_249.jpg)

**Figure 5-44**

- **Primary features, no background**: This view displays a black background, with vectors, intersections, and barcode features displayed as separate layers. This view shows the minimum content detected by Pixy2; the advantage is that it does not significantly reduce Pixy2's image processing speed, allowing Pixy2 to process frames at the maximum rate of 60 frames per second. In this view, PixyMon also typically displays images at 60 frames per second.
- **Primary features**: In addition to displaying vectors, intersections, and barcode features as separate layers, the background displayed is grayscale. This view usually reduces Pixy2's processing speed because the background layer takes time to transmit via USB.
- **All features**: This view is the same as "Primary features" above but adds all detected (filtered) lines and intersections. This view essentially presents all data returned by the `getAllFeatures()` function in the line tracking API. Because both the background layer and extra features take time to transmit via USB, this view further reduces Pixy2's processing speed.

The "Tuning" tab contains all the most effective parameters for "tuning" the accuracy of the line tracking algorithm. To open this tab, you must first click to run the line tracking program in the "Program" menu, then open the "Configure" dialog (click the gear icon or select "File" → "Configure"), then select the "Tuning" tab under "Pixy Parameters" (this should be the first window you see by default when opening the "Configure" dialog), as shown in Figure 5-45.

- **Edge threshold**: Adjusting this parameter configures the line detection algorithm. If the edge threshold is decreased, more potential lines will be detected; if increased, fewer potential lines will be detected. If Pixy2 is not detecting the lines you want, you may need to adjust this parameter.
- **Maximum line width**: This parameter adjusts the maximum width (in pixels) of detected lines; wider lines will not be detected.
- **Minimum line width**: This parameter adjusts the minimum width (in pixels) of detected lines; narrower lines will not be detected.
- **White line**: Enabling this parameter changes Pixy2's line detection algorithm from detecting dark lines on a light background to detecting bright lines on a dark background.
- **Camera brightness**: This parameter appears in the "Tuning" tab regardless of whether a program is running. It sets the exposure for Pixy, controlling image brightness. The "Camera brightness" setting is important because correct exposure settings yield the best detection accuracy.

![](EPUB/images/image_250.jpg)

**Figure 5-45**

## 5.4.2 PixyMon "Expert" and "Barcode Labels" Tabs

The "Expert" tab contains more advanced parameters for tuning the line tracking algorithm. To open this tab, you must first click to run the line tracking program in the "Program" menu, then open the "Configure" dialog (click the gear icon or select "File" → "Configure"), then select the "Expert" tab under "Pixy Parameters", as shown in Figure 5-46.

> **Warning**
>
> All parameters in this tab are advanced parameters, some used for testing or debugging; certain values may cause strange behavior in the line tracking algorithm. Pixy2 will not suffer a "smoke" failure, but it is best to treat them with caution!

![](EPUB/images/image_251.jpg)

**Figure 5-46**

- **Edge distance**: This parameter adjusts the distance (in pixels) between edge detectors. Increasing the edge distance helps detect less distinct (blurrier) edges but affects barcode detection and decoding, meaning the minimum barcode size will increase.
- **Line extraction distance**: This parameter is for testing or debugging and is currently invalid.
- **Maximum line compare**: This parameter can be viewed as the maximum line speed parameter for tracking lines between two frames. For example, if a line moves from one side of the frame to the other in one frame period (very high line speed), it likely isn't the same line; thus, this parameter acts as an integrity check for the line tracking algorithm. Decreasing this value makes the algorithm more restrictive on line speed, while increasing it makes the algorithm less restrictive.
- **Maximum merge distance**: This parameter determines the distance the line tracking algorithm searches for nearby lines to form intersections. If the lines to be tracked are wide, increase this parameter until intersections formed with these wider lines can be accurately detected.
- **Minimum line length**: Allows the algorithm to discard lines shorter than this parameter.
- **Line filtering**: Increasing this parameter performs more filtering on results and can reduce false positives in line detection, but may introduce extra latency when presenting newly detected lines.
- **Intersection filtering**: Increasing this parameter performs more filtering on results and can reduce false positives in intersection detection, but may introduce extra latency when presenting newly detected intersections.
- **Barcode filtering**: Increasing this parameter performs more filtering on results and can reduce false positives in barcode detection, but may introduce extra latency when presenting newly detected barcodes.
- **Default turn angle**: This parameter sets the path the algorithm chooses when encountering an intersection; the algorithm will select the path closest to the angle specified by this parameter. The turn angle is in degrees; for example, `0` means straight ahead, left turn is `90`, right turn is `-90`, and can be set to any valid angle value (valid angles range between `-180` and `180`). This parameter is usually set to `0`, meaning go straight at intersections unless otherwise instructed; the algorithm can be instructed via the `setNextTurn()` function in the API.
- **Delayed turn**: This parameter stops the algorithm from automatically choosing a path at intersections. The host program must instruct the path via `setNextTurn()` in the API before the intersection leaves the frame. This is an additional advanced option. You might need this when you don't know which way to turn until you see the next intersection (knowing the number of branches or angles, etc.). Setting this parameter is also useful and interesting because PixyMon will label intersections as "3-way intersection", "4-way intersection", etc., since it doesn't know which way to turn.
- **Manual vector select**: When the algorithm initializes, it needs to determine which line is the "vector" (the line you want to track). The algorithm automatically selects the vector based on features it deems representative, such as lines intersecting the bottom of the frame or near-vertical lines. You can bypass this selection process by enabling this parameter; the algorithm then sets the vector via the `setVector()` function in the API.
- **Default program**: This parameter appears in the "Expert" tab regardless of the currently running program. It sets the program to run on power-up boot; available programs can be seen in the dropdown menu.
- **Program select on power-up**: This parameter appears in the "Expert" tab regardless of the currently running program. Selecting this parameter allows choosing the program to run at power-up via the button.
- **Debug**: This parameter appears in the "Expert" tab regardless of the currently running program. It sets the debug level in Pixy2; setting this value to `0` means no debug information is displayed.

The "Barcode Labels" tab allows you to label barcodes detected by Pixy2. To open this tab, you must first click to run the line tracking program in the "Program" menu, then open the "Configure" dialog (click the gear icon or select "File" → "Configure"), then select the "Barcode Labels" tab under "Pixy parameters", as shown in Figure 5-47.

> **Note**
>
> Barcode labels are stored on Pixy2. Therefore, after you set barcode labels, if you plug Pixy2 into another computer running PixyMon, the labels will appear there.

![](EPUB/images/image_252.jpg)

**Figure 5-47**

To label a barcode, simply fill in the corresponding content in the label's text box, then click "Apply" or "OK", as shown in Figure 5-48.

![](EPUB/images/image_253.jpg)

**Figure 5-48**

Then, when a matching barcode is detected, it will be displayed on PixyMon, as shown in Figure 5-49.

![](EPUB/images/image_254.jpg)

**Figure 5-49**

## 5.4.3 Quick Start with Line Tracking Algorithm

This guide helps you quickly get started with the line tracking algorithm on Pixy2. PixyMon displays what Pixy2 detects in image form; it is a great tool to help you familiarize yourself with the line tracking algorithm's features and a good way to perform tuning. First, select the line tracking algorithm in the "Program" menu, as shown in Figure 5-50.

![](EPUB/images/image_255.jpg)

**Figure 5-50**

Draw a line on a piece of paper and place it in front of Pixy2. You can use a pencil, but a black marker works better. You should then see something like Figure 5-51.

![](EPUB/images/image_256.jpg)

**Figure 5-51**

What Pixy2 does is find the best candidate line and designate it as the vector to be tracked—that is, the line Pixy2 (or the robot) is currently tracking. You can move the paper to see if Pixy2 can track the line quickly. PixyMon usually reduces the tracking algorithm's frame rate to 20-30 frames per second because sending raw frame data over USB takes time, but if you change the view to "No background" (see Figure 5-52), you can feel the frame rate reach 60 frames per second.

![](EPUB/images/image_257.jpg)

**Figure 5-52**

60 frames per second is also the frame rate at which the robot scans objects.

What happens when you turn on Pixy2's lamp (see Figure 5-53)?

![](EPUB/images/image_258.jpg)

**Figure 5-53**

Note that with the lamp on, Pixy2 usually shortens the image exposure (sampling) time, thereby reducing motion blur. Motion blur typically occurs because the camera samples a moving object. Now, the objects we are imaging are lines or sets of lines; if they become blurred due to motion (robot movement), it may make it difficult for Pixy2 to detect and track lines. The lamp on Pixy2 helps solve this problem, so the lamp can allow your robot to move faster.

Please use the "All features" view; if you switched views previously, you need to switch back. Next, draw another line on the original line to form a 4-way intersection and place it in front of Pixy2. Pixy2 will recognize the intersection and quickly select the default branch of the intersection—straight ahead—as shown in Figure 5-54.

![](EPUB/images/image_259.jpg)

**Figure 5-54**

You can change the "Default turn angle" in the "Expert" tab; with the current default configuration, you can see the default turn angle is `0`, meaning straight ahead. This angle is determined by an angular coordinate system referenced to the straight-ahead direction, as shown in Figure 5-55.

![](EPUB/images/image_260.jpg)

**Figure 5-55**

Now try changing the Default turn angle to `-90` (right turn direction), then click the "Apply" button, as shown in Figure 5-56.

![](EPUB/images/image_261.jpg)

**Figure 5-56**

Then place the intersection in front of Pixy2 again; you will find it sets the right-turn direction as the default branch, as shown in Figure 5-57.

By default, Pixy2 automatically selects the default branch based on the Default turn angle or the most recent `setNextTurn()` call. You can change this setting by enabling the "Delayed turn" option in the "Expert" tab. Enable the "Delayed turn" feature, then click "Apply", as shown in Figure 5-58.

![](EPUB/images/image_262.jpg)

**Figure 5-57**

![](EPUB/images/image_263.jpg)

**Figure 5-58**

Now when you place the intersection in front of Pixy2, it will no longer automatically select a branch but will mark the intersection because it doesn't know what to do next, as shown in Figure 5-59.

![](EPUB/images/image_264.jpg)

**Figure 5-59**

In delayed turn mode, Pixy2 waits for the controller to call `setNextTurn()`, which causes Pixy2 to select the branch closest to the set angle.

We wanted Pixy2 to receive simple commands or information while running the line tracking algorithm, so we added barcodes as features; barcodes function like road signs. You can add graphics to barcodes for human readability, provided it doesn't affect their function. To test this function, please download and print the `all_codes.pdf` document. (Note: other barcode files and images can be found here.)

Place the document in front of Pixy2 while it is running the line tracking algorithm; you will see the effect shown in Figure 5-60.

![](EPUB/images/image_265.jpg)

**Figure 5-60**

## 5.4.4 Running Line Tracking Program on Microcontroller

If you haven't connected an Arduino to Pixy2, you can use an Arduino data cable to connect it to Pixy.

Next, download the Arduino library file `arduino_pixy2-x.y.z.zip`, open the Arduino IDE, select `Sketch → Include Library → Add .ZIP Library...` to import the Pixy library (older versions use `Sketch → Include Library`), then select the zip file you just downloaded.

Next, in the Arduino IDE, select `File → Examples → Pixy2` to load the `line_hello_world` example. After uploading, open the Serial Monitor; you can see output similar to the following:

![](EPUB/images/image_266.jpg)

Specifically, when you place a barcode, line, or intersection in front of Pixy2, it returns a number. Remember, this value is based on the coordinate system shown in Figure 5-61.

![](EPUB/images/image_267.jpg)

