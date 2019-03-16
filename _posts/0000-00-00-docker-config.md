---
title: docker config
layout: post
date: 2017-03-28 16:48:57 +0800
categories: 容器
tags: docker
---

* content
{:toc}
本文 记录 配置



#### 测试环境
```
IP:192.168.183.114 root/1qaz@WSX
tomcat地址：/export/servers/tomcat6.0.33
tomcat配置：/export/home/tomcat/domains/[domain.com]/server1/
war地址：export/data/tomcatRoot/[domain.com]
nginx config: /export/servers/nginx

```
##### bin/start.sh
```
#!/bin/bash
#chown 555 -R /export/home/tomcat/domains/
export CATALINA_HOME=/export/servers/tomcat6.0.33
export CATALINA_BASE=/export/home/tomcat/domains/[www.domain.com]/server1
###JAVA
export JAVA_HOME=/export/servers/jdk1.6.0_25
export JAVA_BIN=/export/servers/jdk1.6.0_25/bin
export PATH=/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin:/bin
export CLASSPATH=.:/lib/dt.jar:/lib/tools.jar
export  JAVA_OPTS="-Xdebug -Xrunjdwp:transport=dt_socket,server=y,address=17899,suspend=n -Djava.library.path=/usr/local/lib -server -Xms1024m -Xmx2048m -XX:MaxPermSize=1024m -Djava.awt.headless=true -Dsun.net.client.defaultConnectTimeout=60000 -Dsun.net.client.defaultReadTimeout=60000 -Djmagick.systemclassloader=no -Dnetworkaddress.cache.ttl=300 -Dsun.net.inetaddr.ttl=300"

export JAVA_HOME JAVA_BIN PATH CLASSPATH JAVA_OPTS
$CATALINA_HOME/bin/startup.sh -config $CATALINA_BASE/conf/server.xml
```

##### bin/stop.sh
```
#!/bin/bash
export CATALINA_HOME=/export/servers/tomcat6.0.33
export CATALINA_BASE=/export/home/tomcat/domains/[www.domain.com]/server1
###JAVA
#export JAVA_HOME=/export/servers/jdk1.6.0_25
#export JAVA_BIN=/export/servers/jdk1.6.0_25/bin
#export PATH=/usr/kerberos/sbin:/usr/kerberos/bin:/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin:/root/bin:/bin
#export CLASSPATH=.:/lib/dt.jar:/lib/tools.jar
#export  JAVA_OPTS="-Djava.library.path=/usr/local/lib -server -Xms512m -Xmx1200m -XX:MaxPermSize=256m -Djava.awt.headless=true -Dsun.net.client.defaultConnectTimeout=60000 -Dsun.net.client.defaultReadTimeout=60000 -Djmagick.systemclassloader=no -Dnetworkaddress.cache.ttl=300 -Dsun.net.inetaddr.ttl=300"
#export JAVA_HOME JAVA_BIN PATH CLASSPATH JAVA_OPTS

$CATALINA_HOME/bin/shutdown.sh -config $CATALINA_BASE/conf/server.xml
```
##### conf/server.sh
```
<?xml version='1.0' encoding='utf-8'?>
 <Server port="9008" shutdown="SHUTDOWN">

  <Listener className="org.apache.catalina.core.AprLifecycleListener" SSLEngine="on" />
  <Listener className="org.apache.catalina.core.JasperListener" />
  <Listener className="org.apache.catalina.core.JreMemoryLeakPreventionListener" />
  <Listener className="org.apache.catalina.mbeans.ServerLifecycleListener" />
  <Listener className="org.apache.catalina.mbeans.GlobalResourcesLifecycleListener" />

  <GlobalNamingResources>
    <Resource name="UserDatabase" auth="Container"
              type="org.apache.catalina.UserDatabase"
              description="User database that can be updated and saved"
              factory="org.apache.catalina.users.MemoryUserDatabaseFactory"
              pathname="conf/tomcat-users.xml" />
  </GlobalNamingResources>

  <Service name="Catalina">
<!--  <Connector port="8011" maxParameterCount="1000" protocol="AJP/1.3" redirectPort="8443"  maxSpareThreads="750" maxThreads="1000" minSpareTHreads="50" acceptCount="1000" URIEncoding="gbk" useBodyEncodingForURI="true"/> -->
    <Connector port="8008" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="18443"  URIEncoding="gbk" useBodyEncodingForURI="true"/>
    <Engine name="Catalina" defaultHost="localhost" jvmRoute="s1">
      <Realm className="org.apache.catalina.realm.UserDatabaseRealm" resourceName="UserDatabase"/>
      <Host name="localhost"  appBase="webapps" unpackWARs="false" autoDeploy="false" xmlValidation="false" xmlNamespaceAware="false"> </Host>
    </Engine>
  </Service>
</Server>

```
##### nginx/conf/domains/www.domain.com
```
        upstream tomcat_domain {
                server 127.0.0.1:8001  weight=10 max_fails=2 fail_timeout=30s;
                }
server
                {
                listen                   80;
                server_name              www.domain.com;
                access_log               /export/servers/nginx/logs/www.domain.com/www.domain.com_access.log main;
                error_log                /export/servers/nginx/logs/www.domain.com/www.domain.com_error.log warn;
                chunkin on;
                error_page 411 = @my_error;
                location @my_error {
                chunkin_resume;
                 }
                root /export/data/tomcatRoot/www.domain.com/;
      location / {
        proxy_next_upstream     http_500 http_502 http_503 http_504 error timeout invalid_header;
        proxy_set_header        Host  $host;
        proxy_set_header        X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_pass              http://tomcat_domain;
        expires                 1d;
        }
        location /logs/ {
                autoindex       off;
                deny all;
        }
}

```
