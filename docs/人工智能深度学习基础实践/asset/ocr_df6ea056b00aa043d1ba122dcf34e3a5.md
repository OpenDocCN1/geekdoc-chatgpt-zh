# 表5-3 汽车油耗量数据集数据的说明

| 数据名称 | 说明 |
|---|---|
| distance | 汽车某次行驶的距离 |
| speed | 汽车某次行驶的平均速度 |
| temp_inside | 汽车在某次行驶时其内部的温度 |
| temp_outside | 汽车在某次行驶时其外部的温度 |
| 92 | 汽车在某次行驶时是否使用 92 号汽油 |
| 98 | 汽车在某次行驶时是否使用 98 号汽油 |
| snow | 汽车在某次行驶时是否下雪 |
| sun | 汽车在某次行驶时是否天晴 |
| rain | 汽车在某次行驶时是否下雨 |
| ac | 汽车在某次行驶时是否开启空调 |
| consume | 汽车行驶油耗量 |

(3) 因数据为 CSV 文件，所以采用 read_csv() 函数进行数据读取，代码如下。

```
data = pd.read_csv('./data/gas_use.csv').drop('Unnamed: 0', axis = 1)
```

# 步骤 3：重命名列表

为了使读取的结果具有更好的可读性，需要对列名进行重命名，代码如下。

```
calNameDict={
    'distance':'距离',
    'speed':'平均速度',
    'temp_inside':'内部温度',
    'temp_outside':'外部温度',
    '92':'92 号汽油',
    '98':'98 号汽油',
    'snow':'下雪',
    'ac':'空调',
    'sun':'晴天',
    'rain':'雨天',
    'consume':'油耗量'
}
data.rename(columns=calNameDict, inplace=True)
```

# 步骤 4：概览数据

(1) 读取数据以后，使用 head() 函数概览数据。改变 head() 函数中参数的值可以查看对应行数的数据情况，此处设置为默认值 5。