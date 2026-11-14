

![](OEBPS/Images/image_178.jpg)

▲Figure 2-59

![](OEBPS/Images/image_179.jpg)

Figure 2-60

**Step 03** Press the `Enter` key to confirm. A message prompt box appears; click the "OK" button, as shown in Figure 2-61.

![](OEBPS/Images/image_180.jpg)

Figure 2-61

**Step 04** After performing the operation, open the ChatGPT chat window, click the input box at the bottom, and enter the following instruction in the input box: "In an Excel worksheet, I need to retain 3 decimal places for the value in cell A2 in cell B2. Please help me check the correctness of the formula and improve it: `=ROUND(A2)`", as shown in Figure 2-62.

![](OEBPS/Images/image_181.jpg)

Figure 2-62

**Step 05** Press the `Enter` key to send. ChatGPT will check and improve the formula, as shown in Figure 2-63.

![](OEBPS/Images/image_182.jpg)

Figure 2-63

**Step 06** Click the `Copy code` button in the upper right corner of the formula to copy it, as shown in Figure 2-64.

![](OEBPS/Images/image_183.jpg)

Figure 2-64

**Step 07** Return to the Excel worksheet and paste the copied formula into cell `B2`: `=ROUND(A2,3)`, as shown in Figure 2-65.

**Step 08** Press the `Enter` key to confirm. The value from cell `A2` will be extracted with 3 decimal places, as shown in Figure 2-66.

![](OEBPS/Images/image_184.jpg)

▲Figure 2-65

![](OEBPS/Images/image_185.jpg)

Figure 2-66

**Step 09** Move the cursor to the lower-right corner of cell `B2`, hold down the left mouse button, and drag down to cell `B6` to fill the formula. This will batch extract values from column A with 3 decimal places. The result is shown in Figure 2-67.

![](OEBPS/Images/image_186.jpg)

Figure 2-67

## 2.3 Writing Formulas with ChatGPT

In Excel, functions are powerful tools for data processing. Through function formulas, you can perform summarization, analysis, and prediction, achieving efficient calculations and improving work and life efficiency. This section introduces methods for writing function calculation formulas using ChatGPT, allowing users to perform calculations directly in Excel worksheets.

### 030 Calculating Averages with ChatGPT

![](OEBPS/Images/image_187.jpg)

Scan to watch the tutorial video

In Excel, when users need to calculate an average in a cell, they can obtain the calculation formula through ChatGPT. The specific operation method is described below.

**Step 01** Open a worksheet, as shown in Figure 2-68. The average sales volume for various products needs to be calculated in column `E`.

![](OEBPS/Images/image_188.jpg)

Figure 2-68

**Step 02** Open the ChatGPT chat window and enter the following instruction in the input box: "In an Excel worksheet, I need to write a calculation formula to calculate the average of the cell range `B2:D2` in cell `E2`." Press the `Enter` key to send. ChatGPT will reply with the formula for calculating the average based on the query, as shown in Figure 2-69.

![](OEBPS/Images/image_189.jpg)

Figure 2-69

**Step 03** Copy the replied formula, return to the Excel worksheet, select the cell range `E2:E5`, and paste the copied formula into the formula bar: `=AVERAGE(B2:D2)`, as shown in Figure 2-70.

**Step 04** Press the `Ctrl+Enter` shortcut key to batch calculate the averages, as shown in Figure 2-71.

![](OEBPS/Images/image_192.jpg)

▲Figure 2-70

![](OEBPS/Images/image_193.jpg)

Figure 2-71

### 031 Getting Performance Rankings with ChatGPT

![](OEBPS/Images/image_194.jpg)

Scan to watch the tutorial video

In Excel, when users need to quickly calculate employee performance rankings without changing the sort order, they can obtain the ranking formula through ChatGPT. The specific operation method is described below.

**Step 01** Open a worksheet, as shown in Figure 2-72. The performance rankings of each employee need to be calculated in column `C`.

**Step 02** Open the ChatGPT chat window and enter the following instruction in the input box: "In an Excel worksheet, I need to write a calculation formula to calculate the rank of the values in column `B` within the cell range `B2:B7` in column `C`." Press the `Enter` key to send. ChatGPT will reply with the formula for calculating the ranking based on the query, as shown in Figure 2-73.

