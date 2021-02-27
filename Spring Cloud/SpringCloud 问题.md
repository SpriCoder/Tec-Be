SpringCloud 问题
---

# 1. Feign Docker无法连接的问题
1. `eureka.instance.prefer-ip-address`
   1. False(默认值)，注册到Eureka中的IP地址则为本机IP地址(Docker不生效)
   2. True，并且设置了`eureka.instance.ip-address`则将该IP地址注册到Eureka中，调用时请求目的地址为该IP地址。
   3. 对应源代码:`return this.preferIpAddress ? this.ipAddress : this.hostname;`

## 1.1. 不同配置方式
1. IDEA调试运行服务:

```
eureka.instance.prefer-ip-address=true
eureka.instance.ip-address=[localhost或者物理机内网IP地址]
```

2. Docker运行（同一物理机）:不同容器分配不同内网IP

```
eureka.instance.prefer-ip-address=true
eureka.instance.ip-address=[localhost或者物理机内网IP地址或者物理机外网IP地址]
```

3. docker运行不同物理机

```
eureka.instance.prefer-ip-address=true
eureka.instance.ip-address=[物理机外网IP地址]
```

## 1.2. 参考
1. <a href = "https://blog.csdn.net/sinat_40735973/article/details/108486954">spring cloud feign docker上无法连接的问题</a>