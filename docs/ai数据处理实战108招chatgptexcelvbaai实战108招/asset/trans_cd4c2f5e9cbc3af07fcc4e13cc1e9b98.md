

## 3.3 Finding Data with ChatGPT

Similar to data extraction, ChatGPT can also provide assistance when users need to find data in Excel spreadsheets, allowing users to easily locate required data in tables through various functions and tools. Whether it is a simple lookup requirement or advanced data retrieval based on conditions, combining ChatGPT and Excel can provide users with multiple methods to meet different lookup needs.

### 047 Calculating Age from ID Number with ChatGPT

![](OEBPS/Images/image_317.jpg)

Scan to watch tutorial video

Besides extracting the date of birth from an ID number, users can also combine ChatGPT in Excel to calculate age based on the ID number. The specific operation method is introduced below through an example.

**Step 01** Open a worksheet, as shown in Figure 3-32. Column `A` cells contain virtual ID numbers, and the age needs to be calculated in column `B` cells based on the ID numbers.

![](OEBPS/Images/image_318.jpg)

Figure 3-32

**Step 02** Open the ChatGPT chat window, enter the instruction "In an Excel worksheet, column A cells are ID numbers. I need to calculate age based on the ID numbers. What function formula can be used to solve this?" in the input box. Press `Enter` to send, and ChatGPT will provide the formula for calculating age based on ID numbers, as shown in Figure 3-33.

![](OEBPS/Images/image_319.jpg)

Figure 3-33

**Step 03** Copy the formula provided by ChatGPT. In the Excel worksheet, select the cell range `B2:B6`; paste the copied formula into the formula bar:

```excel
=INT((TODAY()-DATEVALUE(MID(A2,7,4)&"-"&MID(A2,11,2)&"-"&MID(A2,13,2)))/365)
```

As shown in Figure 3-34.

![](OEBPS/Images/image_322.jpg)

Figure 3-34

**Step 04** Press the `Ctrl+Enter` shortcut to confirm, and the age can be calculated in batches based on ID numbers, as shown in Figure 3-35.

![](OEBPS/Images/image_323.jpg)

Figure 3-35

### 048 Determining Gender from ID Number with ChatGPT

![](OEBPS/Images/image_324.jpg)

Scan to watch tutorial video

Usually, in an ID number, if the second-to-last digit is odd, it indicates male; if even, it indicates female. Therefore, users can use ChatGPT to write a calculation formula to determine gender in Excel. The specific operation method is introduced below through an example.

**Step 01** Open a worksheet, as shown in Figure 3-36. Column `A` cells contain virtual ID numbers, and the gender needs to be determined in column `B` cells based on the ID numbers.

![](OEBPS/Images/image_325.jpg)

Figure 3-36

**Step 02** Open the ChatGPT chat window, enter the instruction "In an Excel worksheet, column A cells are ID numbers. I need to determine gender based on the ID numbers. What function formula can be used to solve this?" in the input box. Press `Enter` to send, and ChatGPT will provide the formula for determining gender based on ID numbers, as shown in Figure 3-37.

![](OEBPS/Images/image_326.jpg)

Figure 3-37

**Step 03** Copy the calculation formula provided by ChatGPT. In the Excel worksheet, select the cell range `B2:B6`; paste the copied formula into the formula bar, and change `A1` to `A2`:

```excel
=IF(MID(A2,LEN(A2)-1,1)*1=1,"Male","Female")
```

As shown in Figure 3-38.

![](OEBPS/Images/image_329.jpg)

Figure 3-38

**Step 04** Press the `Ctrl+Enter` shortcut to confirm, and the gender can be determined based on ID numbers, as shown in Figure 3-39.

![](OEBPS/Images/image_330.jpg)

Figure 3-39

### 049 Highlighting Sales Data with ChatGPT

![](OEBPS/Images/image_331.jpg)

Scan to watch tutorial video

In Excel spreadsheets, highlighting data makes it easier for users to identify and analyze data. Users can ask ChatGPT for methods to highlight data in Excel. Generally, ChatGPT provides simpler methods first and details the operation steps. The specific operation method is introduced below through an example.

