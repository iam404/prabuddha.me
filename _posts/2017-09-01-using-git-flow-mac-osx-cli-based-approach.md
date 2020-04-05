---
id: 516
title: Using git flow in Mac OSX (CLI based approach)
date: 2017-09-01T05:51:50+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=516
permalink: /using-git-flow-mac-osx-cli-based-approach/
dsq_thread_id:
  - "6202808993"
categories:
  - git
---
<h1><strong>Installation :</strong></h1>
<ul>
 	<li><a id="user-content-homebrew" class="anchor" href="https://github.com/nvie/gitflow/wiki/Mac-OS-X#homebrew" aria-hidden="true"></a>Homebrew</li>
</ul>
If you're using <a href="http://github.com/mxcl/homebrew">homebrew</a>, it's simple. :
<pre><code>$ brew install git-flow
</code></pre>
<ul>
 	<li><a id="user-content-macports" class="anchor" href="https://github.com/nvie/gitflow/wiki/Mac-OS-X#macports" aria-hidden="true"></a>MacPorts</li>
</ul>
If you're using <a href="http://macports.org/">MacPorts</a>, it's simple, too:
<pre><code>$ port install git-flow
</code></pre>
If you run into problems due to Xcode 4.2 upgrades &amp; MacPorts 2.04 with the expat dependency not installing then simply use the workaround specified in <a href="https://trac.macports.org/wiki/ProblemHotlist">https://trac.macports.org/wiki/ProblemHotlist</a> like so:
<pre><code>$ sudo port install expat
---&gt;  Configuring expat
Error: Target org.macports.configure returned: configure failure: shell command failed (see log for details)
Log for expat is at: /opt/local/var/macports/logs/_opt_local_var_macports_sources_rsync.macports.org_release_tarballs_ports_textproc_expat/expat/main.log
Error: Status 1 encountered during processing.
To report a bug, see &lt;http://guide.macports.org/#project.tickets&gt;

$ sudo port clean expat
---&gt;  Cleaning expat
$ sudo port install expat configure.compiler=llvm-gcc-4.2
---&gt;  Fetching archive for expat
---&gt;  Attempting to fetch expat-2.1.0_0.darwin_11.x86_64.tbz2 from http://packages.macports.org/expat
---&gt;  Attempting to fetch expat-2.1.0_0.darwin_11.x86_64.tbz2.rmd160 from http://packages.macports.org/expat
---&gt;  Installing expat @2.1.0_0
---&gt;  Activating expat @2.1.0_0
---&gt;  Cleaning expat 

$ port install git-flow
</code></pre>
<ul>
 	<li><a id="user-content-wget" class="anchor" href="https://github.com/nvie/gitflow/wiki/Mac-OS-X#wget" aria-hidden="true"></a>wget</li>
</ul>
Even using wget its a one line effort.
<pre><code>wget --no-check-certificate -q -O - https://github.com/nvie/gitflow/raw/develop/contrib/gitflow-installer.sh | sudo bash
</code></pre>
<ul>
 	<li><a id="user-content-curl" class="anchor" href="https://github.com/nvie/gitflow/wiki/Mac-OS-X#curl" aria-hidden="true"></a>curl</li>
</ul>
wget: command not found? curl is only option.
<pre><code>curl -L -O https://raw.github.com/nvie/gitflow/develop/contrib/gitflow-installer.sh
sudo bash gitflow-installer.sh
</code></pre>
<h1>Commands to use git-flow :</h1>
<h2>Initializing git-flow</h2>
<pre><code>git flow init -d
</code></pre>
Above command will setup branches for your project.
<pre><code>Which branch should be used for bringing forth production releases?
   - master
Branch name for production releases: [master] 
Branch name for "next release" development: [develop] 

How to name your supporting branch prefixes?
Feature branches? [feature/] 
Release branches? [release/] 
Hotfix branches? [hotfix/] 
Support branches? [support/] 
Version tag prefix? [] ee-v
</code></pre>
We are using default configuration for this project.
<h2><a id="user-content-how-to-start-for-new-feature" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#how-to-start-for-new-feature" aria-hidden="true"></a>How to start for new feature.</h2>
<pre><code>git flow feature start &lt;feature-name&gt;
</code></pre>
here will be the branch like feature/
<h3><a id="user-content-publish-newly-created-feature" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#publish-newly-created-feature" aria-hidden="true"></a>Publish newly created feature.</h3>
You must publish your feature as you are working on collaboration over that feature.
<pre><code>git flow feature publish &lt;feature-name&gt;
</code></pre>
<h3><a id="user-content-pull-the-newly-published-feature-for-dev-other-than-who-published" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#pull-the-newly-published-feature-for-dev-other-than-who-published" aria-hidden="true"></a>Pull the newly published feature (For dev other than who published)</h3>
<pre><code>git flow feature pull &lt;feature-name&gt;
</code></pre>
<h3><a id="user-content-finish-the-feature" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#finish-the-feature" aria-hidden="true"></a>Finish the feature</h3>
<pre><code>git flow feature finish &lt;feature-name&gt;
</code></pre>
Finishing a feature will merge your feature branch to develop branch.
<h2><a id="user-content-how-to-start-for-release" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#how-to-start-for-release" aria-hidden="true"></a>How to start for release</h2>
<pre><code>git flow release start &lt;releasename&gt;
</code></pre>
for example:
<pre><code>git flow release start v3.x.x
</code></pre>
This will create a branch release/v3.x.x from <em>develop</em> branch.
<h3><a id="user-content-publish-the-release" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#publish-the-release" aria-hidden="true"></a>Publish the release</h3>
<pre><code>git flow release publish &lt;releasename&gt;
</code></pre>
For example:
<pre><code>git flow release publish v3.x.x
</code></pre>
<h3><a id="user-content-finish-the-release" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#finish-the-release" aria-hidden="true"></a>Finish the release</h3>
<pre><code>git flow release finish &lt;releasename&gt;
</code></pre>
For example:
<pre><code>git flow release finish v3.x.x
</code></pre>
<h3><a id="user-content-push-tags" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#push-tags" aria-hidden="true"></a>Push tags</h3>
<pre><code>git push --tags
</code></pre>
<h2><a id="user-content-how-to-start-hotfix" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#how-to-start-hotfix" aria-hidden="true"></a>How to start hotfix</h2>
Hot fixes are forked from master branches.
<pre><code>git flow hotfix start &lt;hotfix version&gt;
</code></pre>
For example:
<pre><code>git flow hotfix start &lt;v3.1.x&gt;
</code></pre>
<h3><a id="user-content-finishing-hotfix" class="anchor" href="https://github.com/rtCamp/docs.rtcamp.com/blob/master/easyengine/dev/git-flow.md#finishing-hotfix" aria-hidden="true"></a>Finishing Hotfix</h3>
<pre><code>git flow hotfix finish &lt;hotfix version&gt;
</code></pre>
For example:
<pre><code>git flow hotfix finish v3.1.x
</code></pre>
Finishing a hotfix will merge the hotfix in master and develop branch.

&nbsp;