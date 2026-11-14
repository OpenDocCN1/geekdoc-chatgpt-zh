
**Step 06** Select the filtered product rows, expand the `Fill Color` list box in the `Font` panel on the `Home` tab, and select the `Gold, Accent 4, Lighter 80%` color swatch under `Theme Colors`, as shown in Figure 3-72, to set the background color for product rows with discounts.

![](OEBPS/Images/image_382.jpg)

▲Figure 3-71

![](OEBPS/Images/image_383.jpg)

Figure 3-72

**Step 07** After performing the operation, restore all data in the table to highlight the product information with discounts, with the result shown in Figure 3-73.

![](OEBPS/Images/image_384.jpg)

Figure 3-73

# Chapter 4 ChatGPT + Excel: Intelligent Loading of AI Assistant

## Learning Tips

In Excel, users can integrate the AI assistant ChatGPT directly into Excel for use by loading an add-in. This add-in provides a convenient way to incorporate ChatGPT's powerful language processing capabilities into the Excel workflow.

## Chapter Highlights Navigation

![](OEBPS/Images/image_385.png)

### 4.1 Loading the ChatGPT Add-in

By loading the ChatGPT add-in in Excel, users can converse with ChatGPT in the worksheet and utilize its intelligent natural language understanding and generation capabilities to perform various tasks. This combination will greatly improve user efficiency in Excel; without switching to other applications or browsers, users can get help from ChatGPT directly in Excel. This section mainly introduces the operation methods for loading the ChatGPT add-in.

#### 054 Load ChatGPT Add-in

![](OEBPS/Images/image_386.jpg)

Scan to watch tutorial video

In Excel, users can access the ChatGPT add-in via the `Get Add-ins` feature in the `Add-ins` panel on the `Insert` tab. The specific operation methods are described below.

**Step 01** Open a blank worksheet, click the `Get Add-ins` button in the `Add-ins` panel on the `Insert` tab, as shown in Figure 4-1.

**Step 02** After performing the operation, the `Office Add-ins` dialog box appears, as shown in Figure 4-2. It displays multiple popular add-ins, including free ones and paid ones.

![](OEBPS/Images/image_387.jpg)

▲Figure 4-1

![](OEBPS/Images/image_388.jpg)

Figure 4-2

**Step 03** Enter `ChatGPT` in the search box, as shown in Figure 4-3.

**Step 04** Click the `Search` button ![](OEBPS/Images/image_389.jpg) to find add-ins related to ChatGPT. Click the `Add` button on the right side of the `ChatGPT for Excel` add-in, as shown in Figure 4-4.

**Step 05** A `Please wait` dialog box pops up; click the `Continue` button, as shown in Figure 4-5.

**Step 06** Wait a moment for the ChatGPT add-in to load. Once integrated into Excel, the `ChatGPT for Excel` add-in icon will appear at the end of the `Home` tab, as shown in Figure 4-6.

![](OEBPS/Images/image_390.jpg)

▲Figure 4-3

![](OEBPS/Images/image_391.jpg)

▲Figure 4-4

![](OEBPS/Images/image_392.jpg)

Figure 4-5

**Step 07** Click the `ChatGPT for Excel` add-in icon to expand the `ChatGPT for Excel` add-in pane. The user needs to enter the key in the `Your OpenAI API Key` text box at the bottom of the pane to use the `ChatGPT for Excel` add-in, as shown in Figure 4-7.

![](OEBPS/Images/image_393.jpg)

▲Figure 4-6

![](OEBPS/Images/image_394.jpg)

Figure 4-7

#### 055 Obtain OpenAI API Key

![](OEBPS/Images/image_395.jpg)

Scan to watch tutorial video

OpenAI is an artificial intelligence research laboratory and technology company, while ChatGPT is a natural language processing model developed by OpenAI. Integrating ChatGPT in Excel requires the use of an OpenAI API Key. The operation method for obtaining the key is described below.

**Step 01** First, the user needs to visit the ChatGPT website and log in to their account, then go to the OpenAI official website, and click the `Log in` button in the upper right corner of the webpage, as shown in Figure 4-8.