**Step 01** Open a worksheet, as shown in Figure 3-40. Column `D` contains sales volume. Cells with sales volume exceeding 500 need to be highlighted.

**Step 02** Open the ChatGPT chat window, enter the instruction "In an Excel worksheet, column D is sales volume. I need to highlight cells where sales volume exceeds 500. What method can be used to solve this?" in the input box. Press `Enter` to send, and ChatGPT will provide the method for highlighting sales data, as shown in Figure 3-41.

![](OEBPS/Images/image_332.jpg)

Figure 3-40

![](OEBPS/Images/image_333.jpg)

Figure 3-41

**Step 03** Referring to the method provided by ChatGPT, in the Excel worksheet, select the cell range `D2:D11`. In the "Styles" panel of the "Home" tab, click the "Conditional Formatting" dropdown button; select "New Rule" from the expanded list, as shown in Figure 3-42.

**Step 04** The "New Formatting Rule" dialog box appears. Select "Use a formula to determine which cells to format". In the "Format values where this formula is true" text box, enter the formula provided by ChatGPT, and change `D1` to `D2`:

```excel
=D2>500
```

As shown in Figure 3-43, indicating that conditional formatting starts from cell `D2`.

![](OEBPS/Images/image_338.jpg)

Figure 3-42

![](OEBPS/Images/image_339.jpg)

Figure 3-43

**Step 05** Click the "Format" button. The "Format Cells" dialog box appears. In the "Fill" tab, select the yellow color block, as shown in Figure 3-44.

![](OEBPS/Images/image_340.jpg)

Figure 3-44

**Step 06** Click "OK" to return to the worksheet. Cells with sales volume greater than 500 are now highlighted, as shown in Figure 3-45.

![](OEBPS/Images/image_341.jpg)

Figure 3-45

### 050 Many-to-One Lookup of Department Heads with ChatGPT

![](OEBPS/Images/image_342.jpg)

Scan to watch tutorial video

In an Excel worksheet, a many-to-one lookup refers to querying a single corresponding result from multiple lookup values. Users can use ChatGPT to write a combination formula of the `INDEX` and `MATCH` functions for many-to-one lookups. The `MATCH` function locates the position of the lookup value in another column, and then the `INDEX` function returns the corresponding value based on that position. The specific operation method is introduced below through an example.

**Step 01** Open a worksheet, as shown in Figure 3-46. The left table is the source data query area, and the right table is the query result area. It is necessary to find the corresponding person in charge in column `C` based on the department names in column `E` cells and return the results to column `F`.

![](OEBPS/Images/image_343.jpg)

Figure 3-46

**Step 02** Open the ChatGPT chat window, enter the instruction "In an Excel worksheet, columns A:C are source data, where column B is the department and column C is the person in charge. I need to find the corresponding person in charge based on the department names provided in column E cells and return the results to column F. Write a combination formula using the INDEX and MATCH functions." in the input box. Press `Enter` to send, and ChatGPT will provide the formula for querying the department head, as shown in Figure 3-47.

![](OEBPS/Images/image_344.jpg)

Figure 3-47

**Step 03** Copy the calculation formula provided by ChatGPT. In the Excel worksheet, select the cell range `F2:F3`; paste the copied formula into the formula bar:

```excel
=INDEX(C2:C10,MATCH(E2,B2:B10,0))
```

As shown in Figure 3-48.

![](OEBPS/Images/image_347.jpg)

Figure 3-48

**Step 04** Press the `Ctrl+Enter` shortcut to confirm, and the many-to-one lookup of department heads is completed, as shown in Figure 3-49.

![](OEBPS/Images/image_348.jpg)

Figure 3-49

### 051 Finding Duplicate Order Numbers with ChatGPT

![](OEBPS/Images/image_349.jpg)

Scan to watch tutorial video

In an Excel worksheet, when users need to find duplicate data, they can use ChatGPT to write function formulas to check if a column contains duplicate content. The specific operation method is introduced below through an example.

