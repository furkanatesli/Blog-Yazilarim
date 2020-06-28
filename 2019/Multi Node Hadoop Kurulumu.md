---
Başlık: Multi Node Hadoop Kurulumu
Paylaşım Tarihi: 02.11.2019
Etiketler: centos7, hadoop, java, linux, multi node
---

## Multi Node Hadoop Kurulumu

**CentOS 7 üzerinde Multi Node Hadoop Kurulumu**

Hadoop Sürümü: 3.2.1

test05: 10.254.183.17(Master)

test01: 10.254.183.14(Slave)

test02: 10.254.183.15(Slave)

test03: 10.254.183.16(Slave)

Hadoop Repo: https://archive.apache.org/dist/hadoop/core/

#### Adım 1: Sistem Güncelleme
Hadoop kurulumu öncesinde sistem güncellemesi yapılır.

```
yum install epel-release -y
yum update -y
shutdown -r now
```

Sistem Güncellemesi Yapılır.

#### Adım 2: Java’nın Yüklenmesi
Hadoop Java tabanlı olduğu için java yüklenir.

```
yum install -y java-1.8.0-openjdk
java -version
```

Kurulumdan sonra JAVA_HOME ve JRE_HOME ortam değişkenlerinin ayarları yapılır.

NOT: Otomatik java path işlemi için aşağıdaki komutlar kullanılabilir.

```
echo >>/etc/profile
jre_h=$(alternatives --display java | grep current | sed 's/link currently points to //' | tail -c +2 | sed 's|/jre/bin/java||')
java_h=$(alternatives --display java | grep current | sed 's/link currently points to //' | tail -c +2 | sed 's|/bin/java||')
d_jre_h="export JAVA_HOME=${jre_h}"
d_java_h="export JRE_HOME=${java_h}"
echo $d_jre_h >> /etc/profile
echo $d_java_h >> /etc/profile
echo 'export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin' >> /etc/profile
source /etc/profile
```

Manuel path işlemi yapmak istiyorsak.

```
vi /etc/profile
```

En alt satıra aşağıdaki komutlar eklenir.

```
export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk
export JRE_HOME=/usr/lib/jvm/jre
export PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
```

Değişikliklerin uygulanmısı için:

```
source /etc/profile
```

Çalışan işlemleri görmek için jps komutunu kullanırız.

```
jps
```

Eğer komut bulunamıyorsa aşağıdaki adımlar yapılır.

```
yum list java*devel*
yum install -y java-1.8.0-openjdk-devel.x86_64
```

Tekrar jps komutu kullanılır.

```
jps
```

#### Adım 3: Hosts Dosyası Güncelleme
hostlara domain vermek için tüm makinalardaki hosts dosyası güncellenir.

```
vi /etc/hosts
```
Dosya içerisine aşağıdaki gibi makina bilgilerini yazıyoruz ve kayıt edip çıkıyoruz.

```
10.254.183.14 test01
10.254.183.15 test02
10.254.183.16 test03
10.254.183.17 test05
```

#### Adım 4: SSH Ayarlanması
Master makinadan tüm makinalara şifresiz erişimin sağlanması gereklidir bunun için ilk olarak ssh ayarlanır.

Master makinadayken:

```
ssh-keygen -t rsa
```
komutu yazılarak ve gelen sorulara enter diyerek ssh anahtarı oluşturulur. Oluşturulan bu anahtarın diğer makinalara kopyalanması gerekiyor.

```
ssh-copy-id -i $HOME/.ssh/id_rsa.pub root@test01
```

Çıkan sorulara sırasıyla “yes” ve şifre bilgisi girilir. Ve tüm makinalar için aynı aşamalar tekrarlanır.(master’da dahil)

SSH ayarları test edilir.

```
ssh test01
```
yazarak şifresiz bağlantı testi yapılır.tüm makinalarda test edilmelidir.(master’da dahil)

#### Adım 5: Hadoop Kurulumu
Hadoop tüm makinalara yüklenir.

```
cd
wget https://archive.apache.org/dist/hadoop/core/hadoop-3.2.1/hadoop-3.2.1.tar.gz
```

Hadoop tardan çıkartılır. Ve dosya ismi değiştirilir.

```
tar -xzf hadoop-3.2.1.tar.gz -C /opt
cd /opt
mv hadoop-3.2.1 hadoop
```

##### Adım 5.1: Hadoop Kurulumu(Makinalar arası bağlantı hızı yüksekse)

Hadoop master makinaya yüklenir.

```
cd
wget https://archive.apache.org/dist/hadoop/core/hadoop-3.2.1/hadoop-3.2.1.tar.gz
```
scp komutu ile diğer makinalara taşınır.

```
scp hadoop-3.2.1.tar.gz root@test01:/opt
```

Master Makinadayken Hadoop tardan çıkartılır. Ve dosya ismi değiştirilir.

```
tar -xzf hadoop-3.2.1.tar.gz -C /opt
cd /opt
mv hadoop-3.2.1 hadoop
```

5.1/end