![](OEBPS/Images/image_396.jpg)

Figure 4-8

**Step 02** After performing the operation, enter the OpenAI page and select the `API` module, as shown in Figure 4-9.

![](OEBPS/Images/image_397.jpg)

Figure 4-9

**Step 03** Since ChatGPT was visited and logged into previously, the OpenAI account will be logged in automatically here. If you skipped logging into ChatGPT and went directly to the OpenAI webpage, you would need to log in to the OpenAI account first. ![](OEBPS/Images/image_398.jpg) Click the `Account Avatar` in the upper right corner; ![](OEBPS/Images/image_399.jpg) select the `View API keys` option from the pop-up list, as shown in Figure 4-10.

**Step 04** Enter the API keys page, where the table displays previously obtained key records. Here, click the `Create new secret key` button, as shown in Figure 4-11.

![](OEBPS/Images/image_400.jpg)

▲Figure 4-10

![](OEBPS/Images/image_401.jpg)

Figure 4-11

**Step 05** The `Create new secret key` dialog box appears; click the `Create secret key` button below the text box, as shown in Figure 4-12.

![](OEBPS/Images/image_402.jpg)

Figure 4-12

**Step 06** After performing the above operations, the key is created. Click the ![](OEBPS/Images/image_403.jpg) `Copy` button on the right side of the text box, as shown in Figure 4-13, to obtain the created key. Create a Notepad file in a folder and paste the key into it to save.

![](OEBPS/Images/image_404.jpg)

Figure 4-13

#### 056 Enter ChatGPT Add-in Key

![](OEBPS/Images/image_405.jpg)

Scan to watch tutorial video

After obtaining the API key from the OpenAI official website, return to the Excel worksheet, expand the `ChatGPT for Excel` add-in pane, ![](OEBPS/Images/image_406.jpg) enter the obtained key in the `Your OpenAI API Key` text box at the bottom of the pane; ![](OEBPS/Images/image_407.jpg) click the `SAVE` button, as shown in Figure 4-14.

After performing the operation, the application is approved successfully, the API key is applied successfully, and the user is prompted that the corresponding AI functions can be used in the worksheet, as shown in Figure 4-15.

![](OEBPS/Images/image_408.jpg)

▲Figure 4-14

![](OEBPS/Images/image_409.jpg)

Figure 4-15

In the `ChatGPT for Excel` add-in pane, the functions of the various AI functions prompted are as follows.

◎ `=AI.ASK`: Get answers to questions.
◎ `=AI.LIST`: Merge row data to generate list output.
◎ `=AI.FILL`: Fill incomplete ranges, automatically generate or fill continuous sequences.
◎ `=AI.EXTRACT`: Extract specific types of data.
◎ `=AI.FORMAT`: Convert numbers and date formats to specified formats.
◎ `=AI.TRANSLATE`: Translate text from one language to another.

### 4.2 Using ChatGPT AI Functions

The ChatGPT add-in provides 6 AI functions. Users can use these AI functions in Excel worksheets to obtain AI-generated answers. This section will introduce the operation methods for using ChatGPT AI functions in Excel worksheets.

#### 057 Use AI.ASK Function to Get Data Analysis Results

![](OEBPS/Images/image_410.jpg)

Scan to watch tutorial video

In Excel, users can ask ChatGPT questions in any cell and get corresponding answers through the `AI.ASK` function. The specific operation method for writing a formula with the `AI.ASK` function to ask ChatGPT for product sales analysis results is described below.

**Step 01** Open a worksheet, as shown in Figure 4-16. Cell B1 lists the question to be asked to ChatGPT.

![](OEBPS/Images/image_411.jpg)

Figure 4-16

**Step 02** Enter the AI function formula in cell B2: `=AI.ASK(B1)`, as shown in Figure 4-17.

![](OEBPS/Images/image_412.jpg)

Figure 4-17

**Step 03** Press the `Enter` key to confirm. If `#BUSY!` appears in the cell, it indicates that the reply is loading, as shown in Figure 4-18.

