---
title: 웹프로그래밍 제 4강[KNU 2022-1]
categories:
  - KNU
tags:
  - JSP
toc: true
---

# 👨‍💻🏫KNU 2022-1 SW & media 웹프로그래밍 필기노트 4

## 1. Action tag

- 서버나 클라이언트에게 행동을 명령하는 태그
- 페이지간 제어(forward) 또는 다른 페이지의 실행 결과를 현재 페이지에 반영(include)하는 태그
- JavaBeans와 같은 클래스 파일을 사용가능
- XML형식의 <jsp: {tagName} {param='value'}/>와 같은 형식을 사용한다.

|태그|형식|설명|
|-|-|-|
|forward|<jsp:forward .../>|페이지 이동|
|include|<jsp:include .../>|외부 페이지 포함 또는 모듈화를 위해 사용|
|useBean|<jsp:useBean .../>|JavaBeans와 같은 클래스 파일을 사용|
|setProperty|<jsp:setProperty .../>|JavaBeans의 property값을 설정|
|getProperty|<jsp:getProperty .../>|JavaBeans의 property값을 가져옴|
|param|<jsp:param .../>}| forward, include,plugin태그에 인자를 추가한다|
|plugin|<jsp:plugin .../>|자바 애플릿의 사용. 플러그인에대한 object,embed태그를 만드는 코드를 생성|
|element|<jsp:element .../>|동적XML요소를 설정|
|attribute|<jsp:attribute .../>|동적XML요소의 속성을 설정|
|body|<jsp:body .../>|동적XML요소의 내용을 설정|
|text|<jsp:text .../>|jsp페이지 또는 문서에서 템플릿 텍스트를 작성함|

## 2. forward action tag

현재 페이지에서 다른 페이지로 이동하는 태그로 이동시 저장된 버퍼의 내용을 삭제하고 설정된 페이지로 프로그램의 제어가 이동된다.

~~~jsp
<jsp:forward page="{page}"/>
<jsp:forward page="{page}"></jsp:forward>
~~~

## 3. include action tag

include태그는 JSP페이지 특정영역에 외부파일의 내용을 포함하는 태그로 HTML, JSP, 서블릿페이지를 포함 가능하다.

~~~jsp
<jsp:include page="{page}" flush="false"/>
~~~

flush가 true일때에는 출력버퍼에 저장된 결과를 출력하고 출력버퍼를 비운다.

## 4. param action tag

현재 페이지에서 다른 페이지에 정보를 전달하는 태그로
단독으로 사용되지 아니하며 다중의 액션 태그를 사용하여 여러개 정보를 전달 할 수 있다.

~~~jsp
<jsp:param name="{a}" value="{1}"/>
<jsp:param name="date" value="<%new java.util.Date%>"/>
~~~

param을 받는 페이지에는 <%request.getParameter(name)%>를 사용하여 값을 가져올 수 있다.

## 4. javaBeans

동적 컨텐츠 개발을 위해 자바 코드를 사용하여 자바 클래스로 로직을 구성하는 방법으로 화면을 표현하기위한 계산식, 자바코드를 따로 분리하여 작성하게 된다.

1. 자바 클래스는 java.io.Serializable을 상속받아야 한다.
2. 인수가 없는 기본 클래스가있어야한다.
3. 모든 멤버변수인 프로퍼티는 private 접근 지정자로 설정해야한다.
4. 모든 멤버변수인 프로퍼티는 getter(), setter()메소드를 제공해야한다.

[자바빈즈 작성 예]

~~~java
package com.dao;
import java.io.Serializable;

public class User implements Serializable {
  //define properties
  private String id;
  private String name;
  //ctor
  public User(){

  }
  //getter, setter
  public int getId() {
    return id;
  }
  public String getName() {
    return name;
  }
  public void setId(int id) {
    this.id = id;
  }
  public void setName(String name) {
    this.name = name;
  }
~~~

### 4.1. useBean action tag

JSP 페이지에서 자바빈즈를 사용하기위해 실제 자바 ㅋ클래스를 선언하고 초기화 하는 태그로 id속성과 scope속성을 바탕으로 자밥빈즈의 객체를 검색하고 객체가 발견 되지않으면 빈 객체를 생성한다.

~~~java
<jsp:useBean id="{beanId}" class="{beanName}" scope="{scope}"/>
~~~

|속성|설명|
|-|-|
|id|자바빈즈를 식별하기위한 이름|
|class|패키지명을 포함한 자바빈즈의 이름으로 인수가 없는  기존 생성자가 있어야하며 추상클래스를 사용할수없다.|
|scope|자바빈즈가 저장되는 영역을 설정함. page(기본값),request,session,application중 하나의 값을 사용함|

#### 4.1.1. 클래스 함수 실행

~~~jsp
<jsp:useBean id="date" class="java.util.Date"/>

<%=date=>//class내의 java.util.Date실행
~~~

#### 4.1.2. 사용자 지정 클래스 함수 실행

~~~java
//자바빈즈 생성
package (생략)

public class Calc{
  public int process(int n){
    return n*n;
  }

}
~~~

~~~jsp
//bean으로 정의하고 위의 클래스 함수 실행
<jsp:useBean id="bean" class="(생략).Calc)"/>

<%
  int m= bean.process(5);
  out.print("제곱 계산결과:"+m);
%>
~~~

#### 4.1.3. setter getter 함수 실행

~~~java
//자바빈즈 생성
package (생략)

public class Person{
  private String id = "123456789";
  private String name "qwer";
  public Person(){

  }
  //getter, setter
  public String getId() {
    return id;
  }
  public String getName() {
    return name;
  }
  public void setId(String id) {
    this.id = id;
  }
  public void setName(String name) {
    this.name = name;
  }

}
  
~~~

~~~jsp
//bean으로 정의하고 위의 클래스 함수 실행
<jsp:useBean id="person" class="(생략).Person" scope="request"/>

<%"id:"+person.getId()%>
<%"name:"+person.getName()%>
<%
  person.setId("newId")
  person.setName("newName")
%>
~~~

### 4.2. setProperty action tag

~~~jsp
<jsp:setProperty name ="자바빈즈 식별명" property="프로퍼티 명" value="값"/>
~~~

|속성|설명|
|-|-|
|name|자바빈즈를 식별하기위한 이름|
|property|자바빈즈의 프로퍼티명으로 *사용시 모든 setter()메소드에 전달됨|
|value|변경할 프로퍼티의 값으로 유효하지 않을시 무시됨|
|param|프로퍼티값을 전달하는 요청 파라미터의 이름.|

프로퍼티의 값을 저장하기위해 사용됨

usebean액션태그와 함께 자바빈즈의 setter 메소드에 접근하여 자바빈즈의 멤버변수인 프로퍼티의 값을 지정하는 태그

홈페이지로부터 전달받은 리퀘스트 파라미터를 직접 저장하거나 자바빈즈의 프로퍼티로 변경하여 저장하는 태그

### 4.2. getProperty action tag

~~~jsp
<jsp:getProperty name ="자바빈즈 식별명" property="프로퍼티 명"/>
~~~

프로퍼티의 값을 가져오기 위해 사용됨

usebean액션태그와 함께 자바빈즈의 getter 메소드에 접근하여 자바빈즈의 멤버변수인 프로퍼티의 값을 가져오는 태그

|속성|설명|
|-|-|
|name|자바빈즈를 식별하기위한 이름|
|property|자바빈즈의 프로퍼티명으로 *사용시 모든 getter()메소드에 전달됨|
