Spring 装配Bean
---

# 1. Spring 配置的可选方案
1. 装配机制
   1. 在XML中进行显式配置
   2. 在Java中进行显式配置
   3. 隐式的bean发现机制和自动装配
2. 原则：
   1. 显式配置越少越好，尽量使用自动配置
   2. 推荐使用类型安全并且比XML更加强大的JavaConfig

# 2. 自动转配Bean
1. 两个角度
   1. 组件扫描(Component Scan)：Spring会自动发现应用上下文所创建的Bean。
   2. 自动转配(autowiring)：Spring自动满足bean之间的依赖。

## 2.1. Bean的配置方式

### 2.1.1. JavaConfig进行配置
1. 组件扫描默认是不启用的，我们需要显式的配置Spring才可以打开，使得Spring自动去找含有@Component注解的类，并为其创建bean，使用如下注解：

```java
@Configuration
@ComponentScan // 如果没有配置，默认扫描当前包和当前包所有的子包
```

### 2.1.2. XML进行配置
1. 使用Spring Context命名空间的元素。

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xmlns:context="http://www.springframework.org/schema/context"
  xmlns:c="http://www.springframework.org/schema/c"
  xmlns:p="http://www.springframework.org/schema/p"
  xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

  <context:component-scan base-package="soundsystem" />

</beans>
```

### 2.1.3. Junit完成测试
```java
package soundsystem;

import static org.junit.Assert.*;

import org.junit.Rule;
import org.junit.Test;
import org.junit.contrib.java.lang.system.StandardOutputStreamLog;
import org.junit.runner.RunWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.test.context.ContextConfiguration;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;

// 在测试开始的时候自动创建Spring的应用上下文
@RunWith(SpringJUnit4ClassRunner.class)
// 告知其在CDPlayerConfig的类中加载配置
@ContextConfiguration(classes=CDPlayerConfig.class)
public class CDPlayerTest {

  @Autowired
  private CompactDisc cd;
  
  @Test
  public void cdShouldNotBeNull() {
    assertNotNull(cd);
  }
  
}
```

### 2.1.4. 更多的细节配置

#### 2.1.4.1. 为组件扫描的Bean命名
1. `@Componet("Bean_Name")`：可以为组件扫描的bean命名
2. `@Named("lonelyHeartsClub")`：Spring支持将@Named作为@Component 注解的替代方案，他们在绝大多数场景中，是可以互相替换的。

#### 2.1.4.2. 设置组件扫描的基础包
1. `@ComponentScan("soundsystem")`
2. `@ComponentScan(basePackages="soundsystem)"`
3. `@ComponentScan(basePackages={"soundsystem", "video"})`
4. `@ComponentScan(basePackageClasses={CDPlayer.class, DVDPlayer.class})`：建议这种方式，可以检查出是否有问题

#### 2.1.4.3. 为Bean添加实现自动装配
1. 使用`@Autowired`注解完成自动装配
   1. 可以修饰构造器。
   2. 可以修饰Setter方法。
   3. 可以修饰变量声明。
   4. 可选参数：
      1. `required=false`:如果没有bean也不会装配。
2. 使用`@Inject`注解：源自于Java依赖注入规范。
3. 在大多数场景下，这两个注解是可以替换的。

## 2.2. 通过Java代码装配Bean

### 2.2.1. 创建配置类
1. 在类上使用`@Configuration`来表明一个类是配置类

### 2.2.2. 声明Bean
1. 在方法上使用`@Bean`来声明一个类为Bean，默认ID和@Bean注解的方法的ID是一致的。
2. 可以重命名bean的名称`@Bean(name="xxx")`

https://potoyang.gitbook.io/spring-in-action-v4/di-2-zhang-zhuang-pei-bean/2.3-tong-guo-java-dai-ma-zhuang-pei-bean/2.3.2-sheng-ming-jian-dan-de-bean