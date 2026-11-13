# 3. 实现初始化函数：

```
public void Init()
{
if (size % 2 == 0)
size++;
terrain = new float[size, size];
terrain[0, 0] = Random.value;
terrain[0, size-1] = Random.value;
terrain[size-1, 0] = Random.value;
terrain[size-1, size-1] = Random.value;
}
```

4. 定义用于构建地形的函数。这个函数比较长，逻辑在下一步中实现：

```
public void Build()
{
// next steps
}
```

# 5. 初始化要用到的变量：

```
int step = size - 1;
float height = maxHeight;
float r = Random.Range(0, height);
```

6. 创建循环，用于遍历整个网格。后面步骤中的代码在这个循环中：

```
for (int sideLength = size-1; sideLength >= 2; sideLength /= 2)
{
// next steps
}
```

# 7. 实现方形步骤的循环：

```
// SQUARE
int half = size / 2;
int x, y;
for (y = 0; y < size - 1; y += sideLength)
{
for (x = 0; x < size -1; x += sideLength)
{
// next step
}
}
```

# 8. 计算用于方形的角的值：

```
float average = terrain[y,x];
average += terrain[x + sideLength, y];
average += terrain[x, y + sideLength];
average += terrain[x + sideLength, y + sideLength];
average /= 4f;
average += Random.value * 2f * height;
terrain[y + half, x + half] = average
```

# 9. 实现菱形步骤的循环：