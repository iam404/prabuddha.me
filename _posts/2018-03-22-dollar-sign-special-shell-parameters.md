---
id: 661
title: What are the dollar sign special shell parameters?
date: 2018-03-22T21:16:00+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=661
permalink: /dollar-sign-special-shell-parameters/
dsq_thread_id:
  - "6570681209"
categories:
  - DevOps
---
<h4 class="subsection">Special Parameters</h4>
The shell treats several parameters specially. These parameters may only be referenced; assignment to them is not allowed.
<div class="post-text">
<ul>
 	<li>
<pre><code>$1</code>, <code>$2</code>, <code>$3</code>, ... are the <a href="https://www.gnu.org/software/bash/manual/html_node/Positional-Parameters.html" rel="noreferrer">positional parameters</a>.</pre>
</li>
 	<li>
<pre><code>"$@"</code> Expands to the positional parameters, starting from one. When the expansion occurs within double quotes, each parameter expands to a separate word. That is, "$@" is equivalent to "$1" "$2" …. If the double-quoted expansion occurs within a word, the expansion of the first parameter is joined with the beginning part of the original word, and the expansion of the last parameter is joined with the last part of the original word. When there are no positional parameters, "$@" and $@ expand to nothing (i.e., they are removed).</pre>
</li>
 	<li>
<pre><code>"$*"</code> Expands to the positional parameters, starting from one. When the expansion is not within double quotes, each positional parameter expands to a separate word. In contexts where it is performed, those words are subject to further word splitting and pathname expansion. When the expansion occurs within double quotes, it expands to a single word with the value of each parameter separated by the first character of the IFS special variable. That is, "$*" is equivalent to "$1<var>c</var>$2<var>c</var>…", where <var>c</var> is the first character of the value of the IFS variable. If IFS is unset, the parameters are separated by spaces. If IFS is null, the parameters are joined without intervening separators.</pre>
</li>
 	<li>
<pre><code>$#</code> Expands to the number of positional parameters in decimal.</pre>
</li>
 	<li>
<pre><code>$-</code> Expands to the current option flags as specified upon invocation, by the <code>set</code> builtin command, or those set by the shell itself (such as the <samp>-i</samp> option).</pre>
</li>
 	<li>
<pre><code>$$</code> Expands to the process <small>ID</small> of the shell. In a <code>()</code> subshell, it expands to the process <small>ID</small> of the invoking shell, not the subshell.</pre>
</li>
 	<li>
<pre><code>$_</code> At shell startup, set to the absolute pathname used to invoke the shell or shell script being executed as passed in the environment or argument list. Subsequently, expands to the last argument to the previous command, after expansion. Also set to the full pathname used to invoke each command executed and placed in the environment exported to that command. When checking mail, this parameter holds the name of the mail file.</pre>
</li>
 	<li>
<pre><code>$IFS</code> is the (input) field separator.</pre>
</li>
 	<li>
<pre><code>$?</code> Expands to the exit status of the most recently executed foreground pipeline.</pre>
</li>
 	<li>
<pre><code>$!</code> Expands to the process <small>ID</small> of the job most recently placed into the background, whether executed as an asynchronous command or using the bg builtin.</pre>
</li>
 	<li>
<pre><code>$0</code> Expands to the name of the shell or shell script. This is set at shell initialization. If Bash is invoked with a file of commands (see <a href="https://www.gnu.org/software/bash/manual/html_node/Shell-Scripts.html#Shell-Scripts">Shell Scripts</a>), <code>$0</code> is set to the name of that file. If Bash is started with the <samp>-c</samp> option (see <a href="https://www.gnu.org/software/bash/manual/html_node/Invoking-Bash.html#Invoking-Bash">Invoking Bash</a>), then <code>$0</code> is set to the first argument after the string to be executed, if one is present. Otherwise, it is set to the filename used to invoke Bash, as given by argument zero.</pre>
</li>
</ul>
Most of the above can be found under <a href="https://www.gnu.org/software/bash/manual/html_node/Special-Parameters.html" rel="noreferrer">Special Parameters</a> in the Bash Reference Manual. There are all the <a href="https://www.gnu.org/software/bash/manual/html_node/Shell-Variables.html" rel="noreferrer">environment variables set by the shell</a>.

For a comprehensive index, please see the <a href="https://www.gnu.org/software/bash/manual/html_node/Variable-Index.html" rel="noreferrer">Reference Manual Variable Index</a>.

</div>