

# Excel VBA Editor Basic Operations and ChatGPT-Assisted Programming

In Excel, the VBA Editor can be used to create custom functions, automate data entry, generate reports, and perform complex data analysis tasks that are difficult to achieve with Excel's built-in functions and features. This section mainly introduces the basic operation methods of the VBA Editor in Excel.

## 5.1 Basic Operations of the VBA Editor

### 063 Add the "Developer" Tab

![](OEBPS/Images/image_445.jpg)

Scan QR code to watch tutorial video

In an Excel worksheet, the "Developer" tab is usually hidden when the Excel software is initially launched. To use the VBA Editor in Excel, users first need to add the "Developer" tab to the Ribbon. The operation method is described below.

**Step 01** Right-click on a blank area of the Excel Ribbon, and select the "Customize the Ribbon" command from the pop-up context menu, as shown in Figure 5-1.

**Step 02** The "Excel Options" dialog box appears. In the "Main Tabs" area, ![](OEBPS/Images/image_446.jpg) check the "Developer" checkbox; ![](OEBPS/Images/image_447.jpg) click the "OK" button, as shown in Figure 5-2, to add the "Developer" tab to the Ribbon.

![](OEBPS/Images/image_448.jpg)

▲Figure 5-1

![](OEBPS/Images/image_449.jpg)

Figure 5-2

### 064 Expand Windows in the VBA Editor

![](OEBPS/Images/image_450.jpg)

Scan QR code to watch tutorial video

In an Excel worksheet, you can open the VBA Editor by clicking the `Visual Basic` button in the "Developer" tab, as shown in Figure 5-3, or by pressing the `Alt+F11` shortcut key to open the Microsoft Visual Basic for Applications (VBA) Editor. After opening the editor, there may be no windows displayed, nor any modules available for code editing. Beginners unfamiliar with VBA might not know how to proceed at this point. The following describes how to expand the windows and panes one by one.

![](OEBPS/Images/image_451.jpg)

Figure 5-3

First, in the Editor, select "View" | "Project Explorer", as shown in Figure 5-4, to expand the corresponding window. In the "Project" Explorer, you can manage files, folders, databases, etc. Then, select "Properties Window" from the "View" menu list to expand the Properties Window, where all properties of an object can be obtained.

![](OEBPS/Images/image_452.jpg)

Figure 5-4

Second, click the "Insert" menu, as shown in Figure 5-5. Select "Module" from the pop-up menu list to open a Code Window, where you can enter code and run it.

### 065 Remove an Inserted Module

![](OEBPS/Images/image_453.jpg)

Scan QR code to watch tutorial video

In the VBA Editor, a module can be understood as the carrier of macro code. Users need to insert a module to write macro code. If an added module is incorrect, it can be removed. The specific operation method is described below.

![](OEBPS/Images/image_454.jpg)

Figure 5-5

**Step 01** In Excel, open the VBA Editor. Right-click on a blank area in the "Project" Explorer, and select "Insert" | "Module" from the pop-up context menu, as shown in Figure 5-6.

**Step 02** After the operation, a new module is inserted, as shown in Figure 5-7. The inserted module folder and module name will be displayed in the "Project" Explorer.

![](OEBPS/Images/image_455.jpg)

▲Figure 5-6

![](OEBPS/Images/image_456.jpg)

Figure 5-7

**Step 03** In the "Project" Explorer, select the inserted module, right-click, and select "Remove Module1" from the pop-up context menu, as shown in Figure 5-8.

**Step 04** An information prompt dialog box appears, asking whether to export and save the module and its code before removing. Since no code was written in this case, click the "No" button here, as shown in Figure 5-9. After the operation, the inserted module is removed.

![](OEBPS/Images/image_457.jpg)

▲Figure 5-8

![](OEBPS/Images/image_458.jpg)

Figure 5-9

### 066 Understand the Macro Code Writing Framework

![](OEBPS/Images/image_459.jpg)

Scan QR code to watch tutorial video

The VBA code writing framework is actually similar to writing a letter or an email; it must have a beginning, a name, a body, and an end. The following introduces the basic operation methods for writing macro code.

**Step 01** In Excel, open the VBA Editor, insert a module, and enter the starting code: `Sub`, as shown in Figure 5-10.

