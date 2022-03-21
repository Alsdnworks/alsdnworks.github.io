---
title: 웹프로그래밍 제 2강[KNU 2022-1]
categories:
  - KNU
tags:
  - JSP
  - JDBC
toc: true
---

# 👨‍💻🏫KNU 2022-1 SW & media 웹프로그래밍 필기노트 2

## 1. JDBC를 사용한 JSP 와 데이터베이스의 연동

### 1.1. JDBC

JDBC는 Java에서 데이터베이스와 연동하는 기능을 제공한다. 관계형 데이터베이스시스템에 접근하여 SQL문을 실행하기 위힌 자바 API또는 라이브러리이다.

### 1.2. JSP와 데이터베이스의 연동

1. java.sql.*패키지 임포트
2. JDBC 드라이버 로드
3. 데이터베이스 접속을 위한 Connection 객체 생성
4. 쿼리 실행을 위한 Statement, PreparedStatement, CallableStatement 객체 생성
5. 쿼리의 실행, 결과 값의 사용(ResultSet)
6. 사용된 객체 반환(종료)

#### 1.2.1. 드라이버의 로드

~~~java
<%
  try {
      Class.forName("com.mysql.jdbc.Driver");
  } catch (SQLException e) {
    //예외처리
    out.println(e.getMessage();)
  }
%>
~~~

#### 1.2.2. Connection 객체 생성

~~~java
DriverManager.getConnection(String url) //user password를 같이 사용한다
DriverManager.getConnection(String url, String user, String password)
DriverManager.getConnection(String url, Properties info)
~~~

>connnection conn=null;
>conn = DriverManager.getConnection(url, user, password);

properties 객체를 사용하는 경우 다음과 같은 방식을 사용한다.

> properties props = new Properties();
> props.put("user", "root");
> props.put("password", "1234");

#### 1.2.3. 데이터베이스 연결 닫기

>void close() throws SQLException

~~~java
connnection conn=null;
conn = DriverManager.getConnection(url, user, password);
conn.close();
~~~

## 2. 데이터베이스 쿼리의 실행

### 2.1. Statement 객체사용

하나의 쿼리만을 실행하는 경우 Statement 객체를 사용한다. 쿼리의 결과는 ResultSet 객체로 반환되며 실행후 즉시 close()를 사용해 객체를 종료해야한다.

>Statement createStatement() throws SQLException

~~~java
executeQuery(String sql); //returns ResultSet, select 문을 실행할때 사용
executeUpdate(String sql); //returns int, DML실행에 사용
close(); //returns void, statememt의 종료
~~~

~~~java
Statement stmt = conn.createStatement();
String sql = "여기에 쿼리입력";
//쿼리가 select인 경우 executeQuery()를 사용한다.
ResultSet rs = stmt.executeQuery(sql);
stmt.close();
//쿼리가 DML인경우 executeUpdate()를 사용
int result = stmt.executeUpdate(sql);
~~~

### 2.2. PreparedStatement 객체사용

하나의 객체로 여러번의 쿼리를 실행할 수 있으며 동일한 쿼리를 수정 사용할때 유용하다.

> PreparedStatement prepareStatement(String sql) throws SQLException

필드 유형에 따라 Setter를 사용하여 값을 설정한다.

> setXxx(int paramIndex, Xxx value)

메소드는 Statement과 같다

~~~java
executeQuery(String sql); //returns ResultSet, select 문을 실행할때 사용
executeUpdate(String sql); //returns int, DML실행에 사용
close(); //returns void, statememt의 종료
~~~

~~~java
//? 의 필드에 현 값을 설정한다.

////executeQuery일 경우
String sql = "select--(생략)--Where id =?";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, "1");
ResultSet rs = pstmt.executeQuery(sql);

////executeUpdate일 경우
String sql = "insert--(생략)--values (?,?,?)";
PreparedStatement pstmt = conn.prepareStatement(sql);
pstmt.setString(1, "1");
pstmt.setString(2, "예시이름");
pstmt.setString(3, "예시전화번호");
pstmt.executeQuery();

pstmt.close();
~~~

### 2.3. ResultSet 객체

Select 문을 실행한 결과를 레코드로 사용하는 객체이다. (판다스 DataFrame이라고 생각하면 편함)

|method|설명|
|-|-|
|getXxx(int columnIndex) |columnIndex번째 필드의 값을 XXX형으로 반환한다.|
|getXxx(String columnName) |columnName의 값을 XXX형으로 반환한다.|
|absolute(int row) |row번째 행으로 이동한다(returns boolean).|
|first() |첫번째 레코드를 선택한다(returns boolean).|
|last() |마지막 레코드를 선택한다(returns boolean).|
|beforeFirst() |첫번째 레코드를 선택한다.|
|afterLast() |마지막 레코드를 선택한다.|
|next() |다음 레코드를 선택한다.|
|previous() |이전 레코드를 선택한다.|
|close() |객체를 반환한다.|