---
layout: post
title: "[Document] About servlet 3.1 9장 번역"
date:   2016-10-20 09:00:00 +0900
categories: etc spec document
---

#### Document
[servlet-3.1 SPEC](https://java.net/downloads/servlet-spec/Final/servlet-3_1-final.pdf)

## 작업중인 공간 (한글 번역본)

### 9.1 RequestDispatcher 가져오기 (Obtaining a RequestDispatcher)

An object implementing the RequestDispatcher interface may be obtained from the
ServletContext via the following methods:

ServletContext 의 아래 메소드를 통해 RequestDispatcher 인터페이스를 구현한 객체를 얻을 수 있다.

 - getRequestDispatcher
 - getNamedDispatcher

 The getRequestDispatcher method takes a String argument describing a path
within the scope of the ServletContext. This path must be relative to the root of the
ServletContext and begin with a ‘/’, or be empty. The method uses the path to
look up a servlet, using the servlet path matching rules in Chapter 12, “Mapping
Requests to Servlets”, wraps it with a RequestDispatcher object, and returns the
resulting object. If no servlet can be resolved based on the given path, a
RequestDispatcher is provided that returns the content for that path.

 getRequestDispatcher 메소드는 ServletContext의 scope을 포함하는 path인 문자열 매개변수를 가진다. 
 이 path는 `ServletContext`의 root를 나타내는 상대경로여야만 하고, `/` 으로 시작하거나 혹은 비어 있어야 한다. 
 해당 메소드는 path 정보를 servlet을 찾는데 사용하고 `RequestDispatcher` 객체를 감싸서 결과 객체(resulting Object) 로 return한다. 
 (Chapter 12, `Mapping Requests to Servlets` 에 소개하는 Rule에 의해 찾는다). 
 만약 주어진 path 기반으로 resolved 되는 servelt이 없다면 a RequestDispatcher is provided that returns the content for that path.

The getNamedDispatcher method takes a String argument indicating the name of a
servlet known to the ServletContext. If a servlet is found, it is wrapped with a
RequestDispatcher object and the object is returned. If no servlet is associated with
the given name, the method must return null.

`getNamedDispatcher` 메소드는 `ServletContext` 에 등록되어 있는 Servlet 이름을 나타내는 문자열 매개변수를 가진다. 
만약 servlet을 찾으면, 메소드에서는 `RequestDispatcher` 객체를 wrapping 하고  해당 객체를 반환한다. 
해당 매개변수의 servelt을 찾지 못한다면 메소드는 반드시 `null`을 반환한다.

To allow RequestDispatcher objects to be obtained using relative paths that are
relative to the path of the current request (not relative to the root of the
ServletContext), the getRequestDispatcher method is provided in the
ServletRequest interface.

(ServletContext 의 root 기준이 아닌) 현재 요청에 상대적인 path를 이용해서 RequestDispatcher 객체를 가져오는 것을 허락하기 위해서 getRequestDispatcher 메소드는 ServletRequest 인터페이스에서 제공된다.

The behavior of this method is similar to the method of the same name in the
ServletContext. The servlet container uses information in the request object to
transform the given relative path against the current servlet to a complete path. For
example, in a context rooted at ’/’ and a request to /garden/tools.html, a request
dispatcher obtained via ServletRequest.getRequestDispatcher("header.html")
will behave exactly like a call to
ServletContext.getRequestDispatcher("/garden/header.html").

이 메소드의 내용은 ServletContext 인터페이스의 같은 이름의 메소드와 비슷하다. 

#### 9.1.1 Query Strings in Request Dispatcher Paths

The ServletContext and ServletRequest methods that create RequestDispatcher
objects using path information allow the optional attachment of query string
information to the path. For example, a Developer may obtain a RequestDispatcher
by using the following code:

path 정보를 사용하여 RequestDispatcher 객체를 만드는 ServeltContext와 ServletRequest 의 메소드는 path 정보에 추가적인 Query String 정보를 붙이는 것을 허용한다. 
예를 들어 개발자는 아래와 같은 Code를 사용하여 RequestDispatcher 객체를 가져올 수 있다.

~~~
String path = “/raisins.jsp?orderno=5”;
RequestDispatcher rd = context.getRequestDispatcher(path);
rd.include(request, response);
~~~


Parameters specified in the query string used to create the RequestDispatcher take
precedence over other parameters of the same name passed to the included servlet.
The parameters associated with a RequestDispatcher are scoped to apply only for
the duration of the include or forward call. 


Query String에 정의된 파라미터 들은 RequestDispatcher를 만드는 과정에서 동일한 이름의 다른 파라미터들 보다 우선권을 가진다.
해당 파라미터들은 include, forward 호출이 진행되는 동안에 파라미터들은 유효 scope을 가진다.

### 9.2 Using a Request Dispatcher
~~~
To use a request dispatcher, a servlet calls either the include method or forward
method of the RequestDispatcher interface. The parameters to these methods can
be either the request and response arguments that were passed in via the service
method of the javax.servlet.Servlet interface, or instances of subclasses of the
request and response wrapper classes that were introduced for version 2.3 of the
specification. In the latter case, the wrapper instances must wrap the request or
response objects that the container passed into the service method.
~~~

RequestDispatcher를 사용하기위해, servlet은 RequestDispatcher 인터페이스의 include()나 forward()를 호출합니다. 
이 메소드들의 파라미터로는 javax.servlet.Servlet 인터페이스의 service()를 통해 전달된 request, response 이거나 
또는 Servlet 2.3 Ver에 소개된 request, response의 subclass들의 인스턴스가 될 수 있습니다. 
후자의 경우에는 그 wrapper 인스턴스는 컨테이너가 service()에 전달한 request, response 객체를 반드시 wrapping해야한다.

~~~
The Container Provider should ensure that the dispatch of the request to a target
servlet occurs in the same thread of the same JVM as the original request.
~~~

컨테이너 제공자는 대상 서블릿으로의 request 전송이 original request로써 동일한 JVM의 동일한 스레드에서 나타나도록 보장해야한다.

### 9.3 The Include Method
~~~
The include method of the RequestDispatcher interface may be called at any time.
The target servlet of the include method has access to all aspects of the request
object, but its use of the response object is more limited.
~~~

RequestDispatcher 인터페이스의 include()는 언제든지 호출될 수 있다. 
include()의 타겟 서블릿은 request 객체의 모든 양상을 접근할 수 있는 반면 
response 객체의 사용에는 좀 더 제한적이다.

~~~
It can only write information to the ServletOutputStream or Writer of the response
object and commit a response by writing content past the end of the response buffer,
or by explicitly calling the flushBuffer method of the ServletResponse interface. It
cannot set headers or call any method that affects the headers of the response, with
the exception of the HttpServletRequest.getSession() and
HttpServletRequest.getSession(boolean) methods. Any attempt to set the
headers must be ignored, and any call to HttpServletRequest.getSession()
or HttpServletRequest.getSession(boolean) that would require adding a
Cookie response header must throw an IllegalStateException if the response
has been committed.
~~~


response 객체의 Writer 객체나, ServletOutputStream 객체를 통해서 정보를 쓰거나 response를 커밋할 수 있다. 
response 버퍼의 끝을 지나 content를 writing 하거나, ServletResponse 인터페이스의 flushBuffer()를 명시적으로 호출하는 방법으로 정보를 쓸 수 있다. 

~~~
If the default servlet is the target of a RequestDispatch.include() and the requested
resource does not exist, then the default servlet MUST throw
FileNotFoundException. If the exception isn't caught and handled, and the response
hasn’t been committed, the status code MUST be set to 500.
~~~
 
 
 9.3.1 Included Request Parameters

~~~
Except for servlets obtained by using the getNamedDispatcher method, a servlet that
has been invoked by another servlet using the include method of
RequestDispatcher has access to the path by which it was invoked.
~~~

The following request attributes must be set:

---
 - javax.servlet.include.request_uri
 - javax.servlet.include.context_path
 - javax.servlet.include.servlet_path
 - javax.servlet.include.path_info
 - javax.servlet.include.query_string
---

~~~
These attributes are accessible from the included servlet via the getAttribute
method on the request object and their values must be equal to the request URI,
context path, servlet path, path info, and query string of the included servlet,
respectively. If the request is subsequently included, these attributes are replaced for
that include.


If the included servlet was obtained by using the getNamedDispatcher method,
these attributes must not be set.
~~~

9.4 The Forward Method
The forward method of the RequestDispatcher interface may be called by the
calling servlet only when no output has been committed to the client. If output data
exists in the response buffer that has not been committed, the content must be
cleared before the target servlet’s service method is called. If the response has been
committed, an IllegalStateException must be thrown.

The path elements of the request object exposed to the target servlet must reflect the
path used to obtain the RequestDispatcher.

The only exception to this is if the RequestDispatcher was obtained via the
getNamedDispatcher method. In this case, the path elements of the request object
must reflect those of the original request.

Before the forward method of the RequestDispatcher interface returns without
exception, the response content must be sent and committed, and closed by the
servlet container, unless the request was put into the asynchronous mode. If an error
occurs in the target of the RequestDispatcher.forward() the exception may be
propagated back through all the calling filters and servlets and eventually back to
the container

9.4.1 Query String

The request dispatching mechanism is responsible for aggregating query string
parameters when forwarding or including requests.

9.4.2 Forwarded Request Parameters

Except for servlets obtained by using the getNamedDispatcher method, a servlet that
has been invoked by another servlet using the forward method of
RequestDispatcher has access to the path of the original request.

The following request attributes must be set:
---
javax.servlet.forward.request_uri
javax.servlet.forward.context_path
javax.servlet.forward.servlet_path
javax.servlet.forward.path_info
javax.servlet.forward.query_string
---

The values of these attributes must be equal to the return values of the
HttpServletRequest methods getRequestURI, getContextPath, getServletPath,
getPathInfo, getQueryString respectively, invoked on the request object passed to
the first servlet object in the call chain that received the request from the client.

These attributes are accessible from the forwarded servlet via the getAttribute
method on the request object. Note that these attributes must always reflect the
information in the original request even under the situation that multiple forwards
and subsequent includes are called.

If the forwarded servlet was obtained by using the getNamedDispatcher method,
these attributes must not be set.

9.5 Error Handling

If the servlet that is the target of a request dispatcher throws a runtime exception or
a checked exception of type ServletException or IOException, it should be
propagated to the calling servlet. All other exceptions should be wrapped as
ServletExceptions and the root cause of the exception set to the original exception,
as it should not be propagated.

9.6 Obtaining an AsyncContext

An object implementing the AsyncContext interface may be obtained from the
ServletRequest via one of startAsync methods. Once you have an AsyncContext,
you can use it to either complete the processing of the request via the complete()
method or use one of the dispatch methods described below.

9.7 The Dispatch Method
The following methods can be used to dispatch requests from the AsyncContext:

■ dispatch(path)

The dispatch method takes a String argument describing a path within the scope
of the ServletContext. This path must be relative to the root of the ServletContext
and begin with a ‘/’.

■ dispatch(servletContext, path)

The dispatch method takes a String argument describing a path within the scope
of the ServletContext specified. This path must be relative to the root of the
ServletContext specified and begin with a ‘/’.

■ dispatch()

The dispatch method takes no argument. It uses the original URI as the path. If the
AsyncContext was initialized via the startAsync(ServletRequest,
ServletResponse) and the request passed is an instance of HttpServletRequest,
then the dispatch is to the URI returned by HttpServletRequest.getRequestURI().
Otherwise the dispatch is to the URI of the request when it was last dispatched by
the container

One of the dispatch methods of the AsyncContext interface may be called by the
application waiting for the asynchronous event to happen. If complete() has been
called on the AsyncContext, an IllegalStateException must be thrown. All the
variations of the dispatch methods returns immediately and do not commit the
response.

The path elements of the request object exposed to the target servlet must reflect the
path specified in the AsyncContext.dispatch.

9.7.1 Query String

The request dispatching mechanism is responsible for aggregating query string
parameters when dispatching requests.

9.7.2 Dispatched Request Parameters

A servlet that has been invoked by using the dispatch method of AsyncContext has
access to the path of the original request.

The following request attributes must be set:
---
javax.servlet.async.request_uri
javax.servlet.async.context_path
javax.servlet.async.servlet_path
javax.servlet.async.path_info
javax.servlet.async.query_string
---

The values of these attributes must be equal to the return values of the
HttpServletRequest methods getRequestURI, getContextPath, getServletPath,
getPathInfo, getQueryString respectively, invoked on the request object passed to
the first servlet object in the call chain that received the request from the client.

These attributes are accessible from the dispatched servlet via the getAttribute
method on the request object. Note that these attributes must always reflect the
information in the original request even under the situation that multiple dispatches
are called.