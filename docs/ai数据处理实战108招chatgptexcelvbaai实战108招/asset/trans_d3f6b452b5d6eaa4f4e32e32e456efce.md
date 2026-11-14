
## 2.2 Using ChatGPT to Assist Calculations

### 3. Mixed References

During operation, pay attention to the reference switching function of the `F4` key, which only applies to the selected formula segment or referenced cells.

![](OEBPS/Images/image_097.jpg)

▲Figure 2-5

![](OEBPS/Images/image_098.jpg)

▲Figure 2-6

![](OEBPS/Images/image_099.jpg)

Figure 2-7

In Excel, a mixed reference is equivalent to recombining absolute and relative references, allowing you to relatively reference rows and absolutely reference columns, or absolutely reference rows and relatively reference columns simultaneously.

**Step 01** Enter `=B1` in cell `B2`. Pressing `F4` the 1st time creates an absolute reference; pressing `F4` the 2nd time changes the formula to `=B$1`, indicating a relative column reference and absolute row reference, as shown in Figure 2-8.

**Step 02** After dragging the cell down, the formula remains unchanged as `=B$1`, meaning the row is fixed, as shown in Figure 2-9.

**Expert Tip**

During operation, note that pressing the `F4` key for the 4th time reverts the formula to its initially entered state.

![](OEBPS/Images/image_100.jpg)

▲Figure 2-8

![](OEBPS/Images/image_101.jpg)

Figure 2-9

**Step 03** After dragging the cell to the right, the formula changes to `=C$1`, meaning the column changes but the row remains constant, as shown in Figure 2-10.

**Step 04** Pressing `F4` a 3rd time in cell `B2` changes the formula to `=$B1`, indicating an absolute column reference and relative row reference, as shown in Figure 2-11.

![](OEBPS/Images/image_102.jpg)

▲Figure 2-10

![](OEBPS/Images/image_103.jpg)

Figure 2-11

**Step 05** After dragging the cell to the right, the formula remains unchanged as `=$B1`, meaning the column is fixed, as shown in Figure 2-12.

**Step 06** After dragging the cell down, the formula changes to `=$B2`, meaning the row changes but the column remains constant, as shown in Figure 2-13.

![](OEBPS/Images/image_104.jpg)

▲Figure 2-12

![](OEBPS/Images/image_105.jpg)

Figure 2-13

### 022 Quickly Create Calculation Formulas

![](OEBPS/Images/image_106.jpg)

Scan to watch tutorial video

Statistical calculations in Excel do not require users to manually calculate and then enter results into cells. You only need to establish the correct formula in a cell and let Excel calculate automatically to quickly obtain results. There are two main ways to create formulas in Excel: one is like a mathematical formula, using arithmetic operators (+, -, *, /) to reference cells for calculation; the other is using functions, writing formulas that conform to function syntax through arguments and conditions to perform specific calculations.

Below we will introduce the methods for quickly creating formulas in Excel.

**Step 01** Open a worksheet, as shown in Figure 2-14. We need to calculate the total sales for various products in the worksheet.

**Step 02** Before creating the formula, first clarify the data in the worksheet: which cells contain data for calculation and which contain data to be ignored. For example, in this case, total sales only require multiplying unit price by sales quantity; any extra data in the table can be ignored.

**Step 03** Select cell `B4` in the worksheet and enter the "`=`" symbol, as shown in Figure 2-15.

![](OEBPS/Images/image_107.jpg)

▲Figure 2-14

![](OEBPS/Images/image_108.jpg)

Figure 2-15

**Step 04** Next, select cell `B2` to reference the unit price, as shown in Figure 2-16.

**Step 05** Continue by entering the multiplication operator; press the `*` (asterisk) key on the keyboard, as shown in Figure 2-17.

![](OEBPS/Images/image_109.jpg)

▲Figure 2-16

![](OEBPS/Images/image_110.jpg)

Figure 2-17

**Step 06** After the operation, select cell `B3` to reference the sales quantity, as shown in Figure 2-18.

