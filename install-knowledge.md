# CentOS7にTomcat8 + knowledgeのセットアップ

## 前提条件
Apacheインストール済み


// アップデート

$ yum -y update

## Java8インストール
$ curl -LO -H "Cookie: oraclelicense=accept-securebackup-cookie" \
"http://download.oracle.com/otn-pub/java/jdk/8u162-b12/0da788060d494f5095bf8624735fa2f1/jdk-8u162-linux-x64.rpm"

$ rpm -Uvh jdk-8u162-linux-x64.rpm

$ vi /etc/profile
```
export JAVA_HOME=/usr/java/default
export PATH=$PATH:$JAVA_HOME/bin
export CLASSPATH=.:$JAVA_HOME/jre/lib:$JAVA_HOME/lib:$JAVA_HOME/lib/tools.jar
```

$ source /etc/profile


## Tomcatのセットアップ

$ curl -O http://ftp.jaist.ac.jp/pub/apache/tomcat/tomcat-8/v8.5.28/bin/apache-tomcat-8.5.28.tar.gz

$ tar zxvf apache-tomcat-8.5.28.tar.gz

$ mvapache-tomcat-8.5.28 /usr/libexec/tomcat8

$ useradd -M -d /usr/libexec/tomcat8 tomcat

$ chown -R tomcat. /usr/libexec/tomcat8

$ vi /usr/lib/systemd/system/tomcat8.service
```
[Unit]
Description=Apache Tomcat 8
After=network.target

[Service]
Type=oneshot
ExecStart=/usr/libexec/tomcat8/bin/startup.sh
ExecStop=/usr/libexec/tomcat8/bin/shutdown.sh
RemainAfterExit=yes
User=tomcat
Group=tomcat

[Install]
WantedBy=multi-user.target
```

$ systemctl start tomcat8

$ systemctl enable tomcat8


## firewalldの設定
$ firewall-cmd --zone=public --add-port=8080/tcp --permanent

$ firewall-cmd --reload


## knowledgeのセットアップ
$ cd /usr/libexec/tomcat8/webapps
$ wget https://github.com/support-project/knowledge/releases/download/v1.12.0/knowledge.war


## Apacheとの連携
$ vim /etc/httpd/conf.d/proxy-ajp.conf
```
<Location /knowledge >
  ProxyPass ajp://localhost:8009/knowledge                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                       
  Order allow,deny
  Allow from all
</Location>
```

$ vim /usr/libexec/tomcat8/conf/server.xml
以下のようにコメントアウトする
```
<!--
<Connector port="8080" protocol="HTTP/1.1"
  connectionTimeout="20000"
  redirectPort="8443" />
-->
```

$ systemctl restart httpd.service




///////////////////////////////////////////

## 参考サイト

- [knowledge release](https://github.com/support-project/knowledge/releases)
- [Tomcat8](https://tomcat.apache.org/download-80.cgi)
- [JDK8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
- [Javaのインストール](https://www.server-world.info/query?os=CentOS_7&p=jdk9)
- [Apacheとの連携](https://qiita.com/Dace_K/items/9d0419aefcb969335ca5)
- [firewallの設定](https://qiita.com/fk_2000/items/ff7b4768260459553fa1)


