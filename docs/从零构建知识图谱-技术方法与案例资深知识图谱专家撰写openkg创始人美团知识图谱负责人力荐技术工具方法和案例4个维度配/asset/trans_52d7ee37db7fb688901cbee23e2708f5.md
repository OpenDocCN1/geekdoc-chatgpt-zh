
# 知识图谱导入与关系生成

## 代码实现

```
1. import com.alibaba.fastjson.JSON;  
2. import com.alibaba.fastjson.JSONArray;  
3. import com.alibaba.fastjson.JSONObject;  
4. import org.neo4j.graphdb.Label;  
5. import org.neo4j.unsafe.batchinsert.BatchInserter;  
6. import org.neo4j.unsafe.batchinsert.BatchInserters;  
7.   
8. import java.io.*;  
9. import java.util.*;  
10.   
11.   
12. public class CommonImporter {  
13.     private String nodeDir = "./node.json";  
14.     private String nodeIdDir = "./node_id.txt";  
15.     private String outputPath = "./graph.db";  
16.   
17.     private void generateKG(){  
18.         String lineStr = null;  
19.         int lineCount = 0;  
20.         BatchInserter inserter = null;  
21.         try{  
22.             inserter = BatchInserters.inserter(new File(outputPath).getAbsoluteFile());  
23.             OutputStreamWriter nodeOut = new OutputStreamWriter(  
24.                     new FileOutputStream(new File(nodeIdDir), false));  
25.             BufferedReader bufferedReader = new BufferedReader(  
26.                     new InputStreamReader(new FileInputStream(nodeDir), "UTF-8"));  
27.             while ((lineStr = bufferedReader.readLine()) != null) {  
28.                 lineCount ++;  
29.                 Map<String, Object> properties = new HashMap<>();  
30.                 Label[] labelArray =  new Label[]{};  
31.                 JSONObject propertyJson = JSON.parseObject(lineStr);  
32.                 for(Map.Entry entry:propertyJson.entrySet()){  
33.                     String propertyKey = (String)entry.getKey();  
34.                     Object propertyValue = entry.getValue();  
35.                     if(propertyKey.equals("label")){  
36.                         List<Label> labelList = new ArrayList<>();  
37.                         for (Object singleValue:(JSONArray)propertyValue) {  
38.                             labelList.add(Label.label((String)singleValue));  
39.                         }  
40.                         labelArray = new Label[labelList.size()];  
41.                         labelList.toArray(labelArray);  
42.                     }else{  
43.                         if (propertyValue instanceof String){  
44.                             properties.put(propertyKey, propertyValue);  
45.                         }else{  
46.                             List<String> valueList = new ArrayList<>();  
47.                             for (Object singleValue:(JSONArray)propertyValue) {  
48.                                 valueList.add((String)singleValue);  
49.                             }  
50.                             String[] valueArray = new String[valueList.size()];  
51.                             valueList.toArray(valueArray);  
52.                             properties.put((String) entry.getKey(), valueArray);  
53.                         }  
54.                     }  
55.                 }  
56.   
57.                 long nodeId = inserter.createNode(properties, labelArray);  
58.                 nodeOut.write(nodeId + "\n");  
59.             }  
60.             nodeOut.close();  
61.         }catch (Exception e){  
62.             e.printStackTrace();  
63.             System.out.println("Error text: " + lineStr);  
64.             System.out.println("Error line: " + lineCount);  
65.         }finally{  
66.             if ( inserter != null ) {  
67.                 inserter.shutdown();  
68.             }  
69.         }  
70.     }  
71.   
72.   
73.     public static void main(String[] args){  
74.         CommonImporter test = new CommonImporter();  
75.         test.generateKG();  
76.     }  
77. }  
```

## 导入流程分析

