# 长会话记忆与召回的落地方案

把多轮对话做成可记忆、可召回、可自清理的体系，是长会话落地的关键。下面结合向量召回与关系数据库给出一条可直接复用的思路。

[PRE0]
该片段演示了 `store_conversation_as_vector` 的工作方式，它会把每轮用户的提问和模型的回应逐条编码成向量，统一写入 `vector_store`。

####### 3. 按相似度召回历史片段
进入后续轮次后，系统需要从历史里挑出与当前提问最贴合的部分，做法是把新输入转成向量，与库内已有向量计算相似度，选出匹配度最高的对话来辅助理解对话背景。

下面这段代码演示了如何依据当前输入做召回：
[PRE1]
示例中 `retrieve_relevant_conversation` 先对最新用户的提问进行编码，随后调用 `similarity_search` 在向量库里寻找最接近的记录，从而返回关联的历史对话。

运行结果示例：
[PRE2]
结果显示，针对“什么是机器学习？”这一问题，系统成功命中了过往的相似交流，为生成回答提供了背景参考。

####### 4. 向量库的增量写入与维护策略
在持续交互过程中，新产生的对话会自动编码并追加进存储。随着轮次累积，库的规模会不断膨胀，因此需要定期清理或更新，以保证检索效率。

下面展示自动增量更新的具体实现：
[PRE3]
借助这套写入与清理的组合，开发者能够更好地控制长会话的背景负担，将摘要压缩与向量召回结合，搭建更轻量、更智能的对话系统。

####### 搭建存储与检索的主流程
[PRE4]python

####### 自定义清理函数

def clean_old_vectors(vector_store, max_entries=10):

    # 假设向量存储只保留最近的 max_entries 条记录

    if len(vector_store) > max_entries:

        # 移除较早的向量记录

        vector_store.remove_oldest_entries(len(vector_store) - max_entries)

####### 定期调用清理函数

clean_old_vectors(vector_store, max_entries=5)
[PRE5]bash
>> sudo apt update
>> sudo apt install postgresql postgresql-contrib
[PRE6]bash
>> sudo service postgresql start
[PRE7]bash
>> sudo -u postgres psql
[PRE8]sql
>> CREATE DATABASE chat_db;
[PRE9]sql
>> CREATE USER chat_user WITH PASSWORD 'your_password';
>> GRANT ALL PRIVILEGES ON DATABASE chat_db TO chat_user;
[PRE10]bash
>> \q
[PRE11]bash
> pip install psycopg2
[PRE12]python
###### 连接数据库并创建游标
connection = psycopg2.connect(...)
cursor = connection.cursor()
[PRE13]python
###### 创建聊天消息表
create_table_query = '''
CREATE TABLE IF NOT EXISTS chat_messages (
    id SERIAL PRIMARY KEY,
    user_id VARCHAR(50),
    message_type VARCHAR(10),
    message_content TEXT,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
)
'''
cursor.execute(create_table_query)
connection.commit()
print("聊天消息表创建成功")
[PRE14]python
###### 插入消息记录函数
def insert_message(user_id, message_type, message_content):
    insert_query = '''
    INSERT INTO chat_messages (user_id, message_type, message_content)
    VALUES (%s, %s, %s)
    '''
    cursor.execute(insert_query, (user_id, message_type, message_content))
    connection.commit()
    print("插入成功")
####### 示例插入用户消息和模型回复
insert_message("user_1", "user", "你好，可以告诉我什么是机器学习吗？")
insert_message("user_1", "model", "机器学习是一种通过数据训练模型的技术。")
[PRE15]python
###### 查询消息记录函数
def fetch_messages(user_id, limit=10):
    fetch_query = '''
    SELECT message_type, message_content, timestamp
    FROM chat_messages
    WHERE user_id=%s
    ORDER BY timestamp DESC
    LIMIT %s
    '''
    cursor.execute(fetch_query, (user_id, limit))
    records = cursor.fetchall()
    for record in records:
        print(f"{record[2]}-{record[0]}: {record[1]}")
    return records
####### 示例查询最近 10 条消息
fetch_messages("user_1", limit=10)
[PRE16]python
###### 删除特定用户的消息记录函数
def delete_user_messages(user_id):
    delete_query = '''
    DELETE FROM chat_messages
    WHERE user_id=%s
    '''
    cursor.execute(delete_query, (user_id,))
    connection.commit()
    print("用户的消息记录已删除")
####### 示例删除用户消息
delete_user_messages("user_1")
[PRE17]python
###### 关闭游标和数据库连接
cursor.close()
connection.close()
print("数据库连接已关闭")