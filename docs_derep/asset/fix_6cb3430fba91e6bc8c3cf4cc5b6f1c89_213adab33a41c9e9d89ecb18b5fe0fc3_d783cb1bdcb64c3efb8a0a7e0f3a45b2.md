同名不同域：球场与银幕

先说体育界的 A.J. Wynder，中文常写作 A.J. 万德。这位美国人一九六四年诞生，运动方向归于篮球，资料里标注体重八十二公斤，国籍为美国。

再说影视界的 A.J. Cook，中文写法 A.J. 库克。Cook 在一九七八年七月二十二号出生于 Canada 的 Ontario 省城市 Oshawa，身高一米六九，从事表演工作，国籍为加国。九七年首次出现在 TV 电影中，角色是 Lisa；九九年参演《The Virgin Suicides》，出演五姐妹里的 Mary Lisbon；零三年主演《Final Destination 2》，该片常被拿来作为他最知名的作品。

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