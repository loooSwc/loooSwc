---
title: SpringCloud
date: 2017-11-09 17:38:02
tags:
  - SpringCloud
---
## 1. 目标

* 了解springcloud是什么
* 如何实现springcloud基本功能
* 如何将springcloud应用到我们的程序中

## 2. springcloud是什么
Spring Cloud是基于Spring Boot的一整套实现微服务的框架。他提供了微服务开发所需的配置管理、服务发现、断路器、智能路由、微代理、控制总线、全局锁、决策竞选、分布式会话和集群状态管理等组件。最重要的是，跟spring boot框架一起使用的话，会让你开发微服务架构的云服务非常好的方便。 Spring Cloud包含了非常多的子框架，其中，Spring Cloud Netflix是其中一套框架，由Netflix开发后来又并入Spring Cloud大家庭，它主要提供的模块包括：服务发现、断路器和监控、智能路由、客户端负载均衡等。。

微服务是可以独立部署、水平扩展、独立访问（或者有独立的数据库）的服务单元，springcloud就是这些微服务的大管家，采用了微服务这种架构之后，项目的数量会非常多，springcloud做为大管家需要管理好这些微服务，也是需要很多服务的支持。

主要的服务有：<font color="red">Spring Cloud Config、Spring Cloud Netflix（Eureka、Hystrix、Zuul、Archaius…）、Spring Cloud Bus、Spring Cloud for Cloud Foundry、Spring Cloud Cluster、Spring Cloud Consul</font>、Spring Cloud Security、Spring Cloud Sleuth、Spring Cloud Data Flow、Spring Cloud Stream、Spring Cloud Task、Spring Cloud Zookeeper、Spring Cloud Connectors、Spring Cloud Starters、Spring Cloud CLI，每个都有自己独特的作用下面来做一一介绍
 **<font color="red">注:标红的为springcloud核心功能</font>**

### 2.1 Spring Cloud Netflix
#### 2.1.1 Netflix Eureka
 服务中心，云端服务发现，一个基于 REST 的服务，用于定位服务，以实现云端中间层服务发现和故障转移。这是springcloud最为核心的部分，服务中心，任何服务需要其它服务的支持什么都需要从这里来取，同样的你有什么新的服务也都需要注册上来，方便以后其它服务来调用；它的好处是你不需要直接找各种什么服务支持，只需要到服务中心来领取，也不需要知道提供支持的其它服务在哪里，还是几个服务来支持的，反正拿来用就行，服务中心来保证稳定性和质量。
 更加通俗的来讲,Netflix Eureka和Dubbo更类似,但是相对于Dubbo来说,Eureka拥有更丰富的功能,涵盖了Dubbo所有的功能,可以认为Dubbo是Eureka的一个子集