**Step 02** Enter a space + custom Sub procedure name + English parentheses: `CalculateVariableValue()`, as shown in Figure 5-11.

![](OEBPS/Images/image_460.jpg)

▲Figure 5-10

![](OEBPS/Images/image_461.jpg)

Figure 5-11

**Step 03** After entering the name, press the `Enter` key to start a new line. At this point, the ending code `End Sub` will be automatically written below the blank line, as shown in Figure 5-12.

**Step 04** Enter the body code in the blank line:

```
n=5
[A1]=n*11
[A2]=n*22
[A3]=n*33
```

This code segment indicates that the variable value is represented by `n`, the value of `n` is 5, cell `A1` returns the value of `n` multiplied by 11, cell `A2` returns the value of `n` multiplied by 22, and cell `A3` returns the value of `n` multiplied by 33, as shown in Figure 5-13.

![](OEBPS/Images/image_462.jpg)

▲Figure 5-12

![](OEBPS/Images/image_463.jpg)

Figure 5-13

**Step 05** After the operation, click the "Run Sub/UserForm" button ![](OEBPS/Images/image_464.jpg), or press the `F5` key to run the macro code, as shown in Figure 5-14.

**Step 06** Return to the Excel worksheet to view the variable value calculation results, as shown in Figure 5-15. You can see that the values in the cells are returned directly, and no calculation formulas are used in the formula bar.

![](OEBPS/Images/image_465.jpg)

▲Figure 5-14

![](OEBPS/Images/image_466.jpg)

Figure 5-15

### 067 View Code in Two Ways

![](OEBPS/Images/image_467.jpg)

Scan QR code to watch tutorial video

In Excel, after closing the VBA Editor, you can view the written code in two ways. The specific operation methods are described below.

**Step 01** Continuing from Example 066, in the "Controls" group of the "Developer" tab, click the "View Code" button, as shown in Figure 5-16.

![](OEBPS/Images/image_468.jpg)

**Step 02** After the operation, you can quickly enter the VBA Editor and open the blank `Sheet1` module simultaneously. Switch to Module1 to view the code, as shown in Figure 5-17. Click the "Close" button in the upper right corner of the module to close the module and exit the VBA Editor.

![](OEBPS/Images/image_469.jpg)

▲Figure 5-16

![](OEBPS/Images/image_470.jpg)

Figure 5-17

**Step 03** Right-click on the `Sheet1` name at the bottom of the worksheet, and select "View Code" from the pop-up context menu, as shown in Figure 5-18.

![](OEBPS/Images/image_471.jpg)

Figure 5-18

**Step 04** After the operation, you can enter the VBA Editor. In the "Project" Explorer, double-click "Module1", or right-click on "Module1" and select "View Code" from the pop-up context menu, as shown in Figure 5-19, to view the code written in the module.

![](OEBPS/Images/image_472.jpg)

Figure 5-19

### 068 Edit a Created Macro

![](OEBPS/Images/image_473.jpg)

Scan QR code to watch tutorial video

In Excel, created macro code can be found through the "Macro" feature, and edited, run, or deleted. You can also set a shortcut key for a created macro. The specific operation method is described below.

**Step 01** Using the result of Example 066 as an example, click the "Macro" button in the "Code" group of the "Developer" tab, as shown in Figure 5-20.

**Step 02** The "Macro" dialog box appears, displaying the name of the created macro, as shown in Figure 5-21.

![](OEBPS/Images/image_474.jpg)

▲Figure 5-20

![](OEBPS/Images/image_475.jpg)

Figure 5-21

**Step 03** Click the "Edit" button to open the VBA Editor. In the "Module1" code, change the value of `n` to 8, as shown in Figure 5-22.

**Step 04** After the operation, close the VBA Editor, open the "Macro" dialog box, and click the "Run" button, as shown in Figure 5-23.

![](OEBPS/Images/image_476.jpg)

▲Figure 5-22

![](OEBPS/Images/image_477.jpg)

Figure 5-23

**Step 05** After the operation, the macro code runs and recalculates the variable value, as shown in Figure 5-24.

**Step 06** Open the "Macro" dialog box again and click the "Options" button, as shown in Figure 5-25.

