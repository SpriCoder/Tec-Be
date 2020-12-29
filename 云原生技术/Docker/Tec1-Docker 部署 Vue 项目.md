Tec1-Docker部署Vue项目
---
> 推荐先将本地的docker源替换成国内镜像。

# 1. nginx安装和配置
1. 获取nginx镜像:`docker pull nginx`
2. 创建nginx的config配置文件文件

```conf
server {
    # 监听内部的端口号
    listen       80;
    # 修改为docker服务宿主机的IP或域名
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html =404;
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

3. 首页指向到`/usr/share/nginx/html/index.html`

# 2. 生成相应环境包
1. 安装依赖环境：`npm install`
2. 构建：`npm run build`

# 3. 编写Dockerfile文件
```Dockerfile
FROM nginx
MAINTAINER name
RUN rm /etc/nginx/conf.d/default.conf
ADD default.conf /etc/nginx/conf.d/
COPY dist/ /usr/share/nginx/html/
```

1. `FROM nginx`：该镜像是基于nginx:latest镜像构建的
2. `MAINTAINER name`：添加说明
3. `RUN rm /etc/nginx/conf.d/default.conf`：删除目录下的default.conf文件
4. `ADD default.conf /etc/nginx/conf.d/`：将default.conf复制到/etc/nginx/conf.d/下，用本地的default.conf配置来替换nginx镜像里的默认配置
5. `COPY dist/ /usr/share/nginx/html/`：将项目根目录下dist文件夹(构建之后才会生成)下的所有文件复制到镜像/usr/share/nginx/html/目录下 

# 4. 构建docker镜像
1. 构建docker镜像:`docker build -t docker_name .`
2. 查看本地镜像:`docker images | grep docker_name`
3. 启动docker容器:`docker run -d -p 8080:80 --name container_name docker_name`
   1. -d：后台方式启动
   2. -p 8080:80: 端口映射，将宿主机的8080端口映射到容器的80端口
   3. --name：容器名container_name
   4. docker_name：要启动的镜像名称
4. 查看运行的容器:`docker ps`，检查刚刚启动的镜像是否正常运行，如果没有，使用`docker log`命令来查看报错进行修改。

# 5. 补充：跨域设置
1. 创建nginx的config配置文件文件

```conf
server {
    # 监听内部的端口号
    listen       80;
    # 修改为docker服务宿主机的IP或域名
    server_name  localhost;

    location / {
        root   /usr/share/nginx/html;
        index  index.html index.htm;
        try_files $uri $uri/ /index.html =404;
    }
    location /pro-api{ # pro-api是vue项目里.env.production里的地址
      proxy_pass 1.1.1.1;  # 这里写的是你后端接口的地址
    }

    error_page   500 502 503 504  /50x.html;
    location = /50x.html {
        root   html;
    }
}
```

2. 项目中:`.env.production`中配置有这样一行:`VUE_APP_BASE_API = "/pro-api"`

# 6. 参考
1. <a href = "https://www.cnblogs.com/zouzou-busy/p/11838524.html">docker--部署vue项目</a>