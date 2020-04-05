---
id: 833
title: Exception Handling annotations in Spring MVC
date: 2018-06-20T22:38:49+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=833
permalink: /exception-handling-annotation-in-spring-mvc/
categories:
  - spring
---
<h2>a. Using HTTP Status Codes</h2>
Normally any unhandled exception thrown when processing a web-request causes the server to return an HTTP 500 response. However, any exception that you write yourself can be annotated with the <code>@ResponseStatus</code> annotation (which supports all the HTTP status codes defined by the HTTP specification). When an <em>annotated</em> exception is thrown from a controller method, and not handled elsewhere, it will automatically cause the appropriate HTTP response to be returned with the specified status-code.

For example, here is an exception for a missing order.
<pre class="prettyprint"><code class="prettyprint java"> <span class="lit">@ResponseStatus</span><span class="pun">(</span><span class="pln">value</span><span class="pun">=</span><span class="typ">HttpStatus</span><span class="pun">.</span><span class="pln">NOT_FOUND</span><span class="pun">,</span><span class="pln"> reason</span><span class="pun">=</span><span class="str">"No such Order"</span><span class="pun">)</span>  <span class="com">// 404</span>
 <span class="kwd">public</span> <span class="kwd">class</span> <span class="typ">OrderNotFoundException</span> <span class="kwd">extends</span> <span class="typ">RuntimeException</span> <span class="pun">{</span>
     <span class="com">// ...</span>
 <span class="pun">}</span></code></pre>
And here is a controller method using it:
<pre class="prettyprint"><code class="prettyprint java"> <span class="lit">@RequestMapping</span><span class="pun">(</span><span class="pln">value</span><span class="pun">=</span><span class="str">"/orders/{id}"</span><span class="pun">,</span><span class="pln"> method</span><span class="pun">=</span><span class="pln">GET</span><span class="pun">)</span>
 <span class="kwd">public</span> <span class="typ">String</span><span class="pln"> showOrder</span><span class="pun">(</span><span class="lit">@PathVariable</span><span class="pun">(</span><span class="str">"id"</span><span class="pun">)</span> <span class="kwd">long</span><span class="pln"> id</span><span class="pun">,</span> <span class="typ">Model</span><span class="pln"> model</span><span class="pun">)</span> <span class="pun">{</span>
     <span class="typ">Order</span><span class="pln"> order </span><span class="pun">=</span><span class="pln"> orderRepository</span><span class="pun">.</span><span class="pln">findOrderById</span><span class="pun">(</span><span class="pln">id</span><span class="pun">);</span>

     <span class="kwd">if</span> <span class="pun">(</span><span class="pln">order </span><span class="pun">==</span> <span class="kwd">null</span><span class="pun">)</span> <span class="kwd">throw</span> <span class="kwd">new</span> <span class="typ">OrderNotFoundException</span><span class="pun">(</span><span class="pln">id</span><span class="pun">);</span><span class="pln">

     model</span><span class="pun">.</span><span class="pln">addAttribute</span><span class="pun">(</span><span class="pln">order</span><span class="pun">);</span>
     <span class="kwd">return</span> <span class="str">"orderDetail"</span><span class="pun">;</span>
 <span class="pun">}</span></code></pre>
A familiar HTTP 404 response will be returned if the URL handled by this method includes an unknown order id.
<h2>b. Controller Based Exception Handling</h2>
<h3><a class="anchor" href="https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc#using-exceptionhandler" name="using-exceptionhandler"></a>Using @ExceptionHandler</h3>
You can add extra (<code>@ExceptionHandler </code>) methods to any controller to specifically handle exceptions thrown by request handling (<code>@RequestMapping</code>) methods in the same controller. Such methods can:
<ol>
 	<li>Handle exceptions without the <code>@ResponseStatus</code> annotation (typically predefined exceptions that you didn’t write)</li>
 	<li>Redirect the user to a dedicated error view</li>
 	<li>Build a totally custom error response</li>