**Step 07** The "Macro Options" dialog box appears. In the blank box under "Shortcut key", enter any English letter, such as Q, to form the `Ctrl+Shift+Q` shortcut key, as shown in Figure 5-26. Click the "OK" button. In the worksheet, press the created shortcut key to execute the macro task.

![](OEBPS/Images/image_478.jpg)

▲Figure 5-24

![](OEBPS/Images/image_479.jpg)

▲Figure 5-25

![](OEBPS/Images/image_480.jpg)

Figure 5-26

## 5.2 Write Macro Code with ChatGPT

When writing macro code in Excel, users need to have a certain understanding of the macro programming language; otherwise, it is easy to make mistakes or fail to write the code. Users can describe the functions and operations they want to achieve in Excel to ChatGPT through dialogue, letting ChatGPT write the code without needing to deeply understand the macro programming language themselves. ChatGPT will quickly generate code drafts based on the user's description and iteratively improve them based on user feedback, providing a more intuitive, faster, and smarter development experience, helping users complete more efficient and automated Excel operations and tasks.

### 069 Get Macro Code in ChatGPT

![](OEBPS/Images/image_481.jpg)

Scan QR code to watch tutorial video

When users do not know how to get macro code in ChatGPT, they can ask ChatGPT directly. ChatGPT will reply with the method to get macro code. The specific operation method is described below.

**Step 01** Open the ChatGPT chat window, and enter "How do I get Excel macro code in ChatGPT? Please give an example" in the input box, as shown in Figure 5-27.

**Step 02** Press `Enter` to send. ChatGPT will reply based on the question and feed back detailed operation steps to the user, as shown in Figure 5-28.

![](OEBPS/Images/image_482.jpg)

▲Figure 5-27

![](OEBPS/Images/image_483.jpg)

Figure 5-28

### 070 Use Code Written by ChatGPT

![](OEBPS/Images/image_484.jpg)

Scan QR code to watch tutorial video

According to ChatGPT's reply in Example 069, we can refer to the described operations to use the code written by ChatGPT in Excel. The specific operation method is described below.

**Step 01** On the code box exemplified by ChatGPT, click the `Copy code` button, as shown in Figure 5-29.

**Step 02** Create a new blank Excel worksheet. In the "Code" group of the "Developer" tab, click the `Visual Basic` button, as shown in Figure 5-30.

**Step 03** Open the VBA Editor and select "Insert" | "Module", as shown in Figure 5-31.

![](OEBPS/Images/image_485.jpg)

▲Figure 5-29

![](OEBPS/Images/image_486.jpg)

▲Figure 5-30

![](OEBPS/Images/image_487.jpg)

Figure 5-31

**Step 04** After the operation, a new module is inserted. Paste the code exemplified by ChatGPT into the module, as shown in Figure 5-32.

![](OEBPS/Images/image_488.jpg)

**Step 05** Close the VBA Editor. In the "Controls" group of the "Developer" tab, ![](OEBPS/Images/image_489.jpg) click the "Insert" dropdown button; ![](OEBPS/Images/image_490.jpg) click the "Button (Form Control)" icon in the pop-up list box, as shown in Figure 5-33.

![](OEBPS/Images/image_491.jpg)

▲Figure 5-32

![](OEBPS/Images/image_492.jpg)

Figure 5-33

**Step 06** After the operation, a control button is drawn in the worksheet, as shown in Figure 5-34.

**Step 07** Release the left mouse button. The "Assign Macro" dialog box appears. Select the created macro, as shown in Figure 5-35. This links the control button to the macro, making it a run button for executing the macro task.

![](OEBPS/Images/image_493.jpg)

▲Figure 5-34

![](OEBPS/Images/image_494.jpg)

Figure 5-35

**Step 08** Click the "OK" button to insert the control button, as shown in Figure 5-36.

**Step 09** Right-click the mouse, and select "Edit Text" from the pop-up context menu, as shown in Figure 5-37.

![](OEBPS/Images/image_495.jpg)

▲Figure 5-36

![](OEBPS/Images/image_496.jpg)

Figure 5-37

**Step 10** After the operation, you can edit the text in the button. Enter "Insert Text" to name the button, as shown in Figure 5-38.

**Step 11** Select any cell, ![](OEBPS/Images/image_497.jpg) click the "Insert Text" control button; ![](OEBPS/Images/image_498.jpg) text will be inserted into the cell, as shown in Figure 5-39.

