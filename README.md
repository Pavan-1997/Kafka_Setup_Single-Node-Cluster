# Kafka Setup Single Node Cluster

## We will be using 3 Zookeepers and 1 Kafka servers in this setup 

1. Install Java
   
```
sudo apt install default-jre
```

Producer - Subscriber

wget https://downloads.apache.org/kafka/3.6.1/kafka-3.6.1-src.tgz (Source)
wget https://downloads.apache.org/kafka/3.6.1/kafka_2.13-3.6.1.tgz (Binary)

tar -xvf kafka-3.6.1-src.tgz

mv kafka-3.6.1-src kafka 



Create 3 config files for 3 Zookeeper instances

cd config

mv zookeeper.properties zookeeper1.properties

cp zookeeper1.properties zookeeper2.properties

cp zookeeper1.properties zookeeper3.properties


Create the data directory for all the 3 Zookeeper instance to store the data

mkdir -p /WD/kafka/data/zookeeper1

mkdir -p /WD/kafka/data/zookeeper2

mkdir -p /WD/kafka/data/zookeeper3


Creating the unique  id for each Zookeeper instance

echo 1 > /WD/kafka/data/zookeeper1/myid

echo 2 > /WD/kafka/data/zookeeper2/myid

echo 3 > /WD/kafka/data/zookeeper3/myid


Edit Zookeeper properties files use the same for all the 3 (Change happens in dataDir and clientPort feilds)

dataDir= /kafka/data/zookeeper1
clientPort=2181

Test server config

tickTime=2000
initLimit=5
syncLimit=2
server.1=localhost:2887:3887
server.2=localhost:2888:3888
server.3=localhost:2889:3889
maxClientCnxns=0

cp zookeeper1.properties zookeeper2.properties

cp zookeeper1.properties zookeeper3.properties


Start the Zookeeper

cd ~/kafka/bin

./zookeeper-server-start.sh -daemon /kafka/config/zookeeper1.properties

./zookeeper-server-start.sh -daemon /kafka/config/zookeeper2.properties

./zookeeper-server-start.sh -daemon /kafka/config/zookeeper3.properties


To check Zookeeper running

nc -v localhost 2181

nc -v localhost 2182

nc -v localhost 2183


Also use below to check

ps -ef | grep -i zookeeper 


Create Zookeeper as a service

sudo vi /usr/lib/systemd/system/zookeeper1.service

[Unit]
Description=Zookeeper1 Service

[Service]
Type=simple
WorkingDirectory=/kafka/data/zookeeper1/
PIDFile=/kafka/data/zookeeper1_server.pid
SyslogIdentifier=zookeeper1
User=root
Group=root
ExecStart=/kafka/bin/zookeeper-server-start.sh /kafka/config/zookeeper1.properties
ExecStop=/kafka/bin/zookeeper-server-stop.sh 
Restart=always
TimeoutSec=20
SuccessExitStatus=130 143
Restart=on-failure

[Install]
WantedBy=multi-user.target


systemctl daemon-reload

systemctl enable zookeeper1.service

systemctl start zookeeper1.service

systemctl status zookeeper1.service

systemctl stop zookeeper1.service

Similarily create service for Zookeeper2 and Zookeeper3


Now configure the Kafka 

cd /kafka/config/

vi server.properties


listeners=PLAINTEXT://0.0.0.0:9092

advertised.listeners=PLAINTEXT://localhost:9092

log.dirs=/kafka/data/kafka-logs

zookeeper.connect=localhost:2181, localhost:2182, localhost:2183



cd /kafka/bin/

./kafka-server-start.sh /kafka/config/server.properties

./kafka-server-start.sh -daemon /kafka/config/server.properties

nc -v localhost 9092



Create a Topic

cd /kafka/bin

./kafka-topics.sh --zookeeper localhost:2181, localhost:2181, localhost:2182 --create --topic TestTopic --replication-factor 1 --partitions 2 
./kafka-topics.sh --bootstrap-server 127.0.0.1:9092 --create --topic TestTopic --replication-factor 1 --partitions 2 


To list the topics:

./kafka-topics.sh --list --bootstrap-server 127.0.0.1:9092


To produce messages:

./kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic TestTopic


To consume those messages:

./kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --opic TestTopic --from-beginning