</ol>
The following controller demonstrates these three options:
<pre class="prettyprint"><code class="prettyprint java"><span class="lit">@Controller</span>
<span class="kwd">public</span> <span class="kwd">class</span> <span class="typ">ExceptionHandlingController</span> <span class="pun">{</span>

  <span class="com">// @RequestHandler methods</span>
  <span class="pun">...</span>
  
  <span class="com">// Exception handling methods</span>
  
  <span class="com">// Convert a predefined exception to an HTTP Status code</span>
  <span class="lit">@ResponseStatus</span><span class="pun">(</span><span class="pln">value</span><span class="pun">=</span><span class="typ">HttpStatus</span><span class="pun">.</span><span class="pln">CONFLICT</span><span class="pun">,</span><span class="pln">
                  reason</span><span class="pun">=</span><span class="str">"Data integrity violation"</span><span class="pun">)</span>  <span class="com">// 409</span>
  <span class="lit">@ExceptionHandler</span><span class="pun">(</span><span class="typ">DataIntegrityViolationException</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">)</span>
  <span class="kwd">public</span> <span class="kwd">void</span><span class="pln"> conflict</span><span class="pun">()</span> <span class="pun">{</span>
    <span class="com">// Nothing to do</span>
  <span class="pun">}</span>
  
  <span class="com">// Specify name of a specific view that will be used to display the error:</span>
  <span class="lit">@ExceptionHandler</span><span class="pun">({</span><span class="typ">SQLException</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">,</span><span class="typ">DataAccessException</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">})</span>
  <span class="kwd">public</span> <span class="typ">String</span><span class="pln"> databaseError</span><span class="pun">()</span> <span class="pun">{</span>
    <span class="com">// Nothing to do.  Returns the logical view name of an error page, passed</span>
    <span class="com">// to the view-resolver(s) in usual way.</span>
    <span class="com">// Note that the exception is NOT available to this view (it is not added</span>
    <span class="com">// to the model) but see "Extending ExceptionHandlerExceptionResolver"</span>
    <span class="com">// below.</span>
    <span class="kwd">return</span> <span class="str">"databaseError"</span><span class="pun">;</span>
  <span class="pun">}</span>

  <span class="com">// Total control - setup a model and return the view name yourself. Or</span>
  <span class="com">// consider subclassing ExceptionHandlerExceptionResolver (see below).</span>
  <span class="lit">@ExceptionHandler</span><span class="pun">(</span><span class="typ">Exception</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">)</span>
  <span class="kwd">public</span> <span class="typ">ModelAndView</span><span class="pln"> handleError</span><span class="pun">(</span><span class="typ">HttpServletRequest</span><span class="pln"> req</span><span class="pun">,</span> <span class="typ">Exception</span><span class="pln"> ex</span><span class="pun">)</span> <span class="pun">{</span><span class="pln">
    logger</span><span class="pun">.</span><span class="pln">error</span><span class="pun">(</span><span class="str">"Request: "</span> <span class="pun">+</span><span class="pln"> req</span><span class="pun">.</span><span class="pln">getRequestURL</span><span class="pun">()</span> <span class="pun">+</span> <span class="str">" raised "</span> <span class="pun">+</span><span class="pln"> ex</span><span class="pun">);</span>

    <span class="typ">ModelAndView</span><span class="pln"> mav </span><span class="pun">=</span> <span class="kwd">new</span> <span class="typ">ModelAndView</span><span class="pun">();</span><span class="pln">
    mav</span><span class="pun">.</span><span class="pln">addObject</span><span class="pun">(</span><span class="str">"exception"</span><span class="pun">,</span><span class="pln"> ex</span><span class="pun">);</span><span class="pln">
    mav</span><span class="pun">.</span><span class="pln">addObject</span><span class="pun">(</span><span class="str">"url"</span><span class="pun">,</span><span class="pln"> req</span><span class="pun">.</span><span class="pln">getRequestURL</span><span class="pun">());</span><span class="pln">
    mav</span><span class="pun">.</span><span class="pln">setViewName</span><span class="pun">(</span><span class="str">"error"</span><span class="pun">);</span>
    <span class="kwd">return</span><span class="pln"> mav</span><span class="pun">;</span>
  <span class="pun">}</span>
<span class="pun">}</span></code></pre>
In any of these methods you might choose to do additional processing - the most common example is to log the exception.

Handler methods have flexible signatures so you can pass in obvious servlet-related objects such as <code>HttpServletRequest</code>, <code>HttpServletResponse</code>, <code>HttpSession</code> and/or <code>Principle</code>.
<h2>c. Global Exception Handling</h2>
<h3><a class="anchor" href="https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc#using-controlleradvice-classes" name="using-controlleradvice-classes"></a>Using @ControllerAdvice Classes</h3>
A controller advice allows you to use exactly the same exception handling techniques but apply them across the whole application, not just to an individual controller. You can think of them as an annotation driven interceptor.

Any class annotated with <code>@ControllerAdvice</code> becomes a controller-advice and three types of method are supported:
<ul>
 	<li>Exception handling methods annotated with <code>@ExceptionHandler</code>.</li>
 	<li>Model enhancement methods (for adding additional data to the model) annotated with
<code>@ModelAttribute</code>. Note that these attributes are <em>not</em> available to the exception handling views.</li>
 	<li>Binder initialization methods (used for configuring form-handling) annotated with
<code>@InitBinder</code>.</li>
</ul>
We are only going to look at exception handling - search the <a href="https://docs.spring.io/spring/docs/current/spring-framework-reference/web.html#mvc-controller">online manual</a> for more on <code>@ControllerAdvice</code> methods.

Any of the exception handlers you saw above can be defined on a controller-advice class - but now they apply to exceptions thrown from <em>any</em> controller. Here is a simple example:
<pre class="prettyprint"><code class="prettyprint java"><span class="lit">@ControllerAdvice</span>
<span class="kwd">class</span> <span class="typ">GlobalControllerExceptionHandler</span> <span class="pun">{</span>
    <span class="lit">@ResponseStatus</span><span class="pun">(</span><span class="typ">HttpStatus</span><span class="pun">.</span><span class="pln">CONFLICT</span><span class="pun">)</span>  <span class="com">// 409</span>
    <span class="lit">@ExceptionHandler</span><span class="pun">(</span><span class="typ">DataIntegrityViolationException</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">)</span>
    <span class="kwd">public</span> <span class="kwd">void</span><span class="pln"> handleConflict</span><span class="pun">()</span> <span class="pun">{</span>
        <span class="com">// Nothing to do</span>
    <span class="pun">}</span>
<span class="pun">}</span></code></pre>
If you want to have a default handler for <em>any</em> exception, there is a slight wrinkle. You need to ensure annotated exceptions are handled by the framework. The code looks like this:
<pre class="prettyprint"><code class="prettyprint java"><span class="lit">@ControllerAdvice</span>
<span class="kwd">class</span> <span class="typ">GlobalDefaultExceptionHandler</span> <span class="pun">{</span>
  <span class="kwd">public</span> <span class="kwd">static</span> <span class="kwd">final</span> <span class="typ">String</span><span class="pln"> DEFAULT_ERROR_VIEW </span><span class="pun">=</span> <span class="str">"error"</span><span class="pun">;</span>

  <span class="lit">@ExceptionHandler</span><span class="pun">(</span><span class="pln">value </span><span class="pun">=</span> <span class="typ">Exception</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">)</span>
  <span class="kwd">public</span> <span class="typ">ModelAndView</span><span class="pln">
  defaultErrorHandler</span><span class="pun">(</span><span class="typ">HttpServletRequest</span><span class="pln"> req</span><span class="pun">,</span> <span class="typ">Exception</span><span class="pln"> e</span><span class="pun">)</span> <span class="kwd">throws</span> <span class="typ">Exception</span> <span class="pun">{</span>
    <span class="com">// If the exception is annotated with @ResponseStatus rethrow it and let</span>
    <span class="com">// the framework handle it - like the OrderNotFoundException example</span>
    <span class="com">// at the start of this post.</span>
    <span class="com">// AnnotationUtils is a Spring Framework utility class.</span>
    <span class="kwd">if</span> <span class="pun">(</span><span class="typ">AnnotationUtils</span><span class="pun">.</span><span class="pln">findAnnotation
                </span><span class="pun">(</span><span class="pln">e</span><span class="pun">.</span><span class="pln">getClass</span><span class="pun">(),</span> <span class="typ">ResponseStatus</span><span class="pun">.</span><span class="kwd">class</span><span class="pun">)</span> <span class="pun">!=</span> <span class="kwd">null</span><span class="pun">)</span>
      <span class="kwd">throw</span><span class="pln"> e</span><span class="pun">;</span>

    <span class="com">// Otherwise setup and send the user to a default error-view.</span>
    <span class="typ">ModelAndView</span><span class="pln"> mav </span><span class="pun">=</span> <span class="kwd">new</span> <span class="typ">ModelAndView</span><span class="pun">();</span><span class="pln">
    mav</span><span class="pun">.</span><span class="pln">addObject</span><span class="pun">(</span><span class="str">"exception"</span><span class="pun">,</span><span class="pln"> e</span><span class="pun">);</span><span class="pln">
    mav</span><span class="pun">.</span><span class="pln">addObject</span><span class="pun">(</span><span class="str">"url"</span><span class="pun">,</span><span class="pln"> req</span><span class="pun">.</span><span class="pln">getRequestURL</span><span class="pun">());</span><span class="pln">
    mav</span><span class="pun">.</span><span class="pln">setViewName</span><span class="pun">(</span><span class="pln">DEFAULT_ERROR_VIEW</span><span class="pun">);</span>
    <span class="kwd">return</span><span class="pln"> mav</span><span class="pun">;</span>
  <span class="pun">}</span>
<span class="pun">}</span></code></pre>
<h2><a class="anchor" href="https://spring.io/blog/2013/11/01/exception-handling-in-spring-mvc#going-deeper" name="going-deeper"></a></h2>