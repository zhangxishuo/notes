# 开发环境

## CentOS 镜像

```bash
# 备份
mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup

# 下载
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo

# 生成缓存
yum makecache
```

## JDK

```bash
# 安装 JDK
yum install java-1.8.0-openjdk.x86_64
```

## Tomcat

```bash
# 下载 Tomcat
wget https://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-9/v9.0.31/bin/apache-tomcat-9.0.31.tar.gz

# 解压
tar -zxvf apache-tomcat-9.0.31.tar.gz

# 更改编码
vim conf/server.xml
```

```xml
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000" redirectPort="8443" URIEncoding="UTF-8" />
```

```bash
# 启动 Tomcat
bin/startup.sh
```