延续流程1中的输出文件格式定义，即`JSON`，这里采用开源工具包`fastjson`[\[1\]](#part0026.html#note_1)来进行解析，第1～3行代码导入了必要的`fastjson`依赖。

整体的处理流程分析如下。

1）读取结点信息文件中的一行`JSON`字符串，流程1中规定了以行为结点单位保存信息。第25～27行代码创建了一个文件读取流。

2）针对每一行结点信息，解析`JSON`字符串，并遍历其中的每一个键值对，即第31～32行代码。

3）针对每一组键值对，若`label`为键，则当前的键值对将被用于构建结点的类别，同时将`JSONArray`对象转换为`Label`类型的数组`labelArray`，对应第35～41行代码。反之，键值对作为属性将被存储于`properties`中，对应第42～54行代码。此时键值对中的值可能存在两种类型，需要通过`instanceof`方法进行类型判断。如果为`String`类型，直接存储即可。如果为`JSONArray`对象，则需要将`JSONArray`对象转换为`String`类型的数组，再存储到`properties`中。

4）回到步骤1，重复以上流程，直到结点信息文件读取完毕。

同时，为了更好地定位可能出现的错误情况，在第62～63行代码中增加了错误信息的输出，即当错误发生时，输出错误信息对应的`JSON`字符串内容以及所在的行数。

## 3. 数据库文件夹及id映射文件

经过步骤2，会生成一个文件夹`graph.db`，即数据库文件夹，以及一个文件`node_id.txt`，即id映射文件。

首先观察一下id映射文件，按照代码中的逻辑，每一行保存的是一个Neo4j结点的id，与结点信息文件中的各行对应。打开该文件后便会发现，第一行的结点id为0，第二行的结点id为1，且id是逐行递增的，表明Neo4j在使用`BatchInserter`生成结点时，id采用的是递增的方式。因此，实体所在的行数即为Neo4j中的实体结点的id。

其次观察`graph.db`文件夹，其包含的内容即Neo4j的数据库文件，由此也证实了，`BatchInserter`可以直接生成Neo4j数据库。在3.3节已经介绍过该文件夹的基本信息，这里不再赘述。下面按照3.3.2节中介绍的Neo4j部署方法，基于已有的数据库文件，启动Neo4j。

### （1）创建路径存放数据库

创建用于存放Neo4j数据库的路径：

```
/kg/general-kg/data/database
```

注意，从`data`开始子目录路径需严格保持一致。将步骤2中生成的`graph.db`数据库文件目录移动到刚刚所创建的路径下。

### （2）启动Neo4j

以`docker`方式启动Neo4j：

```
$ docker run -d \
-p 7474:7474 -p 7687:7687 \
-v /kg/general-kg/data:/var/lib/neo4j/data \
-v /kg/general-kg/import:/var/lib/neo4j/import \
--env=NEO4J_dbms_memory_heap_max__size=10G \
--env=NEO4J_dbms_memory_pagecache_size=10G \
neo4j
```

下面是启动命令中的参数详解（从第2行至第6行依次解读）。

* 将容器内部的7474端口（HTTP前端访问端口）和7687端口（bolt数据库连接端口）暴露到主机中，使其可以在本机中直接访问。
* 将本机路径的`data`目录映射到容器中，持久化存储Neo4j的数据库和账户管理文件。
* 将本机路径的`import`目录映射到容器中，用于后续的关系边数据导入。
* 配置Neo4j的启动参数，将最大的Java heap空间设置为10GB。由于Neo4j基于Java运行，因此所有的查询处理、事务等均需要Java heap空间支持。
* 配置Neo4j的启动参数，将最大的`pagecache`空间设置为10GB。按照Neo4j的缓存机制，Neo4j会将最近查询过的数据加载到内存，这样在后续查询时便可直接获取，提升了数据查询速度。

由于通用知识图谱的数据量级较大，因此需要根据数据的实际情况调整`heap_max__size`和`pagecache_size`的大小，这里设置的参数仅为示例。

### （3）图谱可视化

打开Neo4j前端直观查看图谱情况，将以下URL中的`<host ip>`替换为部署Neo4j的主机ip地址，并在浏览器中打开页面：

```
http://<host ip>:7474
```

详细的Neo4j前端操作方法可参考3.3.3节。

在前端可查看本阶段结点的导入情况，通过以下命令可查看结点的数量：

```
$ MATCH (n) RETURN count(n)
```

通过以下命令，可随机浏览25个结点：

```
$ MATCH (n) RETURN n LIMIT 25
```

## 4. 关系数据文件

关系数据文件用于结点间关系的导入，并将通用知识图谱中的关系持久化存储到Neo4j中。关系数据文件需符合一定格式，以适配下一个步骤中`Load CSV`的要求。首先该文件必须严格遵守`CSV`格式规范，从`Load CSV`的命名便可以得知待导入的文件格式必须是`CSV`。其次，关系中涉及的结点需要和第三步中生成的结点对应起来，最简单的方式便是利用Neo4j的结点id。最后，需要规定`CSV`文件中的字段，由于前文中均采用RDF三元组方式表示，此处也可以直接用`Subject`、`Predicate`、`Object`三个字段作为`CSV`的表头。

延续图4-15的示例内容，在该示例中共存在11条关系边，若转换为以表格的方式呈现，则如表4-5所示。为了区分各个结点，在表中的结点名称前加上了结点类型的前缀。由表4-5可知，该示例已经符合`CSV`的规范，接下来需要解决结点映射的问题。

表4-5　关系三元组示例

![000](images/00108.jpeg)

表4-6是一个结点信息到结点id的映射的示例。事实上，在目前的百科图谱数据中唯一识别一个结点时，仅通过结点名称识别是不够的，还需要通过结点的类型等其他信息。对于`Concept`和`Mention`类型的结点，通过结点名称+结点类型可以唯一确定一个结点，并与结点id对应。但是对于`Entity`类型的结点，则需要通过结点名称+结点id+结点类型来共同唯一确定一个结点。

表4-6　结点信息到结点id的映射

![000](images/00109.jpeg)

通过上述方法，便可将表4-5中的各个结点（`Subject`和`Object`）映射并替换为结点id，再顺利传递给下一步的`Load CSV`，以创建关系边。如前三条关系：

```
<Entity:米粉节, isA, Concept:文化节>
<Entity:米粉节, isA, Concept:节日>
<Entity:北京小米科技有限责任公司, 设立机构, Entity:米粉节>
```

可映射并转换为：

```
<1, isA, 5>
<1, isA, 4>
<0, 设立机构, 1>
```

同时，这里给出一段相应的`CSV`文件生成样例代码：

```
1. import csv  
2.   
3. data_list = [  
4.     ("Subject", "Predicate", "Object"),  
5.     (1, "isA", 5),  
6.     (1, "isA", 4),  
7.     (0, "设立机构", 1),  
8. ]  
9.   
10. with open('rel.csv', 'w') as f:  
11.     f_csv = csv.writer(f)  
12.     f_csv.writerows(data_list)  
```

其中`list`类型的`data_list`初始化了一个数据表，数据表中的第一行数据（对应第4行代码）指定了待生成`CSV`文件的表头，同时也规定了列数为三列。其后的第2～4行代码为样例数据。第10行代码创建了一个文件写入流，在第11行中传递给`CSV`的`writer`方法，同时初始化一个`CSV`写入对象，并在第12行代码中调用了该对象的`writerows`方法，将`data_list`数组写入了文件。代码执行后，可打开`rel.csv`查看文件情况，检查是否符合`CSV`格式。

## 5. 关系生成：Load CSV

`Load CSV`[\[2\]](#part0026.html#note_2)是Neo4j原生支持的数据批量导入工具，作为`Cypher`语言的一部分，可直接在Neo4j的终端中执行。`Load CSV`与`BatchInserter`不同，`BatchInserter`可以在Neo4j数据库关闭的情况下（也必须关闭，无法在已有数据库上操作）直接操作和生成Neo4j数据库文件。而`Load CSV`需要在Neo4j开启的情况下使用，但其优点是采用事务的方式执行，支持数据热更新。

本阶段利用`Load CSV`完成的工作是将剩余的结点间关系边存储到Neo4j中。首先需要读取上一步中生成的关系数据文件，将该数据文件移动到该目录：

```
$ /kg/general-kg/import
```

在流程3中创建Neo4j的Docker容器时，该目录与Docker容器中的Neo4j数据库导入目录进行了映射同步。将关系数据移动到该目录，便可以在Docker容器中同步访问。

之后便可以开始批量导入数据，导入命令如下所示：

```
$ USING PERIODIC COMMIT 10000
LOAD CSV WITH HEADERS FROM "file:///rel.csv" AS line
MATCH (s) where id(s)=toInteger(line.Subject)
MATCH (o) where id(o)=toInteger(line.Object)
MERGE (s)-[:r{name:line.Predicate}]->(o);
```

注意，以上命令需要作为一个整体一次执行。
