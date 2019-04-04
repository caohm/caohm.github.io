---
title: docker镜像仓库Harbor搭建
layout: post
date: 2019-03-27 9:00:57 +0800
categories: 容器云
tags: docker k8s registery
---

* content
{:toc}

Docker容器应用的开发和运行离不开可靠的镜像管理，虽然Docker官方也提供了公共的镜像仓库，但是从安全和效率等方面考虑，部署我们私有环境内的Registry也是非常必要的。
Harbor是由VMware公司开源的企业级的Docker Registry管理项目，它包括权限管理(RBAC)、LDAP、日志审核、管理界面、自我注册、镜像复制和中文支持等功能。












# 系统环境
系统：CentOS Linux release 7.5.1804 (Core)  <br/>
Python version：>=2.7<br/>
Docker version：>=1.10<br/>
Docker Compose version：>=1.6.0 <br/>
Openssl：直接yum安装最新版本即可(# yum -y install openssl openssl-devel)

# 安装依赖包

1. CentOS 7 自带Python版本2.7，不必更新Python
2. 安装Docker(根据自己实际需求，下列安装方式二选一即可)<br/>
    1) 安装Docker ce [官方链接](https://docs.docker.com/install/linux/docker-ce/centos/)

    卸载原来的Docker(如果以前没安装过，略过此步骤即可)
    ```bash
    $ sudo yum remove docker \
              docker-client \
              docker-client-latest \
              docker-common \
              docker-latest \
              docker-latest-logrotate \
              docker-logrotate \
              docker-selinux \
              docker-engine-selinux \
              docker-engine 
    ```

    devicemapper存储驱动依赖于yum-utils，device-mapper-persistent-data，lvm2
    ```bash
    $ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
    ```
    
    添加Docker官方yum源    
    ```bash
    $ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
    ```
    
    安装Docker ce    
    ```bash
    $ sudo yum install docker-ce
    ```
    
    2) 安装Docker engine

    添加yum源
    ```bash
    # cat <<EOF>/root/docker.repo \
    > name=Docker
    > baseurl=https://yum.dockerproject.org/repo/main/centos/7/
    > enabled=1
    > gpgcheck=0
    > EOF
    ```

    安装Docker engine
    ```bash
    # yum -y install docker-engine-1.13.1
    ```
    
# 安装Docker-Compose

## yum 安装

1. 安装epel源
```bash
[root@DockerRegistry ~]# yum -y install epel-release
```

    if failed install epel源 
    ```bash
[root@DockerRegistry ~]# wget http://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
[root@DockerRegistry ~]# rpm -ivh epel-release-latest-7.noarch.rpm 
    ```

2. 安装python-pip
```bash
[root@DockerRegistry ~]# yum -y install python-pip
```
3. 安装docker-compose
```bash
[root@DockerRegistry ~]# pip install -U docker-compose
[root@DockerRegistry ~]# docker-compose -v
docker-compose version 1.21.2, build a133471
```

## 二进制安装

```bash
[root@DockerRegistry ~]# curl -L https://github.com/docker/compose/releases/download/1.21.1/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
[root@DockerRegistry ~]# chmod +x /usr/local/bin/docker-compose
``` 

# 安装Harbor

## 获得Harbor离线安装文件

1. 下载

```bash
caohm@caohm-ThinkPad-E450:~$ wget https://storage.googleapis.com/harbor-releases/harbor-online-installer-v1.7.4.tgz
```

2. scp copy remote to local

```bash
caohm@caohm-ThinkPad-E450:~$ scp root@192.168.112.96:/home/caohm/harbor-offline-installer-v1.7.4.tar /home/caohm/
```

3. scp copy local to remote

```bash
caohm@caohm-ThinkPad-E450:~$ scp harbor-offline-installer-v1.7.4.tar drapp@172.20.27.7:/home/drapp/
```

## 修改配置

### 1. 解压

