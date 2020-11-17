SpringCloud Sidecar
---
1. sidecar是为了方便JVM直接调用Non-JVM应用



# 1. Spring Cloud 集成 Sidecar 整合异构平台微服务s
1. 在Spring Cloud的微服务架构体系中，我们可以通过Sidecar来实现整合Python、Go等其他语言平台的微服务。

## 1.1. Sidecar的主要特点
1. 与应用部署在同一台机器上：第三方平台应用和Sidecar部署在同一个位置
2. 功能与应用独立：即Sidecar不使用应用程序的运行环境与语言
3. 进程间通信：应用和Sidecar一般使用http接口传送json格式数据，以保证其跨语言的通用性。

## 1.2. SpringCloud Sidecar实践

### 1.2.1. 搭建Eureka Server
1. 本文使用的是Hoxton.SR3(version 1.8)
2. 搭建Eureka服务端：注新版本的security中，添加csrf过滤，eureka开启安全策略后，可导致微服务的注册被过滤，我们可以通过Eureka服务端代码中配置来屏蔽csrf的过滤机制。

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.builders.WebSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

@EnableEurekaServer
@SpringBootApplication
public class EurekaServerApplication {

    public static void main(String[] args) {
        SpringApplication.run(EurekaServerApplication.class, args);
    }

    @EnableWebSecurity
    static class WebSecurityConfig extends WebSecurityConfigurerAdapter {
        @Override
        protected void configure(HttpSecurity http) throws Exception {
            http.csrf().disable(); // 配置屏蔽csrf过滤机制
        }
    }
}
```

3. 在Eureka服务端的应用配置文件`application.yml`中，需要完成用户名和密码的配置，以提高Eureka登录安全性，相应部分如下图所示

```yml
spring:
  security:
    user:
      name: user
      password: 123456
```

4. 开启`sidecar`:在Application文件上添加注释`@EnableSidecar`，配置文件中添加相应接口

```yml
sidecar:
  port: 5000
  # 这个是用来检查health的接口
  health-uri: http://localhost:${sidecar.port}/health
# 熔断器等部分
hystrix:
  command:
    default:
      execution:
        isolation:
          thread:
            timeoutInMilliseconds: 10000

ribbon:
  ConnectTimeout: 5000
  ReadTimeout: 5000
```

### 1.2.2. 搭建第三方服务(Flask为例)
1. 配置第三方服务：以Flask为例，必须要有/health接口来确认是否还在。(默认启动端口为5000)

```py
import json

from flask import Flask, Response, make_response, jsonify

app = Flask(__name__)


@app.route('/health')
def health():
    result = {'status': 'UP'}
    return Response(json.dumps(result), mimetype='application/json')


@app.route('/getUser')
def get_user():
    return Response(json.dumps({'username': 'python', 'password': 'python'}),
                    mimetype='application/json')


@app.errorhandler(404)
def not_found(error):
    return make_response(jsonify({'error': 'Not found'}), 404)


if __name__ == '__main__':
    app.run(host="0.0.0.0", port=5000)
```

### 1.2.3. 搭建Consummer服务消费方
1. Consummer-Server的主应用：

```java
import org.springframework.boot.SpringApplication;
import org.springframework.cloud.client.SpringCloudApplication;
import org.springframework.cloud.client.loadbalancer.LoadBalanced;
import org.springframework.cloud.openfeign.EnableFeignClients;
import org.springframework.context.annotation.Bean;
import org.springframework.web.client.RestTemplate;

@EnableFeignClients
@SpringCloudApplication
public class ConsumerServerApplication {

    @Bean
    // 开启负载均衡器Ribbon，注释掉后可以使用http实际地址访问Flask服务API
    @LoadBalanced
    RestTemplate restTemplate() {
        return new RestTemplate();
    }

    public static void main(String[] args) {
        SpringApplication.run(ConsumerServerApplication.class, args);
    }
}
```

2. 配置文件：主应用

```yml
spring:
  application:
    name: consumer-server
  cloud:
    client:
      ipAddress: localhost

server:
  port: 8325

eureka:
  client:
    healthcheck:
      enabled: true
    register-with-eureka: true
    fetch-registry: true
    service-url:
      default-zone: http://${registry.host:localhost}:${registry.port:8761}/eureka/
  instance:
    prefer-ip-address: true
    instance-id: ${spring.cloud.client.ipAddress}:${spring.application.name}:${server.port}
    status-page-url: http://${spring.cloud.client.ipAddress}:${server.port}/

registry:
  host: localhost
  port: 8761
```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.2.6.RELEASE</version>
        <relativePath/> <!-- lookup parent from repository -->
    </parent>
    <groupId>com.example</groupId>
    <artifactId>consumer-server</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <name>consumer-server</name>
    <description>Demo project for Spring Boot</description>

    <properties>
        <java.version>1.8</java.version>
        <spring-cloud.version>Hoxton.SR3</spring-cloud.version>
    </properties>

    <dependencies>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!--Hystrix 依赖 主要是用  @HystrixCommand-->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-hystrix</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
            <exclusions>
                <exclusion>
                    <groupId>org.junit.vintage</groupId>
                    <artifactId>junit-vintage-engine</artifactId>
                </exclusion>
            </exclusions>
        </dependency>
        <!-- 健康检查 -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
    </dependencies>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
            </plugin>
        </plugins>
    </build>
</project>
```

3. Controller接口：

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.client.RestTemplate;

@RestController
public class UserController {

    @Autowired
    private RestTemplate restTemplate;

    @RequestMapping("/java-user")
    public String JavaUser() {
        return "{'username': 'java', 'password': 'java'}";
    }

    @RequestMapping("/python-user")
    public String PythonUser() {
        // 访问第三方服务
        return restTemplate.getForEntity("http://sidecar-server/getUser", String.class).getBody();
    }
}
```

### 1.2.4. 测试
1. 启动顺序:`Flask -> Eureka -> Sidecar -> Consumer`
2. 请求:`localhost:8325/java-user`:有返回
3. 请求:`localhost:8325/python-user`:有返回

## 1.3. 使用FeignClient调用
1. 我们可以将相应的部分配置为FeignClient进行调用。

```java
// 其他服务需要用的话直接调用接口就行
@FeignClient(value = "microservice-name", path="/")
public interface SideCarClient{
  @GetMapping("/query")
  public String query();
}
```

## 直接转发进行调用
1. TODO

# 2. 注意点

## 2.1. POST不支持
1. 请务必注意被调用服务是不是支持通过POST的方式完成调用，比如flask没有写的话默认是只支持Get

# 3. 参考
1. <a href = "https://blog.csdn.net/pierre_/article/details/105587621">SpringCloud通过Sidecar整合异构平台微服务（Python Flask）</a>
2. <a href = "https://www.jianshu.com/p/e970b9a4e61a">springcloud使用 sidecar实现跨语言微服务调用</a>