**Step 07** A complete formula is now established: `=B2*B3`. Press `Enter` to confirm and calculate the total sales for Product A, as shown in Figure 2-19.

![](OEBPS/Images/image_111.jpg)

▲Figure 2-18

![](OEBPS/Images/image_112.jpg)

Figure 2-19

**Step 08** Drag the fill handle (bottom-right corner) of cell `B4` to cell `D4` to fill the formula, calculating the total sales for Product B and Product C, as shown in Figure 2-20.

![](OEBPS/Images/image_113.jpg)

Figure 2-20

### 023 Quickly Insert Function Formulas

![](OEBPS/Images/image_114.jpg)

Scan to watch tutorial video

In Excel, users can quickly insert function formulas in the formula bar, leveraging Excel's powerful calculation capabilities to summarize table data. Below we introduce the method for quickly inserting function formulas.

**Step 01** Open a worksheet, as shown in Figure 2-21. We need to calculate the total sales volume for various products in the worksheet.

**Step 02** Select cell `B6` in the worksheet; click the "**Insert Function**" button in the formula bar, as shown in Figure 2-22.

**Step 03** The "**Insert Function**" dialog box appears, as shown in Figure 2-23.

**Step 04** Users can enter the desired function or the operation they want to perform (e.g., "sum") in the text box below "**Search for a function**"; click the "**Go**" button, as shown in Figure 2-24.

![](OEBPS/Images/image_120.jpg)

▲Figure 2-21

![](OEBPS/Images/image_121.jpg)

▲Figure 2-22

![](OEBPS/Images/image_122.jpg)

▲Figure 2-23

![](OEBPS/Images/image_123.jpg)

Figure 2-24

**Step 05** After the operation, the "**Select a function**" list box displays the sum function; select the `SUM` function here, as shown in Figure 2-25. The formula syntax and description of the selected function appear at the bottom of the list box.

**Step 06** Click the "**OK**" button. The "**Function Arguments**" dialog box appears, with the `Number1` text box automatically referencing a cell range, indicating calculation of values in the `B2:B5` range, as shown in Figure 2-26.

**Expert Tip**

There are two faster methods for summing with the `SUM` function in Excel.

- Select the cell where you want the result, press the `Alt+=` shortcut to quickly sum.
- In the "**Editing**" group on the "**Home**" tab, click the "**AutoSum**" button to quickly sum.

![](OEBPS/Images/image_124.jpg)

▲Figure 2-25

![](OEBPS/Images/image_125.jpg)

Figure 2-26

**Step 07** Click the "**OK**" button to return the calculated value in cell `B6`, as shown in Figure 2-27.

**Step 08** Select cell `B6`, drag the fill handle to cell `D6` to fill the formula, batch calculating the total sales volume for Product B and Product C, as shown in Figure 2-28.

![](OEBPS/Images/image_126.jpg)

▲Figure 2-27

![](OEBPS/Images/image_127.jpg)

Figure 2-28

### 024 Select Functions Using the Function List

![](OEBPS/Images/image_128.jpg)

Scan to watch tutorial video

In an Excel worksheet, users can also quickly select functions from the Function List (AutoComplete) to facilitate formula writing and avoid input errors. Below we introduce the specific operation for selecting functions using the Function List.

**Step 01** Open a worksheet, as shown in Figure 2-29. We need to calculate the sales volume for each salesperson in the worksheet.

**Step 02** Select cell `E2` in the worksheet and enter `=SU`; a Function List containing functions starting with "SU" appears, as shown in Figure 2-30.

![](OEBPS/Images/image_131.jpg)

▲Figure 2-29

![](OEBPS/Images/image_132.jpg)

Figure 2-30

**Step 03** Select the desired function from the list, here the `SUM` function, as shown in Figure 2-31.

**Step 04** After the operation, double-click the left mouse button to add the selected function to the formula being written, as shown in Figure 2-32.

![](OEBPS/Images/image_133.jpg)

▲Figure 2-31

![](OEBPS/Images/image_134.jpg)

Figure 2-32

