
### 5.2.3 爬虫获取

![](images/txt005_18.jpg "pagenumber_ebook=136,pagenumber_book=127")

**图5.18 删除子节点**

在遵循法律规定和道德规范的前提下，可以使用爬虫程序对公开在网络上的法律文本进行数据抓取和分析。例如，澳大利亚法律信息协会 (Australasian Legal Information Institute，AustLII)在网络上公开了澳大利亚联邦法院 (Federal Court of Australia，FCA)近些年的判例，如图5.19所示：

![](images/txt005_19.jpg "pagenumber_ebook=136,pagenumber_book=127")

**图5.19 FCA判例**

图中所示的2006年的沙曼网络有限公司诉环球唱片有限公司的判例可以通过 `http://www8.austlii.edu.au/cgi-bin/viewdoc/au/cases/cth/FCA/2006/1.html` 访问。每个判例包括实践和程序、援引和讨论的判例、原告和被告、判决结果、判决理由和依据。实践和程序这部分内容是法官根据本案情况归纳和引申的法律要点或者法律问题，便于其他法律从业人员进行类似法律问题的检索、查阅、引用、讨论、确认，在某种程度上可以认为是描述该案的关键词或者宣传本案用的标语口号。

网站通过超文本传输协议 (`HTTP`)为用户提供网页，客户端以统一资源定位符 (`URL`)发出网页请求服务，服务器响应请求并返回客户端所请求的网页。Python的标准库`Requests`提供各种`HTTP`连接服务，支持`URL`请求和响应数据的自动编码，避免编码格式的不统一。为了保存请求之间的状态，使用 `session`保持一些会话。`requests.Session()`方法创建的`session`对象可以保存一些连接请求参数，在向同一个站点发送的一系列请求中携带会话cookie，使不同的请求有了连贯性。`HTTP`协议是基于客户端/服务器模式的访问，爬虫程序以代理客户端进行访问，在`headers`信息中需要填充将要代理客户端的相关字段。通过`session.get()`方法即`HTTP`请求返回的html网页是一种标记语言格式的数据，`BeautifulSoup`模块提供对html网页解析的功能和服务。如图5.20和图5.21所示：

![](images/txt005_20.jpg "pagenumber_ebook=137,pagenumber_book=128")

**图5.20 Requests请求**

![](images/txt005_21.jpg "pagenumber_ebook=138,pagenumber_book=129")

**图5.21 BeautifulSoup对象**

在`BeautifulSoup`对象中，可以看到很多控制符号。例如，`<title>`是名称为“title”的标签，`<meta charset="utf-8">`是名称为“meta”的标签，且有一个属性名称为“charset”，属性值为“utf-8”。`bsObj.title`返回第一个 title标签，`bsObj.title.name`返回第一个 title标签的名称，`bsObj.title.string`返回第一个 title标签包含的文本内容，`bsObj.title.parent.name`返回第一个title标签的父标签的标签名称。

![](images/txt005_22.jpg "pagenumber_ebook=138,pagenumber_book=129")

**图5.22 BeautifulSoup对象的输出标签名称等操作**

`bsObj.meta["content"]`输出的是第一个 meta标签的 content属性值，`bsObj.link["href"]`输出的是第一个link标签的href属性值，为已有href属性的`link["href"]`赋值是修改属性值，为目前还没有 website属性的 `link["website"]`赋值是添加一个新属性，`del`是删除一个指定的标签属性，如图5.23所示；`bsObj.head.contents`输出标签的所有子节点，如图5.24所示：

![](images/txt005_23.jpg "pagenumber_ebook=139,pagenumber_book=130")

**图5.23 BeautifulSoup对象的输出标签属性等操作**

![](images/txt005_24.jpg "pagenumber_ebook=139,pagenumber_book=130")

**图5.24 BeautifulSoup对象的输出标签所有的子节点**

`bsObj.find_all('p')` 以列表形式返回所有的名称为p的标签，如图5.25所示。`bsObj.find(charset="utf-8")` 输出第一个属性charset等于“utf-8”的标签，`bsObj.meta.attrs`以字典形式返回第一个 meta标签的所有属性，`bsObj.head.children` 输出 head 标签的所有子节点，如图 5.26 所示。`tag.attrs.get("class")`在tag标签的所有属性中查找class属性的值，`tag.get_text()`输出tag标签的文本内容，如图5.27所示。

![](images/txt005_25.jpg "pagenumber_ebook=140,pagenumber_book=131")

**图5.25 BeautifulSoup对象的查找标签**

![](images/txt005_26.jpg "pagenumber_ebook=140,pagenumber_book=131")

**图5.26 BeautifulSoup对象查找属性**

