# Ribbon负载均衡

#### 1. Ribbon负载均衡规则

-   规则接口是IRule
-   默认实现是ZoneAvoidanceRule，根据zone选择服务列表，然后轮询

#### 2. 负载均衡自定义方式

-   代码方式：配置灵活，但修改时需要重新打包发布
-   配置方式：直观，方便，无需重新打包发布，但是无法做全局配置

#### 3. 饥饿加载

-   开启饥饿加载
-   指定饥饿加载的微服务名称

```yaml
spring:
  cloud:
    nacos:
      server-addr: localhost:8848 # nacos 服务地址
      discovery:
        cluster-name: SH # 集群名称
```

# Nacos服务分级模型

#### 1. Nacos服务分级存储模型

1.  一级是服务，例如userservice
2.  二级是集群，例如杭州或上海
3.  三级是实例，例如杭州机房的某台部署了userservice的服务器

#### 2. 如何设置实例的集群属性

修改application.yml文件，添加spring.cloud.nacos.discovery.cluster-name属性即可

## Nacos负载均衡策略

#### 1. NacosRule负载均衡策略

- 优先选择同集群服务实例列表

- 本地集群找不到提供者，才去找其他集群寻找，并且会报警告

- 确定了可用实例列表后，再采用随机负载均衡挑选实例

  

# Nacos注册中心 - 加权负载均衡

#### 1. 实例的权重控制

-   Nacos控制台可以设置势实例的权重值，0\~1
-   同集群内的多个实例，权重越高被访问的频率越高
-   权重设置为0则完全不会被访问

### 
