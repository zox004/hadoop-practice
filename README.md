# Hadoop Practice
## SSH(Secure Shell Protocol)
* **What is SSH?**
  * 안전하지 않은 네트워크 상에서 안전하게 통신할 수 있는 암호화 네트워크 프로토콜
* **Where does SSH used?**
  * 파일 전송
  * 원격 접속
* **Why should we use SSH?**
  * 기존의 Telnet과 같은 불안전한 Remote Shell Protocol들은 사용자의 아이디와 비밀번호를 평문으로 전송함(패킷 분석을 통해 정보를 가로채어 노출시킬 수 있음)
  * SSH는 개인키와 공개키를 통해 Client-Server 구조의 안전한 통신 채널을 형성해 줌(RSA 공개키 암호화)

* **SSH Architecture**
  * SSH-Client
    * Server로 접속 요청을 보내는 곳
    * 한 쌍의 개인키 & 공개키를 생성하고, 공개키를 Server로 전송
  * SSH-Server
    * Client가 원격으로 접속하기를 원하는 곳
    * Client가 생성한 공개키를 전달 받음
* **SSH on Hadoop**
  * Master는 한 쌍의 공개키 & 개인키를 생성
  * Master의 개인키를 모든 Slave에 복사

![master-slave](https://user-images.githubusercontent.com/56228085/169262355-3ce65886-181e-4bba-b1b0-327221ebae32.PNG)
* **How SSH works?**
  1. 클라이언트가 서버에 접속 승인 요청과 함께 공개키를 요구
  2. 서버는 공개키를 찾은 후에 클라이언트가 개인키를 가지고 있는지 확인 요청
  3. 클라이언트는 개인키가 있는지 탐색한 후에 개인키를 찾았다고 응답
  4. 서버가 SSH 접속을 승인 및 연결


## Configuration
### **Installation**
  * Virtual Machine Tool : Oracle Virtualbox
  * OS : Linux(ubuntu 16.04)
### **Editing VM Network Environment**
  * Settings 
    * Network
      * Adapter1 : Connect to NAT
      * Adapter2 : Connect to Host-Only Adapter
    * General - Advanced 
      * sharing Clipboard : both-way
      * Drag and Drop : both-way