![](images/txt005_27.jpg "pagenumber_ebook=140,pagenumber_book=131")

**图5.27 BeautifulSoup对象返回属性值**

`bsObj.get_text()`获取 BeautifulSoup对象的所有文本，如图 5.28所示。`catchphases`通过正则表达式获取“PRACTICE AND PROCEDURE”部分的法律要点，返回结果是包含一些特殊符号的多个法律要点的字符串，如图5.29所示。`catchphase`通过正则表达式以数组列表形式获取的法律要点，列表的每一项是一个法律要点，如图5.30所示。

![](images/txt005_28.jpg "pagenumber_ebook=141,pagenumber_book=132")

**图5.28 BeautifulSoup对象返回所有文本**

![](images/txt005_29.jpg "pagenumber_ebook=141,pagenumber_book=132")

**图5.29 catchphases获取包含特殊符号的多个法律要点**

![](images/txt005_30.jpg "pagenumber_ebook=141,pagenumber_book=132")

**图5.30 catchphase获取法律要点**

对超文本传输标记语言格式的`html`网页提取法律要点和判决依据等内容，按照`xml`格式进行规范存储，一个`xml`格式的法律文本如图5.31所示：

![](images/txt005_31.jpg "pagenumber_ebook=142,pagenumber_book=133")

**图5.31 xml格式的法律文本**

图中的第一行是xml的版本说明，`<case>`是根节点，下面有4个子节点，分别是`<name>`、`<AustLII>`、`<catchphrases>`和`<sentences>`，如图 5.32 所示。`<catchphrases>`节点下有多个子节点`<catchphrase>`，不同的`<catchphrase>`以法律要点属性id区分。`<sentences>`节点下有多个子节点`<sentence>`，不同的`<sentence>`以正文句子序号属性id区分。

![](images/txt005_32.jpg "pagenumber_ebook=142,pagenumber_book=133")

**图5.32 xml格式法律文本的树形结构**

Python中的 `xml.dom.minidom`是一个轻量级的文件对象模型 (Document Object Model，DOM)解析器，可以在内存中建立一个树结构来保存所有标签或元素，并且将整个内容写入到xml文件，在解析xml文档时将整个文件读入，使用`minidom`各个函数可以访问和修改xml结构和内容。通过`import`导入模块`xml.dom.minidom`，并加载`parse`解析器。`os.listdir(path)` 获取由path指定的目录下的所有文件名，`sort()`对列表进行排序，如图5.33所示：

![](images/txt005_33.jpg "pagenumber_ebook=143,pagenumber_book=134")

**图5.33 导入xml.dom.minidom模块**

方法`parse()`根据xml文件创建DOM对象，`DOMTree.documentElement`获取根节点，`nodeName`是节点名称，`nodeType`为节点类型，`childNodes`是当前节点的所有的子节点，以列表形式呈现，如图5.34所示：

![](images/txt005_34.jpg "pagenumber_ebook=143,pagenumber_book=134")

**图5.34 获取根节点和子节点**

`getElementsByTagName('catchphrase')`方法根据参数指定的标签名称获取所有的catchphrase节点，并以数组列表形式赋值给`catchtags`。`catchtags[0]`是获取的第一个 catchphrase标签，即 `getElementsByTagName('catchphrase')[0]`。由`<catchphrase>`和`</catchphrase>`包围的文本作为catchphrase的子节点，`data`属性是文本内容。因此，`DOMTree.getElementsByTagName('catchphrase')[0].childNodes[0].data`通过DOM对象访问第一个catchphrase标签的子节点的data属性的文本内容，同理，`DOMTree.getElementsByTagName('catchphrase')[1].childNodes[0].data`获取第二个catchphrase标签的子节点的文本内容。`getElementsByTagName('sentence')`方法获取以数组列表保存的所有的sentence节点，并赋值给 `sentences`。`sentences[0]`是第一个 sentence标签，`sentences[0].getAttribute('id')`方法是输出第一个 sentence标签的 id属性的值。`sentences[0].parentNode.nodeName`返回当前节点的父节点的标签名称，即 sentences标签，如图5.35和图5.36所示：

![](images/txt005_35.jpg "pagenumber_ebook=144,pagenumber_book=135")

**图5.35 按标签名称获取catchphrase节点文本内容**

![](images/txt005_36.jpg "pagenumber_ebook=144,pagenumber_book=135")

**图5.36 按标签名称获取sentence节点id和文本**