![](https://i.imgur.com/WIngwGV.png)

#### 2.1.2 Netflix Ribbon
 Ribbon，主要提供客户侧的软件负载均衡算法。

 Ribbon客户端组件提供一系列完善的配置选项，比如连接超时、重试、重试算法等。Ribbon内置可插拔、可定制的负载均衡组件。下面是用到的一些负载均衡策略：
* 简单轮询负载均衡
* 加权响应时间负载均衡
* 区域感知轮询负载均衡
* 随机负载均衡

#### 2.1.3 Netflix Hystrix
 熔断器，容错管理工具，旨在通过熔断机制控制服务和第三方库的节点,从而对延迟和故障提供更强大的容错能力。比如突然某个服务挂掉了，但是服务调用方并不知情，然后调用之后长时间没有响应，调用方却不知道，一直在等这个响应；此时还有可能别的服务也正在调用挂掉的服务，那么当请求多之后，就会发生严重的阻塞影响整体的运行速度甚至会导致系统奔溃。这个时候Hystrix就派上用场了，当Hystrix发现某个挂掉或连接不上时，会自动切换到其他服务中去，或者直接返回一个错误信息告知该服务已挂掉。

#### 2.1.4 Netflix Zuul
 Zuul 是在云平台上提供动态路由,监控,弹性,安全等边缘服务的框架。Zuul 相当于是设备和 Netflix 流应用的 Web 网站后端所有请求的前门。当其它门派来找大哥办事的时候一定要先经过zuul,看下有没有带刀子什么的给拦截回去，或者是需要找那个小弟的直接给带过去。

#### 2.1.5 Netflix Archaius
 配置管理API，包含一系列配置管理API，提供动态类型化属性、线程安全配置操作、轮询框架、回调机制等功能。可以实现动态获取配置，
原理是每隔60s（默认，可配置）从配置源读取一次内容，这样修改了配置文件后不需要重启服务就可以使修改后的内容生效，前提使用archaius的API来读取。

### 2.2 Spring Cloud Config
 配置管理开发工具包，可以让你把配置放到远程服务器，目前支持本地存储、Git以及Subversion。

### 2.3 Spring Cloud Bus
 事件、消息总线，用于在集群（例如，配置变化事件）中传播状态变化，可与Spring Cloud Config联合实现热部署。

### 2.4 Spring Cloud for Cloud Foundry
 通过Oauth2协议绑定服务到CloudFoundry，CloudFoundry是VMware推出的开源PaaS云平台。

### 2.5 Spring Cloud Cluster
 Spring Cloud Cluster将取代Spring Integration。提供在分布式系统中的集群所需要的基础功能支持，如：选举、集群的状态一致性、全局锁、tokens等常见状态模式的抽象和实现。

### 2.6 Spring Cloud Consul
 Consul 是一个支持多数据中心分布式高可用的服务发现和配置共享的服务软件,由 HashiCorp 公司用 Go 语言开发, 基于 Mozilla Public License 2.0 的协议进行开源. Consul 支持健康检查,并允许 HTTP 和 DNS 协议调用 API 存储键值对.
Spring Cloud Consul 封装了Consul操作，consul是一个服务发现与配置工具，与Docker容器可以无缝集成。

### 2.7  Spring Cloud Security
 安全工具包，为应用程序添加安全控制，主要是指OAuth2。是对Spring Security的封装，并能配合Netflix使用

## 3. 如何实现springcloud的核心功能
### 3.1 服务注册与发现
#### 3.1.1 服务注册中心
 pom.xml文件中添加

```
<dependency>
   <groupId>org.springframework.cloud</groupId>
   <artifactId>spring-cloud-starter-eureka-server</artifactId>
</dependency>
```
启动一个服务注册中心，只需要一个注解`@EnableEurekaServer`，这个注解需要在`springboot`工程的启动`application`类上加：
```
@SpringBootApplication
@EnableEurekaServer
public class EurekaServerStartUp {
    public static void main(String[] args) {
        SpringApplication.run(EurekaServerStartUp.class, args);
    }
}
```
eureka是一个高可用的组件，它没有后端缓存，每一个实例注册之后需要向注册中心发送心跳（因此可以在内存中完成），在默认情况下erureka server也是一个eureka client ,必须要指定一个 server。eureka server的配置文件`appication.yml`(也可为`application.properties`,推荐使用YAML)：
```
server:
  port: 8080 # 注册中心占用8080端口
eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/  #其实就是 http://localhost:8080/eureka/
```
registerWithEureka表示是否注册自身到eureka服务器，因为当前这个应用就是eureka服务器，没必要注册自身，所以这里是false。fetchRegistry表示是否从eureka服务器获取注册信息，同上，这里不需要。defaultZone就比较重要了，是设置eureka服务器所在的地址，查询服务和注册服务都需要依赖这个地址。
通过main方法启动工程,启动成功后在浏览器中输入localhost:8080,界面如下:
![](https://i.imgur.com/L7WSY9a.png)
> No application available 表示没有服务被发现 

### 3.1.2 服务提供者
当client向server注册时，它会提供一些元数据，例如主机和端口，URL，主页等。Eureka server 从每个client实例接收心跳消息。 如果心跳超时，则通常将该实例从注册server中删除。

在服务提供者的`pom.xml`中添加:
```
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```
在启动类中通过注解`@EnableEurekaClient` 表明自己是一个eurekaclient.
```
@SpringBootApplication
@EnableEurekaClient
public class Service1StartUp{
    public static void main(String[] args) {
        SpringApplication.run(Service1StartUp.class, args);
    }
}

```
仅仅`@EnableEurekaClient`是不够的，还需要在配置文件中注明自己的服务注册中心的地址，`application.yml`配置文件如下：
```
spring:
  application:
    name: service1
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8080/eureka/
  instance:
    hostname: localhost
    instance-id: ${spring.application.name}:${spring.cloud.client.ipAddress}:${spring.application.instance_id:${server.port}}
server:
  port: 8081

```
需要指明`spring.application.name`,这个很重要，这在以后的服务与服务之间相互调用一般都是根据这个name 。 
简单写一个服务,代表对外所提供的服务:
```
@RestController
public class Controller{

    @Value("${server.port}")
    String port;
    @RequestMapping("/hi")
    public String home(@RequestParam String name) {
        return "hi "+name+",i am from port:" +port;
    }
}
```
启动工程，打开http://localhost:8080 ，即eureka server 的网址：
![](https://i.imgur.com/HeKpuFH.png)

这时打开 http://localhost:8081/hi?name=cxy ，你会在浏览器上看到 :
> hi cxy,i am from port:8081

## 3.2 服务消费者
在微服务架构中，业务都会被拆分成一个独立的服务，服务与服务的通讯是基于http restful的。Spring cloud有两种服务调用方式，一种是ribbon+restTemplate，另一种是feign。
### 3.2.1 ribbon+restTemplate
启动上一步的服务注册中心(eureka-server),服务提供者(service1),端口为8081,将service1的配置文件的端口改为8082,并启动,会发现service1在eureka-server注册了2个实例，这就相当于一个小的集群。访问localhost:8080如图所示：
![](https://i.imgur.com/UPogClg.png)
在服务消费者的`pom.xml`中添加:
```
<dependency>
	<groupId>org.springframework.cloud</groupId>
	<artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```
![](https://i.imgur.com/5omlmTv.png)
配置文件`application.yml`如下：
```
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8080/eureka/
server:
  port: 8090
spring:
  application:
    name: service-ribbon
```
启动类:
```
@SpringBootApplication
@EnableDiscoveryClient
public class ServerRibbonStartUp{
    public static void main(String[] args) {
        SpringApplication.run(ServerRibbonStartUp.class, args);
    }

    @Bean
    @LoadBalanced
    RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```
调用远程服务的Servcie:
```
@Service
public class RestTestService{
    @Autowired
    RestTemplate restTemplate;

    public String hiService(String name) {
        return restTemplate.getForObject("http://service1/hi?name="+name,String.class);
    }
}
```
服务消费者入口:
```
@RestController
public class RestTestController{
    @Autowired
    RestTestService restTestService;
    @RequestMapping(value = "/hi")
    public String hi(@RequestParam String name){
        return restTestService.hiService(name);
    }
}
```
启动程序后,登录http://localhost:8080 ,可以看到消费者已注册到注册中心:
![](https://i.imgur.com/zu4ZJm1.png)

随即在浏览器中多次访问http://localhost:8090/hi?name=cxy ,可以发现调用的服务是轮询8081和8082端口的
>hi cxy,i am from port:8081
>hi cxy,i am from port:8082

此时,如果8082端口的服务停掉,再次访问http://localhost:8090/hi?name=cxy 时,8082端口可以调成功,轮询到8081端口时,会报如下错误:
>Whitelabel Error Page

> This application has no explicit mapping for /error, so you are seeing this as a fallback.

> Wed Nov 08 18: 15:19 CST 2017
> There was an unexpected error (type=Internal Server Error, status=500).
I/O error on GET request for "http://service1/hi": Connection refused: connect; nested exception is java.net.ConnectException: Connection refused: connect

说明ribbon只是实现了负载均衡作用,无法做到高可用

### 3.2.2 feign
`pom.xml`中文件引入Feign的起步依赖`spring-cloud-starter-feign`、Eureka的起步依赖`spring-cloud-starter-eureka`:
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-feign</artifactId>
</dependency>
```
在工程的配置文件`application.yml`文件，指定程序名为`service-feign`，端口号为`8100`，服务注册地址为http://localhost:8080/eureka/ ，代码如下：
```
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8080/eureka/
server:
  port: 8100
spring:
  application:
    name: service-feign
```
在程序的启动类`ServcieFeignStartUp` ，加上`@EnableFeignClients`注解开启Feign的功能：
```
@SpringBootApplication
@EnableFeignClients
@EnableDiscoveryClient
public class ServcieFeignStartUp{
    public static void main(String[] args) {
        SpringApplication.run(ServcieFeignStartUp.class, args);
    }
}
```

定义一个feign接口，通过`@FeignClient（“服务名”）`，来指定调用哪个服务。比如在代码中调用了service1服务的`/hi`接口，代码如下：

```
@FeignClient(value = "service1")
public interface FeignTestService {
    @RequestMapping(value = "/hi",method = RequestMethod.GET)
    String sayHi(@RequestParam(value = "name") String name);
}
```
在Web层的controller层，对外暴露一个”/hi”的API接口，通过上面定义的Feign客户端`FeignTestService` 来消费服务。代码如下：
```
@RestController
public class FeignTestController{
    @Autowired
    FeignTestService feignTestService;
    @RequestMapping(value = "/hi",method = RequestMethod.GET)
    public String sayHi(@RequestParam String name){
        return feignTestService.sayHi(name);
    }
}
```
启动程序，多次访问http://localhost:8100/hi?name=cxy ,浏览器交替显示：
> hi cxy,i am from port:8081
> hi cxy,i am from port:8082

和使用ribbon+restTemeplete方式一样,feign只实现了负载均衡,没有做到高可用

## 3.3 断路器(Hystrix)
在微服务架构中，根据业务来拆分成一个个的服务，服务与服务之间可以相互调用（RPC），在Spring Cloud可以用RestTemplate+Ribbon和Feign来调用。为了保证其高可用，单个服务通常会集群部署。由于网络原因或者自身的原因，服务并不能保证100%可用，如果单个服务出现问题，调用这个服务就会出现线程阻塞，此时若有大量的请求涌入，Servlet容器的线程资源会被消耗完毕，导致服务瘫痪。服务与服务之间的依赖性，故障会传播，会对整个微服务系统造成灾难性的严重后果，这就是服务故障的“雪崩”效应。

为了解决这个问题，业界提出了断路器模型。
Netflix开源了Hystrix组件，实现了断路器模式，SpringCloud对这一组件进行了整合。 在微服务架构中，一个请求需要调用多个服务是非常常见的，如下图：

![](https://i.imgur.com/S7R8hkx.png)

较底层的服务如果出现故障，会导致连锁故障。当对特定的服务的调用的不可用达到一个阀值（Hystric 是5秒20次） 断路器将会被打开。

![](https://i.imgur.com/ASR4lot.png)

断路打开后，可用避免连锁故障，fallback方法可以直接返回一个固定值。
### 3.3.1 在ribbon使用断路器
改造`serice-ribbon` 工程的代码，首先在`pox.xml`文件中加入`spring-cloud-starter-hystrix`的起步依赖：
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
```
在程序的启动类`ServiceRibbonApplication` 加`@EnableHystrix`注解开启Hystrix：
```
@SpringBootApplication
@EnableDiscoveryClient
@EnableHystrix
public class ServerRibbonStartUp{
    public static void main(String[] args) {
        SpringApplication.run(ServerRibbonStartUp.class, args);
    }

    @Bean
    @LoadBalanced
    RestTemplate restTemplate() {
        return new RestTemplate();
    }
}
```
改造`RestTestService`类，在`hiService`方法上加上`@HystrixCommand`注解。该注解对该方法创建了熔断器的功能，并指定了`fallbackMethod`熔断方法，熔断方法直接返回了一个字符串，字符串为`”hi,”+name+”,sorry,error!”`，代码如下：
```
@Service
public class RestTestService{
    @Autowired
    RestTemplate restTemplate;
    @HystrixCommand(fallbackMethod = "hiError")
    public String hiService(String name) {
        return restTemplate.getForObject("http://service1/hi?name="+name,String.class);
    }
    public String hiError(String name) {
        return "hi,"+name+",sorry,error!";
    }
}
```
启动：service-ribbon 工程，当我们访问http://localhost:8090/hi?name=cxy ,浏览器显示：
> hi cxy,i am from port:8081
> hi cxy,i am from port:8082

当我们停掉8081端口的服务后,再次访问http://localhost:8090/hi?name=cxy ,浏览器显示:
> hi,cxy,sorry,error!
> hi cxy,i am from port:8082

当我们再次打开8081端口服务后,访问http://localhost:8090/hi?name=cxy ,浏览器显示:
> hi cxy,i am from port:8081
> hi cxy,i am from port:8082

说明服务已恢复正常

###3.3.2 Feign中使用断路器
Feign是自带断路器的，在D版本的Spring Cloud中，它没有默认打开。需要在配置文件中配置打开它，在配置文件加以下代码：
```
feign:
  hystrix:
    enabled: true
```
基于service-feign工程进行改造，只需要在FeignClient的`FeignTestService`接口的注解中加上fallback的指定类就行了：
```
@FeignClient(value = "service1",fallback = FeignTestErrorHystric.class)
public interface FeignTestService {
    @RequestMapping(value = "/hi",method = RequestMethod.GET)
    String sayHi(@RequestParam(value = "name") String name);
}

```

`FeignTestErrorHystric`需要实现`FeignTestService` 接口，并注入到Ioc容器中，代码如下：
```
@Component
public class FeignTestErrorHystric implements FeignTestService{


    @Override
    public String sayHi(@RequestParam(value = "name") String name) {
        return "sorry "+name;
    }
}
```
启动四servcie-feign工程，浏览器打开http://localhost:8100/hi?name=cxy,注意此时8081端口服务没有启动，网页显示：
> sorry,cxy
> hi cxy,i am from port:8082

当我们再次打开8081端口服务后,访问http://localhost:8100/hi?name=cxy ,浏览器显示:
> hi cxy,i am from port:8081
> hi cxy,i am from port:8082

这证明断路器起到作用了。

### 3.3.3 Hystrix Dashboard (断路器：Hystrix 仪表盘)
基于service-ribbon 改造和Feign的改造是一样的。
首选在`pom.xml`引入`spring-cloud-starter-hystrix-dashboard`的起步依赖：
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
</dependency>

<!-- feign方式需要增加该引用 -->
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>
```
在主程序启动类中加入`@EnableHystrixDashboard`注解，开启`hystrixDashboard`：
```
@SpringBootApplication
@EnableFeignClients
@EnableDiscoveryClient
@EnableHystrixDashboard
@EnableCircuitBreaker        //feign方式需要增加该注解
public class ServcieFeignStartUp{
    public static void main(String[] args) {
        SpringApplication.run(ServcieFeignStartUp.class, args);
    }
}
```
打开浏览器：访问http://localhost:8090/hystrix 或者 http://localhost:8100/hystrix ,界面如下：
![](https://i.imgur.com/L5AGVbQ.png)

点击monitor stream 可以看到:
![](https://i.imgur.com/8wm1imV.png)

此时访问http://localhost:8090/hi?name=cxy 或者 http://localhost:8100/hi?name=cxy 后,即可看到监控结果

## 3.4 Zuul
在微服务架构中，需要几个基础的服务治理组件，包括服务注册与发现、服务消费、负载均衡、断路器、智能路由、配置管理等，由这几个基础组件相互协作，共同组建了一个简单的微服务系统。一个简单的微服务系统如下图：
![](https://i.imgur.com/rR4IbSL.png)
** 注意：A服务和B服务是可以相互调用的，作图的时候忘记了。并且配置服务也是注册到服务注册中心的。**

在Spring Cloud微服务系统中，一种常见的负载均衡方式是，客户端的请求首先经过负载均衡（zuul、Ngnix），再到达服务网关（zuul集群），然后再到具体的服务，服务统一注册到高可用的服务注册中心集群，服务的所有的配置文件由配置服务管理，配置服务的配置文件放在git仓库，方便开发人员随时改配置。

Zuul的主要功能是路由转发和过滤器。路由功能是微服务的一部分，比如`/api/user`转发到到user服务，`/api/shop`转发到到shop服务。zuul默认和Ribbon结合实现了负载均衡的功能。

zuul有以下功能：

* Authentication
* Insights
* Stress Testing
* Canary Testing
* Dynamic Routing
* Service Migration
* Load Shedding
* Security
* Static Response handling
* Active/Active traffic management

### 3.4.1 使用zuul
创建`service-zuul`工程,`pom.xml`文件引入:
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-zuul</artifactId>
</dependency>
```

在其入口`ZuulStartUp`类加上注解`@EnableZuulProxy`，开启zuul的功能：
```
@EnableZuulProxy
@EnableEurekaClient
@SpringBootApplication
public class ZuulStartUp{
    public static void main(String[] args) {
        SpringApplication.run(ZuulStartUp.class, args);
    }
}
```

加上配置文件`application.yml`加上以下的配置代码：
```
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8080/eureka/
server:
  port: 8200
spring:
  application:
    name: service-zuul
zuul:
  routes:
    api-a:
      path: /api-a/**
      serviceId: service-ribbon
    api-b:
      path: /api-b/**
      serviceId: service-feign
```
首先指定服务注册中心的地址为http://localhost:8080/eureka/，服务的端口为8200，服务名为service-zuul；以/api-a/ 开头的请求都转发给service-ribbon服务；以/api-b/开头的请求都转发给service-feign服务；
启动service-zuul服务,打开浏览器访问：http://localhost:8200/api-a/hi?name=cxy ;浏览器显示：
> hi cxy,i am from port:8081
> hi cxy,i am from port:8082

打开浏览器访问：http://localhost:8200/api-b/hi?name=cxy ;浏览器显示：
> hi cxy,i am from port:8081
> hi cxy,i am from port:8082

这说明zuul起到了路由的作用

### 3.4.2 服务过滤
zuul不仅只是路由，并且还能过滤，做一些安全验证。继续改造工程；
```
@Component
public class MyFilter extends ZuulFilter{

    @Override
    public String filterType() {
        return "pre";
    }

    @Override
    public int filterOrder() {
        return 0;
    }

    @Override
    public boolean shouldFilter() {
        return true;
    }

    @Override
    public Object run() {
        RequestContext ctx = RequestContext.getCurrentContext();
        HttpServletRequest request = ctx.getRequest();
        System.out.println(String.format("%s >>> %s", request.getMethod(), request.getRequestURL().toString()));
        Object accessToken = request.getParameter("token");
        if(accessToken == null) {
            System.err.println("token is empty");
            ctx.setSendZuulResponse(false);
            ctx.setResponseStatusCode(401);
            try {
                ctx.getResponse().getWriter().write("token is empty");
            }catch (Exception e){}

            return null;
        }
        System.out.println("ok");
        return null;
    }
}
```
filterType：返回一个字符串代表过滤器的类型，在zuul中定义了四种不同生命周期的过滤器类型，具体如下： 
* pre：路由之前
* routing：路由之时
* post： 路由之后
* error：发送错误调用
* filterOrder：过滤的顺序
* shouldFilter：这里可以写逻辑判断，是否要过滤，本文true,永远过滤。
* run：过滤器的具体逻辑。可用很复杂，包括查sql，nosql去判断该请求到底有没有权限访问。

这时访问：http://localhost:8200/api-a/hi?name=cxy ；网页显示：
> token is empty

访问 http://localhost:8200/api-a/hi?name=cxy&token=22 ； 网页显示：
> hi cxy,i am from port:8081

## 3.5 spring cloud config 
在分布式系统中，由于服务数量巨多，为了方便服务配置文件统一管理，实时更新，所以需要分布式配置中心组件。在`Spring Cloud`中，有分布式配置中心组件`spring cloud config` ，它支持配置服务放在配置服务的内存中（即本地），也支持放在远程Git仓库中。在`spring cloud config` 组件中，分两个角色，一是`config server`，二是`config client`。
### 3.5.1 config server
创建config-server工程,在其pom.xml文件中增加:
```
 <dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```
在程序的入口`ConfigServerStartUp`类加上`@EnableConfigServer`注解开启配置服务器的功能，代码如下：
```
@SpringBootApplication
@EnableConfigServer
public class ConfigServerStartUp{
    public static void main(String[] args) {
        SpringApplication.run(ConfigServerStartUp.class, args);
    }
}
```
需要在程序的配置文件`application.yml`文件配置以下：
```
spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/loooSwc/SpringcloudConfig/
          searchPaths: respo
    label: master
server:
  port: 8300
```
* spring.cloud.config.server.git.uri：配置git仓库地址
* spring.cloud.config.server.git.searchPaths：配置仓库路径
* spring.cloud.config.label：配置仓库的分支
* spring.cloud.config.server.git.username：访问git仓库的用户名
* spring.cloud.config.server.git.password：访问git仓库的用户密码
如果Git仓库为公开仓库，可以不填写用户名和密码，如果是私有仓库需要填写，本例子是公开仓库,所以无需用户名密码。

远程仓库https://github.com/loooSwc/SpringcloudConfig/ 中有个文件config-client-dev.properties文件中有一个属性：
> foo = foo version 2

![](https://i.imgur.com/RDtygd5.png)
启动程序：访问http://localhost:8300/foo/dev 
![](https://i.imgur.com/VtEuoyO.png)
证明配置服务中心可以从远程程序获取配置信息。

http请求地址和资源文件映射如下:

* /{application}/{profile}[/{label}]
* /{application}-{profile}.yml
* /{label}/{application}-{profile}.yml
* /{application}-{profile}.properties
* /{label}/{application}-{profile}.properties

{application} 匹配客户端的 “spring.application.name”
{profile} 匹配客户端的”spring.active.profiles”
{label} 如果是svn匹配trunk/branchs等.

### 3.5.2 config client
创建config-client工程,在其pom.xml文件中增加:
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```
其配置文件`bootstrap.yml`：
```
spring:
  application:
    name: config-client
  cloud:
    config:
      label: master
      profile: dev
      uri: http://localhost:8300/
server:
  port: 8400
```
* spring.cloud.config.label 指明远程仓库的分支
* spring.cloud.config.profile
 * dev开发环境配置文件
 * test测试环境
 * pro正式环境
* spring.cloud.config.uri= http://localhost:8888/ 指明配置服务中心的网址。

程序的启动类`ConfigClientStartUp`，代码如下：
```
@SpringBootApplication
public class ConfigClientStartUp {

    public static void main(String[] args) {
        SpringApplication.run(ConfigClientStartUp.class, args);
    }
}
```
在Web层的controller层，对外暴露一个”/hi”的API接口，来获取从config-server中读到的foo参数的值。代码如下：
```
@RestController
public class ConfigClientController{
    @Value("${foo}")
    String foo;
    @RequestMapping(value = "/hi")
    public String hi(){
        return foo;
    }
}
```
启动config-client程序,打开网址访问：http://localhost:8400/hi，网页显示：
> foo version 2

这就说明，config-client从config-server获取了foo的属性，而config-server是从git仓库读取的
### 3.5.3 高可用的分布式配置中心
刚才讲了一个服务如何从配置中心读取文件，配置中心如何从远程git读取配置文件，当服务实例很多时，都从配置中心读取文件，这时可以考虑将配置中心做成一个微服务，将其集群化，从而达到高可用，架构图如下：
![](https://i.imgur.com/HgZ5n8D.png)
在`config-server`和`config-client`工程的`pom.xml`文件中增加`EurekaClient`的起步依赖`spring-cloud-starter-eureka`，代码如下:
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-eureka</artifactId>
</dependency>
```
在`config-server`和`config-client`工程的启动类中增加`@EnableDiscoveryClient`注解,例如:
```
@SpringBootApplication
@EnableDiscoveryClient
public class ConfigClientStartUp {

    public static void main(String[] args) {
        SpringApplication.run(ConfigClientStartUp.class, args);
    }
}

```
`config-server`配置文件`application.yml`，指定服务注册地址为http://localhost:8080/eureka/，其他配置同上一章节，完整的配置如下：
```
spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: https://github.com/loooSwc/SpringcloudConfig/
          searchPaths: respo
    label: master
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8080/eureka/
server:
  port: 8300
```
`config-client`配置文件`bootstrap.yml`，指定服务注册地址为http://localhost:8080/eureka/，其他配置同上一章节，完整的配置如下：
```
spring:
  application:
    name: config-client
  cloud:
    config:
      label: master
      profile: dev
#      uri: http://localhost:8300/
      discovery:
        enabled: true
        serviceId: config-server
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8080/eureka/
server:
  port: 8400
```
* spring.cloud.config.discovery.enabled 是从配置中心读取文件。
* spring.cloud.config.discovery.serviceId 配置中心的servieId，即服务名。

这时发现，在读取配置文件不再写ip地址，而是服务名，这时如果配置服务部署多份，通过负载均衡，从而高可用。

依次启动config-server,config-client 
访问网址：http://localhost:8080/
![](https://i.imgur.com/rKagego.png)

访问http://localhost:8400/hi，浏览器显示：
> foo version 2

## 3.6 Spring Cloud Bus
Spring Cloud Bus 将分布式的节点用轻量的消息代理连接起来。它可以用于广播配置文件的更改或者服务之间的通讯，也可以用于监控。本文要讲述的是用Spring Cloud Bus实现通知微服务架构的配置文件的更改。

按照官方文档，我们只需要在配置文件中配置 spring-cloud-starter-bus-amqp ；这就是说我们需要装rabbitMq.

在`config-client`工程的`pom.xml`文件中加上起步依赖`spring-cloud-starter-bus-amqp`:
```
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-bus-amqp</artifactId>
</dependency>
```
在配置文件`bootstrap.yml`中加上RabbitMq的配置，包括RabbitMq的地址、端口，用户名、密码，代码如下：
```
spring:
  application:
    name: config-client
  cloud:
    config:
      label: master
      profile: dev
#      uri: http://localhost:8300/
      discovery:
        enabled: true
        serviceId: config-server
  rabbitmq:
    host: 175.25.16.221
    port: 9010
    username: eayuncloud
    password: E4yun.cn789
eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8080/eureka/
server:
  port: 8400
```
如果rabbitmq有用户名密码，输入即可。
启动两个config-client，端口为：8400、8401。
访问http://localhost:8400/hi 或者http://localhost:8401/hi 浏览器显示：
> foo version 3

这时我们去代码仓库将foo的值改为“foo version 4”，即改变配置文件foo的值。如果是传统的做法，需要重启服务，才能达到配置文件的更新。此时，我们只需要发送post请求：http://localhost:8400/bus/refresh，会发现config-client会重现读取配置文件
![](https://i.imgur.com/K5BO5eQ.png)
这时我们再访问http://localhost:8400/hi 或者http://localhost:8401/hi 浏览器显示：
> foo version 4

另外，/bus/refresh接口可以指定服务，即使用”destination”参数，比如 “/bus/refresh?destination=customers:**” 即刷新服务名为customers的所有服务，不管ip。

当git文件更改的时候，通过pc端用post 向端口为8400的config-client发送请求/bus/refresh／；此时8400端口会发送一个消息，由消息总线向其他服务传递，从而使整个微服务集群都达到更新配置文件。

## 4. 如何与我们的项目结合
对于我们现在的项目,如果想要使用springcloud,有下面几种做法:

第一种为将现有的所有功能模块拆分成一个个单独的服务,都向注册中心注册,这种方案优点是改完后所有服务都是springcloud框架的程序,方便同一管理,缺点是代码改动量较大

第二种为将现有的一部分代码使用SpringCloud框架,比如将邮件短信等原本在Dubbo中的服务转移到SpringCloud中,后续再慢慢拓展;

第三种方案是对于老代码不进行改动,对于新功能模块采用springcloud架构,老模块中需要调取新模块功能时,使用RestTemplete+Ribbon的方式,优点是对现有代码改动量少,缺点是新老模块不统一,无法统一管理

无论是哪种方案,由于SpringCloud框架服务间的调用都是使用REST API方式,所以服务提供者接受请求的地方都是Controller,所以需要在服务提供方新增Controller来接受请求

> 本文借鉴于[方志朋的博客](http://blog.csdn.net/forezp/article/details/70148833 )