![](OEBPS/Images/image_413.jpg)

Figure 4-18

**Step 04** Wait a moment to get ChatGPT's data analysis results in cell B2, as shown in Figure 4-19.

![](OEBPS/Images/image_414.jpg)

Figure 4-19

**Expert Tips**

If users don't know how to use ChatGPT AI functions, they can select the `Examples Spreadsheet` option in the `ChatGPT for Excel` add-in pane, and view and learn AI function examples on the redirected webpage, referencing the example formulas for use.

#### 058 Use AI.EXTRACT Function to Extract Specified Content

![](OEBPS/Images/image_415.jpg)

Scan to watch tutorial video

In Excel, users can extract specified types of information from text through the `AI.EXTRACT` function. This function helps users automatically identify and extract keywords, dates, addresses, and other important information from text for further analysis and processing. The specific operation method for using the `AI.EXTRACT` function to extract specified content is described below.

**Step 01** Open a worksheet, as shown in Figure 4-20. Need to extract the content specified in column B cells from the text in column A cells.

![](OEBPS/Images/image_416.jpg)

Figure 4-20

**Step 02** Enter the AI function formula in cell C2: `=AI.EXTRACT(A2,B2)`, as shown in Figure 4-21.

![](OEBPS/Images/image_417.jpg)

Figure 4-21

**Step 03** Press the `Enter` key to confirm to extract the city from the text in cell A2, as shown in Figure 4-22.

![](OEBPS/Images/image_418.jpg)

Figure 4-22

**Step 04** After performing the operation, drag the fill handle at the lower right corner of cell C2 to fill the formula down to cell C7 to batch extract specified content, as shown in Figure 4-23.

![](OEBPS/Images/image_419.jpg)

Figure 4-23

**Expert Tips**

If the returned result shows `API error (code 429) - Too Many Requests`, you can wait a moment, then select the corresponding cell, click in the formula bar, and press the `Enter` key several times to return the extracted content.

#### 059 Use AI.FILL Function to Automatically Generate Continuous Sequences

![](OEBPS/Images/image_420.jpg)

Scan to watch tutorial video

In Excel, the AI function `AI.FILL` is used to automatically generate continuous sequences or fill repeating patterns in cells. This function helps users quickly generate number sequences, date sequences, custom text sequences, or repeating patterns and fill them into specified cell ranges. The specific operation method for using the `AI.FILL` function to automatically generate continuous sequences is described below.

**Step 01** Open a worksheet, as shown in Figure 4-24. Need to generate continuous serial numbers in column A cells.

**Step 02** Enter the starting value 1 in cell A2, as shown in Figure 4-25.

![](OEBPS/Images/image_421.jpg)

▲Figure 4-24

![](OEBPS/Images/image_422.jpg)

Figure 4-25

**Step 03** Enter the AI function formula in cell A3: `=AI.FILL(A2,10)`, as shown in Figure 4-26, indicating the starting value is the value in cell A2, filling 10 cells backward.

**Step 04** Press the `Enter` key to confirm to generate continuous serial numbers, as shown in Figure 4-27.

![](OEBPS/Images/image_423.jpg)

▲Figure 4-26

![](OEBPS/Images/image_424.jpg)

Figure 4-27

#### 060 Use AI.FORMAT Function to Convert Data Formats

![](OEBPS/Images/image_425.jpg)

Scan to watch tutorial video

In Excel, the AI function `AI.FORMAT` is mainly used to convert numbers or date formats to specified formats. This function helps users customize the display of numbers or dates according to needs, including decimal places, thousands separators, currency symbols, and date formats, etc. The specific operation method for using the `AI.FORMAT` function to convert data formats is described below.

**Step 01** Open a worksheet, as shown in Figure 4-28. Need to convert the data format in column A cells to the format specified in column B cells.

**Step 02** Select cell C2, enter the AI function formula in the formula bar: `=AI.FORMAT(A2,B2)`, as shown in Figure 4-29.

![](OEBPS/Images/image_426.jpg)

▲Figure 4-28

![](OEBPS/Images/image_427.jpg)

