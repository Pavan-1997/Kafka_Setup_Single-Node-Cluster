# Kafka Setup Single Node Cluster

## We will be using 3 Zookeepers and 1 Kafka servers in this setup 

1. Install Java
   
```
sudo apt install default-jre
```


2. Download the Binary tar file for the Kafka not the Source
   
```
wget https://downloads.apache.org/kafka/3.6.1/kafka-3.6.1-src.tgz (Source)
```
```
wget https://downloads.apache.org/kafka/3.6.1/kafka_2.13-3.6.1.tgz (Binary)
```


3. Extract the tar file and Rename it for ease of access
   
```
tar -xvf kafka-3.6.1-src.tgz
```
```
mv kafka-3.6.1-src kafka 
```


4. Create 3 Config files for 3 Zookeeper instances

```
cd config
```
```
mv zookeeper.properties zookeeper1.properties
```
```
cp zookeeper1.properties zookeeper2.properties
```
```
cp zookeeper1.properties zookeeper3.properties
```


5. Create the data directory for all the 3 Zookeeper instance to store the data `WD - Working Directory`
   
```
mkdir -p /WD/kafka/data/zookeeper1
```
```
mkdir -p /WD/kafka/data/zookeeper2
```
```
mkdir -p /WD/kafka/data/zookeeper3
```


6. Creating the unique id for each Zookeeper instance

```
echo 1 > /WD/kafka/data/zookeeper1/myid
```
```
echo 2 > /WD/kafka/data/zookeeper2/myid
```
```
echo 3 > /WD/kafka/data/zookeeper3/myid
```


7. Edit Zookeeper properties files use the same for all the 3 (Change happens in dataDir and clientPort feilds)

   `dataDir= /kafka/data/zookeeper1`
   
   `clientPort=2181`
   
   Now we will also add Test server config in the same properties file
```
tickTime=2000
initLimit=5
syncLimit=2
server.1=localhost:2887:3887
server.2=localhost:2888:3888
server.3=localhost:2889:3889
maxClientCnxns=0
```

   Use below copy commands to do for other Zookkeeper instances but chnage happens in dataDir and clientPort fields
   
```
cp zookeeper1.properties zookeeper2.properties
```
```
cp zookeeper1.properties zookeeper3.properties
```

8. Start the Zookeeper
   
```
cd ~/kafka/bin
```
```
./zookeeper-server-start.sh -daemon /kafka/config/zookeeper1.properties
```
```
./zookeeper-server-start.sh -daemon /kafka/config/zookeeper2.properties
```
```
./zookeeper-server-start.sh -daemon /kafka/config/zookeeper3.properties
```


9. To check Zookeeper running

```
nc -v localhost 2181
```
```
nc -v localhost 2182
```
```
nc -v localhost 2183
```

![ZOOKEEPER_START](https://github.com/Pavan-1997/Kafka_Setup_Single-Node-Cluster/assets/32020205/b4e8a480-a4b1-4a96-8f1e-fe1ddfa43cae)

Also use below to check

```
ps -ef | grep -i zookeeper 
```


10. Create Zookeeper as a service

```
sudo vi /usr/lib/systemd/system/zookeeper1.service
```
```
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
```
```
systemctl daemon-reload
```
```
systemctl enable zookeeper1.service
```
```
systemctl start zookeeper1.service
```
```
systemctl status zookeeper1.service
```
```
systemctl stop zookeeper1.service
```

![ZOOKEEPER_SERVICE](https://github.com/Pavan-1997/Kafka_Setup_Single-Node-Cluster/assets/32020205/465b19d3-c0b0-4272-8a5f-5c63efe6b90d)

Similarily create service for Zookeeper2 and Zookeeper3


11. Now configure the Kafka using the server properties file by modifying below parameters prior create a directory for `kafka-logs`

```
cd /kafka/data/
```
```
mkdir kafka-logs
```
```
cd /kafka/config/
```
```
vi server.properties
```

`listeners=PLAINTEXT://0.0.0.0:9092`

`advertised.listeners=PLAINTEXT://localhost:9092`

`log.dirs=/kafka/data/kafka-logs`

`zookeeper.connect=localhost:2181, localhost:2182, localhost:2183`


12. Start the Kafka server

```
cd /kafka/bin/
```
```
./kafka-server-start.sh -daemon /kafka/config/server.properties
```


13. To check Kafka running

```
nc -v localhost 9092
```


14. Create a Topic 

```
cd /kafka/bin
```
```
./kafka-topics.sh --bootstrap-server 127.0.0.1:9092 --create --topic TestTopic --replication-factor 1 --partitions 2 
```

![KAFKA_TOPIC_CREATE](https://github.com/Pavan-1997/Kafka_Setup_Single-Node-Cluster/assets/32020205/91052aed-cd99-45b9-bd4e-49a584506b63)


15. To list the topics

```
./kafka-topics.sh --list --bootstrap-server 127.0.0.1:9092
```


16. To produce messages

```
./kafka-console-producer.sh --broker-list 127.0.0.1:9092 --topic TestTopic
```


17. To consume those messages
    
```
./kafka-console-consumer.sh --bootstrap-server 127.0.0.1:9092 --opic TestTopic --from-beginning
```
