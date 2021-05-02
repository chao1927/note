# 一、Dubbo 源码解析

### 1、Dubbo 核心源码模块

​	**dubbo-common** 模块：公共模块，工具类以及公共逻辑。

​	**dubbo-remoting** 模块：Dubbo 的远程通信模块，其中子模块依赖各种开源组件实现远程通信。

​	**dubbo-rpc** 模块：Dubbo 中对远程调用协议进行抽象的模块，其中抽象了各种协议，依赖与 dubbo-remoting 模块的远程调用功能。

​	**dubbo-cluster** 模块：Dubbo 中负责管理集群的模块，提供了负责均衡，容错，路由等一系列集群相关的功能。

​	**dubbo-registry** 模块：Dubbo 中负责与多种开源注册中心进行交互的模块，提供注册中心的功能。

​	**dubbo-monitor** 模块：Dubbo 的监控模块，主要用于统计服务的调用次数，调用时间以及调用链跟踪的服务。

​	**dubbo-config** 模块：配置解析模块。

​	**dubbo-metadata** 模块： 元数据模块。

​	**dubbo-config-center** 模块：Dubbo 的动态配置模块，主要负责外部化的配置以及服务治理规则的存储与通知，提供了多个子模块用来接入多种开源的服务发现组件



### 2. 服务注册与消费方式

	1. xml 配置
	2. 注解配置
	3. api 方式



### 3. Dubbo 的配置总线

Dubbo 中任意的一个实现都可以抽象为一个 URL，Dubbo 使用 URL 来统一描述了所有对象和配置信息，并贯穿在整个 Dubbo 框架之中。这里我们来看 Dubbo 中一个典型 URL 的示例，如下：

```url
dubbo://172.17.32.91:20880/org.apache.dubbo.demo.DemoService?anyhost=true&application=dubbo-demo-api-provider&dubbo=2.0.2&interface=org.apache.dubbo.demo.DemoService&methods=sayHello,sayHelloAsync&pid=32508&release=&side=provider&timestamp=1593253404714
```



**URL**

```
org.apache.dubbo.common.URL

	public URL(String protocol,
               String username,
               String password,
               String host,
               int port,
               String path,
               Map<String, String> parameters,
               Map<String, Map<String, String>> methodParameters) {
        this.protocol = protocol;
        this.username = username;
        this.password = password;
        this.host = host;
        this.port = Math.max(port, 0);
        this.address = getAddress(this.host, this.port);

        // trim the beginning "/"
        while (path != null && path.startsWith("/")) {
            path = path.substring(1);
        }
        this.path = path;
        if (parameters == null) {
            parameters = new HashMap<>();
        } else {
            parameters = new HashMap<>(parameters);
        }
        this.parameters = Collections.unmodifiableMap(parameters);
        this.methodParameters = Collections.unmodifiableMap(methodParameters);
    }
```



URL 辅助类

```
org.apache.dubbo.common.URLBuilder：辅助构造 URL
org.apache.dubbo.common.URLStrParser：将字符串解析成 URL 对象
```



Dubbo 中使用 URL 的好处

1. 使用 URL 这种公共契约进行上下文信息传递，最重要的就是代码更加易读、易懂，不用花大量时间去揣测传递数据的格式和含义，进而形成一个统一的规

    范，使得代码易写、易读。

2. 使用 URL 作为方法的入参（相当于一个 Key/Value 都是 String 的 Map)，它所表达的含义比单个参数更丰富，当代码需要扩展的时候，可以将新的参数以 

    Key/Value 的形式追加到 URL 之中，而不需要改变入参或是返回值的结构。

3. 使用 URL 这种“公共的契约”可以简化沟通，人与人之间的沟通消耗是非常大的，信息传递的效率非常低，使用统一的契约、术语、词汇范围，可以省去很多沟

    通成本，尽可能地提高沟通效率。



URL 在 Dubbo 中的使用

	1. URL 在 SPI 中的应用

```
@SPI("dubbo")
public interface RegistryFactory {

    /**
     * Connect to the registry
     * <p>
     * Connecting the registry needs to support the contract: <br>
     * 1. When the check=false is set, the connection is not checked, otherwise the exception is thrown when disconnection <br>
     * 2. Support username:password authority authentication on URL.<br>
     * 3. Support the backup=10.20.153.10 candidate registry cluster address.<br>
     * 4. Support file=registry.cache local disk file cache.<br>
     * 5. Support the timeout=1000 request timeout setting.<br>
     * 6. Support session=60000 session timeout or expiration settings.<br>
     *
     * @param url Registry address, is not allowed to be empty
     * @return Registry reference, never return empty value
     */
    @Adaptive({"protocol"})
    Registry getRegistry(URL url);

}
```



2. URL 在服务暴露中的应用

作为服务提供者（Provider）提供的服务资源



3. URL 在服务订阅中的应用

Consumer 启动后会向注册中心进行订阅操作，并监听自己关注的 Provider

```
consumer://...?application=dubbo-demo-api-consumer&category=providers,configurators,routers&interface=org.apache.dubbo.demo.DemoService...
```

其中 Protocol 为 consumer ，表示是 Consumer 的订阅协议，其中的 category 参数表示要订阅的分类，这里要订阅 providers、configurators 以及 routers 三个分类；interface 参数表示订阅哪个服务接口，这里要订阅的是暴露 org.apache.dubbo.demo.DemoService 实现的 Provider。



