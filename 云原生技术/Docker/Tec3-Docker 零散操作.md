Tec3-Docker 零散操作
---

# 1. Docker删除所有无名称镜像(悬空镜像)
1. 针对镜像名为`<none>`的镜像
2. 使用命令：`docker rmi $(docker images -f "dangling=true" -q)`

# 2. 参考
1. <a href = "https://www.cnblogs.com/stulzq/p/8962388.html">Docker 删除所有无名称的镜像（悬空镜像）</a>