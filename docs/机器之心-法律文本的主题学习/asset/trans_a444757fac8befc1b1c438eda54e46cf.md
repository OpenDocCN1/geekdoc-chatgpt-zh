
### 5.2.3 Web Crawler Acquisition

![](images/txt005_18.jpg "pagenumber_ebook=136,pagenumber_book=127")

**Figure 5.18 Deleting Child Nodes**

On the premise of complying with legal regulations and ethical norms, crawler programs can be used to capture and analyze legal texts published on the internet. For example, the Australasian Legal Information Institute (AustLII) has published recent judgments of the Federal Court of Australia (FCA) online, as shown in Figure 5.19:

![](images/txt005_19.jpg "pagenumber_ebook=136,pagenumber_book=127")

**Figure 5.19 FCA Judgments**

The 2006 judgment *Sharman Networks Ltd v Universal Music Australia Pty Ltd* shown in the figure can be accessed via `http://www8.austlii.edu.au/cgi-bin/viewdoc/au/cases/cth/FCA/2006/1.html`. Each judgment includes practice and procedure, cited and discussed cases, plaintiffs and defendants, judgment results, and reasons and basis for the decision. The practice and procedure section summarizes legal points or issues derived and extended by the judge based on the circumstances of the case, facilitating retrieval, reference, citation, discussion, and confirmation of similar legal issues by other legal practitioners. To a certain extent, it can be regarded as keywords describing the case or a slogan used to publicize the case.

The website provides web pages to users via the Hypertext Transfer Protocol (`HTTP`). The client issues a web page request service using a Uniform Resource Locator (`URL`), and the server responds to the request and returns the requested web page. Python's standard library `Requests` provides various `HTTP` connection services, supports automatic encoding of `URL` requests and response data, avoiding inconsistent encoding formats. To preserve state between requests, `session` is used to maintain certain sessions. The `session` object created by the `requests.Session()` method can save some connection request parameters and carry session cookies in a series of requests sent to the same site, giving coherence to different requests. The `HTTP` protocol is based on a client/server access model; the crawler program accesses as a proxy client, and relevant fields of the client to be proxied need to be filled in the `headers` information. The HTML web page returned by the `session.get()` method (i.e., the `HTTP` request) is data in a markup language format. The `BeautifulSoup` module provides functions and services for parsing HTML web pages. As shown in Figures 5.20 and 5.21:

![](images/txt005_20.jpg "pagenumber_ebook=137,pagenumber_book=128")

**Figure 5.20 Requests Request**

![](images/txt005_21.jpg "pagenumber_ebook=138,pagenumber_book=129")

**Figure 5.21 BeautifulSoup Object**

In the `BeautifulSoup` object, many control symbols can be seen. For example, `<title>` is a tag named "title", `<meta charset="utf-8">` is a tag named "meta" with an attribute named "charset" and a value of "utf-8". `bsObj.title` returns the first title tag, `bsObj.title.name` returns the name of the first title tag, `bsObj.title.string` returns the text content contained in the first title tag, and `bsObj.title.parent.name` returns the tag name of the parent tag of the first title tag.

![](images/txt005_22.jpg "pagenumber_ebook=138,pagenumber_book=129")

**Figure 5.22 Operations such as Outputting Tag Names of BeautifulSoup Object**

`bsObj.meta["content"]` outputs the content attribute value of the first meta tag, `bsObj.link["href"]` outputs the href attribute value of the first link tag. Assigning a value to an existing `link["href"]` modifies the attribute value; assigning a value to `link["website"]` which currently has no website attribute adds a new attribute; `del` deletes a specified tag attribute, as shown in Figure 5.23; `bsObj.head.contents` outputs all child nodes of the tag, as shown in Figure 5.24:

![](images/txt005_23.jpg "pagenumber_ebook=139,pagenumber_book=130")

**Figure 5.23 Operations such as Outputting Tag Attributes of BeautifulSoup Object**

![](images/txt005_24.jpg "pagenumber_ebook=139,pagenumber_book=130")

**Figure 5.24 Outputting All Child Nodes of a Tag in BeautifulSoup Object**

`bsObj.find_all('p')` returns all tags named p in a list format, as shown in Figure 5.25. `bsObj.find(charset="utf-8")` outputs the first tag with the attribute charset equal to "utf-8". `bsObj.meta.attrs` returns all attributes of the first meta tag in dictionary form. `bsObj.head.children` outputs all child nodes of the head tag, as shown in Figure 5.26. `tag.attrs.get("class")` finds the value of the class attribute among all attributes of the tag tag. `tag.get_text()` outputs the text content of the tag tag, as shown in Figure 5.27.

![](images/txt005_25.jpg "pagenumber_ebook=140,pagenumber_book=131")

**Figure 5.25 Finding Tags in BeautifulSoup Object**

![](images/txt005_26.jpg "pagenumber_ebook=140,pagenumber_book=131")

