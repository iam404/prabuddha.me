---
id: 408
title: 'org.jboss.msc.service.DuplicateServiceException: Service jboss.jdbc-driver.com_mysql is already registered'
date: 2017-04-11T11:54:06+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=408
permalink: /org-jboss-msc-service-duplicateserviceexception-service-jboss-jdbc-driver-com_mysql-already-registered/
dsq_thread_id:
  - "6206628958"
categories:
  - DevOps
---
<section class="field_kcs_issue_txt"><section class="field_kcs_environment_txt">
<h2>Environment</h2>
<ul>
 	<li>JBoss version 7.x</li>
 	<li>MySQL JDBCdriver
<ul>
 	<li>5.1.30 and up</li>
</ul>
</li>
</ul>
</section>
<h2>Issue</h2>
<ul>
 	<li>Using the CLI to register the mysql driver, we get an error.</li>
 	<li>Using the jar installer and adding the mysql driver during install, we get an error.</li>
</ul>
<div class="code-raw">
<pre>10:15:02,335 INFO  [org.jboss.as.connector.subsystems.datasources] (ServerService Thread Pool -- 25) JBAS010404: Deploying non-JDBC-compliant driver class com.mysql.jdbc.Driver (version 5.1)
10:15:02,344 INFO  [org.jboss.as.connector.subsystems.datasources] (ServerService Thread Pool -- 25) JBAS010404: Deploying non-JDBC-compliant driver class com.mysql.fabric.jdbc.FabricMySQLDriver (version 5.1)
10:15:02,346 ERROR [org.jboss.as.controller.management-operation] (ServerService Thread Pool -- 25) JBAS014612: Operation ("add") failed - address: ([
    ("subsystem" =&gt; "datasources"),
    ("jdbc-driver" =&gt; "mysql")
]): org.jboss.msc.service.DuplicateServiceException: Service jboss.jdbc-driver.mysql is already registered
        at org.jboss.msc.service.ServiceRegistrationImpl.setInstance(ServiceRegistrationImpl.java:154) [jboss-msc.jar:1.0.4.GA-redhat-1]
...
</pre>
</div>
<ul>
 	<li>We're trying to create a new JDBC dataSource using cli command and we're getting this error :</li>
</ul>
<div class="code-raw">
<pre>{
    "outcome" =&gt; "failed",
    "failure-description" =&gt; "JBAS014749: Manejador de operaciones fallido: Service jboss.jdbc-driver.XYZDriver is already registered",
    "rolled-back" =&gt; true
}
</pre>
</div>
</section><section class="field_kcs_resolution_txt">
<h2>Resolution</h2>
<ul>
 	<li>This is fixed in <a href="https://issues.jboss.org/browse/WFLY-3218">WFLY-3218</a> by only registering the first driver. Any second driver class needs to be specifically set.</li>
 	<li>For the current <code>EAP 6.x</code> versions, setting the driver class is accepted as the solution. There are no plans to backport.</li>
 	<li>When adding the driver using the <code>CLI</code>, one needs to specify the driver class name (prefix with the correct profile for domain mode)</li>
</ul>
<div class="code-raw">
<pre>/subsystem=datasources/jdbc-driver=mysql:add(driver-module-name=com.mysql.jdbc,driver-xa-datasource-class-name=com.mysql.jdbc.jdbc2.optional.MysqlXADataSource,driver-name=mysql,driver-class-name=com.mysql.jdbc.Driver)
</pre>
</div>
<ul>
 	<li>Add this line to the "drivers" section in the subsystem "datasources" in standalone.xml.

<strong>&lt;driver-class&gt;com.mysql.jdbc.Driver&lt;/driver-class&gt;

</strong>This results in the following xml snippet:</li>
</ul>
<div class="code-raw">
<pre>&lt;subsystem xmlns="urn:jboss:domain:datasources:1.1"&gt;
  &lt;datasources&gt;
...
    &lt;drivers&gt;
       &lt;driver name="mysql" module="com.mysql.jdbc"&gt;
<strong>         &lt;driver-class&gt;com.mysql.jdbc.Driver&lt;/driver-class&gt;</strong>
         &lt;xa-datasource-class&gt;com.mysql.jdbc.jdbc2.optional.MysqlXADataSource&lt;/xa-datasource-class&gt;
       &lt;/driver&gt;
...
</pre>
</div>
</section><section class="field_kcs_rootcause_txt">
<h2>Root Cause</h2>
<ul>
 	<li>The <code>MySQL</code> driver version <code>5.1.30</code> now has two driver classes registered in <code>META-INF/services/java.sql.Driver</code></li>
</ul>
<div class="code-raw">
<pre>com.mysql.jdbc.Driver
com.mysql.fabric.jdbc.FabricMySQLDriver
</pre>
</div>
The JDBC 4 specifications in section "9.2.1 Loading a driver that implements java.sql.Driver" does not explicitly state that more then one entry is allowed or disallowed.

</section>