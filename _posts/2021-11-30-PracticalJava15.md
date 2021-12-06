---
title: 자바실무 제 15강[KNU 2021-2]
categories:
  - KNU
tags:
  - JAVA
  - GUI
toc: true
---

# 👨‍💻🏫KNU 2021-2 SW & media 자바실무 필기노트 15


## 1. 소켓 프로그래밍

TCP/IP 네트워크를 이용하여 쉽게 통신 프로그램을 작성하도록 지원하는 기반기술

**소켓** 

- 두 응용프로그램간 양방향 통신링크의 한쪽 끝 단
- 소켓끼리 데이터를 주고받음
- 특정 포트번호에 연결되어 데이터를 교환
- 서버소켓, 클라이언트 소켓

자바는 소켓 통신을 위한 라이브러리를 지원하며, 이를 이용하여 소켓 프로그래밍을 할 수 있다.

### 1.1. 소켓 서버-클라이언트 통신프로그램의 구조


|클라이언트| - |서버|
|-|-|-|
|<span style="color:#00FFFF">*Socket*</span> | | <span style="color:#00FFFF">*ServerSocket*</span>|
|--| |1. listener= new ServerSocket(port);|
|3. ClientSocket = new Socket("서버IP", 서버 port);|->접속|2. Socket soc= listener.accept();|
|4. clientSocket.getOutputStream();||<span style="color:#00FFFF">*Socket*</span> <br> 4. soc.getInputStream();|
|5. 소켓 스트림을 이용한|<->| 데이터 입출력|
|6. clientSocket.close();| |6. soc.close();|

### 1.2. 소켓 클래스, 클라이언트 소켓

클라이언트 소켓에 사용되는 클래스로 java.net.Socket 클래스를 사용하여 소켓을 생성한다.

|소켓 클래스 생성자|설명|
|-|-|
| <span style="color:#00FFFF"> Socket</span> |미연결 소켓|
| <span style="color:#00FFFF"> Socket(String address, int port)</span>|지정된 IP와 포트번호에서 대기하는 원격 프로그램 소켓 연결|
| <span style="color:#00FFFF"> Socket(String host, int port)</span>|지정된 호스트와 포트번호에서 대기하는 원격 프로그램 소켓 연결 <br> 호스트명이 null인경우 루프백주소 가정|

|소켓 클래스 메소드|설명|
|-|-|
|void bind(SocketAddress bindpoint)|소켓을 지정된 주소에 바인딩한다.|
|void close()|소켓을 닫는다.|
|void connect(SocketAddress endpoint)|소켓을 지정된 주소에 연결한다.|
|InetAddress getInetAddress()|소켓의 주소를 반환한다.|
|InputStream getInputStream()|소켓의 입력 스트림을 반환한다. 상대로부터 받은 데이터 리드 가능|
|InexAddress getLocalAddress()|소켓의 로컬 주소를 반환한다.|
|int getLocalPort()|소켓의 로컬 포트번호를 반환한다.|
|int getPort()|소켓인 연결된 포트번호를 반환한다.|
|OutputStream getOutputStream()|소켓의 출력 스트림을 반환한다. 이 스트링에 출력하면 소켓이 서버로 데이터 전송|
|bool isBound()|소켓이 로컬주소에 연결되어있다면 true반환|
|bool isClosed()|소켓이 닫혀있다면 true반환|
|bool isConnected()|소켓이 연결되어있다면 true반환|
|void setSoTimeout(int timeout)|소켓의 타임아웃 시간을 지정. 0이면 타임아웃 없음|

|서버소켓 클래스 생성자|설명|
|-|-|
| <span style="color:#00FFFF"> ServerSocket(int port)</span> |지정된 포트번호에 결합된 소켓 생성|

|서버소켓 클래스 메소드|설명|
|-|-|
|Socket accept()|클라이언트로부터 연결요청 대기, 요청시 수락하고 클라이언트와 연결된 새 Socket 객체 반환|
|void close()|서버 소켓을 닫는다.|
|InetAddress getInetAddress()|서버 소켓의 로컬 IP 주소를 반환한다.|
|int getLocalPort()|서버 소켓의 로컬 포트번호를 반환한다.|
|bool isBound()|서버 소켓이 로컬주소에 연결되어있다면 true반환|
|bool isClosed()|서버 소켓이 닫혀있다면 true반환|
|void setSoTimeout(int timeout)|accept()의 타임아웃 시간을 지정. 0이면 타임아웃 없음|


