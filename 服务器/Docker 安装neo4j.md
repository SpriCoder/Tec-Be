Docker 安装 neo4j
---

# 1. 安装过程
1. 拉取Neo4j的镜像：`docker pull neo4j:3.5`
2. 建立物理盘对应文件夹:`mkdir $HOME/neo4j/data`
3. 运行Neo4j容器:`docker run -it -d --publish=7474:7474 --publish=7687:7687  --volume=$HOME/neo4j/data:/data  neo4j:3.5`
4. 访问Neo4j:`http://xxxx:7474/browser/`，按照指导，来设置连接，初始用户名和密码为`neo4j`，然后重置密码

# 2. 参考
1. <a href = "https://blog.csdn.net/Maple470/article/details/86494128">使用docker安装neo4j</a>
2. <a href = "https://blog.csdn.net/yangfengling1023/article/details/89413232">linux下docker安装neo4j</a>