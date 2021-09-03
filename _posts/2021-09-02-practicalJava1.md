---
title: 자바실무 제 1강[KNU 2021-2]
categories:
  - KNU
tags:
  - JAVA
  - GUI
toc: true
---

작성중..

# 👨‍💻🏫KNU 2021-2 SW & media 자바실무 필기노트 1


## 1. 자바의 GUI

### 1.1. GUI 사용의 목적성 

키보드, 마우스 입력을 지원하는 그래픽 사용으로 사용자에게 친숙한 환경을 제공한다.<br>
-&gt; 입출력의 간편성은 GUI의 정수!!

### 1.2. 자바 GUI의 특징 

강력한 GUI 컴포넌트를 제공, 쉬운 GUI 프로그래밍이 가능하다는 장점을 가지고있다.

## 2. 자바의 GUI프로그래밍 방법

GUI 컴포넌트와 그래픽을 사용하는 AWT, Swing 패키지를 사용한다.

### 2.1. java.awt 
자바 초기부터 배포된 패키지로 peer의 OS의 GUI 컴포넌트로 작동되기에 
속도는 빠르다는 `장점`을 가지고 있다.<br>
`단점`으로는 OS에 의존성이 크므로 OS에 따라 표현이 달라질 수 있다는 점과 
프로세서에 주는 부담이 상대적으로 크다는 점이 있다.<br>

### 2.2. javax.swing 
AWT 컴포넌트의 개량형으로 AWT컴포넌트에 J를 붙인 클래스 명을 가진다.<br>
-&gt;JButton, JCheckbox..과 같은 구성요소를 컴포넌트라고 부른다.<br>
Swing 컴포넌트는 운영체제에 의존하지 않아 일관적인 GUI 표현이 가능하다.

~~모위키에 의하면 요즘은 둘다 도태된 패키지라고한다.~~
## 3. Swing GUI Components

![java_1](/assets/img/java_1.png) 

### 3.1. Object
AWT컴포넌트를 상속받는 클래스-최상위컨테이너

### 3.2. Frame
Swing에서 Jframe은 컴포넌트를 담는 '그릇'이다. 창이라고 보면 된다.

### 3.3. Container
다른 GUI컴포넌트를 포함하는 컴포넌트로 java.awt.Component로부터 상속됨

 - 모든 컴포넌트의 최상위 클래스는 java.awt.Component

 - 스윙 컴포넌트의 최상위 클래스는 javax.swing.JComponent

## 4. Swing GUI 프로그램 작성법
   1. Swing Frame 작성
   2. main() method 작성
   3. Frame에 Swing Component를 attachment
   <br>
~~~
    //스윙 패키지사용을 위한 import 문
    import java.awt.(경로명) //그래픽처리를 위한 클래스 경로
    import java.awt.event(경로명) //AWT 이벤트 사용을 위한 경로명
    import javax.swing(경로명) //Swing 컴포넌트 클래스들의 경로명
    import javax.swing.event(경로명) //스윙 이벤트를 위한 경로명
~~~

예제1. Create 300*300 Sized Swing Frame
~~~
    import javax.swing.(경로명);
    public class MyFrame extends JFrame{
        public MyFrame(){
            setTitle("Title"); //title
            setSize(300,300); //sizeFix
            setVisible(true); //frameOut

        }
    public static void main(String[]args){
        MyFrame frame = new MyFrame();
        }
    }
  ~~~