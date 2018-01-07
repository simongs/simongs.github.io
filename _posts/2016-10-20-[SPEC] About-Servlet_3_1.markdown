---
layout: post
title: "[Document] About servlet 3.1 9장 번역"
date:   2016-10-20 09:00:00 +0900
categories: ETC
---

#### Document
[servlet-3.1 SPEC](https://java.net/downloads/servlet-spec/Final/servlet-3_1-final.pdf)

## 작업중인 공간 (한글 번역본)

## 9. Dispatching Requests

### 9.1 Obtaining a RequestDispatcher

ServletContext 의 아래 메소드를 통해 RequestDispatcher 인터페이스를 구현한 객체를 얻을 수 있다.

```
 - getRequestDispatcher
 - getNamedDispatcher
```

getRequestDispatcher()는 ServletContext의 scope 내의 path인 문자열 매개변수를 가진다. 
이 path는 ServletContext의 root에서의 상대경로여야만 하고, / 으로 시작하거나 혹은 비어 있어야 한다. 

해당 메소드는 path 정보를 servlet을 찾는데 사용하고 RequestDispatcher 객체를 감싸서 결과 객체(resulting Object) 로 return한다. (Chapter 12, Mapping Requests to Servlets 에 소개하는 Rule에 의해 찾는다)

만약 주어진 path 기반으로 resolved 되는 servelt이 없다면 path에 있는 컨텐츠를 반환하는 RequestDispatcher 가 제공됩니다.

getNamedDispatcher 메소드는 ServletContext 에 등록되어 있는 Servlet 이름을 나타내는 문자열 매개변수를 가진다. 만약 servlet을 찾으면, 메소드에서는 RequestDispatcher 객체를 wrapping 하여 반환합니다. 해당 매개변수의 servelt을 찾지 못한다면 메소드는 반드시 null 을 반환한다.

(ServletContext 의 root 기준이 아닌) 현재 요청으로부터 상대적인 path를 사용해 RequestDispatcher 객체를 가져오기 위해서 ServletRequest 인터페이스는 getRequestDispatcher()를 제공된다.

이 메소드와 비슷한 역할을 하는 동일한 이름의 메소드가 ServletContext()에도 있습니다. 서블릿 컨테이너는 요청 객체의 정보를 사용하여 현재 상대 경로를 완전한 경로로 변환합니다.

예를 들어서 context의 root는 '/' 이고 요청은 '/garden/tools.html'로 왔다고 가정합니다. 이때 ServletRequest.getRequestDispatcher("header.html")로 얻은 RequestDispatcher는 ServletContext.getRequestDispatcher("/garden/header.html")로 얻은 RequestDispatcher와 같은 행동을 합니다.
 
#### 9.1.1 Query Strings in Request Dispatcher Paths

path 정보를 사용하여 RequestDispatcher 객체를 만드는 ServeltContext와 ServletRequest 의 메소드는 path 정보에 추가적인 Query String 정보를 붙이는 것을 허용한다. 예를 들어 개발자는 아래와 같은 Code를 사용하여 RequestDispatcher 객체를 가져올 수 있다.

```
String path = “/raisins.jsp?orderno=5”;
RequestDispatcher rd = context.getRequestDispatcher(path);
rd.include(request, response);
```

Query String에 정의된 파라미터들은 RequestDispatcher를 만드는 과정에서 동일한 이름의 다른 파라미터들 보다 우선권을 가진다. RequestDispatcher와 연관된 파라미터들은 include, forward 호출이 진행되는 동안에만 유효합니다.

### 9.2 Using a Request Dispatcher

RequestDispatcher를 사용하기 위하여, servlet은 RequestDispatcher 인터페이스의 include()나 forward()를 호출합니다. 

이 메소드들의 파라미터로는 javax.servlet.Servlet 인터페이스의 service()를 통해 전달된 request, response 이거나  또는 Servlet 2.3 Ver에 소개되었던 request, response의 subclass들의 인스턴스가 될 수 있습니다. 
후자의 경우에는 그 wrapper 인스턴스는 컨테이너가 service()에 전달한 request, response 객체를 반드시 wrapping해야한다.

컨테이너 제공자는 대상 서블릿으로의 request 전송이 original request로써 동일한 JVM의 동일한 스레드에서 나타나도록 보장해야한다.

### 9.3 The Include Method

RequestDispatcher 인터페이스의 include()는 언제든지 호출될 수 있다. 
include()의 타겟 서블릿은 request 객체의 모든 속성을 접근할 수 있는 반면 
response 객체의 사용에는 좀 더 제한적이다.

response 객체의 Writer 객체나, ServletOutputStream 객체를 통해서 정보를 쓸 수 있습니다. 그리고 컨텐츠를 response 버퍼 끝까지 쓰거나, ServletResponse 인터페이스의 flushBuffer()를 명시적으로 호출하는 방법으로 response를 커밋할 수 있다. 

HttpServletRequest.getSession() 및 HttpServletRequest.getSession (boolean) 메소드를 제외하고는  헤더를 설정하거나 response 헤더에 영향을 주는 메소드를 호출 할 수 없습니다.

header를 설정하는 시도는 반드시 무시되며, 만약 response가 commit된 다음에 response 헤더에 쿠키정보를 추가하려한다면 IllegalStateException을 던져야 합니다.

만약 default 서블릿이 RequestDispatcher 의 include()의 대상이면서 요청된 resource가 존재하지 않으면, 그 default 서블릿은 반드시 FileNotFoundException 을 던져야 합니다. 만약 이 exception 을 처리하지 않는다면 response 는 commit될 수 없고 status code는 반드시 500으로 세팅해야 합니다.
 
#### 9.3.1 Included Request Parameters

getNamedDispatcher()를 사용하여 얻는 서블릿들을 제외하고, RequestDispatcher의 inclue() 를 사용하여 다른 서블릿에 의해 호출된 서블릿은 호출된 경로에 접근할 수 있습니다.

아래의 request 속성들은 반드시 세팅되어야 합니다.

```
 - javax.servlet.include.request_uri
 - javax.servlet.include.context_path
 - javax.servlet.include.servlet_path
 - javax.servlet.include.path_info
 - javax.servlet.include.query_string
```

이 속성들은 include된 서브릿이 request 객체의 getAttribute()를 통해서 접근이 가능합니다. 해당 속성들의 값은 반드시 [request URI, context path, servlet path, path info, and query string] 정보와 각각 동일해야합니다. 이후에 요청이 include 되면, 해당 속성들은 교채됩니다.

getNamedDispatcher() 를 사용하여 서블릿을 얻은 경우 이러한 속성을 설정되면 안됩니다.

### 9.4 The Forward Method

RequestDispatcher 인터페이스의 forward()는 오직 클라이언트에게 output이 commit되지 않았을 때만 호출됩니다. response 버퍼에 output 데이터가 있고 commit되지 않았다면 대상 서블릿의 service()가 호출되기 전에 클리어됩니다.
만약 response가 commit되었다면 IllegalStateException 가 던져집니다.

targer 서블릿에 노출돤 request 객체의 path 요소는 반드시 RequestDispatcher 를 가져오는데 사용된 path로 반영해야 합니다.

단 하나의 예외상황은 getNamedDispatcher()를 통하여 RequestDispatcher를 가져온 상황입니다. 이 경우의 path 요소는 original request의 path 정보를 반영해야 합니다.

RequestDispatcher 인터페이스의 forward()가 예외없이 return 되기전에 response 컨텐츠는 반드시 commit 되어 보내져야 합니다.

그리고 servlet 컨테이너에 의해 close 되어야합니다. (요청이 비동기모드가 아니라면) 만약 forward() 중에 exception이 발생하였다면 해당 exception은 호출스택을 따라 결국 컨테이너에게로 전파될 것입니다.

#### 9.4.1 Query String

request dispatching mechanism은 request를 forward 하거나 include 할때 query string을 집계합니다.

#### 9.4.2 Forwarded Request Parameters

getNamedDispatcher() 를 통해서 서블릿을 획득하는 경우를 제외하고, 다른 서블릿에서 forward()로 호출된 서블릿은 original request의 path 정보에 접근이 가능합니다.

아래 request 속성들은 반드시 세팅되어야 합니다.

```
javax.servlet.forward.request_uri
javax.servlet.forward.context_path
javax.servlet.forward.servlet_path
javax.servlet.forward.path_info
javax.servlet.forward.query_string
```

이 속성들의 값은 반드시 클라이언트로부터 request를 받은 call chain의 
첫번째 서블릿 객체에 전달된 요청 객체에서 호출하는 HttpServletRequest의 아래 method들의 return 값들과 동등해야 합니다. (getRequestURI, getContextPath, getServletPath, getPathInfo, getQueryString)

request 객체의 getAttribute()를 통해 forward된 서블릿으로부터 해당 속성들은 접근가능하다. 이 속성들은 여러번의 forward(), 후속 include()가 호출되는 상황에도 원래 요청 정보를 반영해야 합니다.

getNamedDispatcher()를 사용하여 획득된 forward된 서블릿은 이 속성들을 세팅하면 안됩니다.

### 9.5 Error Handling

RequestDispatcher의 대상 서블릿에서 RuntimeException 이나 (ServletException 또는 IOException) 같은 CheckedException을 던지면 호출한 서블릿까지 전파합니다.
전파를 하지 않기 위해서는 모든 예외는  root cause에 원래 예외를 세팅하여 ServletExceptions로 감싸져야합니다.

### 9.6 Obtaining an AsyncContext

AsyncContext 인터페이스를 구현한 객체를 ServletRequest로부터 startAsync() 를 통해서 가져올 수 있습니다.
AsyncContext가 있으면 complete()을 통해서 요청 처리를 완료할 수 있고, 혹은 아래에 설명된 dispatch() 중 하나를 사용할 수 있습니다. 

### 9.7 The Dispatch Method

AsyncContext에서 request를 dispatch 하기 위하여 아래 메소드들을 사용할 수 있다.

■ dispatch(path)

dispatch(path)는 servletContext 범위 내의 path를 나타내는 문자열 인자를 받습니다. 이 path정보는 ServletContext의 root로부터 상대적이여야 하고 반드시 '/' 으로 시작해야 합니다.

■ dispatch(servletContext, path)

dispatch(servletContext, path)는 전달된 특정 ServletContext 범위 내의 path를 설명하기 위해 문자열 인자를 받습니다. 이 path정보는 ServletContext의 root로부터 상대적이여야 하고 반드시 '/' 으로 시작해야 합니다.

■ dispatch()

이 dispatch()는 인자가 없습니다. 이 경우는 원래 URI를 path로 사용합니다.

만약 AsyncContext가 startAsync(ServletRequest, ServletResponse)에 의해 초기화 되었을때 전달된 request가 HttpServletRequest의 인스턴스이면 HttpServletRequest.getRequestURI()애 의해 리턴되는 URI로 dispatch 될것이고, 
아니라면 컨테이너에 의해 마지막으로 dispatch된 request의 URI로 dispatch 될 것입니다.

비동기적인 이벤트를 기다리고 있는 어플리케이션의 의해서 AsyncContext 인터페이스의 dispatch() 들중 하나가 호출됩니다.
만약 AsyncContext에 대해 complete()이 호출되면, IllegalStateException은 반드시 발생해야 합니다.  dispatch()는 즉시 리턴을 하고,  response를 commit하지 않습니다.

대상 서블릿에 노출된 request 객체의 path 요소는 반드시 AsyncContext.dispatch()에 지정된 path정보를 반영해야 합니다.

#### 9.7.1 Query String

request dispatching 메카니즘은 요청을 dispatching 할 때 query string을 집계합니다.

#### 9.7.2 Dispatched Request Parameters

AsyncContext의 dispatch()를 사용하여 호출된 서블릿은 original request의 경로에 접근이 가능합니다.

아래 속성은 반드시 세팅되어야 합니다.

```
javax.servlet.async.request_uri
javax.servlet.async.context_path
javax.servlet.async.servlet_path
javax.servlet.async.path_info
javax.servlet.async.query_string
```

이 속성들의 값은 반드시 클라이언트로부터 request를 받은 call chain의 
첫번째 서블릿 객체에 전달된 요청 객체에서 호출하는 HttpServletRequest의 아래 method들의 return 값들과 동등해야 합니다. (getRequestURI, getContextPath, getServletPath, getPathInfo, getQueryString)

이 속성들은 통해 dispatch된 서블릿으로부터 request 객체의 getAttribute()를 접근이 가능합니다. 이 속성들은 여러번의 dispatch()가 호출되는 상황에서도 original request의 정보를 반영해야 합니다.