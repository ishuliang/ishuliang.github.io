---
title: SpringBoot和Netty遇到的问题
date: 2024-10-12 15:28:46
image: 20241012.jpg
description: "工作中需要netty来接收hl7报文，启动时遇到了问题，无法启动springboot。"
slug: /20241012
---
> 在工作中，使用netty来开发遇到了两个问题记录一下

## 启动问题

如果直接注册到容器中启动，如下代码所示，那么Netty会阻塞springboot，导致服务并未完全启动。

```java
package top.tangyh.lamp.msg.his.netty;

import lombok.extern.slf4j.Slf4j;
import org.springframework.boot.ApplicationArguments;
import org.springframework.boot.ApplicationRunner;
import org.springframework.scheduling.annotation.Async;
import org.springframework.stereotype.Component;

import javax.annotation.Resource;
import java.net.InetSocketAddress;

/**
 * @author 刘书良
 * @date 2024年10月12日 14:02
 * @describe
 */
@Component
@Slf4j
public class NettyServerStart implements ApplicationRunner {

    private static String HOST = "127.0.0.1";
    private static Integer PORT = 8848;
    @Resource
    private NettyServer nettyServer;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        InetSocketAddress address = new InetSocketAddress(HOST, PORT);
        log.info("neety服务器启动地址: " + HOST + ":" + PORT);
        nettyServer.start(address);
    }
}

```

解决办法就是异步处理，在启动方法添加注解@Async，并且在Spring Boot启动类上开启异步注解，代码如下：

```Java
	   @Override
    @Async
    public void run(ApplicationArguments args) throws Exception {
        InetSocketAddress address = new InetSocketAddress(HOST, PORT);
        log.info("neety服务器启动地址: " + HOST + ":" + PORT);
        nettyServer.start(address);
    }
-------------------------------------
    @SpringBootApplication
@EnableDiscoveryClient
@EnableTransactionManagement
@ComponentScan({
        UTIL_PACKAGE, BUSINESS_PACKAGE
})
@EnableFeignClients(value = {
        UTIL_PACKAGE, BUSINESS_PACKAGE
})
@Slf4j
@EnableFormValidator
@EnableAsync
public class MsgServerApplication {

    public static void main(String[] args) throws UnknownHostException {
        ConfigurableApplicationContext application = SpringApplication.run(MsgServerApplication.class, args);
        Environment env = application.getEnvironment();
        log.info("\n----------------------------------------------------------\n\t" +
                        "应用 '{}' 运行成功! 访问连接:\n\t" +
                        "Swagger文档: \t\thttp://{}:{}/doc.html\n\t" +
                        "数据库监控: \t\thttp://{}:{}/druid\n" +
                        "----------------------------------------------------------",
                env.getProperty("spring.application.name"),
                InetAddress.getLocalHost().getHostAddress(),
                env.getProperty("server.port", "8080"),
                InetAddress.getLocalHost().getHostAddress(),
                env.getProperty("server.port", "8080")

        );
    }
}
    
```



## 依赖加载问题

在netty的handler类中无法注入依赖，这是因为初始化netty时，handler是new出来的没有托管给spring，所以无法依赖注入。

解决办法是：

1️⃣加`@Component`

```Java
@Slf4j
@Component
public class NettyServerHandler extends ChannelInboundHandlerAdapter
```

2️⃣创建静态的handler静态对象

```java
private static NettyServerHandler nettyServerHandler;
```

3️⃣使用`@PostConstruct`初始化

```java
    @PostConstruct
    public void init() {
        nettyServerHandler = this;
    }
```

**在使用时需要在依赖注入前加上handle**

```java
nettyServerHandler.icuObsvRdClient.saveXt(recordDTO);
```

