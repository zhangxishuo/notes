# 项目部署

## 项目打包

打包项目并跳过单元测试：

```shell
mvn clean package -Dmaven.test.skip=true
```

## 文件拷贝

将打包后的文件拷贝到服务器：

```shell
scp target/sell.jar root@192.168.30.113:/opt/apps
```

## CentOS

启动后台服务：

```shell
nohup java -jar sell.jar > /dev/null 2>&1 &
```

定位到系统服务目录：

```shell
cd /etc/systemd/system
```

创建文件`(app).service`，并编辑：

```shell
[Unit]
Description=app
After=syslog.target network.target

[Service]
Type=Simple

ExecStart=/usr/bin/java -jar /opt/apps/app.jar
ExecStop=/bin/kill -15 $MAINPID

User=root
Group=root

[Install]
WantedBy=multi-user.target
```

启动服务：

```shell
systemctl start app
```

关闭服务：

```shell
systemctl stop app
```