**Figure 5.26 Finding Attributes in BeautifulSoup Object**

![](images/txt005_27.jpg "pagenumber_ebook=140,pagenumber_book=131")

**Figure 5.27 Returning Attribute Values in BeautifulSoup Object**

`bsObj.get_text()` gets all text of the BeautifulSoup object, as shown in Figure 5.28. `catchphases` obtains the legal points of the "PRACTICE AND PROCEDURE" section via regular expressions; the returned result is a string containing multiple legal points with some special symbols, as shown in Figure 5.29. `catchphase` obtains legal points in the form of an array list via regular expressions; each item in the list is a legal point, as shown in Figure 5.30.

![](images/txt005_28.jpg "pagenumber_ebook=141,pagenumber_book=132")

**Figure 5.28 BeautifulSoup Object Returns All Text**

![](images/txt005_29.jpg "pagenumber_ebook=141,pagenumber_book=132")

**Figure 5.29 catchphases Obtains Multiple Legal Points Containing Special Symbols**

![](images/txt005_30.jpg "pagenumber_ebook=141,pagenumber_book=132")

**Figure 5.30 catchphase Obtains Legal Points**

Content such as legal points and judgment bases is extracted from HTML (Hypertext Markup Language) format web pages and stored in a standardized manner in `xml` format. An `xml` format legal text is shown in Figure 5.31:

![](images/txt005_31.jpg "pagenumber_ebook=142,pagenumber_book=133")

**Figure 5.31 Legal Text in XML Format**

The first line in the figure is the XML version declaration. `<case>` is the root node, under which there are 4 child nodes: `<name>`, `<AustLII>`, `<catchphrases>`, and `<sentences>`, as shown in Figure 5.32. The `<catchphrases>` node has multiple child nodes `<catchphrase>`, distinguished by the legal point attribute id. The `<sentences>` node has multiple child nodes `<sentence>`, distinguished by the body sentence sequence number attribute id.

![](images/txt005_32.jpg "pagenumber_ebook=142,pagenumber_book=133")

**Figure 5.32 Tree Structure of XML Format Legal Text**

`xml.dom.minidom` in Python is a lightweight Document Object Model (DOM) parser. It can establish a tree structure in memory to store all tags or elements and write the entire content to an XML file. When parsing an XML document, it reads the entire file into memory, and various functions of `minidom` can be used to access and modify the XML structure and content. By using `import` to import the module `xml.dom.minidom` and loading the `parse` parser. `os.listdir(path)` gets all filenames in the directory specified by path, and `sort()` sorts the list, as shown in Figure 5.33:

![](images/txt005_33.jpg "pagenumber_ebook=143,pagenumber_book=134")

**Figure 5.33 Importing xml.dom.minidom Module**

The `parse()` method creates a DOM object based on the XML file. `DOMTree.documentElement` gets the root node. `nodeName` is the node name, `nodeType` is the node type, and `childNodes` are all child nodes of the current node, presented as a list, as shown in Figure 5.34:

![](images/txt005_34.jpg "pagenumber_ebook=143,pagenumber_book=134")

**Figure 5.34 Getting Root Node and Child Nodes**

The method `getElementsByTagName('catchphrase')` gets all catchphrase nodes according to the specified tag name in the parameter and assigns them to `catchtags` as an array list. `catchtags[0]` gets the first catchphrase tag, i.e., `getElementsByTagName('catchphrase')[0]`. The text enclosed by `<catchphrase>` and `</catchphrase>` serves as the child node of catchphrase, and the `data` attribute is the text content. Therefore, `DOMTree.getElementsByTagName('catchphrase')[0].childNodes[0].data` accesses the text content of the data attribute of the child node of the first catchphrase tag through the DOM object. Similarly, `DOMTree.getElementsByTagName('catchphrase')[1].childNodes[0].data` gets the text content of the child node of the second catchphrase tag. The method `getElementsByTagName('sentence')` gets all sentence nodes stored as an array list and assigns them to `sentences`. `sentences[0]` is the first sentence tag. `sentences[0].getAttribute('id')` outputs the value of the id attribute of the first sentence tag. `sentences[0].parentNode.nodeName` returns the tag name of the parent node of the current node, i.e., the sentences tag, as shown in Figures 5.35 and 5.36:

![](images/txt005_35.jpg "pagenumber_ebook=144,pagenumber_book=135")

**Figure 5.35 Getting Catchphrase Node Text Content by Tag Name**

![](images/txt005_36.jpg "pagenumber_ebook=144,pagenumber_book=135")

**Figure 5.36 Getting Sentence Node ID and Text by Tag Name**

