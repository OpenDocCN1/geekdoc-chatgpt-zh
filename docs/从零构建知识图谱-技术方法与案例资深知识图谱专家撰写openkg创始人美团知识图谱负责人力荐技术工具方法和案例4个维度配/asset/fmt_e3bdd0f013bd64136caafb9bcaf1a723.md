
# 知识图谱建模与存储

## 建模原则

### 1. 通用知识图谱的建模特点
通用知识图谱基于百科数据，原始数据质量一般，无法在初期形成较为完善的模式定义，需要不断迭代优化。若将定义不够充分的概念直接作为结点的类型，那么一旦需要更新，其代价将会很大。例如“组织”这一概念，若在后期发现应当采用更加合适的“组织机构”替换该定义，便需要修改所有标为“组织”的结点，可能是几十万甚至几百万的数量级，需要进行大量的数据库操作。但如果采用结点方式建模和存储这个概念，便可以直接修改“组织”结点的名称，仅需要一次数据库修改。因此结点的建模方式灵活，适用于不确定性数据和数据迭代。

### 2. 概念层建模
概念层除了与实体层之间存在映射关系，其内部还刻画了概念的上下位和同位关系，可以视为一个独立子图。如图4-15中，“文化节”为“节日”的下位概念。采用结点的建模方式，可以完整保留概念层的子图结构。而如果采用实体标签的方式存储，则会丢失该信息，需要其他方式建模和存储概念层的上下位和同位关系。因此结点的建模方式是契合概念层的图结构需求的方式。

## 结点属性
`Neo4j`中结点属性的存储，需要满足键值对形式。

整体上，对于每一个结点，需要固定设置一个字段`name`，用于存储该结点的名称。对于表述来说，`name`为表述的具体文本内容；对于实体来说，`name`指实体的标准名称；对于概念来说，`name`指概念的名称。

此外，实体结点可以存储更多与该实体相关的属性。如图4-15所示，“小米公司”结点包含“成立时间”和“公司口号”两个属性。

## 结点间的关系
通过关系，可以将结点连接到一起。在图4-15中，存在4种类型的关系。

1）实体->实体：实体间的具体关系，由百科数据决定。示例中，米粉节实体和小米公司实体之间存在的关系为“设立机构”。

2）实体->表述：实体指向表述的关系，采用一个固定的名称`reference`。

3）实体->概念：实体指向概念的关系，采用一个固定的名称`isA`。

4）概念->概念：概念间的上下位关系，出结点为入结点的下位概念，同样定义为`isA`。

## 4.3.2 知识存储
在定义完知识的存储模型后，可以正式进入知识存储阶段。

图4-16是完整的百科数据导入流程图，从原始数据的输入，到最终形成存储于`Neo4j`数据库的通用知识图谱。除了对数据进行基本加工，如格式转换，还将使用两个主要的`Neo4j`数据导入工具，即`BatchInserter`和`Load CSV`。前面3.3节介绍了`Neo4j`的各个导入方式，这里不再赘述。

![000](images/00106.jpeg)

图4-16 数据导入流程图

接下来，针对流程中的各个子模块分别进行详细阐述。

### 1. 结点数据文件
结点数据文件，需要包含图谱存储的所有结点的信息，包括结点类型和结点属性。以下是结点数据文件的一个样例，是基于图4-15样例中的结点信息：

```
{"name": "北京小米科技有限责任公司", "label": ["Entity"], "url_name": "北京小米科技有限责任公司", "url_id": "3250213", "disambiguation": ["小米公司"], "成立时间": "2010-3", "公司口号": ["小米为发烧而生","探索黑科技"]}
{"name": "米粉节", "label": ["Entity"], "url_name": "米粉节", "url_id": "", "disambiguation": [], "节日时间": "4-6"}
{"name": "公司", "label": ["Concept"]}
{"name": "组织机构", "label": ["Concept"]}
{"name": "文化节", "label": ["Concept"]}
{"name": "节日", "label": ["Concept"]}
{"name": "北京小米科技有限责任公司", "label": ["Mention"]}
{"name": "小米", "label": ["Mention"]}
{"name": "MI", "label": ["Mention"]}
{"name": "米粉节", "label": ["Mention"]}
```

