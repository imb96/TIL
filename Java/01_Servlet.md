## Servlet이란?

Servlet은 Java를 사용하여 웹 서버에서 동적인 콘텐츠를 생성하는 서버 사이드 프로그램이다.
HTTP 요청을 받아 처리하고, HTTP 응답을 생성하는 Java 클래스라고 할 수 있다.

### Servlet의 특징

- **플랫폼 독립적**: Java로 작성되어 JVM이 설치된 모든 환경에서 실행 가능하다.
- **성능**: 스레드 기반으로 동작하여 CGI보다 효율적이다.
- **보안**: Java의 보안 관리자가 제공하는 보안 기능을 활용할 수 있다.

### Servlet 생명주기

Servlet은 다음과 같은 생명주기를 가진다:

1. **init()**: Servlet이 메모리에 로드될 때 한 번 실행된다.
2. **service()**: 클라이언트 요청이 올 때마다 실행된다.
3. **destroy()**: Servlet이 메모리에서 해제될 때 한 번 실행된다.

### 기본 Servlet 구현

```java
import javax.servlet.*;
import javax.servlet.http.*;
import java.io.*;

public class HelloServlet extends HttpServlet {

    @Override
    public void init() throws ServletException {
        // 초기화 코드
        System.out.println("Servlet 초기화");
    }

    @Override
    protected void doGet(HttpServletRequest request,
                        HttpServletResponse response)
                        throws ServletException, IOException {

        response.setContentType("text/html;charset=UTF-8");
        PrintWriter out = response.getWriter();

        out.println("<html>");
        out.println("<head><title>Hello Servlet</title></head>");
        out.println("<body>");
        out.println("<h1>안녕하세요, Servlet입니다!</h1>");
        out.println("</body>");
        out.println("</html>");
    }

    @Override
    public void destroy() {
        // 정리 코드
        System.out.println("Servlet 종료");
    }
}
```

### web.xml 설정

Servlet을 웹 애플리케이션에서 사용하려면 web.xml에 매핑 정보를 설정해야 한다:

```xml
<servlet>
    <servlet-name>HelloServlet</servlet-name>
    <servlet-class>com.example.HelloServlet</servlet-class>
</servlet>

<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
```

또는 Servlet 3.0 이상에서는 어노테이션을 사용할 수 있다:

```java
@WebServlet("/hello")
public class HelloServlet extends HttpServlet {
    // ...
}
```

## JSP(JavaServer Pages)란?

JSP는 HTML 내에 Java 코드를 삽입하여 동적인 웹 페이지를 생성하는 기술이다.
Servlet이 Java 코드 안에 HTML을 작성하는 방식이라면, JSP는 HTML 안에 Java 코드를 작성하는 방식이다.

### JSP의 특징

- **개발 편의성**: HTML과 Java 코드를 분리하여 개발이 용이하다.
- **자동 컴파일**: JSP는 최초 요청 시 자동으로 Servlet으로 변환된다.

### JSP 구성 요소

#### 1. 지시자(Directive)

JSP 페이지에 대한 설정 정보를 지정한다:

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" %>
<%@ include file="header.jsp" %>
<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core" %>
```

#### 2. 스크립트 요소

**선언문(Declaration)**

```jsp
<%!
    private int count = 0;

    public String getGreeting() {
        return "안녕하세요!";
    }
%>
```

**스크립틀릿(Scriptlet)**

```jsp
<%
    String name = request.getParameter("name");
    if (name != null) {
        out.println("환영합니다, " + name + "님!");
    }
%>
```

**표현식(Expression)**

```jsp
<p>현재 시간: <%= new java.util.Date() %></p>
<p>방문자 수: <%= ++count %></p>
```

#### 3. 액션 태그

```jsp
<jsp:include page="footer.jsp" />
<jsp:forward page="result.jsp" />
<jsp:useBean id="user" class="com.example.User" scope="session" />
```

### JSP 내장 객체

JSP는 다음과 같은 내장 객체를 제공합니다:

- **request**: HttpServletRequest 객체
- **response**: HttpServletResponse 객체
- **session**: HttpSession 객체
- **application**: ServletContext 객체
- **out**: JspWriter 객체
- **config**: ServletConfig 객체
- **page**: 현재 JSP 페이지 객체
- **pageContext**: PageContext 객체
- **exception**: 예외 객체 (에러 페이지에서만 사용)

### 실제 JSP 예제

```jsp
<%@ page language="java" contentType="text/html; charset=UTF-8" %>
<!DOCTYPE html>
<html>
<head>
    <title>회원 목록</title>
</head>
<body>
    <h1>회원 목록</h1>

    <%
        List<Member> members = (List<Member>) request.getAttribute("members");
        if (members != null && !members.isEmpty()) {
    %>
        <table border="1">
            <tr>
                <th>ID</th>
                <th>이름</th>
                <th>이메일</th>
            </tr>
            <% for (Member member : members) { %>
            <tr>
                <td><%= member.getId() %></td>
                <td><%= member.getName() %></td>
                <td><%= member.getEmail() %></td>
            </tr>
            <% } %>
        </table>
    <%
        } else {
    %>
        <p>등록된 회원이 없습니다.</p>
    <%
        }
    %>
</body>
</html>
```

## Servlet과 JSP의 관계

JSP는 결국 Servlet으로 변환되어 실행된다. JSP 파일이 처음 요청되면:

1. JSP 파일이 Servlet Java 소스 코드로 변환
2. Java 소스 코드가 클래스 파일로 컴파일
3. 클래스 파일이 메모리에 로드되어 실행

이 과정은 최초 요청 시 한 번만 수행되며, 이후 요청에서는 이미 컴파일된 Servlet이 실행된다.