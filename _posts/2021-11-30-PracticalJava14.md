---
title: 자바실무 제 14강[KNU 2021-2]
categories:
  - KNU
tags:
  - JAVA
toc: true
---

# 👨‍💻🏫KNU 2021-2 SW & media 자바실무 필기노트 14


## 1. 팝업 다이얼로그

JOptionPane를 사용하여 다양한 팝업 다이얼로그를 만들수있다. 이는 static type의 메소드를 사용해 구현 가능하다.

### 1.1. 입력 다이얼로그

한줄을 입력받는다.

>static String JOptionPane.showInputDialog(String Message)

~~~java
String input = JOptionPane.showInputDialog("InputDialogEX");
~~~

### 1.2. 확인 다이얼로그

사용자로부터 Yes/No의 선택을 받는다.

>static int JOptionPane.showConfirmDialog(Component parentComponent, Object message, String title, int optionType, int messageType)

~~~java
int result = JOptionPane.showConfirmDialog(null, "Continue?", "windowname", JOptionPane.YES_NO_OPTION, JOptionPane.QUESTION_MESSAGE);
if(result == JOptionPane.YES_OPTION) {
    System.out.println("Yes");
    }
else if(result == JOptionPane.NO_OPTION) {
    System.out.println("No");
    }
else if(result == JOptionPane.CLOSED_OPTION) {
    System.out.println("Closed");
    }
~~~

### 1.3. 메세지 다이얼로그

단순 메세지를 출력한다.

>static void JOptionPane.showMessageDialog(Component parentComponent, Object message, String title, int messageType)

~~~java
JOptionPane.showMessageDialog(null, "i", "windowname", JOptionPane.INFORMATION_MESSAGE);
JOPtionPane.showMessageDialog(null, "X", "windowname", JOptionPane.ERROR_MESSAGE);
JOptionPane.showMessageDialog(null, "!", "windowname", JOptionPane.WARNING_MESSAGE);
JOptionPane.showMessageDialog(null, "?", "windowname", JOptionPane.QUESTION_MESSAGE);
JOptionPane.showMessageDialog(null, "HelloWorld.", "windowname", JOptionPane.PLAIN_MESSAGE);
~~~

### 1.4. 파일 다이얼로그

JFileChooser를 사용하여 파일을 선택할수있다. 파일을 선택하면 파일의 이름을 포함한 경로를 반환하며 파일을 선택하지 않으면 null을 반환한다. 
다이얼로그의 종류는 파일 열기(File Open Dialog), 파일 저장(File Save Dialog)이 있다.

파일열기

~~~java
JFileChooser chooser=new JFileChooser();
int ret=chooser.showOpenDialog(null);
if(ret==JFileChooser.APPROVE_OPTION) {
    String pathName=chooser.getSelectedFile().getPath();
    String fileName=chooser.getSelectedFile().getName();
}
~~~

파일저장

~~~java
JFileChooser chooser=new JFileChooser();
int ret=chooser.showSaveDialog(null);
if(ret==JFileChooser.APPROVE_OPTION) {
    String pathName=chooser.getSelectedFile().getPath();
    String fileName=chooser.getSelectedFile().getName();
}
~~~

### 1.5. 컬러 다이얼로그

ColorChooser를 사용하여 선택한 컬러를 반환할수있다. 취소할 경우 null을 반환한다.

~~~java
Color selectedColor=JColorChooser.showDialog(null, "windowname", Color.BLACK);
if(selectedColor!=null) {
    System.out.println("You choosed "+selectedColor.toString());
}
~~~

## 2. 탭 패널

탭 패널은 여러개의 패널을 표시하는 패널이다. 여러개의 패널은 하나의 공간을 공유한다. 

>JTabbedPane()//탭 패널 생성자

>JTabbedPane(int tabPlacement)//위치 지정 `JTabbedPane.TOP`, `JTabbedPane.BOTTOM`, `JTabbedPane.LEFT`, `JTabbedPane.RIGHT`등을 사용 가능하다.

>int getTabCount()//탭 개수 반환

>int getSelectedIndex()//현재 선택된 탭의 인덱스 반환

>Component getSelectedComponent()//현재 선택된 탭의 컴포넌트 반환

>void removeTabAt(int index)//탭 제거

>void setTabPlacement(int tabPlacement)//탭 위치 변경(지정)

~~~java
JTabbedPane pane=new JTabbedPane(JTabbedPane.TOP);
pane.addTab("Tab1", new Panel_one());
pane.addTab("Tab2", new Panel_two());
add(pane);
~~~

## 3. JAVA Audio API

### 3.1. Audio API 사용례

~~~java
Clip clip=AudioSystem.getClip();//오디오 재생자 생성
File audioFile=new File("경로");//오디오 파일 생성
AudioInputStream audioStream=AudioSystem.getAudioInputStream(audioFile);//오디오 스트림 생성
clip.open(audioStream);//오디오 재생자에 오디오 스트림 연결
clip.start();//오디오 재생
~~~

### 3.2. Audio API 메소드

~~~java
clip.open(audioStream)//오디오 파일을 재생자에 연결한다.
clip.start()//현재 위치에서 클립을 재생한다.
clip.stop()//클립을 중지한다.
clip.close()//파일을 닫는다(리턴).
clip.loop(int count)//클립을 count만큼 반복재생한다.
clip.setFramePosition(int framePosition)//현재 위치를 framePosition으로 설정한다.

//이하 코파일럿 제안, 수업에는 안나옴
clip.isActive()//클립이 재생중인지 확인한다.
clip.getMicrosecondLength()//마이크로세컨드 개수를 반환한다.
~~~

### 3.3. Line 이벤트

오디오 재생자에서 이벤트를 처리하는 방법은 LineListener를 사용한다.

>public void update(LineEvent event)//재생, 중단, 종료 이벤트 처리

~~~java
Line getLine()//Clip은 Line으로 부터 상속된다.
Long getFramePosition()//이벤트 발생 위치를 반환한다.
LineEvent.Type getType()//이벤트 타입을 반환한다. `LineEvent.Type.OPEN`,`LineEvent.Type.START`, `LineEvent.Type.STOP`, `LineEvent.Type.CLOSE`
~~~