![](OEBPS/Images/image_195.jpg)

▲Figure 2-72

![](OEBPS/Images/image_196.jpg)

Figure 2-73

**Step 03** Copy the replied formula, return to the Excel worksheet, select the cell range `C2:C7`, and paste the copied formula into the formula bar: `=RANK(B2,$B$2:$B$7)`, as shown in Figure 2-74.

![](OEBPS/Images/image_199.jpg)

Figure 2-74

**Step 04** After performing the above operation, press the `Ctrl+Enter` shortcut key to calculate the performance rankings of each employee, as shown in Figure 2-75.

![](OEBPS/Images/image_200.jpg)

Figure 2-75

### 032 Performing Running Totals with ChatGPT

![](OEBPS/Images/image_201.jpg)

Scan to watch the tutorial video

In Excel, when users do not know how to perform a running total on data in a worksheet, they can obtain the running total formula through ChatGPT. The specific operation method is described below.

**Step 01** Open a worksheet, as shown in Figure 2-76. A running total of the values in column `B` needs to be performed in column `C`.

![](OEBPS/Images/image_202.jpg)

Figure 2-76

**Step 02** Open the ChatGPT chat window and enter the following instruction in the input box: "In an Excel worksheet, I need to write a calculation formula to perform a running total of the values in the cell range `B2:B7` in column `C`." Press the `Enter` key to send. ChatGPT will reply with the running total calculation formula based on the query, as shown in Figure 2-77.

![](OEBPS/Images/image_203.jpg)

Figure 2-77

**Step 03** Copy the replied formula, return to the Excel worksheet, select the cell range `C2:C7`, and paste the copied formula into the formula bar: `=SUM($B$2:B2)`, as shown in Figure 2-78.

![](OEBPS/Images/image_206.jpg)

Figure 2-78

**Step 04** Press the `Ctrl+Enter` shortcut key to perform the running total. The result is shown in Figure 2-79.

![](OEBPS/Images/image_207.jpg)

Figure 2-79

### 033 Locating and Finding Employee Information with ChatGPT

![](OEBPS/Images/image_208.jpg)

Scan to watch the tutorial video

In Excel, the `INDEX` function returns the value of a cell or a part of a range from a specified range, while the `MATCH` function searches for a specified value in a specified range and returns the index or relative position of that value. Combining these two functions allows for data location, data lookup, and validation of input values. The following example demonstrates how to use ChatGPT to write `INDEX` and `MATCH` function formulas for locating and finding employee information.

**Step 01** Open a worksheet, as shown in Figure 2-80. Two tables are displayed. The missing information data on the right needs to be found in the left table.

![](OEBPS/Images/image_209.jpg)

Figure 2-80

**Step 02** Open the ChatGPT chat window and enter the following instruction in the input box: "In an Excel worksheet, columns `A:C` are the lookup range. I need to find the row where the name provided in column `F` cells is located in the lookup range, find the column where the column `I` header is located in the lookup range, and finally return the found result to the cells in column `I`. How do I write a complete lookup formula using the `INDEX` and `MATCH` functions?" Press the `Enter` key to send. ChatGPT will write the combined formula of `INDEX` and `MATCH` functions based on the query, as shown in Figure 2-81.

![](OEBPS/Images/image_210.jpg)

▲Figure 2-81

![](OEBPS/Images/image_211.jpg)

Figure 2-81 (Continued)

**Step 03** Copy the function formula, return to the Excel worksheet, select cell `I2`, and paste the formula into the formula bar: `=INDEX(A:C,MATCH(F2,A:A,0),MATCH(I2,1:1,0))`, as shown in Figure 2-82.

![](OEBPS/Images/image_214.jpg)

Figure 2-82

**Step 04** Modify the first reference parameter of the third argument in the formula to `I1`, select the cell referenced in the third argument, and press the `F4` key to switch to absolute reference to prevent rows and columns from changing when filling the formula. The result is shown in Figure 2-83.

![](OEBPS/Images/image_215.jpg)

Figure 2-83

**Step 05** Press the `Enter` key to confirm. The looked-up base salary will be returned, as shown in Figure 2-84.

![](OEBPS/Images/image_216.jpg)

