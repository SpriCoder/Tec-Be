Docker 安装 Jenkins
---

# 1. 安装过程
1. 首先拉取镜像:`docker pull jenkins`(我安装的是Jenkins 2.275)
2. 创建本地数据卷:`mkdir -p /data/jenkins_home/`
3. 修改用户授权:`chown -R 1000:1000 /data/jenkins_home/`
4. 启动容器:`docker run -d --name jenkins -p 7900:8080 -p 50000:50000 -v /data/jenkins_home:/var/jenkins_home jenkins/jenkins`
5. 访问对应网址:`xxx:7900`
6. 获取初始管理员密码(两种方式，因为映射了本地逻辑卷)
   1. `docker exec jenkins cat /var/jenkins_home/secrets/initialAdminPassword`
   2. `cat /data/jenkins_home/secrets/initialAdminPassword`
7. 选择安装推荐的插件等待即可
8. 按照流程创建管理员账户
9. 根据之前的情况创建实例，即完成

# 2. 参考
1. <a href = "https://www.jenkins.io/zh/doc/book/installing/">Jenkins官方教程</a>
2. <a href = "https://segon.cn/install-jenkins-using-docker.html">Docker 安装 Jenkins （超详细）</a>
3. <a href = "https://www.cnblogs.com/ningy1009/p/12665716.html">Jenkins 插件安装失败解决办法</a>

# 实用插件
1. <a href = "https://blog.csdn.net/github_39160845/article/details/108960606">Jenkins 嵌入到 Iframe</a>
2. <a href = "https://www.cnblogs.com/kevingrace/p/6019707.html">Jenkins用户组管理</a>


# 3. 配置Jenkins的分布式构建和部署
1. <a href = "https://blog.csdn.net/achenyuan/article/details/86644954">jenkins分布式构建和部署(master-slave)</a>