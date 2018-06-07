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
 - OS : Linux Ubuntu 16.04 (Oracle VM VirtualBox)
 
## 3. Result
### (1) IPv6 전용 클라이언트 구축
 - 기본적으로 IPv4, IPv6 모두 기본적인 프로그래밍 방법은 동일하다. 모두 BSD::소켓을 기반으로 하고 있으며, 비슷한 함수를 사용한다. 다만 IPv6의 경우 IP 주소체계의 변환과 그에 따른 몇가지 다른 API들이 제공되는 정도에 차이가 있다. 예를 들어 소켓 생성시 *AF_INET*가 아닌 *AF_INET6*를 사용하고, *bind(:12)* 함수 호출 시 *sockaddr_in6* 구조체를 사용하는 등이다.
```
  #include <netinet/in.h>
  struct sockaddr_in6
  {
      u_int16m_t      sin6_family;    // AF_INET6 
      u_int16m_t      sin6_port;      // Port 번호  
      u_int32m_t      sin6_flowinfo;  // IPv6 flow information
      struct in6_addr sin6_addr;      // IPv6 주소 
      u_long sin6_scope_id;           // 인터페이스 인덱스 아이디
  }
```
 - IPv6 는 *sockaddr_in6* 라는 별도의 구조체를 사용한다. 멤버 변수들 중 sin6_scope_id라는 값은 인터페이스를 가리키는 인덱스 값이다. 특정 인터페이스는 IPv4만 혹은 IPv6만 혹은 양쪽 주소를 모두 가질 수 있기 때문에 소켓을 Bind할 때 어떤 인터페이스를 사용해야할지를 명시해야 한다.
 
 - 클라이언트 구축은 소켓을 할당하고, port번호 및 접속하려는 IP주소를 설정한 후, 서버에 connect를 요청한다. 연결 후에는 서버로부터 read 및 출력 그리고 그에 대한 답을 키보드에서 읽고(*read(0,…)*), write하여 서버에 전송하였다. 여기서 read 및 write의 수는 예시 packet의 pcap file을 분석하여 결정하였다. 정보 전달에서는 연속 2회의 read가 수행되고, 키보드에서 입력 받은 뒤 1회의 write가 수행된다.

 - 이 후 *fork()* 함수를 이용하여 멀티 프로세싱을 하였다. 부모로부터 만들어진 자식 프로세스는 나의 server와 접속을 하는 새로운 server가 된다. 이 프로세스에서는 대상의 client에 의해 나의 server가 받은 token 값을 나의 client 프로세스로 보낸다. 자식 프로세스가 종료된 후에, 자식 프로세스로부터 얻어진 token은 나의 client를 이용하여 대상의 server로 보내고, 대상은 토큰 값을 비교하는 메시지를 client로 전송한다. 나의 client는 이를 읽고 출력한다.

### (2) IPv4 서버 구축
 - IPv4용 서버를 구축하는 과정은 소켓을 생성하고(*socket()*), *bind* 함수로 소켓을 특정 ip 및 포트에 연결하고, *listen*하여 클라이언트의 요청을 기다린다. 대상의 client로부터 요청이 들어오면 나의 server는 이를 accept하고, 보내는 토큰을 read한다. 보내는 메시지 뒤의 enter를 지우기 위해 문자열 끝에서 두 번째 문자를 ‘\0’로 설정하였다.
 
 - 토큰을 받은 후 연결을 끊고, 나의 client에서 *fork* 함수를 통해 새로 생성된 자식 프로세스의 server와 연결하기 위해 새로운 client를 아래에 구축한다. 나의 client와 연결한 server는 토큰 값을 전달하고 연결을 끊는다.
 
### (3) 결과
#### a) Server 실행 결과
   ![image](https://user-images.githubusercontent.com/26705935/41096689-55ed0780-6a90-11e8-9646-1753fbfe2e1c.png)
 
 - server가 제대로 실행하였고, 대상의 client로부터 20자리의 토큰을 받아 이를 출력해보았다.

#### b) Client 실행 결과
   ![image](https://user-images.githubusercontent.com/26705935/41096762-8af37450-6a90-11e8-8fb4-c4ebe04d2b07.png)
 
 - client가 제대로 실행하였고, 정보를 입력한 후에 *connect success*라는 문자열이 보였으며, 자식 프로세스를 통해 토큰 값을 제대로 전달 받아 이를 출력하였다. 하지만 자식 프로세스에서 부모 프로세스로 이 토큰 값을 전달하는 과정에 대해서는 구현하지 못하였다. 따라서 대상의 server로의 전송을 제대로 이루지 못하였다.
 
### (4) VM에서의 IP 할당
 - Linux Ubuntu 환경을 VirtualBox를 이용하여 사용하였다. 여기서 IP 할당하는 과정에서, *Port Forwarding* 방법을 사용하였다. 아래는 VirtualBox에서 포트 포워딩을 한 화면이다. Windows에서 사용한 IP 주소는 192.168.56.101 이었다.
 
   ![image](https://user-images.githubusercontent.com/26705935/41096802-b3dd3798-6a90-11e8-8039-fd3fd030b993.png)
 
 ## 4. Future Work
  - 자식 process에서 부모 process로의 토큰 값 전달 과정을 구체화하고 구현해야한다.
  - 이를 통해 받은 토큰 값을 대상의 server로 전송해야 한다.
 
