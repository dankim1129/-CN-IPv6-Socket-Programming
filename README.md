# -CN-IPv6-Socket-Programming
 - 2017년 1학기 성균관대학교 최형기 교수님 컴퓨터네트웤개론 수업 3번째 과제
 - Socket programming을 통해 IPv6 주소를 갖는 client와 server간의 패킷 송,수신 프로그램 
 - 2017 1st semester Sungkyunkwan University Professor Hyoung-Kee Choi's Introduction to Computer Networking class, 3rd assignment
 - Packet sending and receiving program between client and server having IPv6 address through socket programming

## 1. Problem
### (1) Object
 - IPv6 is an emerging protocol which will replace the IPv4 protocol and will become the most popular protocol within 7 years. Hence, it is important for us to understand this important protocol. The Socket interface is API to bridge between applications and the kernel. The socket programming is useful when an application deals with communicating another process in the same machine or different machines.
   
### (2) Communication protocol
 - You must write socket programming in the client and the server. Your client must connect to the designated server running in 2001:0:53aa:64c:289f:12c9:8c6e:5546 IPv6 address and 38492 TCP port number. The designated server uses the following protocol to communicate with the client.
 
   [1] First stage
     - After connecting to the server the designated server will ask your ID and your server’s IP address and port number. Your server should be running at this time and have a valid IPv4 address and TCP port number.
 
   [2] Second stage
     - The designated starts the client on its machine and opens the second connection to your server on the given the IP address and the port number. The new client will deliver the 20-byte string (token) on the second connection. Your server must pass the token to your client and your client must return this token to the designated server on the first connection.
     
       ![image](https://user-images.githubusercontent.com/26705935/40899334-61aa4f2e-6801-11e8-87c2-8e58929ac6f1.png)

### (3) Instruction
 - In the beginning the banner will welcome you to my machine. 
 - You must return the token received from the second connection to the first connection.
 - All the input must be terminated by the carriage return.
 - You can also connect to the designated server using “ping” for the connectivity test, nothing else.
 - Report immediately if you find a bug.
 - Make sure that I am available to connect your server.
 
### (4) Program details
 - The following figure shows the flow of the program.
 
   ![image](https://user-images.githubusercontent.com/26705935/40899387-b8815e82-6801-11e8-8038-83a4c20465c2.png)
 
## 2. Environment
 - language : C
 - IDE : Microsoft Visual studio 2017
 - OS : Linux Ubuntu 16.04
 
## 3. Result
### (1) IPv6 전용 클라이언트 구축
 - 기본적으로 IPv4, IPv6 모두 기본적인 프로그래밍 방법은 동일하다. 모두 BSD::소켓을 기반으로 하고 있으며, 비슷한 함수를 사용한다. 다만 IPv6의 경우 IP 주소체계의 변환과 그에 따른 몇가지 다른 API들이 제공되는 정도에 차이가 있다. 예를 들어 소켓 생성시 *AF_INET*가 아닌 *AF_INET6*를 사용하고, *bind(:12)* 함수 호출 시 *sockaddr_in6* 구조체를 사용하는 등이다.