**Step 01** Open a worksheet, as shown in Figure 3-50. Column `B` contains order numbers. The goal is to find out if there are duplicate order numbers and return the results to column `D` cells.

![](OEBPS/Images/image_350.jpg)

Figure 3-50

**Step 02** Open the ChatGPT chat window, enter the instruction "In an Excel worksheet, column B contains order numbers. I need to find out if there are duplicate order numbers and return the results to column D cells. What method can be used to solve this?" in the input box. Press `Enter` to send, and ChatGPT will provide the formula for finding duplicate order numbers, as shown in Figure 3-51.

**Step 03** Copy the calculation formula provided by ChatGPT. In the Excel worksheet, select the cell range `D2:D9`; paste the copied formula into the formula bar:

```excel
=IF(COUNTIF($B$2:$B$100,B2)>1,"Duplicate","")
```

As shown in Figure 3-52.

![](OEBPS/Images/image_353.jpg)

Figure 3-51

![](OEBPS/Images/image_354.jpg)

Figure 3-52

**Step 04** Press the `Ctrl+Enter` shortcut to confirm, and duplicate orders can be found, as shown in Figure 3-53.

![](OEBPS/Images/image_355.jpg)

Figure 3-53

### 052 Finding Customers by Specified City with ChatGPT

![](OEBPS/Images/image_356.jpg)

Scan to watch tutorial video

In Excel, some functions can look up data from a data range based on specified conditions. For example, in a customer list containing names and cities, to find the customer list based on a specified city, when the user is unsure which function to use, they can let ChatGPT write the function formula. The specific operation method is introduced below through an example.

**Step 01** Open a worksheet, as shown in Figure 3-54. Column `B` contains customer names, and column `C` contains customer cities. The goal is to find the customer list for Beijing.

![](OEBPS/Images/image_357.jpg)

Figure 3-54

**Step 02** Open the ChatGPT chat window, enter the instruction "In an Excel worksheet, column B is customer name, column C is customer city. I need to find the customer list for Beijing and return the results to column E cells. Which function formula can be used?" in the input box. Press `Enter` to send, and ChatGPT will provide the formula for finding the customer list, as shown in Figure 3-55.

![](OEBPS/Images/image_358.jpg)

Figure 3-55

**Step 03** Copy the calculation formula provided by ChatGPT. In the Excel worksheet, select the cell range `E2:E11`; paste the copied formula into the formula bar:

```excel
=IF(C2="Beijing",B2,"")
```

As shown in Figure 3-56.

![](OEBPS/Images/image_359.jpg)

Figure 3-56

**Step 04** Press the `Ctrl+Enter` shortcut to confirm, and the customer list for Beijing can be found, with results as shown in Figure 3-57.

![](OEBPS/Images/image_360.jpg)

Figure 3-57

**Step 05** Next, the blank cells in `E2:E11` need to be deleted. Open the ChatGPT chat window and send the instruction "How to delete blank cells in E2:E11 without affecting other table contents?". After a moment, ChatGPT will provide the method for deleting blanks in a single column, as shown in Figure 3-58.

![](OEBPS/Images/image_361.jpg)

Figure 3-58

![](OEBPS/Images/image_362.jpg)

Figure 3-58 (Continued)

**Step 06** Referring to the method provided by ChatGPT, return to the Excel worksheet, select cell `E1`. In the "Editing" panel of the "Home" tab, expand the "Sort & Filter" list and select "Filter", as shown in Figure 3-59.

**Step 07** After execution, a filter dropdown button is added to cell `E1`. Click this button; in the expanded list, check only the "(Blanks)" checkbox, as shown in Figure 3-60.

![](OEBPS/Images/image_365.jpg)

Figure 3-59

![](OEBPS/Images/image_366.jpg)

Figure 3-60

