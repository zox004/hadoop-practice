# Hadoop Practice

## What is Hadoop?
- 안정적(reliable)이고 확장 가능(scalable)하고 분산 컴퓨팅(distributed computing)이 가능한 오픈 소스 소프트웨어
- 간단한 프로그래밍 모델을 사용하여 컴퓨터 클러스터에 걸쳐 대규모 데이터 분산 처리를 구현
- 단일 서버에서 수천 대의 시스템으로 확장

## SSH(Secure Shell Protocol)
- **What is SSH?**
  - 안전하지 않은 네트워크 상에서 안전하게 통신할 수 있는 암호화 네트워크 프로토콜
- **Where does SSH used?**
  - 파일 전송
  - 원격 접속
- **Why should we use SSH?**
  - 기존의 Telnet과 같은 불안전한 Remote Shell Protocol들은 사용자의 아이디와 비밀번호를 평문으로 전송함(패킷 분석을 통해 정보를 가로채어 노출시킬 수 있음)
  - SSH는 개인키와 공개키를 통해 Client-Server 구조의 안전한 통신 채널을 형성해 줌(RSA 공개키 암호화)

- **SSH Architecture**
  - SSH-Client
    * Server로 접속 요청을 보내는 곳
    * 한 쌍의 개인키 & 공개키를 생성하고, 공개키를 Server로 전송
  - SSH-Server
    * Client가 원격으로 접속하기를 원하는 곳
    * Client가 생성한 공개키를 전달 받음
- **SSH on Hadoop**
  - Master는 한 쌍의 공개키 & 개인키를 생성
  - Master의 개인키를 모든 Slave에 복사

![master-slave](https://user-images.githubusercontent.com/56228085/169262355-3ce65886-181e-4bba-b1b0-327221ebae32.PNG)
- **How SSH works?**
  1. 클라이언트가 서버에 접속 승인 요청과 함께 공개키를 요구
  2. 서버는 공개키를 찾은 후에 클라이언트가 개인키를 가지고 있는지 확인 요청
  3. 클라이언트는 개인키가 있는지 탐색한 후에 개인키를 찾았다고 응답
  4. 서버가 SSH 접속을 승인 및 연결

## Configuration
### **Installation**
    - Virtual Machine Tool : Oracle Virtualbox
    - OS : Ubuntu 16.04-LTS
### **Editing VM Network Environment**
  - Settings 
    - Network
      - Adapter1 : Connect to NAT
      - Adapter2 : Connect to Host-Only Adapter
    - General - Advanced 
      - sharing Clipboard : both-way
      - Drag and Drop : both-way

```bash
cd /etc/network
sudo vi interfaces
```

![image](https://user-images.githubusercontent.com/56228085/169470431-99ff2531-f6ec-4db9-9614-5df62dad51ec.png)

```bash
systemctl restart networking.service
```

### Requirements to install Hadoop
- Java
 - Hadoop framework is implemented in java
 - Version 2.7 and later for Apache Hadoop requires Java 7
- Hadoop Binary File

### Install and Set-up Java
- Install Java
```bash
sudo apt-get install openjdk-8-jdk
```
- Add JAVA_HOME(Environment Variable Setting)
```bash
sudo vi /etc/environment
Add JAVA_HOME="/usr/lib/jvm/java-8-openjdk-amd64"
```

- Update Environment Variable
```bash
source /etc/environment
```

- Check Whether it installed Correctly
```bash
java -version
```

### Download Hadoop
```bash
cd /home
```
- Download hadooop
```bash
sudo wget http://apache.mirror.cdnetworks.com/hadoop/commom/hadoop-3.3.0/hadoop-3.3.0.tar.gz
```
- Decompress hadoop
```bash
sudo tar -xzf hadoop-3.3.0.tar.gz
sudo chmod 757 hadoop-3.3.0
```

### Register Environment Variable
- Register Environment Variable
```bash
vi ~/.bashrc
```
![image](https://user-images.githubusercontent.com/56228085/169474471-10176c63-b062-48f7-b067-d323914cb53f.png)

- Update Environment Variable
```bash
source ~/.bashrc
```

### Register Java Path in Hadoop
- Register Java Path in hadoop
```bash
sudo vi /home/hadoop-3.3.0/etc/hadoop/hadoop-env.sh
Add export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

- Update Hadoop Configuration
```bash
source /home/hadoop-3.3.0/etc/hadoop/hadoop-env.sh
hadoop version
```

### Prepare three machine
- Copy the pre-machine
 - Ubuntu-Master
 - Ubuntu-Slave1
 - Ubuntu-Slave2

- Allocate different IP for 3 Machines
```bash
sudo vi /etc/network/interfaces on all Machines
- Modify <Master Machine> to IP 192.168.56.19
- Modify <Master Machine> to IP 192.168.56.20
- Modify <Master Machine> to IP 192.168.56.21
```
- Modify hostname file on all Machines
```bash
sudo vi /etc/hostname
- Modify Hostname 'Master' to IP 192.168.56.19
- Modify Hostname 'Slave1' to IP 192.168.56.20
- Modify Hostname 'Slave2' to IP 192.168.56.21
```
- Modify hosts file
```bash
sudo vi /etc/hosts on all Machines
127.0.0.1  localhost
192.168.56.19  Master
192.168.56.20  Slave1
192.168.56.21  Slave2
```
```bash
reboot
```

- Download Openssh on all Machines
```bash
sudo apt-get install openssh-client openssh-server
```

- Settings for SSH Connection
 - RSA 방식으로 키 생성 on Master Machines
```bash
ssh-keygen -t rsa
```

 - Master의 공개키를 Slave로 전송 on Master Machines
```bash
scp ~/.ssh/id_rsa.pub test@Master:~/.ssh/authorized_keys
scp ~/.ssh/id_rsa.pub test@Slave1:~/.ssh/authorized_keys
scp ~/.ssh/id_rsa.pub test@Slave2:~/.ssh/authorized_keys
```
 - Check
```bash
ssh test@Slave1
ssh test@Slave2
```

## Apache Hadoop
- Large clusters of computers를 통해 Large Datasets를 distributed processing하기 위한 Software Framework
- Map-Reduce라는 데이터 처리 모델을 기반으로 한다

### Architecture
- Multi-Layer 구조
  - MapReduce Layer
    - Job Tracker : 사용자로부터 Job을 요청 받고 Task Tracker에 작업 할당
    - Task Tracker : Job Tracker로부터 할당 받은 작업을 Map-Reduce하여 결과 반환
  - HDFS Layer
    - Name Node : 작업을 해야 하는 파일을 Block으로 나누어 Data Node에 전달
    - Data Node : 전달받은 파일의 읽기/쓰기 등을 실제로 수행

- Master-Slave 구조
  - Master Node(Single Node)
    - Name Node
    - Data Node
    - Job Tracker
    - Task Tracker
  - Slave Node
    - Data Node
    - Task Tracker

![image](https://user-images.githubusercontent.com/56228085/169651996-2105575f-cac9-4f61-b04c-798b56f31045.png)

## Hadoop Cluster Configuration
- Move to Hadoop settings directory on Master Machine
```bash
cd /home/hadoop-3.3.0/etc/hadoop
```
- Edit slaves file : Slaves 등록
```bash
sudo vi workers
Add Slave1
Add Slave2
```

- Edit core-site.xml file : NameNode 위치 설정
```bash
sudo vi core-site.xml
```