![](OEBPS/Images/image_499.jpg)

▲Figure 5-38

![](OEBPS/Images/image_500.jpg)

Figure 5-39

### 071 Save the Created Macro File

![](OEBPS/Images/image_501.jpg)

Scan QR code to watch tutorial video

In Excel, after editing or pasting code and running it, you must remember to save the created macro file. Otherwise, the next time you open the prepared workbook, you will find that the previously created effects are gone. Saving the code not only preserves the created effects but also facilitates future code modification. Therefore, the VBA code must be saved after editing is complete. The specific operation method is described below.

**Step 01** Continuing from Example 070, click the "Save" button ![](OEBPS/Images/image_502.jpg). The "Save As" dialog box appears. Set the save path and file name, as shown in Figure 5-40.

![](OEBPS/Images/image_503.jpg)

Figure 5-40

**Step 02** Clicking the "Save" button directly will pop up a Microsoft Excel dialog box. Click the "No" button, as shown in Figure 5-41.

![](OEBPS/Images/image_504.jpg)

Figure 5-41

**Step 03** Return to the "Save As" dialog box. Expand the "Save as type" dropdown list and select "Excel Macro-Enabled Workbook (`*.xlsm`)" type, as shown in Figure 5-42. Click the "Save" button to save the created macro file.

![](OEBPS/Images/image_505.jpg)

Figure 5-42

## 5.3 Write Calculation Code with ChatGPT

In Excel, besides using formulas and functions for data calculation, you can also automatically calculate worksheet data by writing VBA code. Users can create simple and effective calculation codes through interaction with ChatGPT, letting the computer perform various mathematical operations. This section will detail how to use ChatGPT to write calculation code, providing new possibilities for data analysis and processing.

### 072 Write Group Sum Code with ChatGPT

![](OEBPS/Images/image_506.jpg)

Scan QR code to watch tutorial video

When an Excel worksheet has mixed product categories and a large amount of data, users can perform grouping and summing via VBA. Let ChatGPT write VBA code to deduplicate and categorize products, summarize and sum them, and return the results to a specified location. The specific operation method is described below.

**Step 01** Open a worksheet, as shown in Figure 5-43. It is a Product Sales Receipt List. Products need to be grouped and summed, and the calculation results returned to `Column F` and `Column G`.

![](OEBPS/Images/image_507.jpg)

Figure 5-43

**Step 02** Open the ChatGPT chat window. Enter the instruction "In the `Sheet1` worksheet of Excel, `Column B` is Product, `Column C` is Sales Amount. Please use VBA code to group and sum Sales Amount by Product, and return the results to `Column F` and `Column G` of the `Sheet1` worksheet." Press `Enter` to send. ChatGPT will reply with the group sum calculation code, as shown in Figure 5-44.

![](OEBPS/Images/image_508.jpg)

▲Figure 5-44

![](OEBPS/Images/image_509.jpg)

Figure 5-44 (Continued)

**Step 03** Copy the code written by ChatGPT. Return to the Excel worksheet, open the VBA Editor, insert a module, and paste the copied code into the module, as shown in Figure 5-45.

![](OEBPS/Images/image_510.jpg)

Figure 5-45

**Step 04** After the operation, click the "Run Sub/UserForm" button ![](OEBPS/Images/image_511.jpg) to run the macro code, as shown in Figure 5-46.

![](OEBPS/Images/image_512.jpg)

Figure 5-46

**Step 05** Close the VBA Editor. In the worksheet, the product sales amounts are now grouped and summed, as shown in Figure 5-47.

![](OEBPS/Images/image_513.jpg)

Figure 5-47

**Step 06** Enter headers in `Column F` and `Column G`, add borders, set fill colors, etc. The final effect is shown in Figure 5-48.

![](OEBPS/Images/image_514.jpg)

Figure 5-48

### 073 Write Product Quantity Statistics Code with ChatGPT

![](OEBPS/Images/image_515.jpg)

Scan QR code to watch tutorial video

In a product inventory table, when multiple products have low stock quantities, inventory counting and restocking can begin. Users can let ChatGPT write VBA code to count the quantity of products with low stock, analyzing whether bulk restocking is needed. The specific operation method is described below.

