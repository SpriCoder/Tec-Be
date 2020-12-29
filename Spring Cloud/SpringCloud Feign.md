SpringCloud Feign
---

# 1. 访问时间超时
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

# 2. 参考
1. <a href = "https://blog.csdn.net/qq_40369944/article/details/93194159">springcloud feign设置超时时间(Spring boot2.0及以上)</a>