The function `xml.dom.minidom.Document()` creates a new DOM object and assigns it to `new_dom`. The method `new_dom.createElement('case')` creates any node through the DOM object, including the root node, second-level nodes, third-level nodes, etc. Here, a root node named case is created by specifying the parameter and assigned to `root_case_node`. The method `new_dom.appendChild(root_case_node)` adds the root node to the new empty DOM tree. The method `new_dom.createElement('name')` creates a node named name and assigns it to `name_node`. `root_case_node.appendChild(name_node)` adds the name node to the child nodes of the root node. The method `new_dom.createTextNode()` creates a text node specified by the parameter and assigns it to `name_text`. `name_node.appendChild(name_text)` adds the text node to the child nodes of the name node. The AustLII node and text content can be added in the same way.

![](images/txt005_37.jpg "pagenumber_ebook=145,pagenumber_book=136")

**Figure 5.37 Adding case, name, and AustLII Nodes**

`new_dom.createElement('catchphrases')` creates a catchphrases node and assigns it to `catchphrases_node`. The method `root_case_node.appendChild(catchphrases_node)` adds the catchphrases node to the child nodes of the root node. `new_dom.createElement('catchphrase')` creates a catchphrase node and assigns it to `catchphrase_node`. The catchphrase node has attributes and text. `catchphrase_node.setAttribute('id', 'c00')` sets the attribute. `new_dom.createTextNode()` can create a text node, and `catchphrase_node.appendChild()` adds the text node as a child node to the catchphrase node. `catchphrases_node.appendChild(catchphrase_node)` adds the catchphrase node to the child nodes of the catchphrases node, as shown in Figure 5.38:

![](images/txt005_38.jpg "pagenumber_ebook=145,pagenumber_book=136")

**Figure 5.38 Adding catchphrases Node and Child Node catchphrase**

`new_dom.createElement('sentences')` creates a sentences node and assigns it to `sentences_node`. The method `root_case_node.appendChild(sentences_node)` adds the sentences node to the child nodes of the root node. `new_dom.createElement('sentence')` creates a sentence node and assigns it to `sentence_node`. The sentence node has attributes and text. `sentence_node.setAttribute('id', 's00')` sets the attribute. `new_dom.createTextNode()` can create a text node, and `sentence_node.appendChild()` adds the text node as a child node to the catchphrase node (Note: original text says "catchphrase节点" here, context suggests "sentence节点"). `sentences_node.appendChild(sentence_node)` adds the sentence node to the child nodes of the sentences node, as shown in Figure 5.39:

![](images/txt005_39.jpg "pagenumber_ebook=146,pagenumber_book=137")

**Figure 5.39 Adding sentences Node and Child Node sentence**

The function `new_dom.writexml()` supports writing the DOM object completely to a disk file in XML format. Parameters include file pointer, padding characters before tags, indentation characters for child nodes, padding characters after tags, and encoding format, as shown in Figure 5.40:

![](images/txt005_40.jpg "pagenumber_ebook=146,pagenumber_book=137")

**Figure 5.40 Writing XML File**

In the figure, `fcase` is the file operation pointer. `indent='  '` means two spaces padding before each tag. `addindent='\t'` means each child node indents one tab position backward. `newl='\n'` means the padding symbol after each tag ends is a newline. `encoding='UTF-8'` means the encoding format is UTF-8, i.e., the encoding attribute value appearing in the XML file header, as shown in Figure 5.41:

![](images/txt005_41.jpg "pagenumber_ebook=147,pagenumber_book=138")

**Figure 5.41 Generated XML File Legal Text**

## 5.3 Processing Raw Legal Text

### 5.3.1 Sentence Segmentation

Raw legal text data generally exists in paragraph form. If sentences need to be obtained from it, sentence segmentation can be performed using relevant Python functions and methods. The legal text data source is an XML format file. Sentence segmentation can use the `xml.dom.minidom` parser to obtain judgment text sentences based on the `<sentence>` tag. As shown in Figure 5.42:

![](images/txt005_42.jpg "pagenumber_ebook=147,pagenumber_book=138")

**Figure 5.42 DOMTree Obtains XML Legal Text**

As can be seen from the figure, `DOMTree.getElementsByTagName("sentence")` returns an array list with a total of 233 sentences. A `for` loop is used to print and output each sentence. If the legal text data source is an HTTP web page, Python's modules `requests` and `BeautifulSoup` can be used to obtain text and parse data. For text sentence segmentation, `nltk`'s `sent_tokenize` function or `polyglot`'s `text` method can be used. As shown in Figure 5.43:

![](images/txt005_43.jpg "pagenumber_ebook=148,pagenumber_book=139")

**Figure 5.43 polyglot or nltk Obtains HTTP Legal Text**

It can be seen that for the same legal text content, the DOMTree method obtains 233 sentences, the polyglot method obtains 249 sentences, while the nltk method returns 219 sentences. Taking the sentence numbered 164 in DOMTree as an example, the corresponding number in polyglot is 181, and in nltk it is 158, as shown in Figures 5.44 and 5.45:

![](images/txt005_44.jpg "pagenumber_ebook=148,pagenumber_book=139")

**Figure 5.44 Corresponding Number in polyglot**
