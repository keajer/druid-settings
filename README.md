## Druid Installation:

* install java:
```
cd /opt
wget --no-cookies --no-check-certificate --header "Cookie: gpw_e24=http%3A%2F%2Fwww.oracle.com%2F; oraclelicense=accept-securebackup-cookie" "http://download.oracle.com/otn-pub/java/jdk/8u121-b13/e9e7ea248e2c4826b92b3f075a80e441/jdk-8u121-linux-x64.tar.gz"
tar xzf jdk-8u73-linux-x64.tar.gz
update-alternatives --install /usr/bin/java java /opt/jdk1.8.0_73/bin/java 2
update-alternatives --config java

update-alternatives --install /usr/bin/jar jar /opt/jdk1.8.0_73/bin/jar 2
update-alternatives --install /usr/bin/javac javac /opt/jdk1.8.0_73/bin/javac 2
update-alternatives --set jar /opt/jdk1.8.0_73/bin/jar
update-alternatives --set javac /opt/jdk1.8.0_73/bin/javac 
echo "export JAVA_HOME=/opt/jdk1.8.0_73" >> /root/.bashrc
echo "export JRE_HOME=/opt/jdk1.8.0_73/jre" >> /root/.bashrc
echo "export PATH=\$PATH:/opt/jdk1.8.0_73/bin:/opt/jdk1.8.0_73/jre/bin" >> /root/.bashrc
source /root/.bashrc
```
* install zookeeper:
```
cd /opt
curl http://www.gtlib.gatech.edu/pub/apache/zookeeper/zookeeper-3.4.8/zookeeper-3.4.8.tar.gz -o zookeeper-3.4.8.tar.gz
tar xzf zookeeper-3.4.8.tar.gz
cd zookeeper-3.4.8
cp conf/zoo_sample.cfg conf/zoo.cfg
./bin/zkServer.sh start
```

* install mysql
```
cd /tmp
wget http://dev.mysql.com/get/mysql-apt-config_0.6.0-1_all.deb
sudo dpkg -i mysql-apt-config_0.6.0-1_all.deb
sudo apt-get update
sudo apt-get install mysql-server-5.7
sudo service mysql status
sudo service mysql start
sudo mysql
CREATE DATABASE druid DEFAULT CHARACTER SET utf8;
CREATE USER 'druid'@'localhost' IDENTIFIED BY 'druid';
GRANT ALL ON druid.* TO 'druid'@'localhost';
```
> below need to be down for both overlord and coordinator hosts

```
CREATE USER 'druid'@'overlord hostname' IDENTIFIED BY 'druid';
GRANT ALL ON druid.* TO 'druid'@'overlord hostname';
\q
mysql -u druid -pdruid -D druid
\q
```
> add mysql configs, then restar:

```
vi /etc/mysql/my.conf
bind_address: 0.0.0.0
init_connect='SET collation_connection = utf8_unicode_ci' 
init_connect='SET NAMES utf8' 
character-set-server=utf8 
collation-server=utf8_unicode_ci 
skip-character-set-client-handshake
show_compatibility_56 = on
```
> nice to have on mysql node

```
echo "overlord hostname" >> /etc/hosts
echo "coordinator hostname" >> /etc/hosts
```

* install memcached:
> this is skipped for now


* install druid:
```
cd /opt
wget http://static.druid.io/artifacts/releases/druid-0.8.3-bin.tar.gz
tar xzf druid-0.8.3-bin.tar.gz
cd druid-0.8.3/lib/
wget http://repo1.maven.org/maven2/org/fusesource/sigar/1.6.4/sigar-1.6.4.jar
```
> start individual servers with following command

```
/opt/druid-0.8.3/run_druid_server.sh overlord
..
..
/opt/druid-0.8.3/run_druid_server.sh broker
```
