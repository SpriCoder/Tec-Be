Java 代理
---

# 1. 什么是代理
1. 动态代理技术是用来生成一个对象的代理对象。
2. 代理对象：
   1. 代理对象存在的价值主要是用于拦截对真实业务对象的访问。
   2. 代理对象应该具有和目标对象(真实业务对象)相同的方法

# 2. Java中的代理
1. 代理对象我们通常也需要编写一个类来生成。
2. JDK 1.5后提供了`Java.lang.reflect.Proxy`，通过`Proxy`类提供的一个`newProxyInstance`方法来创建一个对象的代理对象。
   1. 返回一个代理对象
   2. `ClassLoader`:用来指明生成代理对象使用哪个类装载器。
   3. `Class<?>[] interfaces`:用来指明生成哪个对象的代理对象，通过接口指定。
   4. `InvocationHandler`用来指明产生的这个代理对象要做什么事情。

```java
static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h) 
```

## 2.1. 编写生成代理对象的类
1. Java中规定，要想生成一个对象的代理对象，那么这个对象必须要有一个接口，因此我们编写一个实现了接口的类ConcretePerson

```java
public interface Person {
    /**
     * 唱歌
     *
     * @param name
     * @return
     */
    String sing(String name);

    /**
     * 跳舞
     *
     * @param name
     * @return
     */
    String dance(String name);
}

public class ConcretePerson implements Person {
    /**
     * 唱歌
     *
     * @param name
     * @return
     */
    @Override
    public String sing(String name) {
        System.out.println("ConcretePerson sing " + name + "!");
        return "Finish Singing!";
    }

    /**
     * 跳舞
     *
     * @param name
     * @return
     */
    @Override
    public String dance(String name) {
        System.out.println("ConcretePerson dance " + name + "!");
        return "Finish Dancing!";
    }
}
```

2. 之后为这个类编写具体的代理对象，所有在这个对象上的调用，都会有代理对象来处理

```java
public class ConcretePersonProxy {
    private Person person = new ConcretePerson();

    /**
     * 返回代理对象
     *
     * @return
     */
    public Person getProxy(){
        return (Person) Proxy.newProxyInstance(ConcretePersonProxy.class.getClassLoader(),
                person.getClass().getInterfaces(),
                new InvocationHandler() {
                    /**
                     * 直接使用内部的匿名类来实现接口，是针对InvocationHandler接口的匿名实现类
                     * 在invoke方法编码指定返回的代理对象干的工作，实际上调用person.sing方法欧式调用的invoke中的代码
                     * @param proxy 代理对象自己
                     * @param method 代理对象当前调用的方法
                     * @param args 把方法参数传递进来
                     * @return
                     * @throws Throwable
                     */
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        if(method.getName().equals("sing")){
                            System.out.println("I am Proxy, I will tell ConcretePerson to sing!");
                            return method.invoke(person, args);
                        }
                        if(method.getName().equals("dance")){
                            System.out.println("I am Proxy, I will tell ConcretePerson to dance!");
                            return method.invoke(person, args);
                        }
                        return null;
                    }
                });
    }
}
```


# 3. 参考
1. <a href = "https://www.cnblogs.com/xdp-gacl/p/3971367.html">Java基础加强总结(三)——代理(Proxy)</a>