Figure 2-84

**Step 06** Drag the fill handle in the lower-right corner of cell `I2` to fill the formula down to cell `I7` to batch look up the base salary of each employee. The result is shown in Figure 2-85.

![](OEBPS/Images/image_217.jpg)

Figure 2-85

### 034 Finding Employee Departments with ChatGPT

![](OEBPS/Images/image_218.jpg)

Scan to watch the tutorial video

The `LOOKUP` function is a commonly used lookup function in Excel. It can search for a specified value in a specified range and return the closest value or corresponding result. The `LOOKUP` function is known as the "cure-all" in Excel lookup functions; whether it is reverse lookup or forward lookup, the `LOOKUP` function is very practical and applicable.

The following example demonstrates how to use ChatGPT to write a `LOOKUP` function formula to find specified data within a specified range, using finding the corresponding department for an employee as an example.

**Step 01** Open a worksheet, as shown in Figure 2-86. The corresponding department needs to be found based on the employee name within the table range in columns `A` and `B`.

![](OEBPS/Images/image_219.jpg)

Figure 2-86

**Step 02** Open the ChatGPT chat window and enter the following instruction in the input box: "In column `E` cells of an Excel worksheet, how do I use a `LOOKUP` function formula to look up the value in column `D` cells within the cell range `B2:B11` and return the corresponding result from the cell range `A2:A11`?" Press the `Enter` key to send. ChatGPT will write the function formula based on the query, as shown in Figure 2-87.

![](OEBPS/Images/image_220.jpg)

Figure 2-87

**Step 03** Copy the function formula, return to the Excel worksheet, select the cell range `E2:E5`, and paste the formula into the formula bar: `=LOOKUP(D2,B2:B11,A2:A11)`, as shown in Figure 2-88.

![](OEBPS/Images/image_223.jpg)

Figure 2-88

**Step 04** After performing the operation, select the second and third argument parameters and press the `F4` key to switch to absolute reference, as shown in Figure 2-89.

![](OEBPS/Images/image_224.jpg)

Figure 2-89

**Step 05** After performing the operation, press the `Ctrl+Enter` shortcut key to find the corresponding department based on the employee name, as shown in Figure 2-90.

![](OEBPS/Images/image_225.jpg)

Figure 2-90

### 035 Calculating Headcount in Bonus Intervals with ChatGPT

![](OEBPS/Images/image_226.jpg)

Scan to watch the tutorial video

Calculating the number of people within an interval can use the `FREQUENCY` function. It is a statistical function in Excel used to calculate the frequency distribution of values in a dataset. This function helps users understand the number of times values appear in a dataset and groups these counts into specified interval ranges. The following example demonstrates how to use ChatGPT to write a `FREQUENCY` function formula to calculate the headcount in bonus intervals.

**Step 01** Open a worksheet, as shown in Figure 2-91. The headcount for each bonus interval needs to be counted. Note that when entering interval conditions in cells, the upper limit of each interval is entered (e.g., for 0–800, the upper limit entered is 800).

![](OEBPS/Images/image_227.jpg)

Figure 2-91

**Step 02** Open the ChatGPT chat window and send the instruction: "In an Excel worksheet, I need to count the values in cells `C2:C9` based on the interval conditions in cells `E2:E5`. Please write a calculation formula using the `FREQUENCY` function." ChatGPT will reply with the calculation formula written using the `FREQUENCY` function based on the query, as shown in Figure 2-92.

![](OEBPS/Images/image_228.jpg)

Figure 2-92

**Step 03** Copy the function formula, return to the Excel worksheet, select the cell range `F2:F5`, paste the copied formula into the formula bar: `=FREQUENCY(C2:C9,E2:E5)`, and press the `Ctrl+Shift+Enter` key combination to confirm. This will count the quantities meeting the interval conditions, as shown in Figure 2-93.

![](OEBPS/Images/image_231.jpg)

Figure 2-93

#### Expert Tip

In Excel, when the entered formula is an array formula, you need to use the `Ctrl+Shift+Enter` key combination to confirm the formula as an array formula.