Diğer makinalara kopyaladığımız dosya “/opt” dizininde olduğu için

```
cd /opt
tar -xzf hadoop-3.2.1.tar.gz
mv hadoop-3.2.1 hadoop
```

Profile dosyası düzenlenir.

```
vi ~/.bashrc
```

En alt satıra aşağıdaki kodlar eklenir.

```
export HADOOP_HOME=/opt/hadoop
export HADOOP_INSTALL=$HADOOP_HOME
export HADOOP_MAPRED_HOME=$HADOOP_HOME
export HADOOP_COMMON_HOME=$HADOOP_HOME
export HADOOP_HDFS_HOME=$HADOOP_HOME
export YARN_HOME=$HADOOP_HOME
export HADOOP_COMMON_LIB_NATIVE_DIR=$HADOOP_HOME/lib/native
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
export HDFS_NAMENODE_USER="root"
export HDFS_DATANODE_USER="root"
export HDFS_SECONDARYNAMENODE_USER="root"
export YARN_RESOURCEMANAGER_USER="root"
export YARN_NODEMANAGER_USER="root"
```

Değişikliklerin uygulanması için:

```
source ~/.bashrc
```

“/opt/hadoop/etc/hadoop” dizinine gidilir.

```
cd /opt/hadoop/etc/hadoop
```
“hadoop-env.sh” dosyası açılarak JAVA_HOME satırı düzenlenir.

```
vi hadoop-env.sh
```

Java Home yerine aşağıdaki satır eklenir.

```
export JAVA_HOME=/usr/lib/jvm/jre-1.8.0-openjdk
```

core-site.xml açılır ve düzenlenir.

```
vi core-site.xml
```

Açılan core-site.xml dosyasına tagı arasına aşağıdaki kodlar eklenir.

```
<property>
<name>fs.default.name</name>
<value>hdfs://test05:9000</value>
</property>
```

hdfs-site.xml açılır ve düzenlenir.

```
vi hdfs-site.xml
```

Açılan hdfs-site.xml dosyasına tagı arasına aşağıdaki kodlar eklenir.

```
<property>
<name>dfs.replication</name>
<value>1</value>
</property>
<property>
<name>dfs.name.dir</name>
<value>file:/opt/hadoop/hadoopdata/hdfs/namenode</value>
</property>
<property>
<name>dfs.data.dir</name>
<value>file:/opt/hadoop/hadoopdata/hdfs/datanode</value>
</property>
<property> 
<name>dfs.webhdfs.enabled</name> 
<value>true</value>
</property> 
```

mapred-site.xml açılır ve düzenlenir.

```
vi mapred-site.xml
```

Açılan mapred-site.xml dosyasına tagı arasına aşağıdaki kodlar eklenir.

```
<property>
<name>mapreduce.framework.name</name>
<value>yarn</value>
</property>
<property>
<name>yarn.app.mapreduce.am.env</name>
<value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>
<property>
<name>mapreduce.map.env</name>
<value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>
<property>
<name>mapreduce.reduce.env</name>
<value>HADOOP_MAPRED_HOME=${HADOOP_HOME}</value>
</property>
```

yarn-site.xml açılır ve düzenlenir.

```
vi yarn-site.xml
```

Açılan yarn-site.xml dosyasına tagı arasına aşağıdaki kodlar eklenir.

```
<property>
<name>yarn.nodemanager.aux-services</name>
<value>mapreduce_shuffle</value>
</property>
```

Düzenlenilen bu dosyalar diğer makinalarada atılır.

```
scp core-site.xml hdfs-site.xml mapred-site.xml yarn-site.xml test01:/opt/hadoop/etc/hadoop
```
Masters dosyası yoksa oluşturulur ve düzenlenir varsa düzenlenir.(Tüm makinalarda)

```
vi masters
```

İçerisine master makinalara ait bilgiler girilir ve kayıt edilir.

```
test05
```

Slaves dosyası yoksa oluşturulur ve düzenlenir varsa düzenlenir.(Master makinada)

```
vi slaves
```

İçerisine slave makinalara ait bilgiler girilir ve kayıt edilir.

```
test01
test02
test03
```

Namenode formatlanır:

```
hdfs namenode -format
```

dfs deamon’u başlatılır.

```
start-dfs.sh
```

yarn deamon’u başlatılır.

```
start-yarn.sh
```

Daemon’ları kontrol etmek için jps komutu kullanılır.

```
jps
```

Eğer datanode deamonları başlamazsa her data nodeda datanode daemon’u manuel olarak başlşlatılır.

```
cd /opt/hadoop/sbin
hadoop-daemon.sh start datanode
```

Eğer datanodelar çalışmıyorsa tüm slave node’lar da hadoop-daemon.sh dosyası datanode olarak çalıştırılır.

```
hadoop-daemon.sh start datanode
```

All Application: 10.254.183.17:8088

Name Node: 10.254.183.17:9870

Hadoop içerisindeki dosyaları webhdfs içinde görmek için: 10.254.183.17:9870/explorer.html#/
