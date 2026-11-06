

# 计算皮尔逊相关系数

```python
# Movies rated by both user1 and user2
common_movies = {}
```

提取两个用户都评价的电影：

```python
for item in dataset[user1]:
    if item in dataset[user2]:
        common_movies[item] = 1
```

如果没有普通电影，那么我们将无法计算相似度得分：

```python
num_ratings = len(common_movies)
# If there are no common movies between
# user1 and user2, then the score is 0
```

```python
if num_ratings == 0:
    return 0
```

计算两个用户都已评分的所有电影的评分总和：

```python
# Calculate the sum of ratings of all the common movies
user1_sum = np.sum([dataset[user1][item] for item in common_movies])
user2_sum = np.sum([dataset[user2][item] for item in common_movies])
```

计算两个用户都已评分的所有电影的评分的平方和：

```python
# Calculate the sum of squares of ratings
# of all the common movies
user1_squared_sum = np.sum([np.square(dataset[user1][item]) for item in common_movies])
user2_squared_sum = np.sum([np.square(dataset[user2][item]) for item in common_movies])
```

计算两个输入用户都评价过的所有电影的评价结果的总和：

```python
# Calculate the sum of products of the
# ratings of the common movies
sum_of_products = np.sum([dataset[user1][item] * dataset[user2][item] for item in common_movies])
```

使用前面的计算来计算计算皮尔逊分数所需的各种参数：

```python
# Calculate the Pearson correlation score
Sxy = sum_of_products - (user1_sum * user2_sum / num_ratings)
Sxx = user1_squared_sum - np.square(user1_sum) / num_ratings
Syy = user2_squared_sum - np.square(user2_sum) / num_ratings
```

如果没有偏差，则得分为`0`：

```python
if Sxx * Syy == 0:
```

```python
return 0
```

返回皮尔逊得分：

```python
return Sxy / np.sqrt(Sxx * Syy)
```

定义`main`函数并解析输入参数：

```python
if __name__=='__main__':
    args = build_arg_parser().parse_args()
    user1 = args.user1
    user2 = args.user2
    score_type = args.score_type
```

将文件`ratings.json`中的评级加载到字典中：

```python
ratings_file = 'ratings.json'
```

```python
with open(ratings_file, 'r') as f:
    data = json.loads(f.read())
```

根据输入参数计算相似度得分：

```python
if score_type == 'Euclidean':
    print("\nEuclidean score:")
    print(euclidean_score(data, user1, user2))
else:
    print("\nPearson score:")
    print(pearson_score(data, user1, user2))
```

完整代码在文件`compute_scores.py`中给出。 让我们用一些组合来运行代码。 要计算`David Smith`和`Bill Duffy`之间的欧几里得分数：

```bash
$ python3 compute_scores.py --user1 "David Smith" --user2 "Bill Duffy" --score-type Euclidean
```

如果运行前面的命令，将获得以下输出：

```bash
Euclidean score:
0.585786437627
```

如果要计算同一对之间的皮尔逊得分，请运行以下命令：

```bash
$ python3 compute_scores.py --user1 "David Smith" --user2 "Bill Duffy" --score-type Pearson
```

您将看到以下输出：

```bash
Pearson score:
0.99099243041
```

也可以使用其他参数组合来运行。

在本节中，我们学习了如何计算相似性得分，并了解了为什么在推荐系统的构建中该重要因素。 在下一节中，我们将学习如何通过协同过滤来识别具有相似偏好的用户。

# 使用协同过滤查找相似用户

协同过滤是指在数据集中的对象之间标识模式以决定新对象的过程。 在推荐引擎的上下文中，协同过滤用于通过查看数据集中的相似用户来提供推荐。

通过收集数据集中不同用户的偏好，我们可以协作该信息来过滤用户。 因此，名称为协同过滤。

这里的假设是，如果两个人对一组电影的收视率相似，那么他们对一组新的未知电影的选择也将相似。 通过识别那些普通电影中的模式，可以对新电影做出预测。 在上一节中，我们学习了如何比较数据集中的不同用户。 现在将使用讨论的评分技术在数据集中查找相似的用户。 协同过滤算法可以并行化并在大数据系统（例如 AWS EMR 和 Apache Spark）中实现，从而能够处理数百 TB 的数据。 这些方法可用于各种垂直领域，例如金融，在线购物，市场营销，客户研究等。

让我们开始构建协同过滤系统。

创建一个新的 Python 文件并导入以下包：

```python
import argparse
import json
import numpy as np
```

```python
from compute_scores import pearson_score
```

定义一个函数来解析输入参数。 输入参数是用户的名称：

```python
def build_arg_parser():
    parser = argparse.ArgumentParser(description='Find users who are similar to the input user')
    parser.add_argument('--user', dest='user', required=True,
            help='Input user')
    return parser
```

定义一个函数以在数据集中查找与给定用户相似的用户。 如果用户不在数据集中，则引发错误：

```python
# Finds users in the dataset that are similar to the input user
def find_similar_users(dataset, user, num_users):
    if user not in dataset:
        raise TypeError('Cannot find ' + user + ' in the dataset')
```

计算皮尔森得分的函数已导入。 让我们使用该函数来计算输入用户与数据集中所有其他用户之间的皮尔森得分：

```python
# Compute Pearson score between input user
# and all the users in the dataset
scores = np.array([[x, pearson_score(dataset, user,
        x)] for x in dataset if x != user])
```

