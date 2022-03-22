---
title: 웹프로그래밍 제 2강 실습[KNU 2022-1]
categories:
  - KNU
tags:
  - JSP
  - JDBC
toc: true
---

# 👨‍💻🏫KNU 2022-1 SW & media 웹프로그래밍 실습 2

## 1.Init Table

~~~sql
USE exercisedb;

DROP TABLE IF exists student2;

CREATE TABLE IF NOT EXISTS Student2(
   num int NOT NULL,
   pwd VARCHAR(20),
   depart  VARCHAR(20),
   name VARCHAR(30), 
   address VARCHAR(50), 
   phone VARCHAR(20),
   email VARCHAR(30),
   PRIMARY KEY (num)
);

insert into student2 values(2018100001,'123','모바일과','홍길순','서울시','010-9002-1234','ab@naver.com');
insert into student2 values(2018100002,'qwe','모바일과','홍길동','경기도','010-2009-4321','qwe@kangwon.ac.kr');
insert into student2 values(2018200001,'ert','영어과','수여인','인천시','010-3918-0007','zxc@ac.kr');
insert into student2 values(2018200002,'34','영어과','김다운','서울시','010-3002-0101','aqz@daum.net');
~~~

## 2. DBconnect

~~~java
<%@ page import="java.sql.*"%>?
<%
	Connection conn = null;

	String url = "jdbc:mysql://-----/ExerciseDB";
	String user = "-----";
	String password = "-----";

	Class.forName("com.mysql.jdbc.Driver");
	conn = DriverManager.getConnection(url, user, password);
%>
~~~

## 3.Create as Insert

~~~jsp
<%@ page contentType="text/html; charset=utf-8"%>
<html>
<head>
<title>Database SQL</title>
</head>
<body>
	<form method="post" action="insert_process.jsp">
		<p>학번 : <input type="text" name="num">
		<p>비밀번호 : <input type="text" name="pwd">
		<p>학과 : <input type="text" name="depart">
		<p>이름 : <input type="text" name="name">
		<p>주소 : <input type="text" name="address">
		<p>연락처 : <input type="text" name="phone">
		<p>이메일 : <input type="text" name="email">
		<p><input type="submit" value="전송">
	</form>
</body>
</html>
~~~

~~~jsp
<%@ page contentType="text/html; charset=utf-8"%>
<%@ page import="java.sql.*"%>
<html>
<head>
<title>Database SQL</title>
</head>
<body>
<%@ include file="dbconn.jsp" %>
<%
	request.setCharacterEncoding("utf-8");

	String num = request.getParameter("num");
	String pwd = request.getParameter("pwd");
	String depart = request.getParameter("depart");
	String name = request.getParameter("name");
	String address = request.getParameter("address");
	String phone = request.getParameter("phone");
	String email = request.getParameter("email");
	
	PreparedStatement pstmt = null;

	try {
		String sql = "insert into student2(num, pwd, depart, name, address, phone,email) values(?,?,?,?,?,?,?)";
		pstmt = conn.prepareStatement(sql);
		pstmt.setInt(1, Integer.valueOf(num));
		pstmt.setString(2, pwd);
		pstmt.setString(3, depart);
		pstmt.setString(4, name);
		pstmt.setString(5, address);
		pstmt.setString(6, phone);
		pstmt.setString(7, email);
		pstmt.executeUpdate();
		out.println("Student 테이블  삽입이 성공했습니다.");
	} catch (SQLException ex) {
		out.println("Student 테이블 삽입이 실패했습니다.<br>");
		out.println("SQLException: " + ex.getMessage());
	} finally {
		if (pstmt != null)
			pstmt.close();
		if (conn != null)
			conn.close();
	}
%>
</body>
</html>

~~~

## 4.Read as Select

~~~jsp
<%@ page contentType="text/html; charset=utf-8"%>
<%@ page import="java.sql.*"%>                   
<html>
<head>
<title>Database SQL</title>
</head>
<body>
<%@ include file="dbconn.jsp" %>				
	<table width="100%" border="1">
		<tr>
			<th>학번</th>
			<th>비밀번호</th>
			<th>학과</th>
			<th>이름</th>
			<th>주소</th>
			<th>연락처</th>
			<th>이메일</th>
		</tr>
		<%
			ResultSet rs = null;			
			PreparedStatement pstmt = null;

			try {
				String sql = "select * from student2";
				pstmt = conn.prepareStatement(sql);
				rs = pstmt.executeQuery();

				while (rs.next()) {
					String num = rs.getString("num");
					String pwd = rs.getString("pwd");
					String depart = rs.getString("depart");
					String name = rs.getString("name");
					String address = rs.getString("address");
					String phone = rs.getString("phone");
					String email = rs.getString("email");
		%>
		<tr>
			<td><%=num%></td>
						<td><%=pwd%></td>
			<td><%=depart%></td>
			<td><%=name%></td>
			<td><%=address%></td>
			<td><%=phone%></td>
						<td><%=email%></td>
		</tr>
		<%
				}
			} catch (SQLException ex) {
				out.println("Student 테이블 호출이 실패했습니다.<br>");
				out.println("SQLException: " + ex.getMessage());
			} finally {
				if (rs != null)
					rs.close();
				if (pstmt != null)
					pstmt.close();
				if (conn != null)
					conn.close();
			}
		%>
	</table>