In Excel, an array formula is a special formula used to perform calculations on multiple cell ranges and return multiple results. These formulas typically involve array operations, such as calculating, summarizing, or filtering each cell in a range. After confirming an array formula, Excel automatically adds curly braces `{}` around the formula to indicate that the result is an array; you do not need to enter the curly braces manually.

### 036 Automatically Updating Date and Time with ChatGPT

![](OEBPS/Images/image_232.jpg)

Scan to watch the tutorial video

In Excel, `TODAY` and `NOW` are commonly used date and time functions. The `TODAY` function returns the current date, and the `NOW` function returns the current date and time. The following example demonstrates how to get the usage of `TODAY` and `NOW` function formulas in ChatGPT.

**Step 01** Open a worksheet, as shown in Figure 2-94. The preparation date and preparation time need to be entered in the worksheet.

**Step 02** Open the ChatGPT chat window and enter the following instruction in the input box: "In an Excel worksheet, how do I use `TODAY` and `NOW` functions to write formulas so that the preparation date and preparation time can be updated automatically?" Press the `Enter` key to send. After a moment, ChatGPT will reply with the formulas and usage methods for the `TODAY` and `NOW` functions based on the query, as shown in Figure 2-95.

![](OEBPS/Images/image_233.jpg)

▲Figure 2-94

![](OEBPS/Images/image_234.jpg)

Figure 2-95

**Step 03** Copy the function formulas, return to the Excel worksheet, and paste the two formulas into cells `B1` and `B2` respectively. After performing the operation, the preparation date and time will be updated automatically. The result is shown in Figure 2-96. After closing the worksheet, the next time you open it, the date and time in cells `B1` and `B2` will update automatically.

![](OEBPS/Images/image_235.jpg)

Figure 2-96

### 037 Calculating Time Between Dates with ChatGPT

![](OEBPS/Images/image_236.jpg)

Scan to watch the tutorial video

The `DATEDIF` function is a date function in Excel used to calculate the difference between two dates. This function can be used to calculate age, length of service, and project duration. The following example demonstrates how to use ChatGPT to write a `DATEDIF` function formula to calculate the time interval between two dates.

**Step 01** Open a worksheet, as shown in Figure 2-97. Column `C` is the hire date, and column `D` is the termination date. The length of service for terminated employees needs to be calculated in the worksheet.

![](OEBPS/Images/image_237.jpg)

Figure 2-97

**Step 02** Open the ChatGPT chat window and send the instruction: "In an Excel worksheet, column `C` is the hire date, and column `D` is the termination date. How do I use a `DATEDIF` function formula to calculate employee length of service?" ChatGPT will write the `DATEDIF` function formula based on the query, as shown in Figure 2-98.

![](OEBPS/Images/image_238.jpg)

Figure 2-98

**Step 03** Copy the function formula, return to the Excel worksheet, paste the formula into cell `E2`: `=DATEDIF(C2,D2,"Y")`, and fill the formula down to cell `E6` to batch calculate employee length of service. The result is shown in Figure 2-99.

![](OEBPS/Images/image_241.jpg)

Figure 2-99

### 038 Calculating Perfect Attendance Bonuses Conditionally with ChatGPT

![](OEBPS/Images/image_242.jpg)

Scan to watch the tutorial video

In Excel, the `IF` function is categorized as a logical function. It returns different values based on a given condition and is widely used in Excel for conditional judgment and logical operations. The following example demonstrates how to use ChatGPT to write an `IF` function formula, using calculating perfect attendance bonuses based on conditions as an example.

**Step 01** Open a worksheet, as shown in Figure 2-100. Column `B` is the number of employee attendance days. When the attendance days are equal to or greater than the standard days, it counts as perfect attendance, and employees with perfect attendance receive a bonus of 500 yuan.

![](OEBPS/Images/image_243.jpg)

Figure 2-100

**Step 02** Open the ChatGPT chat window and enter the following instruction in the input box: "In an Excel worksheet, column `B` is the number of employee attendance days. When attendance days are equal to or greater than 25 days, it counts as perfect attendance. Employees with perfect attendance receive a bonus of 500 yuan. How do I calculate this using an `IF` function formula?" Press the `Enter` key to send. ChatGPT will write the `IF` function formula based on the query, as shown in Figure 2-101.

![](OEBPS/Images/image_244.jpg)

Figure 2-101