分数按降序排列：

```python
# Sort the scores in decreasing order
scores_sorted = np.argsort(scores[:, 1])[::-1]
```

提取输入参数指定的最高`num_users`个用户，并返回数组：

```python
# Extract the top 'num_users' scores
top_users = scores_sorted[:num_users]
```

```python
return scores[top_users]
```

定义`main`函数并解析输入参数以提取用户名：

```python
if __name__=='__main__':
    args = build_arg_parser().parse_args()
    user = args.user
```

从电影分级文件`ratings.json`加载数据。 此文件包含人物名称及其对各种电影的评分：

```python
ratings_file = 'ratings.json'
```

```python
with open(ratings_file, 'r') as f:
    data = json.loads(f.read())
```

查找与输入参数指定的用户相似的前三个用户。 您可以根据自己的选择将其更改为任意数量的用户。 打印输出和分数：

```python
print('\nUsers similar to ' + user + ':\n')
similar_users = find_similar_users(data, user, 3)
print('User\t\t\tSimilarity score')
print('-'*41)
for item in similar_users:
    print(item[0], '\t\t', round(float(item[1]), 2))
```

完整代码在文件`collaborative_filtering.py`中给出。 让我们运行代码，找到像 Bill Duffy 这样的用户：

```bash
$ python3 collaborative_filtering.py --user "Bill Duffy"
```

您将获得以下输出：

![](Images/cb966ca9d12c8bcb4a89b24604a5e395.png)

图 8：用户相似度输出

让我们运行代码，找到像 Clarissa Jackson 这样的用户：

```bash
$ python3 collaborative_filtering.py --user "Clarissa Jackson"
```

您将获得以下输出：

![](Images/c7851fd46b26d632d574990e31cfba5a.png)

图 9：用户相似度输出

在本节中，我们学习了如何在数据集中找到彼此相似的用户，以及如何分配分数来确定用户与另一个用户的相似程度。 在下一节中，我们将把它们放在一起并构建我们的推荐系统。

# 建立电影推荐系统

到目前为止，我们通过了解以下内容为构建推荐系统奠定了基础：

*   提取最近邻
*   建立 K 近邻分类器
*   计算相似度分数
*   使用协同过滤查找相似用户

现在所有的构建块都已就绪，现在该构建电影推荐系统了。 我们学习了构建推荐系统所需的所有基本概念。 在本节中，我们将基于文件`ratings.json`中提供的数据构建电影推荐系统。 此文件包含一组人物及其对各种电影的评分。 要查找给定用户的电影推荐，我们需要在数据集中找到相似的用户，然后提出针对此人的推荐。 让我们开始吧。

创建一个新的 Python 文件并导入以下包：

```python
import argparse
import json
import numpy as np
```

```python
from compute_scores import pearson_score
from collaborative_filtering import find_similar_users
```

定义一个函数来解析输入参数。 输入参数是用户的名称：

```python
def build_arg_parser():
    parser = argparse.ArgumentParser(description='Find recommendations for the given user')
    parser.add_argument('--user', dest='user', required=True,
            help='Input user')
    return parser
```

定义一个函数以获取给定用户的电影推荐。 如果数据集中不存在该用户，则代码将引发错误：

```python
# Get movie recommendations for the input user
def get_recommendations(dataset, input_user):
    if input_user not in dataset:
        raise TypeError('Cannot find ' + input_user + ' in the dataset')
```

定义变量以跟踪分数：

```python
overall_scores = {}
similarity_scores = {}
```

计算输入用户与数据集中所有其他用户之间的相似性得分：

```python
for user in [x for x in dataset if x != input_user]:
    similarity_score = pearson_score(dataset, input_user, user)
```

如果相似度得分小于`0`，则可以继续使用数据集中的下一个用户：

```python
if similarity_score <= 0:
    continue
```

提取当前用户已评级但输入用户未评级的电影列表：

```python
filtered_list = [x for x in dataset[user] if x not in \
                dataset[input_user] or dataset[input_user][x] == 0]
```

对于过滤列表中的每个项目，请根据相似性分数跟踪加权等级。 同时跟踪相似度分数：

```python
for item in filtered_list:
    overall_scores.update({item: dataset[user][item] * similarity_score})
    similarity_scores.update({item: similarity_score})
```

如果没有这样的电影，那么我们将不推荐任何东西：

```python
if len(overall_scores) == 0:
    return ['No recommendations possible']
```

根据加权分数归一化分数：

```python
# Generate movie ranks by normalization
movie_scores = np.array([[score/similarity_scores[item], item]
        for item, score in overall_scores.items()])
```

排序分数并提取电影推荐：

```python
# Sort in decreasing order
movie_scores = movie_scores[np.argsort(movie_scores[:, 0])[::-1]]
```

```python
# Extract the movie recommendations
movie_recommendations = [movie for _, movie in movie_scores]
```

```python
return movie_recommendations
```

定义`main`函数并解析输入参数以提取输入用户的名称：

```python
if __name__=='__main__':
    args = build_arg_parser().parse_args()
    user = args.user
```

从文件`ratings.json`加载电影收视率数据：

```python
ratings_file = 'ratings.json'
```

```python
with open(ratings_file, 'r') as f:
    data = json.loads(f.read())
```

提取电影推荐并打印输出：

