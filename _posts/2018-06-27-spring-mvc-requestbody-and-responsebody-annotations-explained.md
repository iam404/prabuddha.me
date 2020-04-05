---
id: 816
title: Spring MVC RequestBody and ResponseBody Annotations explained
date: 2018-06-27T20:22:32+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=816
permalink: /spring-mvc-requestbody-and-responsebody-annotations-explained/
categories:
  - spring
---
They are annotations of the Spring MVC framework and can be used in a controller to implement smart object serialization and deserialization. They help you avoid boilerplate code by extracting the logic of message conversion and making it an aspect. Other than that they help you support multiple formats for a single REST resource without duplication of code. <strong>If you annotate a method with @ResponseBody, spring will try to convert its return value and write it to the HTTP response automatically. If you annotate the parameter of a method with @RequestBody, spring will try to convert the content of the incoming request body to your parameter object on the fly.</strong>
<h2 id="@requestbody"><strong><em>@RequestBody</em></strong></h2>
Simply put, the<em> @RequestBody</em> annotation maps the <em>HttpRequest</em> body to a transfer or domain object, enabling automatic deserialization of the inbound <em>HttpRequest</em> body onto a Java object.

First, let’s have a look at a Spring controller method:
<div>
<div id="highlighter_277820" class="syntaxhighlighter notranslate java">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="java color1">@PostMapping</code><code class="java plain">(</code><code class="java string">"/request"</code><code class="java plain">)</code></div>
<div class="line number2 index1 alt1"><code class="java keyword">public</code> <code class="java plain">ResponseEntity postController(</code></div>
<div class="line number3 index2 alt2"><code class="java spaces">  </code><code class="java color1">@RequestBody</code> <code class="java plain">LoginForm loginForm) {</code></div>
<div class="line number4 index3 alt1"><code class="java spaces"> </code></div>
<div class="line number5 index4 alt2"><code class="java spaces">    </code><code class="java plain">exampleService.fakeAuthenticate(loginForm);</code></div>
<div class="line number6 index5 alt1"><code class="java spaces">    </code><code class="java keyword">return</code> <code class="java plain">ResponseEntity.ok(HttpStatus.OK);</code></div>
<div class="line number7 index6 alt2"><code class="java plain">}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
Spring automatically deserializes the JSON into a Java type assuming an appropriate one is specified. By default, the type we annotate with the <em>@RequestBody</em> annotation must correspond to the JSON sent from our client-side controller:
<div>
<div id="highlighter_972223" class="syntaxhighlighter notranslate java">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="java keyword">public</code> <code class="java keyword">class</code> <code class="java plain">LoginForm {</code></div>
<div class="line number2 index1 alt1"><code class="java spaces">    </code><code class="java keyword">private</code> <code class="java plain">String username;</code></div>
<div class="line number3 index2 alt2"><code class="java spaces">    </code><code class="java keyword">private</code> <code class="java plain">String password;</code></div>
<div class="line number4 index3 alt1"><code class="java spaces">    </code><code class="java comments">// ...</code></div>
<div class="line number5 index4 alt2"><code class="java plain">}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
Here, the object we use to represent the <em>HttpRequest</em> body maps to our <em>LoginForm</em> object.

Let’s test this using CURL:
<div>
<div id="highlighter_892233" class="syntaxhighlighter notranslate actionscript3">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="actionscript3 plain">curl -i \</code></div>
<div class="line number2 index1 alt1"><code class="actionscript3 plain">-H </code><code class="actionscript3 string">"Accept: application/json"</code> <code class="actionscript3 plain">\</code></div>
<div class="line number3 index2 alt2"><code class="actionscript3 plain">-H </code><code class="actionscript3 string">"Content-Type:application/json"</code> <code class="actionscript3 plain">\</code></div>
<div class="line number4 index3 alt1"><code class="actionscript3 plain">-X POST --data </code></div>
<div class="line number5 index4 alt2"><code class="actionscript3 spaces">  </code><code class="actionscript3 string">'{"username": "johnny", "password": "password"}'</code> <code class="actionscript3 string">"<a href="https://localhost:8080/.../request">https://localhost:8080/.../request</a>"</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
This is all that is needed for a Spring REST API and an Angular client using the @<em>RequestBody</em> annotation!
<h2 id="requestbody"><strong><em>@ResponseBody</em></strong></h2>
The <em>@ResponseBody</em> annotation tells a controller that the object returned is automatically serialized into JSON and passed back into the <em>HttpResponse</em> object.

Suppose we have a custom <em>Response</em> object:
<div>
<div id="highlighter_144907" class="syntaxhighlighter notranslate java">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="java keyword">public</code> <code class="java keyword">class</code> <code class="java plain">ResponseTransfer {</code></div>
<div class="line number2 index1 alt1"><code class="java spaces">    </code><code class="java keyword">private</code> <code class="java plain">String text; </code></div>
<div class="line number3 index2 alt2"><code class="java spaces">    </code></div>
<div class="line number4 index3 alt1"><code class="java spaces">    </code><code class="java comments">// standard getters/setters</code></div>
<div class="line number5 index4 alt2"><code class="java plain">}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
Next, the associated controller can be implemented:
<div>
<div id="highlighter_930020" class="syntaxhighlighter notranslate java">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="java color1">@Controller</code></div>
<div class="line number2 index1 alt1"><code class="java color1">@RequestMapping</code><code class="java plain">(</code><code class="java string">"/post"</code><code class="java plain">)</code></div>
<div class="line number3 index2 alt2"><code class="java keyword">public</code> <code class="java keyword">class</code> <code class="java plain">ExamplePostController {</code></div>
<div class="line number4 index3 alt1"></div>
<div class="line number5 index4 alt2"><code class="java spaces">    </code><code class="java color1">@Autowired</code></div>
<div class="line number6 index5 alt1"><code class="java spaces">    </code><code class="java plain">ExampleService exampleService;</code></div>
<div class="line number7 index6 alt2"></div>
<div class="line number8 index7 alt1"><code class="java spaces">    </code><code class="java color1">@PostMapping</code><code class="java plain">(</code><code class="java string">"/response"</code><code class="java plain">)</code></div>
<div class="line number9 index8 alt2"><code class="java spaces">    </code><code class="java color1">@ResponseBody</code></div>
<div class="line number10 index9 alt1"><code class="java spaces">    </code><code class="java keyword">public</code> <code class="java plain">ResponseTransfer postResponseController(</code></div>
<div class="line number11 index10 alt2"><code class="java spaces">      </code><code class="java color1">@RequestBody</code> <code class="java plain">LoginForm loginForm) {</code></div>
<div class="line number12 index11 alt1"><code class="java spaces">        </code><code class="java keyword">return</code> <code class="java keyword">new</code> <code class="java plain">ResponseTransfer(</code><code class="java string">"Thanks For Posting!!!"</code><code class="java plain">);</code></div>
<div class="line number13 index12 alt2"><code class="java spaces">     </code><code class="java plain">}</code></div>
<div class="line number14 index13 alt1"><code class="java plain">}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
In the developer console of our browser or using a tool like Postman, we can see the following response:
<div>
<div id="highlighter_561171" class="syntaxhighlighter notranslate text">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="text plain">{"text":"Thanks For Posting!!!"}</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
Remember, we don’t need to annotate the<em> @RestController-</em>annotated controllers with the <em>@ResponseBody</em> annotation since it’s done by default here.