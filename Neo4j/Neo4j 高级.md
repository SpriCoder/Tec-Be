Neo4j 高级
---

# 1. 索引的创建与删除
```cypher
CREATE INDEX ON :LABEL_NAME(property)
DROP INDEX ON :LABEL_NAME(property)
```

# 2. 如何清空Property Keys(V 3.5)
1. 我们使用数据清空命令后仅仅能清空数据，但是不能清空Property Keys
2. 我们只能通过暂停服务，然后到Neo4j的/data/databases路径下删除对应的graph.db命令来删除

# 3. 快速数据导入(CSV文件导入)
1. <a href = "https://cloud.tencent.com/developer/article/1387710">Neo4j各种导入方式</a>
2. <a href = "https://blog.csdn.net/quiet_girl/article/details/71155442">CSV文件导入数据</a>