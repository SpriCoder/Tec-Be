SpringCloud Feign
---

# 访问时间超时
```yml
feign:
  client:
    config:
      #default代表所有服务
      default: 
        #feign客户端建立连接超时时间
        connect-timeout: 10000
        #feign客户端建立连接后读取资源超时时间
        read-timeout: 20000
      #而service-test表示当调用service-test这个服务时，用下面的配置
      service-test:
        connect-timeout: 10000
        read-timeout: 20000
```