> **Expert Tip**
>
> Note that the reason for filtering first here, instead of directly performing the "Go To Special" operation for blanks as suggested by ChatGPT, is that although the blank cells appear empty, they contain formulas. Therefore, you need to filter out the blank cells first, clear their contents, and *then* perform the "Go To Special" operation for blanks.

**Step 08** Click "OK" to filter out the blank cells. Select the blank cells, right-click; in the shortcut menu, select "Clear Contents", as shown in Figure 3-61.

![](OEBPS/Images/image_369.jpg)

Figure 3-61

**Step 09** After execution, the formulas in the blank cells are cleared, and the hidden content from filtering is restored. Select the cell range `E2:E11`. In the "Editing" panel of the "Home" tab, expand the "Find & Select" list and select "Go To Special", as shown in Figure 3-62.

**Step 10** The "Go To Special" dialog box appears. Select the "Blanks" radio button, as shown in Figure 3-63.

![](OEBPS/Images/image_370.jpg)

Figure 3-62

![](OEBPS/Images/image_371.jpg)

Figure 3-63

**Step 11** Click "OK" to locate the blank cells. Right-click, and in the shortcut menu, select "Delete", as shown in Figure 3-64.

**Step 12** After execution, the "Delete" dialog box appears. Select the "Shift cells up" radio button, as shown in Figure 3-65.

![](OEBPS/Images/image_372.jpg)

Figure 3-64

![](OEBPS/Images/image_373.jpg)

Figure 3-65

**Step 13** Click "OK" to delete the blank cells in column `E` without affecting other table contents. The result is shown in Figure 3-66.

![](OEBPS/Images/image_374.jpg)

Figure 3-66

### 053 Finding Keywords in a Specified Range with ChatGPT

![](OEBPS/Images/image_375.jpg)

Scan to watch tutorial video

Suppose a user has a product description table and needs to check if a certain column contains "discount". At this time, "discount" can be used as the keyword to search for. Through the function formula written by ChatGPT, the search result of whether there is a discount can be returned. The specific operation method is introduced below through an example.

**Step 01** Open a worksheet, as shown in Figure 3-67. Column `D` contains product promotion information. It is necessary to search column `D` for the keyword "discount" and return the search results to column `E` cells.

**Step 02** Open the ChatGPT chat window, enter the instruction "In an Excel worksheet, column D contains product promotion information. I need to search column D for the keyword 'discount'. If found, return 'Has Discount' in column E cells; if not found, return blank. Which function formula can be used?" in the input box. Press `Enter` to send, and ChatGPT will provide the formula for finding the keyword, as shown in Figure 3-68.

![](OEBPS/Images/image_376.jpg)

Figure 3-67

![](OEBPS/Images/image_377.jpg)

Figure 3-68

> **Expert Tip**
>
> In the formula provided by ChatGPT, the `IF` function is a logical function, introduced in Example 038 of this book. Here we introduce the roles of the `ISNUMBER` and `SEARCH` functions used in this example.
>
> The `ISNUMBER` function is an information function used to check whether a given cell or value is numeric (i.e., a number). It returns a logical value (`TRUE` or `FALSE`) indicating whether the checked value is a number.
>
> The `SEARCH` function is a text function used to search for a specific substring within a text string and returns the starting position of the substring in the text. This function is case-insensitive.

**Step 03** Copy the calculation formula provided by ChatGPT. In the Excel worksheet, select the cell range `E2:E9`; paste the copied formula into the formula bar:

```excel
=IF(ISNUMBER(SEARCH("discount",D2)),"Has Discount","")
```

As shown in Figure 3-69.

![](OEBPS/Images/image_380.jpg)

Figure 3-69

**Step 04** Press the `Ctrl+Enter` shortcut to confirm, and the search results are returned. As shown in Figure 3-70, cells where "discount" is found return "Has Discount", while those not found return blank.

![](OEBPS/Images/image_381.jpg)

Figure 3-70

**Step 05** To make products with discounts stand out more, users can highlight the entire rows of products with discounts in the table. Add filter dropdown buttons in the header cells via the "Filter" function, and filter for "Has Discount" cells, as shown in Figure 3-71.

