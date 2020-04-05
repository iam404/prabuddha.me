---
id: 482
title: Publishing a Docker image to Amazon ECR
date: 2017-01-27T22:15:40+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=482
permalink: /publishing-a-docker-image-to-amazon-ecr/
categories:
  - Uncategorized
---
<h2>How to push a container</h2>
Let me share a few quick steps on how you can push your Docker container to the Amazon ECR repository.

<strong>Step1: Creating a repository</strong>

The first step is to create a repository where your Docker container can be pushed to. A single repository can contain multiple versions of a docker container with a maximum of 2k versions. For different docker containers, you would create individual repositories.

In order to create a repository, you may create it directly from AWS Dashboard webui or use the AWS CLI like here. Let’s say our test-svc docker container let’s just run this command using the AWS CLI:
<div>
<div id="highlighter_110125" class="syntaxhighlighter plain">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">aws ecr create-repository --repository-name test-svc</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<strong>Step2: Logging in to ECR</strong>

In order to be able to push containers via Docker, you need to login to the AWS ECR repository. You can do this by running this AWS CLI:
<div>
<div id="highlighter_796406" class="syntaxhighlighter plain">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">aws ecr get-login</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
This will give an output something like this:
<div>
<div id="highlighter_998656" class="syntaxhighlighter plain">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">docker login -u AWS -p password -e none <a href="https://aws_account_id.dkr.ecr.eu-west-1.amazonaws.com">https://aws_account_id.dkr.ecr.eu-west-1.amazonaws.com</a></code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
You need to take that output and run it in the console to do the actual login so that you can push your container.

<strong>Step3: Pushing the container</strong>

Now that we are authenticated we can start pushing the docker container, let’s make sure that we tag the container we want to push first:
<div>
<div id="highlighter_207522" class="syntaxhighlighter plain">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">docker tag test-svc:latest aws_account_id.dkr.ecr.eu-west-1.amazonaws.com/test-svc:latest</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
And after this we push the container as following:
<div>
<div id="highlighter_832424" class="syntaxhighlighter plain">
<table border="0" cellspacing="0" cellpadding="0">
<tbody>
<tr>
<td class="gutter">
<div class="line number1 index0 alt2">1</div></td>
<td class="code">
<div class="container">
<div class="line number1 index0 alt2"><code class="plain plain">docker push aws_account_id.dkr.ecr.eu-west-1.amazonaws.com/test-svc:latest</code></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
<em>Note: Please make sure to replace aws_account_id with your actual AWS account id. This repository URL with the account ID is also returned when your repository was created in Step 1.</em>

Hope the above helps for people that want to publish their Docker containers to AWS ECR ? If you have any questions do not hesitate to reach out to me via the different channels.