```bash
caohm@caohm-ThinkPad-E450:~$ tar -xvf harbor-offline-installer-v1.7.4.tar 
caohm@caohm-ThinkPad-E450:~/harbor$ ls
common                          docker-compose.clair.yml   docker-compose.yml  harbor.v1.7.4.tar.gz  LICENSE              prepare
docker-compose.chartmuseum.yml  docker-compose.notary.yml  harbor.cfg          install.sh            open_source_license
```

### 2. 修改harbor.cfg

```bash
caohm@caohm-ThinkPad-E450:~/harbor$ vi harbor.cfg 
```

    ## Configuration file of Harbor
    
    #This attribute is for migrator to detect the version of the .cfg file, DO NOT MODIFY!
    _version = 1.7.0
    #The IP address or hostname to access admin UI and registry service.
    #DO NOT use localhost or 127.0.0.1, because Harbor needs to be accessed by external clients.
    #DO NOT comment out this line, modify the value of "hostname" directly, or the installation will fail.
    hostname = reg.mydomain.com
    
    #The protocol for accessing the UI and token/notification service, by default it is http.
    #It can be set to https if ssl is enabled on nginx.
    ui_url_protocol = http
    
    #Maximum number of job workers in job service  
    max_job_workers = 10 
    
    #Determine whether or not to generate certificate for the registry's token.
    #If the value is on, the prepare script creates new root cert and private key 
    #for generating token to access the registry. If the value is off the default key/cert will be used.
    #This flag also controls the creation of the notary signer's cert.
    customize_crt = on
    
    #The path of cert and key files for nginx, they are applied only the protocol is set to https
    ssl_cert = /data/cert/server.crt
    ssl_cert_key = /data/cert/server.key
    
    #The path of secretkey storage
    secretkey_path = /data
    
    #Admiral's url, comment this attribute, or set its value to NA when Harbor is standalone
    admiral_url = NA
    
    #Log files are rotated log_rotate_count times before being removed. If count is 0, old versions are removed rather than rotated.
    log_rotate_count = 50
    #Log files are rotated only if they grow bigger than log_rotate_size bytes. If size is followed by k, the size is assumed to be in kilobytes. 
    #If the M is used, the size is in megabytes, and if G is used, the size is in gigabytes. So size 100, size 100k, size 100M and size 100G 
    #are all valid.
    log_rotate_size = 200M
    
    #Config http proxy for Clair, e.g. http://my.proxy.com:3128
    #Clair doesn't need to connect to harbor internal components via http proxy.
    http_proxy =
    https_proxy =
    no_proxy = 127.0.0.1,localhost,core,registry
    
    #NOTES: The properties between BEGIN INITIAL PROPERTIES and END INITIAL PROPERTIES
    #only take effect in the first boot, the subsequent changes of these properties 
    #should be performed on web ui
    
    #************************BEGIN INITIAL PROPERTIES************************
    
    #Email account settings for sending out password resetting emails.
    
    #Email server uses the given username and password to authenticate on TLS connections to host and act as identity.
    #Identity left blank to act as username.
    email_identity = 
    
    email_server = smtp.mydomain.com
    email_server_port = 25
    email_username = sample_admin@mydomain.com
    email_password = abc
    email_from = admin <sample_admin@mydomain.com>
    email_ssl = false
    email_insecure = false
    
    ##The initial password of Harbor admin, only works for the first time when Harbor starts. 
    #It has no effect after the first launch of Harbor.
    #Change the admin password from UI after launching Harbor.
    harbor_admin_password = Harbor12345
    
    ##By default the auth mode is db_auth, i.e. the credentials are stored in a local database.
    #Set it to ldap_auth if you want to verify a user's credentials against an LDAP server.
    auth_mode = db_auth
    
    #The url for an ldap endpoint.
    ldap_url = ldaps://ldap.mydomain.com
    
    #A user's DN who has the permission to search the LDAP/AD server. 
    #If your LDAP/AD server does not support anonymous search, you should configure this DN and ldap_search_pwd.
    #ldap_searchdn = uid=searchuser,ou=people,dc=mydomain,dc=com
    
    #the password of the ldap_searchdn
    #ldap_search_pwd = password
    
    #The base DN from which to look up a user in LDAP/AD
    ldap_basedn = ou=people,dc=mydomain,dc=com
    
    #Search filter for LDAP/AD, make sure the syntax of the filter is correct.
    #ldap_filter = (objectClass=person)
    
    # The attribute used in a search to match a user, it could be uid, cn, email, sAMAccountName or other attributes depending on your LDAP/AD  
    ldap_uid = uid 
    
    #the scope to search for users, 0-LDAP_SCOPE_BASE, 1-LDAP_SCOPE_ONELEVEL, 2-LDAP_SCOPE_SUBTREE
    ldap_scope = 2 
    
    #Timeout (in seconds)  when connecting to an LDAP Server. The default value (and most reasonable) is 5 seconds.
    ldap_timeout = 5
    
    #Verify certificate from LDAP server
    ldap_verify_cert = true
    
    #The base dn from which to lookup a group in LDAP/AD
    ldap_group_basedn = ou=group,dc=mydomain,dc=com
    
    #filter to search LDAP/AD group
    ldap_group_filter = objectclass=group
    
    #The attribute used to name a LDAP/AD group, it could be cn, name
    ldap_group_gid = cn
    
    #The scope to search for ldap groups. 0-LDAP_SCOPE_BASE, 1-LDAP_SCOPE_ONELEVEL, 2-LDAP_SCOPE_SUBTREE
    ldap_group_scope = 2
    
    #Turn on or off the self-registration feature
    self_registration = on
    
    #The expiration time (in minute) of token created by token service, default is 30 minutes
    token_expiration = 30
    
    #The flag to control what users have permission to create projects
    #The default value "everyone" allows everyone to creates a project. 
    #Set to "adminonly" so that only admin user can create project.
    project_creation_restriction = everyone
    
    #************************END INITIAL PROPERTIES************************
    
    #######Harbor DB configuration section#######
    
    #The address of the Harbor database. Only need to change when using external db.
    db_host = postgresql
    
    #The password for the root user of Harbor DB. Change this before any production use.
    db_password = root123
    
    #The port of Harbor database host
    db_port = 5432
    
    #The user name of Harbor database
    db_user = postgres
    
    ##### End of Harbor DB configuration#######
    
    ##########Redis server configuration.############
    
    #Redis connection address
    redis_host = redis
    
    #Redis connection port
    redis_port = 6379
    
    #Redis connection password
    redis_password = 
    
    #Redis connection db index
    #db_index 1,2,3 is for registry, jobservice and chartmuseum. 
    #db_index 0 is for UI, it's unchangeable
    redis_db_index = 1,2,3
    
    ########## End of Redis server configuration ############
    
    ##########Clair DB configuration############
    
    #Clair DB host address. Only change it when using an exteral DB.
    clair_db_host = postgresql
    #The password of the Clair's postgres database. Only effective when Harbor is deployed with Clair.
    #Please update it before deployment. Subsequent update will cause Clair's API server and Harbor unable to access Clair's database.
    clair_db_password = root123
    #Clair DB connect port
    clair_db_port = 5432
    #Clair DB username
    clair_db_username = postgres
    #Clair default database
    clair_db = postgres
    
    #The interval of clair updaters, the unit is hour, set to 0 to disable the updaters.
    clair_updaters_interval = 12
    
    ##########End of Clair DB configuration############
    
    #The following attributes only need to be set when auth mode is uaa_auth
    uaa_endpoint = uaa.mydomain.org
    uaa_clientid = id
    uaa_clientsecret = secret
    uaa_verify_cert = true
    uaa_ca_cert = /path/to/ca.pem
    
    
    ### Harbor Storage settings ###
    #Please be aware that the following storage settings will be applied to both docker registry and helm chart repository.
    #registry_storage_provider can be: filesystem, s3, gcs, azure, etc.
    registry_storage_provider_name = filesystem
    #registry_storage_provider_config is a comma separated "key: value" pairs, e.g. "key1: value, key2: value2".
    #To avoid duplicated configurations, both docker registry and chart repository follow the same storage configuration specifications of docker registry.
    #Refer to https://docs.docker.com/registry/configuration/#storage for all available configuration.
    registry_storage_provider_config =
    #registry_custom_ca_bundle is the path to the custom root ca certificate, which will be injected into the truststore
    #of registry's and chart repository's containers.  This is usually needed when the user hosts a internal storage with self signed certificate.
    registry_custom_ca_bundle = 
    
    #If reload_config=true, all settings which present in harbor.cfg take effect after prepare and restart harbor, it overwrites exsiting settings.
    #reload_config=true
    #Regular expression to match skipped environment variables
    #skip_reload_env_pattern=(^EMAIL.*)|(^LDAP.*)

