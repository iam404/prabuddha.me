---
id: 836
title: Spring MVC @RequestMapping annotation
date: 2018-05-28T23:04:55+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=836
permalink: /spring-mvc-requestmapping-annotation/
categories:
  - DevOps
---
Let’s start with a simple example – mapping an HTTP request to a method using some basic criteria.
<h3><strong><em>@RequestMapping</em> – by Path</strong></h3>
<pre class="line number1 index0 alt2">@RequestMapping(value = "/ex/foos", method = RequestMethod.GET)
@ResponseBody
public String getFoosBySimplePath() {

    return "Get some Foos";

}</pre>
To test out this mapping with a simple <em>curl</em> command, run:
<pre>curl -i http://localhost:8080/spring-rest/ex/foos</pre>
<h3><strong><em>@RequestMapping</em> – the HTTP Method</strong></h3>
The HTTP <em>method</em> parameter has <strong>no default</strong> – so if we don’t specify a value, it’s going to map to an HTTP request.

Here’s a simple example, similar to the previous one – but this time mapped to an HTTP POST request:
<pre class="line number1 index0 alt2">@RequestMapping(value = "/ex/foos", method = RequestMethod.POST)
@ResponseBody
public String postFoos() {

    return "Post some Foos";

}</pre>
Again to test the POST via a <em>curl</em> command:
<pre>curl -i -X POST http://localhost:8080/spring-rest/ex/foos</pre>
<div></div>
<h2 id="http-headers"><strong><em>RequestMapping</em> and HTTP Headers</strong></h2>
<h3><strong> <em>@RequestMapping</em> with the <em>headers</em> Attribute</strong></h3>
The mapping can be narrowed even further by specifying a header for the request:
<pre class="line number1 index0 alt2">@RequestMapping(value = "/ex/foos", headers = "key=val", method = RequestMethod.GET)
@ResponseBody
public String getFoosWithHeader() {

    return "Get some Foos with Header";

}</pre>
And even multiple headers via the <em>header</em> attribute of <em>@RequestMapping</em>:
<pre class="line number1 index0 alt2">@RequestMapping(
  value = "/ex/foos",
  headers = { "key1=val1", "key2=val2" }, method = RequestMethod.GET)
@ResponseBody

public String getFoosWithHeaders() {

    return "Get some Foos with Header";

}</pre>
<h3><strong><em>@RequestMapping</em> Consumes and Produces</strong></h3>
Mapping <strong>media types produced by a controller</strong> method is worth special attention – we can map a request based on its <em>Accept</em> header via the <em>@RequestMapping</em> headers attribute introduced above:
<pre class="line number1 index0 alt2">@RequestMapping(
  value = "/ex/foos",
  method = RequestMethod.POST,
  consumes="application/json",
  produces = "application/json" )
