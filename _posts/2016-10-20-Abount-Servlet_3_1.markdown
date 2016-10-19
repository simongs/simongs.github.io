---
layout: post
title: "About servlet 3.1 9장 번역"
date:   2016-10-20 09:00:00 +0900
categories: etc 
---

#### Document
[servlet-3.1 SPEC](https://java.net/downloads/servlet-spec/Final/servlet-3_1-final.pdf)

~~~
9.1 Obtaining a RequestDispatcher
An object implementing the RequestDispatcher interface may be obtained from the
ServletContext via the following methods:

■ getRequestDispatcher
■ getNamedDispatcher

The getRequestDispatcher method takes a String argument describing a path
within the scope of the ServletContext. This path must be relative to the root of the
ServletContext and begin with a ‘/’, or be empty. The method uses the path to
look up a servlet, using the servlet path matching rules in Chapter 12, “Mapping
Requests to Servlets”, wraps it with a RequestDispatcher object, and returns the
resulting object. If no servlet can be resolved based on the given path, a
RequestDispatcher is provided that returns the content for that path.

The getNamedDispatcher method takes a String argument indicating the name of a
servlet known to the ServletContext. If a servlet is found, it is wrapped with a
RequestDispatcher object and the object is returned. If no servlet is associated with
the given name, the method must return null.

To allow RequestDispatcher objects to be obtained using relative paths that are
relative to the path of the current request (not relative to the root of the
ServletContext), the getRequestDispatcher method is provided in the
ServletRequest interface.

The behavior of this method is similar to the method of the same name in the
ServletContext. The servlet container uses information in the request object to
transform the given relative path against the current servlet to a complete path. For
example, in a context rooted at ’/’ and a request to /garden/tools.html, a request
dispatcher obtained via ServletRequest.getRequestDispatcher("header.html")
will behave exactly like a call to
ServletContext.getRequestDispatcher("/garden/header.html").

9.1.1 Query Strings in Request Dispatcher Paths
The ServletContext and ServletRequest methods that create RequestDispatcher
objects using path information allow the optional attachment of query string
information to the path. For example, a Developer may obtain a RequestDispatcher
by using the following code:

---
String path = “/raisins.jsp?orderno=5”;
RequestDispatcher rd = context.getRequestDispatcher(path);
rd.include(request, response);
---

Parameters specified in the query string used to create the RequestDispatcher take
precedence over other parameters of the same name passed to the included servlet.
The parameters associated with a RequestDispatcher are scoped to apply only for
the duration of the include or forward call. 


9.2 Using a Request Dispatcher
To use a request dispatcher, a servlet calls either the include method or forward
method of the RequestDispatcher interface. The parameters to these methods can
be either the request and response arguments that were passed in via the service
method of the javax.servlet.Servlet interface, or instances of subclasses of the
request and response wrapper classes that were introduced for version 2.3 of the
specification. In the latter case, the wrapper instances must wrap the request or
response objects that the container passed into the service method.

The Container Provider should ensure that the dispatch of the request to a target
servlet occurs in the same thread of the same JVM as the original request.

9.3 The Include Method
The include method of the RequestDispatcher interface may be called at any time.
The target servlet of the include method has access to all aspects of the request
object, but its use of the response object is more limited.

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

If the default servlet is the target of a RequestDispatch.include() and the requested
resource does not exist, then the default servlet MUST throw
FileNotFoundException. If the exception isn't caught and handled, and the response
hasn’t been committed, the status code MUST be set to 500.

9.3.1 Included Request Parameters

Except for servlets obtained by using the getNamedDispatcher method, a servlet that
has been invoked by another servlet using the include method of
RequestDispatcher has access to the path by which it was invoked.

The following request attributes must be set:

---
javax.servlet.include.request_uri
javax.servlet.include.context_path
javax.servlet.include.servlet_path
javax.servlet.include.path_info
javax.servlet.include.query_string
---

These attributes are accessible from the included servlet via the getAttribute
method on the request object and their values must be equal to the request URI,
context path, servlet path, path info, and query string of the included servlet,
respectively. If the request is subsequently included, these attributes are replaced for
that include.

If the included servlet was obtained by using the getNamedDispatcher method,
these attributes must not be set.
~~~