### 3. 修改挂载路径Path

数据目录挂在/data下
日志目录挂在/var/log/harbor下
查看所以包含‘/data’的文件
[root@uat96 ~/caohm/harbor]$find . | xargs grep '/data' 
harbor.cfg
docker-compose.yml
docker-compose.chartmuseum.yml
[root@uat96 ~/caohm/harbor]$ vi harbor.cfg
```
:%s#/data#/opt/registry_harbor#g
```
[root@uat96 ~/caohm/harbor]$ vi docker-compose.yml
```
:%s#- /data/#- /opt/registry_harbor/#g
```
[root@uat96 ~/caohm/harbor]$ vi docker-compose.chartmuseum.yml 
```
:%s#- /data/#- /opt/registry_harbor/#g
```
[root@uat96 ~/caohm/harbor]$ vi prepare
```
%s#DATA_VOL = "/data"#DATA_VOL = "/opt/registry_harbor"#g
```


### 4. 修改页面端口

1) vi docker-compose.yml;

```yaml
proxy:
    image: vmware/nginx-photon:v1.5.1
    container_name: nginx
    restart: always
    volumes:
      - ./common/config/nginx:/etc/nginx:z
    networks:
      - harbor
    ports:
      - 5388:80                      #绑定5380端口
      - 443:443
    depends_on:
```

