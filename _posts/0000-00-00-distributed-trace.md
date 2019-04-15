---
title: invoke trace
layout: post
date:  2017-04-15 15:11:49 +0800 
categories: [链路追踪]
tags: [trace]
---


* content
{:toc}


# skywalking 
链路追踪 skywalking

# 一、目录结构说明
```
├── agent-docker                         # 构建包含agent的docker镜像
│   ├── apm-toolkit-log                  # java日志增强库
│   ├── Makefile                         # 批量构建脚本
│   ├── ngcp-auth                        # auth服务的构建脚本
│   ├── ngcp-uus                         # uus服务的构建脚本
│   ├── README.md                        # 说明文档
│   └── skywalking-agent.tar.gz          # skywalking-agent包，会添加都镜像中，在启动java时加载
├── agent-kubernetes                     # 存放已经打入skywalking-agent的applications的kubernetes脚本
├── README.md                            # 说明文档
├── skywalking-6.0.0-GA-docker           # skywalking的docker镜像脚本
│   ├── base                             # 基础镜像
│   ├── compose                          # docker-compose脚本
│   ├── Makefile                         # 批量构建docker镜像脚本
│   ├── oap                              # oap-server服务构建脚本
│   ├── oap-patch                        # 包装原镜像增加上海时区
│   ├── README.md                        # 说明文档
│   ├── ui                               # skywalking-UI的构建脚本
│   └── ui-patch                         # 包装原镜像增加上海时区
└── skywalking-6.0.0-GA-kubernetes       # skywalking kubernetes 部署脚本
    ├── elasticsearch                    # elasticsearch相关脚本
    ├── istio                            # istio相关脚本
    ├── namespace.yml                    # 创建skywalking命名空间
    ├── oap                              # skywalking服务相关脚本
    ├── README.md                        # 说明文档
    └── ui                               # skywalking界面相关脚本
```
## 二、创建es

没有现成的elasticsearch需要创建es服务，相关脚本在`skywalking-6.0.0-GA-kubernetes/elasticsearch`下，
es没有配置存储，需要的话自行配置。

## 三、创建oap服务

1. 首先使用`skywalking-6.0.0-GA-docker/oap_patch`脚本构建上海时区的oap镜像
2. 在skywalking-6.0.0-GA-docker目录下使用`make oap_patch`命令构建oap镜像并上传仓库
3. 修改es配置 使用真实的es集群的IP:端口

    路径：`skywalking-6.0.0-GA-kubernetes/oap/01-config.yml`
    ```yml
     application.yml:
       storage:
         elasticsearch:
           clusterNodes: 192.168.1.108:9200
    ```
4. 镜像使用构建好的`192.168.29.2:5000/apache/skywalking-oap-server:6.0.0-GA-timezone`
5. 使用`skywalking-6.0.0-GA-kubernetes/oap`下的脚本在kubernetes平台执行

## 四、创建ui服务

1. 使用`skywalking-6.0.0-GA-kubernetes/ui`下的脚本在kubernetes平台执行

## 五、istio

1. 使用`skywalking-6.0.0-GA-kubernetes/istio`下的脚本在kubernetes平台执行

## 六、创建应用服务

`agent-docker/Makefile`提取了相关参数对应修改

```makefile
# 构建镜像版本
export AGENT_VER = latest
# docker镜像仓库地址
export DOCKER_REGISTRY = 192.168.29.2:5000
# oap服务地址　
export COLLECTOR_BACKEND_SERVICES = 192.168.136.8:31180
```
### 6.1 uus服务

在agent-docker目录下执行`make ngcp-uus`构建uus镜像并上传仓库

使用`agent-kubernetes/ngcp-uus.yaml`脚本在kubernetes平台执行创建uus服务

### 6.2 auth服务

在agent-docker目录下执行`make ngcp-auth`构建auth镜像并上传仓库

使用`agent-kubernetes/ngcp-auth.yaml`脚本在kubernetes平台执行创建auth服务

## 修改时区

### alpine Linux 修改timezone
```bash
RUN apk add -U tzdata
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
RUN apk del tzdata
```
### k8s 修改centos镜像的 timezone
```json
        "initContainers":[
          {
            "name":"increase-the-timezone",
            "image": "busybox",
            "command":["sh","-c","echo 'Asia/Shanghai' > /etc/timezone"],
            "securityContext":{
                "privileged": true
             }
          }
        ]
```

## skywalking 侵入式 增强日志输出traceId

在项目maven中增加相关日志增强依赖

```xml
        <dependency>
            <artifactId>apm-application-toolkit</artifactId>
            <artifactId>apm-toolkit-log4j-1.x</artifactId>
            <version>6.1.0-SNAPSHOT</version>
        </dependency>
        <!--或者-->
        <dependency>
            <artifactId>apm-application-toolkit</artifactId>
            <artifactId>apm-toolkit-log4j-2.x</artifactId>
            <version>6.1.0-SNAPSHOT</version>
        </dependency>        
        <!--或者-->
        <dependency>
            <artifactId>apm-application-toolkit</artifactId>
            <artifactId>apm-toolkit-logback-1.x</artifactId>
            <version>6.1.0-SNAPSHOT</version>
        </dependency>        
```

配置日志配置文件比如logback

logback使用增强的TraceIdPatternLogbackLayout

```xml
  <appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
    <layout class="org.apache.skywalking.apm.toolkit.log.logback.v1.x.TraceIdPatternLogbackLayout">
      <pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{36} - %tid - %msg%n</pattern>
    </layout>
  </appender>
```

或则在构建镜像时动态加入日志依赖

脚本`pre.sh`
```bash
mkdir tmp
cp ngcp-auth-0.1.0-SNAPSHOT.jar tmp/ngcp.jar
cd tmp
jar -xf ngcp.jar
rm -rf ngcp.jar
cp ../../lib/* lib/
cp ../logback.xml .
jar -cfM0 ../ngcp-auth-0.1.0-SNAPSHOT.jar *
cd ../
rm -rf tmp
```



## 请求auth
```bash
curl -X GET \
  http://192.168.136.7:13256/api/v1/users/18640048460 \
  -H 'authorization: Basic OTk5OTU6VnBGT1ZCWWExcFlaMFZXZEVka1EzSjE=' \
  -H 'cache-control: no-cache' \
  -H 'postman-token: cd5bc7c2-6de9-c21e-1d33-83cdde3e4bb5'

```


  
  
  
# 参考


技术

[zipkin](https://zipkin.io/)

pinprint

OpenTracing

实现

Jaeger 

    [开放分布式追踪（OpenTracing）入门与 Jaeger 实现](https://yq.aliyun.com/articles/514488)

[SkyWalking 分布式追踪系统](https://www.jianshu.com/p/2fd56627a3cf)

    SkyWalking 支持  [Istio](https://istio.io/)