|연결과정|
|-|
|1. <span style="color:#00FFFF">  서버 - 포트 - 리슨  </span>|
|2. <span style="color:#00FFFF">  서버 - 포트 - 리슨  </span> <-연결요청 <span style="color:orange"> 포트 - 클라이언트 1 </span>|
|3. <span style="color:#00FFFF">  서버 - 포트 - 리슨  </span> <-연결요청 <span style="color:orange">  포트 - 클라이언트 2  </span> <br>
<span style="color:teal">  클라이언트  </span> <-연결요청 <span style="color:orange"> 포트 - 클라이언트 1 </span>|

~~~java
ServerSocket serverSocket = new ServerSocket(port);
Socket socket = serverSocket.accept();
BufferedReader in = new BufferedReader(new InputStreamReader(socket.getInputStream()));
BufferedWriter out = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream()));
//Socket 객체의 getInputStream(), getOutputStream() 메소드를 사용하여 입출력 스트림을 생성.
~~~

## 2. 예제1. 채팅프로그램

<table>
<tr>
<th>
서버
</th>
<th>
클라이언트
</th>
</tr>
<tr>
<th>

### ServerEx.java

~~~java
import java.io.*;
import java.net.*;
import java.util.*;

public class ServerEx {
	public static void main(String[] args) {
		BufferedReader in = null;
		BufferedWriter out = null;
		ServerSocket listener = null;
		Socket socket = null;
		Scanner scanner = new Scanner(System.in); 
		try {
			listener = new ServerSocket(9999); // 서버 소켓 생성 
			System.out.println("서버 연결 대기중");
			socket = listener.accept(); 
			System.out.println("서버 연결 성공");
			in = new BufferedReader(new InputStreamReader(socket.getInputStream())); 
			out = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream())); 
			while (true) {
				String inputMessage = in.readLine(); // 클라이언트 명령 read
				if (inputMessage.equalsIgnoreCase("종료")) {
					System.out.println("클라이언트로부터 세션이 종료되었습니다."); 
					break;  
				}
				System.out.println("메세지: " + inputMessage); 			
				System.out.print("메세지 입력: "); 
				String outputMessage = scanner.nextLine(); 
				out.write(outputMessage + "\n"); 
				out.flush();//out버퍼 내용을 서버로 전송
			}
		} catch (IOException e) {
			System.out.println(e.getMessage());
		} finally {
			try {
				scanner.close(); 
				socket.close(); 
				listener.close();
			} catch (IOException e) {
				System.out.println("자원 해제 중 오류 발생");
			}
		}
	}
}
~~~
</th>

<th>

### ClientEx.java

~~~java
import java.io.*;
import java.net.*;
import java.util.*;

public class ClientEx {
	public static void main(String[] args) {
		BufferedReader in = null;
		BufferedWriter out = null;
		Socket socket = null;
		Scanner scanner = new Scanner(System.in); 
		try {
			socket = new Socket("localhost", 9999); 
			in = new BufferedReader(new InputStreamReader(socket.getInputStream())); 
			out = new BufferedWriter(new OutputStreamWriter(socket.getOutputStream())); 
			while (true) {
				System.out.print("메세지 입력: "); 
				String outputMessage = scanner.nextLine(); 
				if (outputMessage.equalsIgnoreCase("종료")) { 
					out.write(outputMessage+"\n"); 
					out.flush();
					break; 
				}
				out.write(outputMessage + "\n"); 
				out.flush();
				String inputMessage = in.readLine();
				System.out.println("메세지: " + inputMessage);
			}
		} catch (IOException e) {
			System.out.println(e.getMessage());
		} finally {
			try {
				scanner.close();
				if(socket != null) socket.close();
			} catch (IOException e) {
				System.out.println("자원 해제 중 오류 발생");
			}
		}
	}
}
~~~

</th>
</tr>
</table>