2) vi common/templates/registry/config.yml :

```yaml
auth:
  token:
    issuer: harbor-token-issuer
    # 添加端口5380
    realm: $public_url:5380/service/token
```

### 5. https

1) 方式1

```bash
caohm@caohm-ThinkPad-E450:~$ mkdir -p /opt/harbor/cert /opt/harbor/data

caohm@caohm-ThinkPad-E450:~$ cd /opt/harbor/cert

#生成私钥
caohm@caohm-ThinkPad-E450:/opt/harbor/cert$ openssl genrsa -out private_key.pem 4096
Generating RSA private key, 4096 bit long modulus
.......++
............++
e is 65537 (0x10001)

#生成证书
caohm@caohm-ThinkPad-E450:/opt/harbor/cert$ openssl req -new -x509 -key private_key.pem -out root.crt -days 3650
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN  (国家)
State or Province Name (full name) []:Beijing  (州或省名称)
Locality Name (eg, city) [Default City]:Beijing  (城市)
Organization Name (eg, company) [Default Company Ltd]:harbor  (机构名称)
Organizational Unit Name (eg, section) []:harbor  (组织单位名称)
Common Name (eg, your name or your server's hostname) []:req.yourdomain.com  (访问时用的域名)
Email Address []:yourmail@yourdomain.com  (邮箱)

caohm@caohm-ThinkPad-E450:/opt/harbor/cert$ cp /opt/harbor/cert/private_key.pem /opt/harbor/common/config/ui/private_key.pem
caohm@caohm-ThinkPad-E450:/opt/harbor/cert$ cp /opt/harbor/cert/root.crt        /opt/harbor/common/config/registry/root.crt

```

2) 方式2