@ResponseBody
public String getFoosAsJsonFromBrowser() {

    return "Get some Foos with Header Old";

}</pre>
This is consumed via <em>curl</em> in the same way:
<pre class="line number1 index0 alt2">curl -H "Accept:application/json" http://localhost:8080/spring-rest/ex/foos</pre>
Additionally, <em>produces</em> support multiple values as well:
<pre class="line number1 index0 alt2">@RequestMapping(
  value = "/ex/foos",
  method =RequestMethod.GET,
  produces = { "application/json", "application/xml" }
)</pre>
<h2 id="path-variable"><strong><em>RequestMapping</em> with Path Variables</strong></h2>
Parts of the mapping URI can be bound to variables via the <em>@PathVariable</em> annotation.
<h3><strong>Single <em>@PathVariable</em></strong></h3>
A simple example with a single path variable:
<pre class="line number1 index0 alt2">@RequestMapping(value = "/ex/foos/{id}", method = GET)
@ResponseBody
public String getFoosBySimplePathWithPathVariable(
  @PathVariable("id") long id) {

    return "Get a specific Foo with id=" + id;

}</pre>
If the name of the method parameter matches the name of the path variable exactly, then this can be simplified by <strong>using <em>@PathVariable</em> with no value</strong>:
<pre class="line number1 index0 alt2">@RequestMapping(value = "/ex/foos/{id}", method = GET)
@ResponseBody
public String getFoosBySimplePathWithPathVariable(
  @PathVariable String id) {
    return "Get a specific Foo with id=" + id;

}</pre>
<div><strong style="color: #333332; font-family: 'Myriad Pro', 'Lucida Grande', 'Lucida Sans Unicode', 'Lucida Sans', Geneva, Verdana, sans-serif; font-size: 1.6em; letter-spacing: -1px;">Multiple <em>@PathVariable</em></strong></div>
More complex URI may need to map multiple parts of the URI to <strong>multiple values</strong>:
<pre class="line number1 index0 alt2">@RequestMapping(value = "/ex/foos/{fooid}/bar/{barid}", method = GET)
@ResponseBody
public String getFoosBySimplePathWithPathVariables
  (@PathVariable long fooid, @PathVariable long barid) {
    return "Get a specific Bar with id=" + barid +
      " from a Foo with id=" + fooid;
}</pre>
<div></div>
<div><strong style="color: #333332; font-family: 'Myriad Pro', 'Lucida Grande', 'Lucida Sans Unicode', 'Lucida Sans', Geneva, Verdana, sans-serif; font-size: 1.6em; letter-spacing: -1px;"><em>@PathVariable</em> with RegEx</strong></div>
Regular expressions can also be used when mapping the <em>@PathVariable</em>; for example, we will restrict the mapping to only accept numerical values for the <em>id</em>:
<pre class="line number1 index0 alt2">@RequestMapping(value = "/ex/bars/{numericId:[\\d]+}", method = GET)
@ResponseBody
public String getBarsBySimplePathWithPathVariable(
  @PathVariable long numericId) {
    return "Get a specific Bar with id=" + numericId;
}</pre>
<h2 id="corner-cases"><strong><em>RequestMapping</em> Corner Cases</strong></h2>
<h3><strong> <em>@RequestMapping</em> – multiple paths mapped to the same controller method</strong></h3>
Although a single <em>@RequestMapping</em> path value is usually used for a single controller method, this is just good practice, not a hard and fast rule – there are some cases where mapping multiple requests to the same method may be necessary. For that case, <strong>the <em>value</em> attribute of <em>@RequestMapping</em> does accept multiple mappings</strong>, not just a single one:
<pre class="line number1 index0 alt2">@RequestMapping(
  value = { "/ex/advanced/bars", "/ex/advanced/foos" },\
  method = GET)
@ResponseBody
public String getFoosOrBarsByPath() {
    return "Advanced - Get some Foos or Bars";
}</pre>
<h3><strong><em>@RequestMapping</em> – multiple HTTP request methods to the same controller method</strong></h3>
Multiple requests using different HTTP verbs can be mapped to the same controller method:
<pre class="line number1 index0 alt2">@RequestMapping(
  value = "/ex/foos/multiple",
  method = { RequestMethod.PUT, RequestMethod.POST }
)
@ResponseBody
public String putAndPostFoos() {
    return "Advanced - PUT and POST within single method";
}</pre>
<strong style="color: #333332; font-family: 'Myriad Pro', 'Lucida Grande', 'Lucida Sans Unicode', 'Lucida Sans', Geneva, Verdana, sans-serif; font-size: 1.6em; letter-spacing: -1px;"><em>@RequestMapping</em> – a fallback for all requests</strong>

To implement a simple fallback for all requests using a particular HTTP method – for example, for a GET:
<pre class="line number1 index0 alt2">@RequestMapping(value = "*", method = RequestMethod.GET)
@ResponseBody
public String getFallback() {
    return "Fallback for GET Requests";
}</pre>
<div></div>
<div><strong style="color: #333332; font-family: 'Myriad Pro', 'Lucida Grande', 'Lucida Sans Unicode', 'Lucida Sans', Geneva, Verdana, sans-serif; font-size: 1.8em; letter-spacing: -1px;">[UPDATE] New Request Mapping Shortcuts</strong></div>
Spring Framework 4.3 introduced <a href="https://www.baeldung.com/spring-new-requestmapping-shortcuts">a few new</a> HTTP mapping annotations, all based on <em>@RequestMapping</em>:
<ul>
 	<li><strong><em>@GetMapping</em></strong></li>
 	<li><strong><em>@PostMapping</em></strong></li>
 	<li><strong><em>@PutMapping</em></strong></li>
 	<li><strong><em>@DeleteMapping</em></strong></li>
 	<li><strong><em>@PatchMapping</em></strong></li>
</ul>
These new annotations can improve the readability and reduce the verbosity of the code