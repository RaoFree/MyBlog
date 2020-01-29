title: 用Docker搭建DNS服务器
date: 2018-08-07 19:14:43
---
环境：CentOS 7
 
# 一、安装Docker
### 1.查看内核版本
Docker 要求 CentOS 系统的内核版本高于 3.10
```
uname -r
```
[image:5A8F2B2B-0FEA-4453-A7E1-EB67D442AC35-16891-00009B57C7D47126/5ECBA7A9-B9EB-4B96-8D64-D16ABF20116D.png]
 
### 2.安装Docker
```
yum -y install docker-io
```
[image:60F221DD-4E71-4405-9C58-73F2C94C124F-16891-00009B7C48CB6CE8/6EB39140-C812-4898-92E5-DDD915B509C7.png]
 
### 3.启动Docker服务
```
service docker start
```
 
### 4.安装docker-compose
这里安装1.22.0最新版本
```
curl -L https://github.com/docker/compose/releases/download/1.22.0/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose
```
 
增加可执行权限
```
chmod +x /usr/local/bin/docker-compose
```
 
# 二、Docker搭建DNS服务器
### 1.下载andyshinn/dnsmasq镜像
```
docker pull andyshinn/dnsmasq
```
 
### 2.创建dns卷挂载路径
```
/dockerdata/docker-dns/
```
 
### 3.创建配置文件
```
/dockerdata/docker-dns/resolv.dnsmasq
```
 
添加公共DNS地址：
```
vi /dockerdata/docker-dns/resolv.dnsmasq
```
[image:BD9C4008-8E50-4CA1-B591-DFDFAD4DA37D-16891-00009BC1CD928B08/A5AAC712-4B13-453B-A980-65FB24636595.png]
 
### 4.创建配置文件
```
/dockerdata/docker-dns/dnsmasqhosts
```
 
添加DNS解析规则：
```
vi /dockerdata/docker-dns/dnsmasqhosts
```
[image:6898CCB2-4B05-4F71-B597-ABC36B5A74C6-16891-00009BCA5CB8233D/8E7F45AF-EE70-4312-9C8D-2BAF0F7160F7.png]
 
### 5.新建配置文件
```
/dockerdata/docker-dns/dnsmasq.conf
```
 
```
vi /dockerdata/docker-dns/dnsmasq.conf
```
 
内容：
```
resolv-file=/etc/resolv.dnsmasq
addn-hosts=/etc/dnsmasqhosts
```
[image:69D7AC16-1493-40C9-A2C4-1C0D2437917F-16891-00009BCF972690B3/27ACD73E-1B96-4DE6-A237-8BB7274A87E4.png]
 
### 6.新建Docker启动文件，配置Docker的DNS服务
```
/dockerdata/docker-dns/dns.yaml
```
 
```
vi /dockerdata/docker-dns/dns.yaml
```
 
内容：
```
version: “2”
services:
  docker-dns:
    container_name: docker-dns
    image: andyshinn/dnsmasq
    hostname: docker-dns
    volumes:
      - /dockerdata/docker-dns/resolv.dnsmasq:/etc/resolv.dnsmasq
      - /dockerdata/docker-dns/dnsmasqhosts:/etc/dnsmasqhosts
      - /dockerdata/docker-dns/dnsmasq.conf:/etc/dnsmasq.conf
      - /etc/localtime:/etc/localtime:ro
    ports:
      - 53:53/tcp
      - 53:53/udp
    cap_add: 
      - NET_ADMIN
    restart: on-failure:1
```
 
### 7.运行Docker的DNS服务
```
docker-compose -f /dockerdata/docker-dns/dns.yaml up -d
```
[image:5E38B6F3-53ED-4BBD-B8E7-11A4E1F3AFD3-16891-00009BD745AC0F9C/EB89A2E9-BC90-4F54-8972-B73C545F8480.png]
 
### 8.修改宿主机的DNS服务器
回到宿主机，修改宿主机DNS服务器地址为：docker-dns所在虚拟机IP
[image:3D9ED3CC-0A1E-4819-92CC-09804E521600-16891-00009BDBF257BEB6/51A26DC9-2A92-4C39-B126-F8E92BAC70AF.png]
 
### 9.在宿主机上进行测试
在宿主机的命令行中，使用dig命令，对虚拟机配置文件dnsmasqhosts中配置的IP-域名映射，进行测试。
这里是：
```
10.211.55.8  raofree.com
```
[image:ACAAF793-B106-48D1-A095-5F91A76056F6-16891-00009BE484FF43D5/045AA063-CCE5-40E6-A258-7C7E2EBA2F82.png]
 
由图可见：Docker的DNS服务器上配置的域名raofree.com，在宿主机上通过该DNS服务器解析成功。
 
 
# 三、遇到的问题&解决方案
### 1.问题
在运行Docker时，执行：
```
docker-compose -f /dockerdata/docker-dns/dns.yaml up -d
```
 
报异常：
```
ERROR: Conflict. The container name “/docker-dns” is already in use by container f62d2e5ee13c8e22bf1f2f918c94b7534e6f6f7f8516a530d929023fd6645812. You have to remove (or rename) that container to be able to reuse that name.
```
 
### 2.解决方案
列出所有容器，执行：
```
docker ps -a
```
[image:6562913C-EF71-47A0-9B8D-5BF5964B3ED5-16891-00009C1AE41EA707/692F2356-F75F-48E0-AF66-F6F79F28D5F1.png]
 
找到报错信息中的container_id
根据id（建议写id号前4+位）删除docker容器
执行：
```
docker rm f6
```
[image:86E2A055-82F9-461C-9F25-7A2B4EF9082E-16891-00009C1EB9833416/A78DEB54-601F-4CFB-AF44-BC7C2D5D0748.png]
 
再次运行Docker，执行：
```
docker-compose -f /dockerdata/docker-dns/dns.yaml up -d
```
 
成功
