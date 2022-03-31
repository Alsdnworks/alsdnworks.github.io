---
title: 웹프로그래밍 제 3강[KNU 2022-1]
categories:
  - KNU
tags:
  - JSP
toc: true
---

# 👨‍💻🏫KNU 2022-1 SW & media 웹프로그래밍 필기노트 3

## 1. Directive tag

- jsp를 어떻게 작성할지를 설정하는 메타데이터로 서블릿프로그램에서 서블릿 클래스로 변환될때 사용
- 페이지와 관련된 정보를 JSP컨테이너에 지시하는 메세지

|태그|형식|설명|
|-|-|-|
|page| <@page%> | JSP 페이지에 대한 정보를 설정 |
|include| <@include%> | JSP 페이지의 특정 영역에 다른 문서를 포함합니다 |
|taglib| <@taglib%> | JSP 페이지에서 사용할 태그 라이브러리를 설정 |

## 2. page directive tag

현재 JSP페이지에 대한 정보를 설정하는 태그로, 페이지 최상단에서 정의

~~~jsp
<@ page language="java" contentType="text/html; caharset=EUC-KR" pageEncoding="EUC-KR"%>
~~~

|속성|설명|기본값|
|-|-|-|
|language| 페이지에 사용할 언어 |java|
|pageEncoding| 페이지의 인코딩 |text/html|
|contentType| 페이지의 컨텐츠 타입(MIME-type) |text/html; charset=ISO-8859-1|
|import|사용할 자바클래스를 설정||
|session| 세션의 사용여부 |true|
|buffer| 출력 버퍼의 크기를 설정 |8KB|
|autoFlush| 출력 버퍼를 자동으로 전송할지 여부 |true|
|isThreadSafe| 멀티스레드 사용 여부 |true|
|info| 페이지에 대한 정보를 설정 ||
|errorPage| 에러 페이지 경로||
|isErrorPage| 에러 페이지 여부 |false|
|isELIgnored| EL 표현 사용 여부 |false|
|isScriptingEnabled| 스크립팅(선언문,스크립틀릿,표현문) 사용 여부 ||

### 2.1. import 속성

사용할 자바클래스를 설정하는 속성.

예시. java.io.&#42;과 java.ilang&#42;를 임포트 하는경우

~~~jsp
<@ page import = "java.io.*, java.lang.*" @>
~~~

## 3. include directive tag

현재 JSP페이지 특정영역에 외부 파일을 포함하는 태그로, 페이지 내 원하는 위치에 선언가능

~~~jsp
<@ include file="foobar.jsp"%>
~~~

## 4. taglib directive tag

현재 JSP페이지에 표현언어, JSTL, 사용자 정의 태그등 라이브러리를 설정하는 태그

~~~jsp
<@ taglib url="http://java.sun.com/jsp/jstl/core" prefix="c"%>
<c:/*사용자코드*/>
~~~

|속성|설명|
|-|-|
|url| 라이브러리 경로 |
|prefix| 태그라이브러리 단축어 |