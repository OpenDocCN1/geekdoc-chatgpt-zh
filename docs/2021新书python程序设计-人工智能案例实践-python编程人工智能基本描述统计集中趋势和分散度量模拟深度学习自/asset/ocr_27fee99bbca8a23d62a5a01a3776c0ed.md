```
In [3]: print(california.DESCR)
.. _california_housing_dataset:

California Housing dataset
------------------------------

**Data Set Characteristics:**

:Number of Instances: 20640

:Number of Attributes: 8 numeric, predictive attributes and the target

:Attribute Information:
- MedInc	median income in block
- HouseAge	median house age in block
- AveRooms	average number of rooms
- AveBedrms	average number of bedrooms
- Population	block population
- AveOccup	average house occupancy
- Latitude	house block latitude
- Longitude	house block longitude

:Missing Attribute Values: None

This dataset was obtained from the StatLib repository.
http://lib.stat.cmu.edu/datasets/

The target variable is the median house value for California districts.

This dataset was derived from the 1990 U.S. census, using one row per census block group. A block group is the smallest geographical unit for which the U.S. Census Bureau publishes sample data (a block group typically has a population of 600 to 3,000 people).

It can be downloaded/loaded using the
:func:'sklearn.datasets.fetch_california_housing' function.

.. topic:: References

- Pace, R. Kelley and Ronald Barry, Sparse Spatial Autoregressions,
Statistics and Probability Letters, 33 (1997) 291-297
```

同样， Bunch对象的data和target属性是NumPy数组， 包含20, 640个样本及其目标值。我们可以通过查看data数组的shape属性来确认样本（行）和特征（列）的数量， 该属性显示出20, 640行8列：

```
In [4]: california.data.shape
Out[4]: (20640, 8)
```

类似地， 可以通过target数组的shape属性来查看目标值的数量（即房价中值）， 确认它和样本数量是匹配的：

```
In [5]: california.target.shape
Out[5]: (20640,)
```

Bunch对象的feature_names属性包含data数组中每列对应的特征名称：