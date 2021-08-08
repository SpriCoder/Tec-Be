Python 操作 Neo4j
---

# 1. 相关Python的依赖库
```py
import py2neo
# or
from py2neo import Graph, Node, Relationship, PropertyDict ...
```

- 查看py2neo的版本:`py2neo.__version__`

# 2. 数据库连接
```py
graph = Graph(host="127.0.0.1", auth=('username', 'password'))
```

# 3. 节点

## 3.1. 创建节点
```py
node = Node("Node_Class", name="Node_Name", id="Node Id", ...) # properties in key-value form.
# create the node
graph.create(node)
```

## 3.2. 匹配节点

### 3.2.1. 使用match查找匹配节点
```py
# get node from node list
graph.nodes[1]
graph.nodes.get[1]
# get node after match
graph.match("Node_Class", name="Node_Name")
graph.match("Node_Class", name="Node_Name").first()
```

### 3.2.2. 使用Cypher查询
```py
graph.run("MATCH(a: Node_Class) RETURN a.name").data()
```

### 3.2.3. 使用Node Matching
```py
matcher = NodeMatcher(graph)
nodes = matcher.match(name="Node_Name").first()
# 找到所有名字以Node开头的节点
nodes = matcher.match().where("_.name=~'Node.*'")
# 上述结果再根据name排序
nodes = matcher.match().where("_.name=~'Node.*'").order_by("_.name")
# 限制返回5条结果
nodes = matcher.match().where("_.name=~'Node.*'").order_by("_.name").limit(5)
```

# 4. 关系

## 4.1. 创建关系
```py
node1 = Node("Node_Class", name="Node_Name", id="Node Id", ...)
node2 = Node("Node_Class", name="Node_Name", id="Node Id", ...)
relation = Relationship(node1, 'relation_type', node2)
# 添加关系的值
relation["field"] = value
graph.create(relation)
```

## 4.2. 匹配关系
```py
matcher = RelationMatcher(graph)
# 查找所有关系
relations = matcher.match()
# 根据类型查找关系
relations = matcher.match(r_type="type")
# 将结果集进行排序
relations = matcher.match(r_type="type").order_by()
# 限制返回5条结果
relations = matcher.match(r_type="type").order_by().limit(5)
```

# 5. 快速使用事务创建图
```py
# 使用Subgraph, nodes和relations是对应的列表
subgraph = Subgraph(nodes, relations)
graph.create(subgraph)
```

# 6. 参考
1. <a href = "https://www.jianshu.com/p/da84712ef62b">python通过py2neo模块操作Neo4j图数据库</a>