</body>
</html>

~~~

## 5.Update

~~~jsp
<%@ page contentType="text/html; charset=utf-8"%>
<html>
<head>
<title>Database SQL</title>
</head>
<body>	
	<form method="post" action="update_process.jsp">
      <p>	학번 : <input type="text" name="num"></p>
      <p>	비밀번호 : <input type="password" name="pwd"></p>
      <p>	변경하는 학과 : <input type="text" name="depart"></p>
      <p>	변경하는 이름 : <input type="text" name="name"></p>
      <p>	변경하는 주소 : <input type="text" name="address"></p>
      <p>	변경하는 연락처 : <input type="text" name="phone"></p>
      <p>	변경하는 이메일 : <input type="text" name="email"></p>
      <p>	<input type="submit" value="전송"></p>
	</form>
</body>
</html>

~~~

~~~jsp
<%@ page contentType="text/html; charset=utf-8"%>
<%@ page import="java.sql.*"%>
<html>
<head>
<title>Database SQL</title>
</head>
<body>
	<%@ include file="dbconn.jsp" %>
	<%
		request.setCharacterEncoding("utf-8");

		String num = request.getParameter("num");
		String pwd = request.getParameter("pwd");
		String depart = request.getParameter("depart");
		String name = request.getParameter("name");
		String address = request.getParameter("address");
		String phone = request.getParameter("phone");
		String email = request.getParameter("email");

		ResultSet rs = null;
		PreparedStatement pstmt = null;		

		try {
			String sql = "select num, pwd from student2 where num = ?";
			pstmt = conn.prepareStatement(sql);
			pstmt.setString(1, num);
			rs = pstmt.executeQuery();

			if (rs.next()) {
				String rId = rs.getString("num");
				String rPasswd = rs.getString("pwd");

				if (num.equals(rId) && pwd.equals(rPasswd)) {
					sql = "update student2 set depart = ? , name = ? , address = ? , phone = ? , email = ? where num = ?";
					pstmt = conn.prepareStatement(sql);
					pstmt.setString(1, depart);
					pstmt.setString(2, name);
					pstmt.setString(3, address);
					pstmt.setString(4, phone);
					pstmt.setString(5, email);
					pstmt.setString(6, num);
					
					pstmt.executeUpdate();
					out.println("Member 테이블을 수정했습니다.");
				} else
					out.println("일치하는 비밀번호가 아닙니다");
			} else
				out.println("Member 테이블에 일치하는 아이디가 없습니다.");
		} catch (SQLException ex) {
			out.println("SQLException: " + ex.getMessage());
		} finally {
			if (rs != null)
				rs.close();
			if (pstmt != null)
				pstmt.close();
			if (conn != null)
				conn.close();
		}
	%>
</body>
</html>
~~~

## 6.Delete

~~~jsp
<%@ page contentType="text/html; charset=utf-8"%>
<html>
<head>
<title>Database SQL</title>
</head>
<body>	
	<form method="post" action="delete_process.jsp">
		<p>	학번 : <input type="text" name="num">
		<p>	비밀번호 : <input type="password" name="pwd">		
		<p>	<input type="submit" value="전송">
	</form>
</body>
</html>

~~~

~~~jsp
<%@ page contentType="text/html; charset=utf-8"%>
<%@ page import="java.sql.*"%>
<html>
<head>
<title>Database SQL</title>
</head>
<body>
<%@ include file="dbconn.jsp" %>
	<%
		request.setCharacterEncoding("utf-8");

		String num = request.getParameter("num");
		String pwd = request.getParameter("pwd");


		ResultSet rs = null;
		PreparedStatement pstmt = null;
		
		try {
			String sql = "select num, pwd from student2 where num = ?";
			pstmt = conn.prepareStatement(sql);
			pstmt.setString(1, num);
			rs = pstmt.executeQuery();
			
			if (rs.next()) {
				String rPasswd = rs.getString("pwd");
				if (pwd.equals(rPasswd)) {
					sql = "delete from student2 where pwd = ?";
					pstmt = conn.prepareStatement(sql);
					pstmt.setString(1, pwd);
					pstmt.executeUpdate();
					out.println("student2 테이블을 삭제했습니다.");
				} else
					out.println("일치하는 비밀번호가 아닙니다");
			} else
				out.println("student2 테이블에 일치하는 아이디가 없습니다.");
		} catch (SQLException ex) {
			out.println("SQLException: " + ex.getMessage());
		} finally {
			if (rs != null)
				rs.close();
			if (pstmt != null)
				pstmt.close();
			if (conn != null)
				conn.close();
		}
	%>
</body>
</html>
~~~

