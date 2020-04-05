---
id: 839
title: Spring MVC @component, @controller, @Repository and @service annotations
date: 2018-05-20T23:57:13+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=839
permalink: /spring-mvc-component-controller-repository-and-service-annotations/
categories:
  - spring
---
Spring @Component, @Service, @Repository, and @Controller annotations are used for automatic bean detection using classpath scan in Spring framework. @Component is a generic annotation. Difference of @Service, @Repository, @Controller with @Component is they are special cases of @Component and used for particular purposes.
<h4>@Component Annotation</h4>
The <code>@Component</code> annotation marks a java class as a bean so the component-scanning mechanism of spring can pick it up and pull it into the application context. To use this annotation, apply it over class as below:
<pre>@Component
public EmployeeDAOImpl implements EmployeeDAO 
{    
   ...
}</pre>
<h4>@Controller Annotation</h4>
<code>@Controller</code> annotation marks a class as a Spring Web MVC controller. It too is a <code>@Component</code>specialization, so beans marked with it are automatically imported into the DI container. When we add the <code>@Controller</code> annotation to a class, we can use another annotation i.e. <code>@RequestMapping</code>; to map URLs to instance methods of a class.
<h4>@Service Annotation</h4>
The <code>@Service</code> annotation is also a specialization of the component annotation. It doesn’t currently provide any additional behavior over the <code>@Component</code> annotation, but it’s a good idea to use <code>@Service</code> over <code>@Component</code> in service-layer classes because <strong>it specifies intent better</strong>. Additionally, tool support and additional behavior might rely on it in the future.
<h4>@Repository Annotation</h4>
Although above use of <code>@Component</code> is good enough but we can use more suitable annotation that provides additional benefits specifically for DAOs i.e. <code>@Repository</code> annotation. The <code>@Repository</code> annotation is a specialization of the <code>@Component</code> annotation with similar use and functionality. In addition to importing the DAOs into the DI container, <strong>it also makes the unchecked exceptions (thrown from DAO methods) eligible for translation</strong> into Spring <code>DataAccessException</code>.

&nbsp;