```bash
caohm@caohm-ThinkPad-E450:/opt/harbor/cert$ openssl req -newkey rsa:4096 -nodes -sha256 -keyout ca.key -x509 -days 365 -out ca.crt
Generating a 4096 bit RSA private key
...........................................................................................++
...................................++
writing new private key to 'ca.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:CN  (国家)
State or Province Name (full name) []:Beijing  (州或省名称)
Locality Name (eg, city) [Default City]:Beijing  (城市)
Organization Name (eg, company) [Default Company Ltd]:harbor  (机构名称)
Organizational Unit Name (eg, section) []:harbor  (组织单位名称)
Common Name (eg, your name or your server's hostname) []:req.yourdomain.com  (访问时用的域名)
Email Address []:yourmail@yourdomain.com  (邮箱)

caohm@caohm-ThinkPad-E450:/opt/harbor/cert$ ll
total 20
-rw-r--r--. 1 root root 2082 May 11 15:30 ca.crt
-rw-r--r--. 1 root root 3272 May 11 15:30 ca.key
```
## 预执行

```bash
caohm@caohm-ThinkPad-E450:~/harbor$ ./prepare
```
## 安装
```bash
# 支持chart仓库
caohm@caohm-ThinkPad-E450:~/harbor$ ./install.sh --with-chartmuseum
```

## 维护
```bash
docker-compose start
docker-compose up -d
docker-compose stop
# 
docker-compose down --remove-orphans
```

# 使用
## registry client 忽略 https 

```bash
caohm@caohm-ThinkPad-E450:~$ sudo vi  /etc/docker/daemon.json
```

```json
{
  "registry-mirrors": ["http://harbor.test.com"], 
  "insecure-registries": ["harbor.test.com","127.0.0.1:5380"], 
  "max-concurrent-downloads": 10
}
```
```bash
caohm@caohm-ThinkPad-E450:~$ sudo service docker restart
```
centos can use `econfigure(systemctl reload docker)` to enable config

[Docker的配置文件 daemon.json 详解](https://blog.csdn.net/kozazyh/article/details/79795559)

[参数详细列表](https://docs.docker.com/engine/reference/commandline/dockerd/#daemon-configuration-file)


## registry client 操作 registry

### login

```bash
caohm@caohm-ThinkPad-E450:~$ docker login 172.16.1.146:8888
Username: caohm
Password: 
Login Succeeded
```
### tag image
```bash
caohm@caohm-ThinkPad-E450:~$ docker images | grep busybox
busybox                                                      latest              c75bebcdd211        22 months ago       1.11MB
caohm@caohm-ThinkPad-E450:~$ docker tag busybox:latest 172.20.27.7:5380/library/busybox:v1
caohm@caohm-ThinkPad-E450:~$ docker images | grep busybox
172.20.27.7:5380/library/busybox                             v1                  c75bebcdd211        22 months ago       1.11MB
busybox                                                      latest              c75bebcdd211        22 months ago       1.11MB
```
### push image to registry
```bash
caohm@caohm-ThinkPad-E450:~$ docker push 172.20.27.7:5380/library/busybox:v1 

```
### delete local image
```bash
caohm@caohm-ThinkPad-E450:~$ docker rmi 172.20.27.7:5380/library/busybox:v1
caohm@caohm-ThinkPad-E450:~$ docker images | grep busybox
busybox                                                      latest              c75bebcdd211        22 months ago       1.11MB
```

### pull image to registry

```bash
caohm@caohm-ThinkPad-E450:~$ docker pull 172.20.27.7:5380/library/busybox:v1 
caohm@caohm-ThinkPad-E450:~$ docker images | grep busybox
172.20.27.7:5380/library/busybox                             v1                  c75bebcdd211        22 months ago       1.11MB
busybox                                                      latest              c75bebcdd211        22 months ago       1.11MB
```

# 参考文档
----
*[Harbor实战](https://www.jianshu.com/p/cf16763942d5)
*[部署Harbor私有镜像仓库](https://blog.csdn.net/weixin_41465338/article/details/80146218)
*[harbor安装和简单使用](https://blog.csdn.net/qq_30062125/article/details/80942998)
*[Harbor安装指南](https://github.com/vmware/harbor/blob/master/docs/installation_guide.md)
*[https设置](https://github.com/goharbor/harbor/blob/master/docs/configure_https.md)