函数`xml.dom.minidom.Document()`创建一个新的 DOM对象，并赋值给`new_dom`，方法`new_dom.createElement('case')`通过DOM对象创建任何节点，包括根节点，第二层节点和第三层节点，等等，此处通过指定参数创建名称为case的根节点并赋值给`root_case_node`。方法`new_dom.appendChild(root_case_node)`将根节点加入到新的空DOM树。方法`new_dom.createElement('name')`创建名称为name的节点并赋值给`name_node`，`root_case_node.appendChild(name_node)`将name节点加入到根节点的子节点。方法`new_dom.createTextNode()`创建由参数指定的文本节点并赋值给`name_text`，`name_node.appendChild(name_text)`将文本节点加入到name节点的子节点。以同样的方法可以添加AustLII节点和文本内容。

![](images/txt005_37.jpg "pagenumber_ebook=145,pagenumber_book=136")

**图5.37 添加case、name和AustLII节点**

`new_dom.createElement('catchphrases')` 创建 catchphrases 节点并赋值给`catchphrases_node`，方法`root_case_node.appendChild(catchphrases_node)` 将catchphrases节点加入到根节点的子节点。`new_dom.createElement('catchphrase')` 创建catchphrase节点并赋值给`catchphrase_node`，catchphrase节点具有属性和文本，通过`catchphrase_node.setAttribute('id', 'c00')` 设置属性，`new_dom.createTextNode()`可以创建文本节点，通过`catchphrase_node.appendChild()`将文本节点以子节点形式加入到catchphrase节点。`catchphrases_node.appendChild(catchphrase_node)`将catchphrase节点加入catchphrases节点的子节点，如图5.38所示：

![](images/txt005_38.jpg "pagenumber_ebook=145,pagenumber_book=136")

**图5.38 添加catchphrases节点和子节点catchphrase**

通过`new_dom.createElement('sentences')` 创建 sentences节点并赋值给 `sentences_node`，方法`root_case_node.appendChild(sentences_node)`将sentences节点加入到根节点的子节点。`new_dom.createElement('sentence')` 创建sentence节点并赋值给 `sentence_node`，sentence节点具有属性和文本，通过 `sentence_node.setAttribute('id', 's00')` 设置属性，`new_dom.createTextNode()` 可以创建文本节点，通过`sentence_node.appendChild()`将文本节点以子节点形式加入到catchphrase节点。`sentences_node.appendChild(sentence_node)`将sentence节点加入sentences节点的子节点，如图5.39所示：

![](images/txt005_39.jpg "pagenumber_ebook=146,pagenumber_book=137")

**图5.39 添加sentences节点和子节点sentence**

函数`new_dom.writexml()`支持DOM对象按照xml格式完整地写入到磁盘文件，参数包括文件指针、标签前填充字符、子节点缩进字符、标签后填充字符和编码格式，如图5.40所示：

![](images/txt005_40.jpg "pagenumber_ebook=146,pagenumber_book=137")

**图5.40 写入xml文件**

在图中，`fcase`为文件操作指针，`indent='  '`为每个标签前有两个空格填充，`addindent='\t'`为每个子节点向后缩进一个制表符位置，`newl='\n'`为每个标签结束后填充符号是换行，`encoding='UTF-8'`为编码格式是UTF-8，即在XML文件头部出现的encoding属性值，如图5.41所示：

![](images/txt005_41.jpg "pagenumber_ebook=147,pagenumber_book=138")

**图5.41 生成xml文件的法律文本**

## 5.3 处理原始法律文本

### 5.3.1 分句

原始法律文本数据一般是以段落形式存在的，如果需要获取其中的句子，可以通过Python相关的函数和方法来分句。法律文本数据源是xml格式文件，句子分割可以使用`xml.dom.minidom`解析器，根据`<sentence>`标签获取判决文本句子。如图5.42所示：

![](images/txt005_42.jpg "pagenumber_ebook=147,pagenumber_book=138")

**图5.42 DOMTree获取xml法律文本**

从图中可以看出，`DOMTree.getElementsByTagName("sentence")`返回的结果是数组列表，一共有233个句子，使用`for`循环打印输出每个句子。如果法律文本数据源是http网页，可以使用Python的模块`requests`和`BeautifulSoup`来获取文本和解析数据，对文本的分句可以使用`nltk`的`sent_tokenize`函数，或者使用`polyglot`的`text`方法。如图5.43所示：

![](images/txt005_43.jpg "pagenumber_ebook=148,pagenumber_book=139")

**图5.43 polyglot或者nltk获取http法律文本**

可以看出，对于相同的法律文本内容，DOMTree方法获得233个句子，polyglot方法获取249个句子，而nltk方法返回219个句子。以DOMTree中的序号为164的句子为例，在polyglot中对应的是序号181，在nltk中对应的是序号158，如图 5.44 和图 5.45 所示：

![](images/txt005_44.jpg "pagenumber_ebook=148,pagenumber_book=139")

**图5.44 polyglot中对应的序号**