**Step 05** Select the `B2:D2` cell range; enter a closing parenthesis in English input mode to complete the sum formula, as shown in Figure 2-33.

**Step 06** Press `Enter` to confirm and return the sales volume for the 1st salesperson, as shown in Figure 2-34.

![](OEBPS/Images/image_137.jpg)

▲Figure 2-33

![](OEBPS/Images/image_138.jpg)

Figure 2-34

**Step 07** Drag the fill handle of cell `E2` to fill the formula down to cell `E5`, batch calculating the sales volume for other salespeople, as shown in Figure 2-35.

![](OEBPS/Images/image_139.jpg)

Figure 2-35

### 025 Automatically Correct Formula Errors

![](OEBPS/Images/image_140.jpg)

Scan to watch tutorial video

In Excel, symbols in formulas must be entered in English input mode. If entered in Chinese input mode, the system performs automatic correction. Additionally, users may accidentally type extra or missing characters when entering formulas; Excel quickly identifies errors based on the input and corrects them. Below we explore the effect of Excel's automatic formula correction through a practical example.

**Step 01** Open a worksheet and enter `==SUM（B2:D5)` in the merged cell in row 6, as shown in Figure 2-36. This formula has an extra equal sign and a parenthesis entered in Chinese input mode (full-width).

**Step 02** Press `Enter` to confirm. Excel detects the error and pops up a message box with a correction suggestion, as shown in Figure 2-37.

![](OEBPS/Images/image_141.jpg)

▲Figure 2-36

![](OEBPS/Images/image_142.jpg)

Figure 2-37

**Step 03** Click the "**Yes**" button to correct the formula and return the calculated value in the merged cell, as shown in Figure 2-38.

![](OEBPS/Images/image_145.jpg)

Figure 2-38

### 026 Use ChatGPT to Provide Sum Shortcut Keys

![](OEBPS/Images/image_146.jpg)

Scan to watch tutorial video

When performing summation statistics, if the user forgets the shortcut key for summing, they can ask ChatGPT to provide the shortcut key and then apply it in Excel. Below we introduce the specific operation method.

**Step 01** Open the ChatGPT chat window, click the input box at the bottom, and enter the prompt: "What is the shortcut key for summing in Excel?", as shown in Figure 2-39.

![](OEBPS/Images/image_147.jpg)

Figure 2-39

**Step 02** Press `Enter` to send. ChatGPT replies based on the query, providing the sum shortcut key along with its function and usage, as shown in Figure 2-40.

![](OEBPS/Images/image_148.jpg)

Figure 2-40

### 027 Use ChatGPT to Explain Function Usage

![](OEBPS/Images/image_149.jpg)

Scan to watch tutorial video

When a user needs to use or understand a specific function's usage during spreadsheet creation, they don't need to spend time searching online; they can simply ask ChatGPT to get the function's syntax, purpose, etc. Below we introduce the specific operation method.

**Step 01** Open the ChatGPT chat window, click the input box at the bottom, and enter the prompt: "Explain the principles and usage of the VLOOKUP and HLOOKUP functions in Excel", as shown in Figure 2-41.

![](OEBPS/Images/image_150.jpg)

Figure 2-41

**Step 02** Press `Enter` to send. ChatGPT explains the usage methods and examples for the `VLOOKUP` and `HLOOKUP` functions as requested, as shown in Figure 2-42.

![](OEBPS/Images/image_151.jpg)

Figure 2-42

### 028 Use ChatGPT to Continue Writing Function Formulas

![](OEBPS/Images/image_152.jpg)

Scan to watch tutorial video

When writing function formulas in an Excel worksheet, users can use ChatGPT to help write a complete function formula or continue writing an unfinished one. Below we introduce the specific operation method.

**Step 01** Open an Excel worksheet where Column B contains store ratings, and we need to represent recommendation intensity with heart symbols in Column C, as shown in Figure 2-43.

![](OEBPS/Images/image_153.jpg)

Figure 2-43