在这份样例数据中，每一行表示一个独立的结点数据。以换行符为分隔符，样例中共计10行10个结点。数据格式采用`JSON`，可以匹配`Neo4j`结点属性的存储格式要求，即键值对形式。

注意，数据格式不是强制的，读者也可以自行规定结点数据文件的保存格式。只需要确保在流程的第二阶段，利用`BatchInserter`批量导入该数据时，程序可以正常读取结点的信息即可。

可以发现，每个结点的数据中都包含两个必要字段，分别是`name`和`label`，分别代表结点的名称和结点的类型。注意，`label`字段的字段值为数组类型，这么做是允许一个结点拥有多个结点类型，也是`Neo4j`原生支持的结点存储方式。

实体类型的结点（`Entity`）包含三个固定的字段。如示例中的“北京小米科技有限责任公司”和“米粉节”，这三个字段分别如下。

1）`url_name`，对应百度百科词条`url`的词条名称部分，与`url_id`组合唯一识别一个实体，并与一个唯一的百度百科词条对应。

2）`url_id`，对应百度百科词条`url`的词条数字`id`部分，与`url_name`组合唯一识别一个实体，并与一个唯一的百度百科词条对应。

3）`disambiguation`，百科对于同名实体的概括性消歧短语，可能存在多个。

基于`Python`对结点数据进行处理时，可以将`Python`原生的`dict`数据结构保存为`JSON`类型的字符串格式，样例代码如下：

```
1. import json  
2.   
3. node_dict = {  
4.     "name": "北京小米科技有限责任公司",  
5.     "label": ["Entity"],  
6.     "url_name": "北京小米科技有限责任公司",  
7.     "url_id": "3250213",  
8.     "disambiguation": ["小米公司"],  
9.     "成立时间": "2010-3",  
10.    "公司口号": ["小米为发烧而生", "探索黑科技"]  
11. }  
12. node_json_str = json.dumps(node_dict, ensure_ascii=False)  
13. with open("test_node.json", "w") as f_out:  
14.     f_out.write(node_json_str + "\n")
```

其中主要利用的是`json`包。第3～11行构建了一个`dict`类型的结点数据样例。第12行利用`json`包的`jumps()`方法将`node_dict`转换为`JSON`格式的字符串。其中参数`ensure_ascii`表示生成的字符编码格式，当置为`True`（默认）时，生成的字符串的中文将以`ASCII`编码方式呈现，丧失可读性。第13～14行则将生成的`JSON`格式的字符串写入文件中。

若想要预览某个结点的数据，可以在`JSON`包的`jumps()`方法中加入参数`indent`，手动控制缩进，使生成的`JSON`字符串具有更好的可读性，具体代码如下：

```
node_json_str = json.dumps(node_dict, ensure_ascii=False, indent=4)
print(node_json_str)
```

格式化`JSON`的呈现效果如下所示：

```
{
    "name":"北京小米科技有限责任公司",
    "label":[
        "Entity"
    ],
    "url_name":"北京小米科技有限责任公司",
    "url_id":"3250213",
    "disambiguation":[
        "小米公司"
    ],
    "成立时间":"2010-3",
    "公司口号":[
        "小米为发烧而生",
        "探索黑科技"
    ]
}
```

### 2. 结点生成：BatchInserter
`BatchInserter`是`Neo4j`原生支持的批量数据导入工具，在生成结点方面效率非常高，并且基于`Java`语言，可以动态适配输入数据的格式。但`BatchInserter`的缺点是内存开销较大，尤其在生成边关系时，约为结点生成所需内存的6倍，而通用知识图谱的实体量级通常在千万级别，关系很可能达到亿级别，因此，在整个通用知识图谱的存储流程中，`BatchInserter`将作为结点生成的主要手段，而结点间关系的构建则在第4阶段，利用`Load CSV`方法实现。

由于`BatchInserter`需要采用`Java`语言编写，若读者对`Java`不是特别熟悉，可跳过本流程的后续内容。在确保结点信息文件的格式与流程1保持一致，并以`node.json`命名后，便可直接执行命令批量生成结点：

```
Java -jar Neo4jImporter.jar
```

以上命令实际是对后续介绍的代码进行的封装。

