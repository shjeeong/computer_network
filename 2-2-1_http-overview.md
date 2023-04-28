## ch2 application layer  \#2 web and http: http overview

# Web and HTTP

web page consists of  **objects**, each which can be stored on different Web servers: 웹은 object로 이루어져 있음

> 과거에는 static object로만 보내줬음, 요즘은 (즉석으로 만들어서) 사용자에 따라 보여지는 내용 다르게 함
>
> object 파일 종류: HTML file, JPEG image, Java applet, audio file, ...

web page consists of **base HTML-file** which includes several **referenced objects**, each addressable by a **URL**: URL은 클라이언트가 접근하려는 IP 주소 + path

>  URL: `www.someschool.edu/`; host name(IP) + `someDept/pic.gif`; path name



# HTTP overview

*과거 버전에 대한 설명*

## HTTP: hypertext transfer protocol

- 가장 많이 사용하는 **application L**의 프로토콜
- client/server model:
  - **client: browser** that requests, receives, (using HTTP protocol) and displays Web objects; browser가 object 요청하는 request 보내고 서버한테서 받은 object를 display
  - **server: Web server** sends (using HTTP protocol) objects in response to requests; client가 보낸 request에 대한 response로 objects 보내줌

## HTTP uses TCP:

- clients initiates TCP connection (creates socket) to server, port 80; client가 소켓 만들어서 TCP port 번호 80으로 요청
- server accepts TCP connection from client: client로부터 TCP 연결 승인
- HTTP messages (application-layer protocol messages) exchanged between browser (HTTP client; browser) and Web server (HTTP server; appache): 메세지가 서버에서 클라이언트로 전달됨
- TCP connection closed: TCP 연결 닫힘

> 요즘 새로 나온 HTTP/3.0은 UDP 사용 (QUIK)

## HTTP is  "stateless"

- server maintains no information about past client requests: 상태가 없다 - 이전 request 기억하고 있지 않음

  > protocols that maintain "state" are complex:  간단하게 만들기 위해 stateless로 만듦
  >
  > stateful(stateless와 반대)의 경우: state 정보 유지를 위한 overhead 필요
  >
  > - past history (state) must be maintained: 과거의 정보가 남아있어야 함
  > - if server/client crashes, their views of "state" may be inconsistent, must be reconciled:  서버나 클라이언트가 끊어질 경우, state를 다시 조정해야 함 



# HTTP connections: two types

**Non-persistent HTTP (HTTP 1.0):** **at most one object** sent over TCP connection; 열려 있는 TCP로 object 하나만 보낼 수 있음

**Persistent HTTP (HTTP 1.1):** **multiple objects** can be sent over single TCP connection between client, and that server; 여러 개의 object들을 다 보낼때까지 TCP 열어둠

> ### connections?
>
> application L --- HTTP
>
> transport L --- TCP
>
> => TCP를 열어야 HTTP가 데이터 보낼 수 있음

# Non-persistent HTTP (HTTP 1.0)

Users enters URL: `www.someSchool.edu/someDepartment/home.index` (containing text, references to 10 jpeg images; 이미지 객체 10개 포함됨)

1. TCP connection opened

   > client: HTTP client initiates TCP connection to HTTP server (process) at `www.someSchool.edu` on port 80(웹 port 번호는 보통 80); 클라이언트가 해당 IP의 서버로 연결하는 TCP 초기화시키고 서버에 연결 요청
   >
   > server: HTTP server at host `www.someSchool.edu` waiting for TCP connection at port 80 "accepts" connection, notifying client: 요청이 올 때까지 대기타다가, 클라이언트의 connection 요청오면 수락

2. **at most one object** sent over TCP connection: 열려 있는 TCP로 object를 하나만 보낼 수 있음

   > client: HTTP client sends HTTP **request message** (containing URL)  into *TCP connection socket*. Message indicates that client wants object `someDepartment/home.index`: TCP 소켓을 통해 request 메세지 내보냄. 메세지 내용: 원하는 object; path name
   >
   > server: HTTP server receives request message, forms **response message** containing requested object, and sends message into its *socket*: 요청한 object를 포함한 response 메세지를 소켓을 통해 내보냄

3. TCP connection closed

   > server: HTTP server closes TCP connection: 응답 후 connection 끊김

downloading multiple objects required multiple connections: 여러 개의 object 보낼 때는 TCP를 열었다 닫았다 해야함

> client: HTTP client receives response message containing html file, display html. Parsing html file, finds 10 referenced jpeg objects; 서버가 한 개의 object 받음
>
> Steps 1-5 repeated for each of 10 jpeg objects: 10개의 object 있으므로 TCP 열번 열고 닫아야 함

## Non-persistent HTTP: response time

**RTT (definition)**; round trip time; 왕복시간: time for a small packet to travel from client to server and back

**HTTP response time (per object):**

- one RTT: initiate TCP connection; client가 TCP 연결 요청 보내고 서버가 accept 했다고 response 할 때까지
- one RTT: HTTP request and first few bytes of HTTP response to return; client가 object request보내고 서버가 보낸 object 포함한 response가 client에 도착한 순간까지
- object/file transmission time; 서버가 보낸 response를 다 받아들일 때까지(object를 받아들임)

> Non-persistent HTTP response time = 2RTT + file transmission time

## Non-persistent HTTP issues:

- require 2 RTTs per object: 오브젝트당 TCP 연결 시간이 걸림
- OS overhead for each TCP connection: protocol stack이 os 커널에 들어가 있으므로 os에 부담
- browsers often open multiple parallel TCP connections to fetch referenced objects in parallel: response time을 줄이기 위해 TCP connection을 동시에 10개 연다면 -> os가 10개의 TCP connection을 유지하는 부담이 있다

# Persistent HTTP (HTTP 1.1)

- server leaves connection open after sending response: 서버가 응답 보낸 후에 connection을 바로 닫지 않고 열어둠
- subsequent HTTP messagaes between same client/server sent over open connection: 같은 클라이언트/서버는 그 열려있는 connection을 통해 메세지를 추가적으로 보낼 수 있음
- clients sends requests as soon as it encounters a referenced object: 클라이언트는 referenced object를 발견하자마자 보내달라고 요청메세지 보낼 수 있음
- as little as one RTT for all the referenced objects (cutting response time in half): 만약 10개의 object를 요청한다면 RTT는 한 번만 발생
