---
title: Spring Framework 시작하기
date: 2023-10-06 15:30:00 +09:00
categories: [IT, Spring]
tags: [Spring, 환경설정, 프로젝트 시작]
image: /assets/img/posts/spring-framework-start.png
---


Spring Framework 생성 및 환경설정에 대한 정리를 해보려한다.     
예전에는 add framework support가 있어서 설정이 빠르게 되었다고 하는데, 이제는 인텔리제이에서 지원하지 않는다고 한다.   
Spring Framework를 add framework support 없이 프로젝트를 실행하는 방법이다.

## 프로젝트 생성
![스프링 프로젝트 생성](https://github.com/honge7694/honge7694.github.io/assets/76715487/ae05bea6-0019-45d3-b84e-1060da2ad33f)
1. 제너레이터에서 Maven 원형 선택.
2. JDK 선택.
3. Archetype에서 org.apache.maven.archetypes:maven-archetype-webapp 선택.
4. 생성 버튼 클릭.

## 프로젝트 생성 후 설정

### 1. webapp 폴더 파일 비우기
src/main/webapp/index.jsp와 src/main/webapp/WEB-INF/web.xml를 지운다.
webapp에는 설정 파일, jsp 파일이 위치하게 된다.

### 2. pom.xml 수정
pom.xml의 내용 수정 후 ctrl + shift + o를 눌러 maven 업데이트 진행한다.
<details>
<summary>pom.xml</summary>

<div markdown="1">

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>org.example</groupId>
  <artifactId>springframework_practice</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>springframework_practice Maven Webapp</name>
  <url>http://maven.apache.org</url>

  <properties>
	<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
	<maven.compiler.source>11</maven.compiler.source>
	<maven.compiler.target>11</maven.compiler.target>
	<java-version>11</java-version>
	<org.springframework-version>5.3.20</org.springframework-version>
	<org.slf4j-version>1.7.25</org.slf4j-version>
  </properties>

  <dependencies>
	<!-- Spring -->
	<dependency>
	  <groupId>org.springframework</groupId>
	  <artifactId>spring-context</artifactId>
	  <version>${org.springframework-version}</version>
	  <exclusions>
		<!-- Exclude Commons Logging in favor of SLF4j -->
		<exclusion>
		  <groupId>commons-logging</groupId>
		  <artifactId>commons-logging</artifactId>
		</exclusion>
	  </exclusions>
	</dependency>
	<dependency>
	  <groupId>org.springframework</groupId>
	  <artifactId>spring-webmvc</artifactId>
	  <version>${org.springframework-version}</version>
	</dependency>

	<!-- Logging -->
	<dependency>
	  <groupId>org.slf4j</groupId>
	  <artifactId>slf4j-api</artifactId>
	  <version>${org.slf4j-version}</version>
	</dependency>
	<dependency>
	  <groupId>org.slf4j</groupId>
	  <artifactId>jcl-over-slf4j</artifactId>
	  <version>${org.slf4j-version}</version>
	  <scope>runtime</scope>
	</dependency>

	<!-- @Inject -->
	<dependency>
	  <groupId>javax.inject</groupId>
	  <artifactId>javax.inject</artifactId>
	  <version>1</version>
	</dependency>

	<!-- javax.servlet-api -->
	<dependency>
	  <groupId>javax.servlet</groupId>
	  <artifactId>javax.servlet-api</artifactId>
	  <version>3.1.0</version>
	  <scope>provided</scope>
	</dependency>
	<dependency>
	  <groupId>javax.servlet.jsp</groupId>
	  <artifactId>jsp-api</artifactId>
	  <version>2.1</version>
	  <scope>provided</scope>
	</dependency>
	<dependency>
	  <groupId>javax.servlet</groupId>
	  <artifactId>jstl</artifactId>
	  <version>1.2</version>
	</dependency>

	<!-- data base -->
	<dependency>
	  <groupId>mysql</groupId>
	  <artifactId>mysql-connector-java</artifactId>
	  <version>8.0.22</version>
	</dependency>
	<dependency>
	  <groupId>org.mybatis</groupId>
	  <artifactId>mybatis</artifactId>
	  <version>3.5.6</version>
	</dependency>
	<dependency>
	  <groupId>org.mybatis</groupId>
	  <artifactId>mybatis-spring</artifactId>
	  <version>1.3.2</version>
	</dependency>
	<dependency>
	  <groupId>org.springframework</groupId>
	  <artifactId>spring-jdbc</artifactId>
	  <version>${org.springframework-version}</version>
	</dependency>
	<dependency>
	  <groupId>org.apache.tomcat</groupId>
	  <artifactId>tomcat-dbcp</artifactId>
	  <version>9.0.31</version>
	</dependency>

	<!-- fileupload -->
	<dependency>
	  <groupId>commons-fileupload</groupId>
	  <artifactId>commons-fileupload</artifactId>
	  <version>1.3.1</version>
	</dependency>

	<!-- Lombok -->
	<dependency>
	  <groupId>org.projectlombok</groupId>
	  <artifactId>lombok</artifactId>
	  <version>1.18.12</version>
	  <scope>provided</scope>
	</dependency>

	<!-- json -->
	<dependency>
	  <groupId>com.googlecode.json-simple</groupId>
	  <artifactId>json-simple</artifactId>
	  <version>1.1.1</version>
	</dependency>
	<dependency>
	  <groupId>com.google.code.gson</groupId>
	  <artifactId>gson</artifactId>
	  <version>2.8.2</version>
	</dependency>
	<!-- jackson-core -->
	<dependency>
	  <groupId>com.fasterxml.jackson.core</groupId>
	  <artifactId>jackson-core</artifactId>
	  <version>2.9.2</version>
	</dependency>
	<dependency>
	  <groupId>junit</groupId>
	  <artifactId>junit</artifactId>
	  <version>4.11</version>
	  <scope>test</scope>
	</dependency>
  </dependencies>

  <build>
	<finalName>mvc_test</finalName>
	<pluginManagement><!-- lock down plugins versions to avoid using Maven defaults (may be moved to parent pom) -->
	  <plugins>
		<plugin>
		  <artifactId>maven-clean-plugin</artifactId>
		  <version>3.1.0</version>
		</plugin>
		<!-- see http://maven.apache.org/ref/current/maven-core/default-bindings.html#Plugin_bindings_for_war_packaging -->
		<plugin>
		  <artifactId>maven-resources-plugin</artifactId>
		  <version>3.0.2</version>
		</plugin>
		<plugin>
		  <artifactId>maven-compiler-plugin</artifactId>
		  <version>3.8.0</version>
		</plugin>
		<plugin>
		  <artifactId>maven-surefire-plugin</artifactId>
		  <version>2.22.1</version>
		</plugin>
		<plugin>
		  <artifactId>maven-war-plugin</artifactId>
		  <version>3.2.2</version>
		</plugin>
		<plugin>
		  <artifactId>maven-install-plugin</artifactId>
		  <version>2.5.2</version>
		</plugin>
		<plugin>
		  <artifactId>maven-deploy-plugin</artifactId>
		  <version>2.8.2</version>
		</plugin>
	  </plugins>
	</pluginManagement>
	<plugins>
	  <plugin>
		<groupId>org.apache.maven.plugins</groupId>
		<artifactId>maven-compiler-plugin</artifactId>
		<configuration>
		  <source>11</source>
		  <target>11</target>
		</configuration>
	  </plugin>
	</plugins>
  </build>
</project>

```
</div>
</details>

### 3. webapp 폴더 파일 추가
src/main/webapp/WEB-INF에 root-context.xml, servlet-context.xml, web.xml 생성
**servlet-context.xml에 패키지 이름 확인하는 곳 주의해야 한다.**

<details>
<summary>root-context.xml</summary>

<div markdown="1">

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
	   xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	   xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	   xmlns:tx="http://www.springframework.org/schema/tx"
	   xmlns:aop="http://www.springframework.org/schema/aop"
	   xsi:schemaLocation="http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/aop http://www.springframework.org/schema/aop/spring-aop.xsd
		http://www.springframework.org/schema/tx http://www.springframework.org/schema/tx/spring-tx.xsd">
</beans>
```
</div>

</details>

<details>
<summary>servlet-context.xml</summary>

<div markdown="1">

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="http://www.springframework.org/schema/mvc"
			 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
			 xmlns:beans="http://www.springframework.org/schema/beans"
			 xmlns:context="http://www.springframework.org/schema/context"
			 xsi:schemaLocation="http://www.springframework.org/schema/mvc http://www.springframework.org/schema/mvc/spring-mvc.xsd
		http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
		http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd">

	<annotation-driven />

	<!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
	<resources mapping="/resources/**" location="/resources/"/>

	<!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
	<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<beans:property name="prefix" value="/WEB-INF/views/" />
		<beans:property name="suffix" value=".jsp" />
	</beans:bean>

	<!-- 프로젝트 패키지 이름 확인 -->
	<context:component-scan base-package="com.hong.project1" />

</beans:beans>
```
</div>

</details>

<details>
<summary>web.xml</summary>

<div markdown="1">

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xmlns="http://xmlns.jcp.org/xml/ns/javaee"
		 xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee http://xmlns.jcp.org/xml/ns/javaee/web-app_3_1.xsd"
		 version="3.1">

	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>
			/WEB-INF/root-context.xml
		</param-value>
	</context-param>

	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>

	<servlet>
		<servlet-name>appServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/servlet-context.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>appServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

	<filter>
		<filter-name>encodingFilter</filter-name>
		<filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
		<init-param>
			<param-name>encoding</param-name>
			<param-value>UTF-8</param-value>
		</init-param>
	</filter>

	<filter-mapping>
		<filter-name>encodingFilter</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>

</web-app>
```
</div>

</details>
 
### 4. WEB-INF 폴더 수정
src/main/webapp/WEB-INF에 views 폴더 생성 및 index.jsp 파일 추가.    
jsp 파일들이 위치하게 되는 폴더이다.
	
```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<html>
<head>
	<title>index</title>
</head>
<body>
	<h2>hello spring framework!!</h2>
</body>
</html>
```

### 5. MAIN 폴더 수정
main/java 폴더 및 패키지 생성
java 폴더에는 클래스 파일들이, resources 폴더에는 DB(MyBatis)가 들어가게 된다.
![main/java 생성](https://github.com/honge7694/honge7694.github.io/assets/76715487/1a8d3348-5172-4150-a143-36835b7f9ebd)
java 폴더에 package를 생성하는데, **servlet-context.xml에 설정한 패키지 이름으로 생성**한다.    
여기서는 `com.hong.project1`으로 생성한다.

### 6. PACKAGE 및 컨트롤러 생성
MAIN 폴더 수정 에서 생성한 패키지에 컨트롤러 패키지 생성 및 파일 생성
1. com.hong.project1.controller 패키지를 생성한다.
2. java파일 HomeController.java를 생성한다.
    ```java
    package com.hong.project1.controller;

    import org.springframework.stereotype.Controller;
    import org.springframework.web.bind.annotation.GetMapping;

    @Controller
    public class HomeController {
        @GetMapping("/")
        public String index(){
            return "index";
        }
    }

    ```

### 7. 톰캣 설정
1. 설정에 들어가서 먼저 톰캣을 설치한다.
	![톰캣 설치](https://github.com/honge7694/honge7694.github.io/assets/76715487/567a9fbf-e017-40fc-aff7-d6270f009731)
2. 상단의 현재 파일을 눌러 구성편집을 클릭하여 실행/디버그 수정 창을 띄운 후 아래와 같이 작성한다.
	![톰캣 설정](https://github.com/honge7694/honge7694.github.io/assets/76715487/67c34330-afe2-4585-b32d-b3d96a201baf)
	
### 8. 프로젝트 실행
이제 프로젝트를 실행해서 웹에서 확인하여 잘 만들어졌는지 확인한다.   
![스프링 실행](https://github.com/honge7694/honge7694.github.io/assets/76715487/ae96eabe-c4b9-4158-b487-03c2325acd0e)
	