利用`BatchInserter`进行的核心操作为生成结点，这里先以一个简单的示例了解一下`BatchInserter`生成`Neo4j`数据库文件的基本方法，如下代码所示：

```
1. import org.neo4j.graphdb.Label;  
2. import org.neo4j.unsafe.batchinsert.BatchInserter;  
3. import org.neo4j.unsafe.batchinsert.BatchInserters;  
 
5. import java.io.File;  
6. import java.util.HashMap;  
7. import java.util.Map;  
8.   
9. public class DemoImporter {  
10.     private void generateSampleKG(){  
11.         String outputPath = "./graph.db";  
12.   
13.         Map<String, Object> properties = new HashMap<String, Object>(){{  
14.                 put("name", "北京小米科技有限责任公司");  
15.                 put("url_name", "北京小米科技有限责任公司");  
16.                 put("url_id", "3250213");  
17.                 put("disambiguation", new String[]{"小米公司"});  
18.                 put("成立时间", "2010-3");  
19.                 put("公司口号", new String[]{"小米为发烧而生", "探索黑科技"});  
20.             }  
21.         };  
22.         Label[] labelArray =  new Label[]{Label.label("Entity")};  
23.   
24.         BatchInserter inserter = null;  
25.         try{  
26.             inserter = BatchInserters.inserter(new File(outputPath).getAbsoluteFile());  
27.             long nodeId = inserter.createNode(properties, labelArray);  
28.         }catch (Exception e){  
29.             e.printStackTrace();  
30.         }finally {  
31.             if ( inserter != null ) {  
32.                 inserter.shutdown();  
33.             }  
34.         }  
35.     }  
36.   
37.     public static void main(String[] args){  
38.         DemoImporter demoImporterTest = new DemoImporter();  
39.         demoImporterTest.generateSampleKG();  
40.     }  
41. }  
```

首先观察第24～34行的代码，它整体由一个`try`、`catch`、`finally`的异常捕捉处理块构成，是涉及`BatchInserter`操作的主要代码。第24行声明了一个`BatchInserter`对象，第26行通过指定`Neo4j`数据库文件生成的目录路径，初始化了`BatchInserter`对象。第27行则基于`properties`（结点属性，必须为`Map`类型）和`labelArray`（结点类型，必须为`Array`类型）两个变量创建了一个结点，关于这两个变量的详情稍后进行分析。第29行代码用于输出异常信息。第31～33行表示，当`BatchInserter`对象不为空时，执行`shutdown()`操作，只有在该操作执行后，`Neo4j`才能完成数据的持久化存储。同时，以上关于`BatchInserter`操作的代码，需要依赖`Neo4j`的库，即第1～3行代码导入的库。

`properties`变量，位于代码的第13～21行，用于存放结点的属性键值对。沿用小米公司的示例，这里使用`Java`内置的`HashMap`类型存放公司属性信息，等价于`Python`的`dict`类型。在初始化构造函数中添加了6个键值对。注意，`disambiguation`和“公司口号”这两个属性的属性值类型与其他属性有所不同，由于它们的属性值可能不唯一且包含多个，因此采用的是`Array`类型。在3.3节中曾经介绍过`Neo4j`支持的`Python`内置数据类型，同样的，`Neo4j`也支持部分`Java`数据类型，具体的映射关系可参考图4-17。值得注意的是，从实践中发现，`List`类型不支持在创建结点时作为属性值存在，但是`Array`类型可以。这可能是`Neo4j`的`Java`版本驱动与`BatchInserter`所在包略有不同所导致的。

![000](images/00107.jpeg)

图4-17 `Neo4j`变量类型与`Java`变量类型的映射关系

注：https://neo4j.com/docs/developer-mannal/3.3/drivers/cypher-values/。

`labelArray`变量，位于代码第22行，用于存放结点的类型，为`Array`类型。数组中的变量则为`Label`类型，`Label`是`Neo4j`的一种内置数据类型。本示例以字符串“`Entity`”初始化了一个`Label`对象，并存放到了`labelArray`中。

基于以上生成思路，只需要将变量`properties`和`labelArray`的赋值转为动态读取结点信息，加上一个数据的预处理流程，便可实现结点的批量生成。修改后的代码如下所示：