Figure 4-29

**Step 03** Press the `Enter` key to confirm to convert the data format in cell A2 to the format specified in cell B2, as shown in Figure 4-30.

**Step 04** After performing the above operation, drag the fill handle at the lower right corner of cell C2 to fill the formula down to cell C6 to batch convert data formats, as shown in Figure 4-31.

![](OEBPS/Images/image_428.jpg)

▲Figure 4-30

![](OEBPS/Images/image_429.jpg)

Figure 4-31

**Expert Tips**

When writing the `AI.FORMAT` function formula, the 1st argument is the value, and the 2nd argument is the format. If the table does not provide a specified format, you can write the format directly into the formula. For example, enter the formula in cell C2: `=AI.FORMAT(A2,"0000年00月00日")`, press `Enter` to confirm to convert the format.

#### 061 Use AI.LIST Function to Merge Text Content of Each Row

![](OEBPS/Images/image_430.jpg)

Scan to watch tutorial video

In Excel, the AI function `AI.LIST` merges a series of values or text into a single string, facilitating subsequent processing or display. The specific operation method for using the `AI.LIST` function to merge text content of each row is described below.

**Step 01** Open a worksheet, as shown in Figure 4-32. Need to merge the text content in column A cells into column B cells and separate each row of text with a顿号 (、).

**Step 02** Enter the AI function formula in cell B2: `=AI.LIST("合并文本",A2:A6)`, as shown in Figure 4-33.

**Step 03** Press the `Enter` key to confirm to merge the text in the cell range A2:A6 together, separated by顿号 (、), as shown in Figure 4-34.

![](OEBPS/Images/image_431.jpg)

▲Figure 4-32

![](OEBPS/Images/image_432.jpg)

▲Figure 4-33

![](OEBPS/Images/image_433.jpg)

Figure 4-34

#### 062 Use AI.TRANSLATE Function to Translate Text

![](OEBPS/Images/image_434.jpg)

Scan to watch tutorial video

In Excel, the AI function `AI.TRANSLATE` translates specified text according to the provided translation dictionary, helping users achieve text translation functionality in Excel. The operation method for using the `AI.TRANSLATE` function to translate text is described below.

**Step 01** Open a worksheet, as shown in Figure 4-35. The worksheet provides Chinese and English vocabulary; need to translate Chinese to English and English to Chinese.

![](OEBPS/Images/image_435.jpg)

Figure 4-35

**Step 02** ![](OEBPS/Images/image_436.jpg) Select cell range B2:B3; ![](OEBPS/Images/image_437.jpg) enter the AI function formula in the formula bar: `=AI.TRANSLATE(A2,"中文简体")`, as shown in Figure 4-36.

**Step 03** After performing the operation, press the `Ctrl+Enter` shortcut to confirm to translate English into Simplified Chinese, as shown in Figure 4-37.

![](OEBPS/Images/image_438.jpg)

▲Figure 4-36

![](OEBPS/Images/image_439.jpg)

Figure 4-37

**Step 04** ![](OEBPS/Images/image_440.jpg) Select cell range B4:B6; ![](OEBPS/Images/image_441.jpg) enter the AI function formula in the formula bar: `=AI.TRANSLATE(A4,"英文")`, as shown in Figure 4-38.

**Step 05** Press the `Ctrl+Enter` shortcut to confirm to translate Chinese into English, as shown in Figure 4-39.

![](OEBPS/Images/image_442.jpg)

▲Figure 4-38

![](OEBPS/Images/image_443.jpg)

Figure 4-39

# Chapter 5 ChatGPT + VBA: Automatically Calculate Table Data

## Learning Tips

`VBA` (`Visual Basic for Applications`) is an automation programming language. In Excel, it can be used to create macros, automate tasks, interact with table data, analyze data, process data, and calculate data, etc. Combining ChatGPT with VBA can achieve more powerful automation functions, creating smarter and more flexible macro code.

## Chapter Highlights Navigation

![](OEBPS/Images/image_444.png)

## Basic Operations of VBA Editor

### 5.1 Basic Operations of VBA Editor
