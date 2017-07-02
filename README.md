Kafka Cluster Role 
=========

This role sets up Kafka on a cluster of servers 

AWS Prerequisites
------------

### Amazon Linux servers

#### Internet access from the Kafka servers

### Open ports 

* between the three servers: Ports: 2181, 2888, 3888, 9092
* from Clients to kafka servers: Port: 9092
* from Ansible server to Kafka serves: Port: 22

### Disks and File Systems

In addition to the root file system have two more:

| Disk | Size | mount point |
|---|---|---|
| ZooKeeper | 10Gb	| /zookeeper |
| Kafka | 240Gb | /kafka | 

OS Level Prerequisites
------------

### SSH Access to the servers

Add ansible inventory ssh public key to user `ec2-user`

### Create file systems

Example commands:

```
fdisk /dev/xvdb
  n #new partition default values
  w # write and quit
mkfs.ext4 /dev/xvdb1
```
To create the mounts:

Example lines in `/etc/fstab`

```
/dev/xvdd1   /zookeeper  ext4    defaults        0   0
/dev/xvdb1   /kafka      ext4    defaults        0   0
```

```
mkdir /kafka /zookeeper
mount -a
```

### Add users:

```
sudo groupadd -g 502 kafkagroup
sudo useradd -m -d /home/kafkauser -s /bin/bash -G 502 -u 502 kafkauser
```

### Update ownership of mounted filesystems

```
sudo chown kafkauser:kafkagroup /kafka
sudo chown kafkauser:kafkagroup /zookeeper
```

3rd Party software Prerequisites
------------

Java Development Kit >= 8

Role tested with Oracle JDK version 8u121

Example Inventory `hosts` and `groups` files
------------------

### `hosts`

```
kafka1 ansible_host=10.0.0.101  zk_id=1
kafka2 ansible_host=10.0.0.102  zk_id=2
kafka3 ansible_host=10.0.0.103  zk_id=3
```

# `groups`

```
[kafkas]
kafka1
kafka2
kafka3
```

Example Playbook file
------------------

`kafka-setup.yml`

```
---
- hosts: kafkas
  roles: 
    - setup-kafka 
```

Example Playbook run
------------------

```
ansible-playbook kafka-setup.yml
```