**Step 02** Open the ChatGPT chat window, click the input box at the bottom, and enter the prompt: "In an Excel worksheet, Column B contains store ratings. Please help me write a function formula to represent the numeric ratings with heart symbols in Column C", as shown in Figure 2-44.

![](OEBPS/Images/image_154.jpg)

Figure 2-44

**Step 03** Press `Enter` to send. ChatGPT writes a complete function formula as requested and explains the formula, as shown in Figure 2-45.

![](OEBPS/Images/image_155.jpg)

Figure 2-45

**Step 04** Select the written function formula; right-click and choose "**Copy**" from the context menu, as shown in Figure 2-46.

![](OEBPS/Images/image_158.jpg)

Figure 2-46

**Step 05** Return to the Excel worksheet and paste the copied formula into cell `C2`: `=REPT ("❤", B2)`, as shown in Figure 2-47.

![](OEBPS/Images/image_159.jpg)

Figure 2-47

**Step 06** Press `Enter` to confirm, representing recommendation intensity with heart symbols, as shown in Figure 2-48.

**Step 07** Select the `C2:C7` cell range, as shown in Figure 2-49.

![](OEBPS/Images/image_160.jpg)

▲Figure 2-48

![](OEBPS/Images/image_161.jpg)

Figure 2-49

**Step 08** Click in the formula bar, press `Ctrl+Enter` to fill the formula, batch representing recommendation intensity with heart symbols, as shown in Figure 2-50.

![](OEBPS/Images/image_162.jpg)

Figure 2-50

**Step 09** Next, we need to hide the middle 4 digits of the store codes using the `*` symbol. Open the ChatGPT chat window, click the input box, and enter the prompt: "Column D contains store codes. Need to hide the middle 4 digits with `*` symbol. Please help me complete the formula: `=REPLACE(D2,3)`", as shown in Figure 2-51.

![](OEBPS/Images/image_163.jpg)

Figure 2-51

**Step 10** Press `Enter` to send. ChatGPT completes the unfinished function formula as requested and explains it, as shown in Figure 2-52.

![](OEBPS/Images/image_164.jpg)

Figure 2-52

**Step 11** Select the completed formula, right-click, and choose "**Copy**" from the context menu, as shown in Figure 2-53.

![](OEBPS/Images/image_167.jpg)

Figure 2-53

**Step 12** Return to the Excel worksheet, select cell `D2`; paste the copied formula into the formula bar: `=REPLACE(D2,3,4,"****")`, as shown in Figure 2-54.

![](OEBPS/Images/image_170.jpg)

Figure 2-54

**Step 13** Select the original store code, press `Ctrl+X` to cut; then select `D2` in the formula, press `Ctrl+V` to paste, replacing the referenced cell `D2` in the formula with the actual store code text, as shown in Figure 2-55.

**Step 14** Press `Enter` to confirm, hiding the middle 4 digits of the store code, as shown in Figure 2-56.

![](OEBPS/Images/image_171.jpg)

▲Figure 2-55

![](OEBPS/Images/image_172.jpg)

Figure 2-56

**Step 15** Copy the formula in cell `D2`, select cell `D3`, paste the formula into the formula bar using the same method; replace the first argument with the store code from cell `D3`, as shown in Figure 2-57.

**Step 16** After the operation, use the same method to hide the middle 4 digits of other store codes, as shown in Figure 2-58.

![](OEBPS/Images/image_175.jpg)

▲Figure 2-57

![](OEBPS/Images/image_176.jpg)

Figure 2-58

### 029 Use ChatGPT to Check Formula Correctness

![](OEBPS/Images/image_177.jpg)

Scan to watch tutorial video

In an Excel worksheet, when a user finds that a written function formula cannot calculate or calculates incorrectly, they can use ChatGPT to help check the formula's correctness and refine it. Below we introduce the specific operation method.

**Step 01** Open an Excel worksheet where Column A contains preset values. We need to extract values from Column A to 3 decimal places in Column B via formula, as shown in Figure 2-59.

**Step 02** Select cell `B2` and enter the formula: `=ROUND(A2)`, as shown in Figure 2-60.