### 4. Dubbo SPI 精析

Dubbo 为了更好地达到 **OCP** 原则（即“对扩展开放，对修改封闭”的原则），采用了“**微内核+插件**”的架构。那什么是微内核架构呢？微内核架构也被称为插件化架

构（Plug-in Architecture），这是一种面向功能进行拆分的可扩展性架构。内核功能是比较稳定的，只负责管理插件的生命周期，不会因为系统功能的扩展而不断

进行修改。功能上的扩展全部封装到插件之中，插件模块是独立存在的模块，包含特定的功能，能拓展内核系统的功能。

微内核架构中，内核通常采用 Factory、IoC、OSGi 等方式管理插件生命周期，**Dubbo 最终决定采用 SPI 机制来加载插件**，Dubbo SPI 参考 JDK 原生的 SPI 机

制，进行了性能优化以及功能增强。



##### 1. Java SPI 机制

当服务的提供者提供了一种接口的实现之后，需要在 Classpath 下的 META-INF/services/ 目录里创建一个以服务接口命名的文件，此文件记录了该 jar 包提供的

服务接口的具体实现类。当某个应用引入了该 jar 包且需要使用该服务时，JDK SPI 机制就可以通过查找这个 jar 包的 META-INF/services/ 中的配置文件来获得具

体的实现类名，进行实现类的加载和实例化，最终使用该实现类完成业务功能。



##### 2. Dubbo 扩展的 Java SPI 机制

JDK SPI 在查找扩展实现类的过程中，需要遍历 SPI 配置文件中定义的所有实现类，该过程中会将这些实现类全部实例化。如果 SPI 配置文件中定义了多个实现

类，而我们只需要使用其中一个实现类时，就会生成不必要的对象。例如，org.apache.dubbo.rpc.Protocol 接口有 InjvmProtocol、DubboProtocol、

RmiProtocol、HttpProtocol、HessianProtocol、ThriftProtocol 等多个实现，如果使用 JDK SPI，就会加载全部实现类，导致资源的浪费。

**Dubbo SPI 不仅解决了上述资源浪费的问题，还对 SPI 配置文件扩展和修改。**

首先，Dubbo 按照 SPI 配置文件的用途，将其分成了三类目录。

- META-INF/services/ 目录：该目录下的 SPI 配置文件用来兼容 JDK SPI 。
- META-INF/dubbo/ 目录：该目录用于存放用户自定义 SPI 配置文件。
- META-INF/dubbo/internal/ 目录：该目录用于存放 Dubbo 内部使用的 SPI 配置文件。

```
dubbo=org.apache.dubbo.rpc.protocol.dubbo.DubboProtocol
```



##### 3. Dubbo SPI 核心实现

1. @SPI 注解

Dubbo 中某个接口被 @SPI注解修饰时，就表示该接口是**扩展接口**，如：

```
package org.apache.dubbo.rpc;

@SPI("dubbo")
public interface Protocol {

    int getDefaultPort();

    @Adaptive
    <T> Exporter<T> export(Invoker<T> invoker) throws RpcException;

    @Adaptive
    <T> Invoker<T> refer(Class<T> type, URL url) throws RpcException;

    void destroy();

    default List<ProtocolServer> getServers() {
        return Collections.emptyList();
    }

}
```

@SPI 注解的 value 值指定了默认的扩展名称，例如，在通过 Dubbo SPI 加载 Protocol 接口实现时，如果没有明确指定扩展名，则默认会将 @SPI 注解的 value 值

作为扩展名，即加载 dubbo 这个扩展名对应的 org.apache.dubbo.rpc.protocol.dubbo.DubboProtocol 这个扩展实现类，相关的 SPI 配置文件在 dubbo-rpc-

dubbo 模块中，（dubbo-rpc/dubbo-rpc-dubbo/src/main/resources/META-INF/dubbo/internal/org.apache.dubbo.rpc.Protocol）

```
dubbo=org.apache.dubbo.rpc.protocol.dubbo.DubboProtocol
```



2. ExtensionLoader 
3. @Adaptive 注解与适配器
4. 自动包装特性
5. 自动装配特性
6. @Activate 注解与自动激活特性



### 5. 定时任务

通过**时间轮**实现定时任务：时间轮是一种高效的、批量管理定时任务的调度模型。时间轮一般会实现成一个环形结构，类似一个时钟，分为很多槽，一个槽代表一

个时间间隔，每个槽使用双向链表存储定时任务；指针周期性地跳动，跳动到一个槽位，就执行该槽位的定时任务。

<img src="https://s0.lgstatic.com/i/image/M00/40/41/CgqCHl8yQfKAEM41AAB8fTu5PCY623.png" alt="img" style="zoom:50%;" />



Dubbo 中对时间轮的应用主要体现在如下两个方面：

- 失败重试， 例如，Provider 向注册中心进行注册失败时的重试操作，或是 Consumer 向注册中心订阅时的失败重试等。

- 周期性定时任务， 例如，定期发送心跳请求，请求超时的处理，或是网络连接断开后的重连机制。

    

### 6. 注册中心：Zookeeper 与 Curator

### 7. RPC 调用

