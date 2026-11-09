同名不同界：A.J.万德与A.J.库克

先说篮球那位。A.J. Wynder 中文常译作 A.J.万德，1964 年出生，国籍是美国，运动领域为篮球，登记体重为 82 公斤。

再看影视那位。A.J. Cook 译为 A.J.库克，1978 年 7 月 22 日出生于加拿大安大略省奥沙瓦，职业演员，国籍加拿大，身高 1.69 米。代表作被普遍认为是《死神来了2》。演艺起步于 1997 年，在电视电影《父亲大人》中客串 Lisa；1999 年以《处女之死》作为银幕处女作，饰演五姐妹之一的 Mary Lisbon；2003 年在《死神来了2》中担纲主演。

[PRE0]

{

[PRE1]

curl -XPUT 'localhost:9200/demo?pretty' -H 'Content-Type: application/json' -d'

{

  "mappings": {

    "person": {

      "properties": {

        "subj": {"type": "keyword"},

        "height": {"type": "integer"},

        "weight": {"type": "integer"},

        "po": {

          "type": "nested", 

          "properties": {

            "pred": {"type": "keyword"},

            "obj": {"type": "keyword"}

          }

        }

      }

    }

  }

}

'

[PRE2]

curl -XGET 'localhost:9200/demo/person/_search?&pretty' -H 'Content- Type: application/json' -d'

{

  "query":{

    "bool":{

      "must":{

        "term":{"subj":"姚明"}

      }

    }

  }

}

[PRE3]python

def _search_multihop_SP(parts):

    has_done = parts[0]

    v = parts[0]

    for i in range(1, len(parts)):

        en = _entity_linking(v)  # 判断知识库中是否存在名为v的实体

        if not len(en):

            return '执行到：' + has_done, '==> 对应的结果为：' + v + ', 知识库中没有该实体：' + v

        card, msg = _search_single_subj(en[-1])  # 同上，检索实体v

        p = _map_predicate(parts[i])  # 判断知识库中是否存在以part[i]为名称的属性

        if not len(p):

            return '执行到：' + has_done, '==> 知识库中没有该属性：' + parts[i]

        p = p[0]

        if p not in card:  # 判断该实体是否存在以part[i]为名称的属性

            return '执行到：' + has_done, '==> 实体 ' + card['subj'] + ' 没有属性'

        v = str(card[p])

        has_done += ':' + parts[i]

    return v, 'done'

[PRE4]json

{

  "range": {

    "height": {

      "gte": 200

    }

  }

}

[PRE5]json

{

  "nested": {  # 查询 nested object

    "path": "po",  # 制定 nested oblect 位置

    "query": {

      "bool": {

        "must": [

          {"bool": {"must_not": {"term": {"po.obj": "中国"}}}},

          {"term": {"po.pred": "nationality"}}

        ]

      }

    }

  }

}