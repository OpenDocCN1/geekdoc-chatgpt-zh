```
```csharp
int j = 0;
int i = 0;
int id = 0;
string line;
Vector3 position = Vector3.zero;
Vector3 scale = Vector3.zero;
```
```

# 3. 读取包含高度和宽度的文件头:

```
```csharp
line = strmRdr.ReadLine();// non-important line
line = strmRdr.ReadLine();// height
numRows = int.Parse(line.Split(' ')[1]);
line = strmRdr.ReadLine();// width
numCols = int.Parse(line.Split(' ')[1]);
line = strmRdr.ReadLine();// "map" line in file
```
```

# 4. 初始化成员变量，同时申请内存:

```
```csharp
vertices = new List<Vertex>(numRows * numCols);
neighbours = new List<List<Vertex>>(numRows * numCols);
costs = new List<List<float>>(numRows * numCols);
vertexObjs = new GameObject[numRows * numCols];
mapVertices = new bool[numRows, numCols];
```
```

# 5. 声明用于迭代读取字符的 for 循环:

```
```csharp
for (i = 0; i < numRows; i++)
{
    line = strmRdr.ReadLine();
    for (j = 0; j < numCols; j++)
    {
        // next steps in here
    }
}
```
```

# 6. 根据读取的字符，把逻辑表达式赋值为 true 或 false:

```
```csharp
bool isGround = true;
if (line[j] != '.')
    isGround = false;
mapVertices[i, j] = isGround;
```
```

# 7. 实例化合适的预制件:

```
```csharp
position.x = j * cellSize;
position.z = i * cellSize;
id = GridToId(j, i);
if (isGround)
    vertexObjs[id] = Instantiate(vertexPrefab, position,
    Quaternion.identity) as GameObject;
else
    vertexObjs[id] = Instantiate(obstaclePrefab, position,
    Quaternion.identity) as GameObject;
```
```

# 8. 把新的游戏对象赋值为图的子节点，并清除其名称:

```
```csharp
vertexObjs[id].name = vertexObjs[id].name.Replace("(Clone)",
id.ToString());
```
```