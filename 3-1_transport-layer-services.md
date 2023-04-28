## ch3 transport layer: Transport-layer services



# Transport layer: overview

our goal:

- understand principles behind transport layer services:

  - multiplexing, demultiplexing: 응답이 나에게 어떻게 찾아올 수 있느냐
  - reliable data transfer: TCP의 특징
  - flow control: sender A와 receiver B 한 쌍 사이의 혼잡 관리; B가 A에게 좀 천천히 보내라고 요청
  - congestion control: network core에서 일어나는 혼잡

- learn about Internet transport layer protocols:

  - UDP: connectionless transport
  - TCP: connection-oriented reliable transport
  - TCP congestion control

  > end to end connection; network 내부는 blackbox
  >
  > application L이 transport L의 서비스 이용했듯이, transport L은 network L의 서비스 이용; network L에 대한 내용도 좀 나옴

  

# Transport services and protocols

- provide **logical communication** between application processes running on different hosts: A 컴퓨터 안의 process와 B 컴퓨터 사이 안의 process 끼리의 통신

- transport protocols actions in end systems:

  - sender: breaks application messages into **segments**, passes to network layer: application L에서 내려온 메세지를 조각내서 여러 개의 segment로 만들고 이를 network L에 전달
  - receiver: reassembles segments into messages, passes to application layer: 조각난 segment들을 다시 모아서 메세지로 복원한 후 application L로 전달

  > segment: transport L의 전송단위

- two transport protocols available to Internet applications: Internet application이 사용할 수 있는transport L의 프로토콜은 아래 두 가지 뿐

  - TCP, UDP



# Transport vs. network layer services and protocols

- **transport layer:** communication between **processes**: transport L은 프로세스 간의 통신
  - relies on, enhances, network layer services; transport L은 network L의 서비스를 이용한다
- **network layer: **communication between **hosts**: network layer은 호스트 간의 통신

> *household analogy: 12 kids in Ann's house sending letters to 12 kids in Bill's house:* 
>
> - hosts = house
> - processes = kids
> - app messages = letters in enelopes



# Transport Layer Actions

Sender:

- is passed an application layer message: application L에서 메세지를 전달받음
- determines segment header fields values: 헤더로 뭘 붙일지 결정하고
- creates segment: 메세지 앞에 헤더를 붙여 세그먼트를 만듦
- passes segment to IP: 만든 세그먼트를 network L의 IP에게 전달

Receiver:

- receives segment from IP: IP가 (network L의 헤더를 뗀) segment를 transport L로 전달
- checks header values: 세그먼트의 헤더 확인
- extracts application-layer message: 헤더에 이상이 없으면 헤더 버리고 application L의 메세지 형태로 만듦
- demultiplexes message up to application via socket: 소켓을 통해서 메세지를 application L로 보냄 (소켓은 application L로 들어가는 관문 같은거)



# Two principle Internet transport protocols

- **TCP:** Transmission Control Protocol

  - **reliable**, **in-order** delivery: 무결성 보장, 순서대로 배달해줌
  - congestion control
  - flow cotrol
  - connection setup: 데이터 전달받기 전에 connection 먼저 완료함

- **UDP:** User *Datagram* Protocol

  - **unreliable**, **unordered** delivery: 데이터 훼손되거나 날라갈 가능성 있음, 순서대로 배달 안해줌
  - no-frills extension of **"best-effort"** IP:  최선을 다하지만 결과에 대한 보장은 못한다.

  > datagram이란 말에 붙는 이유: UDP는 network L의 IP에 (process에 대한) 새로운 주소체계인 port \#을 부여할 뿐, 그외는 딱히 하는 일이 없다; network L와 비슷하게 행동
  >
  > 그래서 UDP datagram이라 부르기도 하고 UDP segment라고 부르기도 함

- services not available: TCP, UDP 둘 다 제공 안하는 것들

  - delay guarantees: 딜레이 보장 못함
  - bandwidth guarantees: 대역폭 보장 못함
