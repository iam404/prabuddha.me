---
id: 318
title: Basic Docker Guide For Dummies
date: 2017-08-12T12:48:08+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=318
permalink: /docker-guide-for-dummies/
dsq_thread_id:
  - "6238493239"
categories:
  - DevOps
---
<h2><a id="user-content-why" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#why"></a>Why Docker</h2>
"With Docker, developers can build any app in any language using any toolchain. “Dockerized” apps are completely portable and can run anywhere - colleagues’ OS X and Windows laptops, QA servers running Ubuntu in the cloud, and production data center VMs running Red Hat.

Developers can get going quickly by starting with one of the 13,000+ apps available on Docker Hub. Docker manages and tracks changes and dependencies, making it easier for sysadmins to understand how the apps that developers build work. And with Docker Hub, developers can automate their build pipeline and share artifacts with collaborators through public or private repositories.

Docker helps developers build and ship higher-quality applications, faster." -- <a href="https://www.docker.com/what-docker#copy1">What is Docker</a>
<h2><a id="user-content-installation" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#installation"></a>Installation</h2>
<h3><a id="user-content-linux-1" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#linux-1"></a>Linux</h3>
Quick and easy install script provided by Docker:
<pre>curl -sSL https://get.docker.com/ | sh
</pre>
If you're not willing to run a random shell script, please see the <a href="https://docs.docker.com/installation/">installation</a> instructions for your distribution.

If you are a complete Docker newbie, you should follow the <a href="https://docs.docker.com/linux/started/">series of tutorials</a> now.
<h3><a id="user-content-mac-os-x" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#mac-os-x"></a>Mac OS X</h3>
Download and install <a href="https://www.docker.com/products/docker-toolbox">Docker Toolbox</a>. If that doesn't work, see the <a href="https://docs.docker.com/installation/mac/">installation instructions</a>.
<blockquote><strong>NOTE</strong> If you have an existing docker toolbox, you might think you can upgrade <a href="https://docs.docker.com/machine/install-machine/">Docker Machine</a> binaries directly (either from URL or <code>docker-machine upgrade default</code>) and it will take care of itself. This is not going to help -- <code>docker-machine</code> will be <code>1.10.3</code> while <code>docker</code> is still <code>1.8.3</code> or whatever your previous version is.

You are much better off using Docker Toolbox DMG file to upgrade, which will take care of all the binaries at once.</blockquote>
Once you've installed Docker Toolbox, install a VM with Docker Machine using the VirtualBox provider:
<pre>docker-machine create --driver=virtualbox default
docker-machine ls
eval "$(docker-machine env default)"
</pre>
Then start up a container:
<pre>docker run hello-world
</pre>
That's it, you have a running Docker container.

If you are a complete Docker newbie, you should probably follow the <a href="https://docs.docker.com/mac/started/">series of tutorials</a> now.
<h2><a id="user-content-containers" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#containers"></a>Containers</h2>
<a href="http://etherealmind.com/basics-docker-containers-hypervisors-coreos/">Your basic isolated Docker process</a>. Containers are to Virtual Machines as threads are to processes. Or you can think of them as chroots on steroids.
<h3><a id="user-content-lifecycle" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#lifecycle"></a>Lifecycle</h3>
<ul>
 	<li><a href="https://docs.docker.com/reference/commandline/create"><code>docker create</code></a> creates a container but does not start it.</li>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/rename/"><code>docker rename</code></a> allows the container to be renamed.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/run"><code>docker run</code></a> creates and starts a container in one operation.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/rm"><code>docker rm</code></a> deletes a container.</li>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/update/"><code>docker update</code></a> updates a container's resource limits.</li>
</ul>
If you want a transient container, <code>docker run --rm</code> will remove the container after it stops.

If you want to map a directory on the host to a docker container, <code>docker run -v $HOSTDIR:$DOCKERDIR</code>.

If you want to remove also the volumes associated with the container, the deletion of the container must include the -v switch like in <code>docker rm -v</code>.

There's also a <a href="https://docs.docker.com/engine/admin/logging/overview/">logging driver</a> available for individual containers in docker 1.10. To run docker with a custom log driver (i.e. to syslog), use <code>docker run --log-driver=syslog</code>
<h3><a id="user-content-starting-and-stopping" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#starting-and-stopping"></a>Starting and Stopping</h3>
<ul>
 	<li><a href="https://docs.docker.com/reference/commandline/start"><code>docker start</code></a> starts a container so it is running.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/stop"><code>docker stop</code></a> stops a running container.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/restart"><code>docker restart</code></a> stops and starts a container.</li>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/pause/"><code>docker pause</code></a> pauses a running container, "freezing" it in place.</li>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/unpause/"><code>docker unpause</code></a> will unpause a running container.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/wait"><code>docker wait</code></a> blocks until running container stops.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/kill"><code>docker kill</code></a> sends a SIGKILL to a running container.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/attach"><code>docker attach</code></a> will connect to a running container.</li>
</ul>
If you want to integrate a container with a <a href="https://docs.docker.com/articles/host_integration/">host process manager</a>, start the daemon with <code>-r=false</code> then use <code>docker start -a</code>.

If you want to expose container ports through the host, see the exposing ports section.

Restart policies on crashed docker instances are covered <a href="https://prabuddha.me/docker-restart-policies/">here.</a>
<h3><a id="user-content-info" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#info"></a>Info</h3>
<ul>
 	<li><a href="https://docs.docker.com/reference/commandline/ps"><code>docker ps</code></a> shows running containers.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/logs"><code>docker logs</code></a> gets logs from container. (You can use a custom log driver, but logs is only available for <code>json-file</code> and<code>journald</code> in 1.10)</li>
 	<li><a href="https://docs.docker.com/reference/commandline/inspect"><code>docker inspect</code></a> looks at all the info on a container (including IP address).</li>
 	<li><a href="https://docs.docker.com/reference/commandline/events"><code>docker events</code></a> gets events from container.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/port"><code>docker port</code></a> shows public facing port of container.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/top"><code>docker top</code></a> shows running processes in container.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/stats"><code>docker stats</code></a> shows containers' resource usage statistics.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/diff"><code>docker diff</code></a> shows changed files in the container's FS.</li>
</ul>
<code>docker ps -a</code> shows running and stopped containers.

<code>docker stats --all</code> shows a running list of containers.
<h3><a id="user-content-import--export" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#import--export"></a>Import / Export</h3>
<ul>
 	<li><a href="https://docs.docker.com/reference/commandline/cp"><code>docker cp</code></a> copies files or folders between a container and the local filesystem..</li>
 	<li><a href="https://docs.docker.com/reference/commandline/export"><code>docker export</code></a> turns container filesystem into tarball archive stream to STDOUT.</li>
</ul>
<h3><a id="user-content-executing-commands" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#executing-commands"></a>Executing Commands</h3>
<ul>
 	<li><a href="https://docs.docker.com/reference/commandline/exec"><code>docker exec</code></a> to execute a command in container.</li>
</ul>
To enter a running container, attach a new shell process to a running container called foo, use: <code>docker exec -it foo /bin/bash</code>.
<h2><a id="user-content-images" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#images"></a>Images</h2>
Images are just <a href="https://docs.docker.com/engine/understanding-docker/#how-does-a-docker-image-work">templates for docker containers</a>.
<h3><a id="user-content-lifecycle-1" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#lifecycle-1"></a>Lifecycle</h3>
<ul>
 	<li><a href="https://docs.docker.com/reference/commandline/images"><code>docker images</code></a> shows all images.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/import"><code>docker import</code></a> creates an image from a tarball.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/build"><code>docker build</code></a> creates image from Dockerfile.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/commit"><code>docker commit</code></a> creates image from a container, pausing it temporarily if it is running.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/rmi"><code>docker rmi</code></a> removes an image.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/load"><code>docker load</code></a> loads an image from a tar archive as STDIN, including images and tags (as of 0.7).</li>
 	<li><a href="https://docs.docker.com/reference/commandline/save"><code>docker save</code></a> saves an image to a tar archive stream to STDOUT with all parent layers, tags &amp; versions (as of 0.7).</li>
</ul>
<h3><a id="user-content-info-1" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#info-1"></a>Info</h3>
<ul>
 	<li><a href="https://docs.docker.com/reference/commandline/history"><code>docker history</code></a> shows history of image.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/tag"><code>docker tag</code></a> tags an image to a name (local or registry).</li>
</ul>
<h3><a id="user-content-cleaning-up" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#cleaning-up"></a>Cleaning up</h3>
While you can use the <code>docker rmi</code> command to remove specific images, there's a tool called <a href="https://github.com/spotify/docker-gc">docker-gc</a> that will clean up images that are no longer used by any containers in a safe manner.
<h3><a id="user-content-loadsave-image" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#loadsave-image"></a>Load/Save image</h3>
Load an image from file:
<pre>docker load &lt; my_image.tar.gz
</pre>
Save an existing image:
<pre>docker save my_image:my_tag &gt; my_image.tar.gz
</pre>
<h3><a id="user-content-importexport-container" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#importexport-container"></a>Import/Export container</h3>
Import a container as an image from file:
<pre>cat my_container.tar.gz | docker import - my_image:my_tag
</pre>
Export an existing container:
<pre>docker export my_container &gt; my_container.tar.gz
</pre>
<h3><a id="user-content-difference-between-loading-a-saved-image-and-importing-an-exported-container-as-an-image-" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#difference-between-loading-a-saved-image-and-importing-an-exported-container-as-an-image-"></a>Difference between loading a saved image and importing an exported container as an image ?</h3>
Loading an image using the <code>load</code> command creates a new image including its history.
Importing a container as an image using the <code>import</code> command creates a new image excluding the history which results in a smaller image size compared to loading an image.
<h2><a id="user-content-networks" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#networks"></a>Networks</h2>
Docker has a <a href="https://docs.docker.com/engine/userguide/networking/dockernetworks/">networks</a> feature. There is a note saying that it's a good way to configure docker containers to talk to each other without using ports. See <a href="https://docs.docker.com/engine/userguide/networking/work-with-networks/">working with networks</a> for more details.
<h3><a id="user-content-lifecycle-2" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#lifecycle-2"></a>Lifecycle</h3>
<ul>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/network_create/"><code>docker network create</code></a></li>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/network_rm/"><code>docker network rm</code></a></li>
</ul>
<h3><a id="user-content-info-2" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#info-2"></a>Info</h3>
<ul>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/network_ls/"><code>docker network ls</code></a></li>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/network_inspect/"><code>docker network inspect</code></a></li>
</ul>
<h3><a id="user-content-connection" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#connection"></a>Connection</h3>
<ul>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/network_connect/"><code>docker network connect</code></a></li>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/network_disconnect/"><code>docker network disconnect</code></a></li>
</ul>
You can specify a <a href="https://blog.jessfraz.com/post/ips-for-all-the-things/">specific IP address for a container</a>:
<pre># create a new bridge network with your subnet and gateway for your ip block
docker network create --subnet 203.0.113.0/24 --gateway 203.0.113.254 iptastic

# run a nginx container with a specific ip in that block
$ docker run --rm -it --net iptastic --ip 203.0.113.2 nginx

# curl the ip from any other place (assuming this is a public ip block duh)
$ curl 203.0.113.2
</pre>
<h2><a id="user-content-registry--repository" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#registry--repository"></a>Registry &amp; Repository</h2>
A repository is a <em>hosted</em> collection of tagged images that together create the file system for a container.

A registry is a <em>host</em> -- a server that stores repositories and provides an HTTP API for <a href="https://docs.docker.com/userguide/dockerrepos/">managing the uploading and downloading of repositories</a>.

Docker.com hosts its own <a href="https://hub.docker.com/">index</a> to a central registry which contains a large number of repositories. Having said that, the central docker registry <a href="https://titanous.com/posts/docker-insecurity">does not do a good job of verifying images</a> and should be avoided if you're worried about security.
<ul>
 	<li><a href="https://docs.docker.com/reference/commandline/login"><code>docker login</code></a> to login to a registry.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/logout"><code>docker logout</code></a> to logout from a registry.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/search"><code>docker search</code></a> searches registry for image.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/pull"><code>docker pull</code></a> pulls an image from registry to local machine.</li>
 	<li><a href="https://docs.docker.com/reference/commandline/push"><code>docker push</code></a> pushes an image to the registry from local machine.</li>
</ul>
<h3><a id="user-content-run-local-registry" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#run-local-registry"></a>Run local registry</h3>
You can run a local registry by using the <a href="https://github.com/docker/distribution">docker distribution</a> project and looking at the <a href="https://github.com/docker/distribution/blob/master/docs/deploying.md">local deploy</a> instructions.

Also see the <a href="https://groups.google.com/a/dockerproject.org/forum/#!forum/distribution">mailing list</a>.
<h2><a id="user-content-dockerfile" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#dockerfile"></a>Dockerfile</h2>
<a href="https://docs.docker.com/reference/builder/">The configuration file</a>. Sets up a Docker container when you run <code>docker build</code> on it. Vastly preferable to <code>docker commit</code>.

Here are some common text editors and their syntax highlighting modules you could use to create Dockerfiles:
<ul>
 	<li><a href="https://packagecontrol.io/packages/Dockerfile%20Syntax%20Highlighting">Sublime Text 2</a></li>
 	<li><a href="https://atom.io/packages/language-docker">Atom</a></li>
 	<li><a href="https://github.com/ekalinin/Dockerfile.vim">Vim</a></li>
 	<li><a href="https://github.com/spotify/dockerfile-mode">Emacs</a></li>
</ul>
<h3><a id="user-content-instructions" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#instructions"></a>Instructions</h3>
<ul>
 	<li><a href="https://docs.docker.com/reference/builder/#dockerignore-file">.dockerignore</a></li>
 	<li><a href="https://docs.docker.com/reference/builder/#from">FROM</a> Sets the Base Image for subsequent instructions.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#maintainer">MAINTAINER</a> Set the Author field of the generated images..</li>
 	<li><a href="https://docs.docker.com/reference/builder/#run">RUN</a> execute any commands in a new layer on top of the current image and commit the results.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#cmd">CMD</a> provide defaults for an executing container.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#expose">EXPOSE</a> informs Docker that the container listens on the specified network ports at runtime. NOTE: does not actually make ports accessible.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#env">ENV</a> sets environment variable.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#add">ADD</a> copies new files, directories or remote file to container. Invalidates caches. Avoid <code>ADD</code> and use <code>COPY</code> instead.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#copy">COPY</a> copies new files or directories to container.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#entrypoint">ENTRYPOINT</a> configures a container that will run as an executable.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#volume">VOLUME</a> creates a mount point for externally mounted volumes or other containers.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#user">USER</a> sets the user name for following RUN / CMD / ENTRYPOINT commands.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#workdir">WORKDIR</a> sets the working directory.</li>
 	<li><a href="https://docs.docker.com/engine/reference/builder/#arg">ARG</a> defines a build-time variable.</li>
 	<li><a href="https://docs.docker.com/reference/builder/#onbuild">ONBUILD</a> adds a trigger instruction when the image is used as the base for another build.</li>
 	<li><a href="https://docs.docker.com/engine/reference/builder/#stopsignal">STOPSIGNAL</a> sets the system call signal that will be sent to the container to exit.</li>
 	<li><a href="https://docs.docker.com/engine/userguide/labels-custom-metadata/">LABEL</a> apply key/value metadata to your images, containers, or daemons.</li>
</ul>
<h3><a id="user-content-tutorial" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#tutorial"></a>Tutorial</h3>
<ul>
 	<li><a href="http://flux7.com/blogs/docker/docker-tutorial-series-part-3-automation-is-the-word-using-dockerfile/">Flux7's Dockerfile Tutorial</a></li>
</ul>
<h3><a id="user-content-examples" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#examples"></a>Examples</h3>
<ul>
 	<li><a href="https://docs.docker.com/reference/builder/#dockerfile-examples">Examples</a></li>
 	<li><a href="https://docs.docker.com/articles/dockerfile_best-practices/">Best practices for writing Dockerfiles</a></li>
 	<li><a href="http://crosbymichael.com/">Michael Crosby</a> has some more <a href="http://crosbymichael.com/dockerfile-best-practices.html">Dockerfiles best practices</a> / <a href="http://crosbymichael.com/dockerfile-best-practices-take-2.html">take 2</a>.</li>
 	<li><a href="http://jonathan.bergknoff.com/journal/building-good-docker-images">Building Good Docker Images</a> / <a href="http://jonathan.bergknoff.com/journal/building-better-docker-images">Building Better Docker Images</a></li>
 	<li><a href="https://speakerdeck.com/garethr/managing-container-configuration-with-metadata">Managing Container Configuration with Metadata</a></li>
</ul>
<h2><a id="user-content-layers" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#layers"></a>Layers</h2>
The versioned filesystem in Docker is based on layers. They're like <a href="https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/">git commits or changesets for filesystems</a>.

Note that if you're using <a href="https://en.wikipedia.org/wiki/Aufs">aufs</a> as your filesystem, Docker does not always remove data volumes containers layers when you delete a container! See <a href="https://github.com/docker/docker/pull/8484">PR 8484</a> for more details.
<h2><a id="user-content-links" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#links"></a>Links</h2>
Links are how Docker containers talk to each other <a href="https://docs.docker.com/userguide/dockerlinks/">through TCP/IP ports</a>. <a href="https://docs.docker.com/examples/running_redis_service/">Linking into Redis</a> and <a href="https://blogs.atlassian.com/2013/11/docker-all-the-things-at-atlassian-automation-and-wiring/">Atlassian</a> show worked examples. You can also (in 0.11) resolve <a href="https://docs.docker.com/userguide/dockerlinks/#updating-the-etchosts-file">links by hostname</a>.

NOTE: If you want containers to ONLY communicate with each other through links, start the docker daemon with <code>-icc=false</code> to disable inter process communication.

If you have a container with the name CONTAINER (specified by <code>docker run --name CONTAINER</code>) and in the Dockerfile, it has an exposed port:
<pre>EXPOSE 1337
</pre>
Then if we create another container called LINKED like so:
<pre>docker run -d --link CONTAINER:ALIAS --name LINKED user/wordpress
</pre>
Then the exposed ports and aliases of CONTAINER will show up in LINKED with the following environment variables:
<pre>$ALIAS_PORT_1337_TCP_PORT
$ALIAS_PORT_1337_TCP_ADDR
</pre>
And you can connect to it that way.

To delete links, use <code>docker rm --link</code>.

If you want to link across docker hosts then you should look at <a href="https://docs.docker.com/swarm/">Swarm</a>. This <a href="https://stackoverflow.com/questions/21283517/how-to-link-docker-services-across-hosts">link on stackoverflow</a> provides some good information on different patterns for linking containers across docker hosts.
<h2><a id="user-content-volumes" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#volumes"></a>Volumes</h2>
Docker volumes are <a href="https://docs.docker.com/userguide/dockervolumes/">free-floating filesystems</a>. They don't have to be connected to a particular container. You should use volumes mounted from <a href="https://medium.com/@ramangupta/why-docker-data-containers-are-good-589b3c6c749e">data-only containers</a> for portability.
<h3><a id="user-content-lifecycle-3" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#lifecycle-3"></a>Lifecycle</h3>
<ul>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/volume_create/"><code>docker volume create</code></a></li>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/volume_rm/"><code>docker volume rm</code></a></li>
</ul>
<h3><a id="user-content-info-3" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#info-3"></a>Info</h3>
<ul>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/volume_ls/"><code>docker volume ls</code></a></li>
 	<li><a href="https://docs.docker.com/engine/reference/commandline/volume_inspect/"><code>docker volume inspect</code></a></li>
</ul>
Volumes are useful in situations where you can't use links (which are TCP/IP only). For instance, if you need to have two docker instances communicate by leaving stuff on the filesystem.

You can mount them in several docker containers at once, using <code>docker run --volumes-from</code>.

Because volumes are isolated filesystems, they are often used to store state from computations between transient containers. That is, you can have a stateless and transient container run from a recipe, blow it away, and then have a second instance of the transient container pick up from where the last one left off.

See <a href="http://crosbymichael.com/advanced-docker-volumes.html">advanced volumes</a> for more details. Container42 is <a href="http://container42.com/2014/11/03/docker-indepth-volumes/">also helpful</a>.

You can <a href="https://docs.docker.com/userguide/dockervolumes/#mount-a-host-directory-as-a-data-volume">map MacOS host directories as docker volumes</a>:
<pre>docker run -v /Users/wsargent/myapp/src:/src
</pre>
You can also use remote NFS volumes if you're <a href="https://docs.docker.com/engine/tutorials/dockervolumes/#/mount-a-shared-storage-volume-as-a-data-volume">feeling brave</a>.

You may also consider running data-only containers as described <a href="http://container42.com/2013/12/16/persistent-volumes-with-docker-container-as-volume-pattern/">here</a> to provide some data portability.
<h2><a id="user-content-exposing-ports" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#exposing-ports"></a>Exposing ports</h2>
Exposing incoming ports through the host container is <a href="https://docs.docker.com/reference/run/#expose-incoming-ports">fiddly but doable</a>.

This is done by mapping the container port to the host port (only using localhost interface) using <code>-p</code>:
<pre>docker run -p 127.0.0.1:$HOSTPORT:$CONTAINERPORT --name CONTAINER -t someimage
</pre>
You can tell Docker that the container listens on the specified network ports at runtime by using <a href="https://docs.docker.com/reference/builder/#expose">EXPOSE</a>:
<pre>EXPOSE &lt;CONTAINERPORT&gt;
</pre>
But note that EXPOSE does not expose the port itself, only <code>-p</code> will do that. To expose the container's port on your localhosts port:
<pre>iptables -t nat -A DOCKER -p tcp --dport &lt;LOCALHOSTPORT&gt; -j DNAT --to-destination &lt;CONTAINERIP&gt;:&lt;PORT&gt;
</pre>
If you're running Docker in Virtualbox, you then need to forward the port there as well, using <a href="https://docs.vagrantup.com/v2/networking/forwarded_ports.html">forwarded_port</a>. It can be useful to define something in Vagrantfile to expose a range of ports so that you can dynamically map them:
<pre>Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  ...

  (49000..49900).each do |port|
    config.vm.network :forwarded_port, :host =&gt; port, :guest =&gt; port
  end

  ...
end
</pre>
If you forget what you mapped the port to on the host container, use <code>docker port</code> to show it:
<pre>docker port CONTAINER $CONTAINERPORT
</pre>
<h2><a id="user-content-best-practices" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#best-practices"></a>Best Practices</h2>
This is where general Docker best practices and war stories go:
<ul>
 	<li><a href="http://gregoryszorc.com/blog/2014/10/16/the-rabbit-hole-of-using-docker-in-automated-tests/">The Rabbit Hole of Using Docker in Automated Tests</a></li>
 	<li><a href="https://twitter.com/bridgetkromhout">Bridget Kromhout</a> has a useful blog post on <a href="http://sysadvent.blogspot.co.uk/2014/12/day-1-docker-in-production-reality-not.html">running Docker in production</a> at Dramafever.</li>
 	<li>There's also a best practices <a href="http://developers.lyst.com/devops/2014/12/08/docker/">blog post</a> from Lyst.</li>
 	<li><a href="https://engineering.salesforceiq.com/2013/11/05/a-docker-dev-environment-in-24-hours-part-2-of-2.html">A Docker Dev Environment in 24 Hours!</a></li>
 	<li><a href="https://tersesystems.com/2013/11/20/building-a-development-environment-with-docker/">Building a Development Environment With Docker</a></li>
 	<li><a href="https://samsaffron.com/archive/2013/11/07/discourse-in-a-docker-container">Discourse in a Docker Container</a></li>
</ul>
<h2><a id="user-content-security" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#security"></a>Security</h2>
This is where security tips about Docker go. The Docker <a href="https://docs.docker.com/engine/articles/security/">security</a> page goes into more detail.

First things first: Docker runs as root. If you are in the <code>docker</code> group, you effectively <a href="http://reventlov.com/advisories/using-the-docker-command-to-root-the-host">have root access</a>. If you expose the docker unix socket to a container, you are giving the container <a href="https://www.lvh.io/posts/dont-expose-the-docker-socket-not-even-to-a-container.html">root access to the host</a>.

Docker should not be your only defense. You should secure and harden it.

For an understanding of what containers leave exposed, you should read is <a href="https://www.nccgroup.trust/globalassets/our-research/us/whitepapers/2016/april/ncc_group_understanding_hardening_linux_containers-10pdf">Understanding and Hardening Linux Containers</a>by <a href="https://twitter.com/dyn___">Aaron Grattafiori</a>. This is a complete and comprehensive guide to the issues involved with containers, with a plethora of links and footnotes leading on to yet more useful content. The security tips following are useful if you've already hardened containers in the past, but are not a substitute for understanding.
<h3><a id="user-content-security-tips" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#security-tips"></a>Security Tips</h3>
For greatest security, you want to run Docker inside a virtual machine. This is straight from the Docker Security Team Lead --<a href="http://www.slideshare.net/jpetazzo/linux-containers-lxc-docker-and-security">slides</a> / <a href="http://www.projectatomic.io/blog/2014/08/is-it-safe-a-look-at-docker-and-security-from-linuxcon/">notes</a>. Then, run with AppArmor / seccomp / SELinux / grsec etc to <a href="http://linux-audit.com/docker-security-best-practices-for-your-vessel-and-containers/">limit the container permissions</a>. See the <a href="https://blog.docker.com/2016/02/docker-engine-1-10-security/">Docker 1.10 security features</a> for more details.

Docker image ids are <a href="https://medium.com/@quayio/your-docker-image-ids-are-secrets-and-its-time-you-treated-them-that-way-f55e9f14c1a4">sensitive information</a> and should not be exposed to the outside world. Treat them like passwords.

See the <a href="https://github.com/konstruktoid/Docker/blob/master/Security/CheatSheet.adoc">Docker Security Cheat Sheet</a> by <a href="https://github.com/konstruktoid">Thomas Sjögren</a>: some good stuff about container hardening in there.

Check out the <a href="https://github.com/docker/docker-bench-security">docker bench security script</a>, download the <a href="https://blog.docker.com/2015/05/understanding-docker-security-and-best-practices/">white papers</a> and subscribe to the <a href="https://www.docker.com/docker-security">mailing lists</a> (unfortunately Docker does not have a unique mailing list, only dev / user).

You should start off by using a kernel with unstable patches for grsecurity / pax compiled in, such as <a href="https://en.wikipedia.org/wiki/Alpine_Linux">Alpine Linux</a>. If you are using grsecurity in production, you should spring for <a href="https://grsecurity.net/business_support.php">commercial support</a> for the <a href="https://grsecurity.net/announce.php">stable patches</a>, same as you would do for RedHat. It's $200 a month, which is nothing to your devops budget.

Since docker 1.11 you can easily limit the number of active processes running inside a container to prevent fork bombs. This requires a linux kernel &gt;= 4.3 with CGROUP_PIDS=y to be in the kernel configuration.
<pre>docker run --pids-limit=64
</pre>
Also available since docker 1.11 is the ability to prevent processes to gain new privileges. This feature is in the linux kernel since version 3.5. You can read more about it in <a href="http://www.projectatomic.io/blog/2016/03/no-new-privs-docker/">this</a> blog post.
<pre>docker run --security-opt=no-new-privileges
</pre>
From the <a href="http://container-solutions.com/content/uploads/2015/06/15.06.15_DockerCheatSheet_A2.pdf">Docker Security Cheat Sheet</a> (it's in PDF which makes it hard to use, so copying below) by <a href="http://container-solutions.com/is-docker-safe-for-production/">Container Solutions</a>:

Turn off interprocess communication with:
<pre>docker -d --icc=false --iptables
</pre>
Set the container to be read-only:
<pre>docker run --read-only
</pre>
Verify images with a hashsum:
<pre>docker pull debian@sha256:a25306f3850e1bd44541976aa7b5fd0a29be
</pre>
Set volumes to be read only:
<pre>docker run -v $(pwd)/secrets:/secrets:ro debian
</pre>
Set memory and CPU sharing:
<pre>docker -c 512 -mem 512m
</pre>
Define and run a user in your Dockerfile so you don't run as root inside the container:
<pre>RUN groupadd -r user &amp;&amp; useradd -r -g user user
USER user
</pre>
<h3><a id="user-content-user-namespaces" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#user-namespaces"></a>User Namespaces</h3>
There's also work on <a href="https://s3hh.wordpress.com/2013/07/19/creating-and-using-containers-without-privilege/">user namespaces</a> -- it is in 1.10 but is not enabled by default.

To enable user namespaces ("remap the userns") in Ubuntu 15.10, <a href="https://raesene.github.io/blog/2016/02/04/Docker-User-Namespaces/">follow the blog example</a>.
<h3>Last Ids</h3>
<pre>alias dl='docker ps -l -q'
docker run ubuntu echo hello world
docker commit $(dl) helloworld
</pre>
<h3><a id="user-content-commit-with-command-needs-dockerfile" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#commit-with-command-needs-dockerfile"></a>Commit with command (needs Dockerfile)</h3>
<pre>docker commit -run='{"Cmd":["postgres", "-too -many -opts"]}' $(dl) postgres
</pre>
<h3><a id="user-content-get-ip-address" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#get-ip-address"></a>Get IP address</h3>
<pre>docker inspect $(dl) | grep IPAddress | cut -d '"' -f 4
</pre>
or install <a href="https://stedolan.github.io/jq/">jq</a>:
<pre>docker inspect $(dl) | jq -r '.[0].NetworkSettings.IPAddress'
</pre>
or using a <a href="https://docs.docker.com/reference/commandline/inspect">go template</a>
<pre>docker inspect -f '{{ .NetworkSettings.IPAddress }}' &lt;container_name&gt;
</pre>
<h3><a id="user-content-get-port-mapping" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#get-port-mapping"></a>Get port mapping</h3>
<pre>docker inspect -f '{{range $p, $conf := .NetworkSettings.Ports}} {{$p}} -&gt; {{(index $conf 0).HostPort}} {{end}}' &lt;containername&gt;
</pre>
<h3><a id="user-content-find-containers-by-regular-expression" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#find-containers-by-regular-expression"></a>Find containers by regular expression</h3>
<pre>for i in $(docker ps -a | grep "REGEXP_PATTERN" | cut -f1 -d" "); do echo $i; done
</pre>
<h3><a id="user-content-get-environment-settings" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#get-environment-settings"></a>Get Environment Settings</h3>
<pre>docker run --rm ubuntu env
</pre>
<h3><a id="user-content-kill-running-containers" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#kill-running-containers"></a>Kill running containers</h3>
<pre>docker kill $(docker ps -q)
</pre>
<h3><a id="user-content-delete-old-containers" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#delete-old-containers"></a>Delete old containers</h3>
<pre>docker ps -a | grep 'weeks ago' | awk '{print $1}' | xargs docker rm
</pre>
<h3><a id="user-content-delete-stopped-containers" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#delete-stopped-containers"></a>Delete stopped containers</h3>
<pre>docker rm -v $(docker ps -a -q -f status=exited)
</pre>
<h3><a id="user-content-delete-dangling-images" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#delete-dangling-images"></a>Delete dangling images</h3>
<pre>docker rmi $(docker images -q -f dangling=true)
</pre>
<h3><a id="user-content-delete-all-images" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#delete-all-images"></a>Delete all images</h3>
<pre>docker rmi $(docker images -q)
</pre>
<h3><a id="user-content-delete-dangling-volumes" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#delete-dangling-volumes"></a>Delete dangling volumes</h3>
As of Docker 1.9:
<pre>docker volume rm $(docker volume ls -q -f dangling=true)
</pre>
In 1.9.0, the filter <code>dangling=false</code> does <em>not</em> work - it is ignored and will list all volumes.
<h3><a id="user-content-show-image-dependencies" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#show-image-dependencies"></a>Show image dependencies</h3>
<pre>docker images -viz | dot -Tpng -o docker.png
</pre>
<h3>Slimming down Docker containers</h3>
<ul>
 	<li>Cleaning APT in a RUN layer This should be done in the same layer as other apt commands. Otherwise, the previous layers still persist the original information and your images will still be fat.</li>
</ul>
<pre>RUN {apt commands} \
 &amp;&amp; apt-get clean \
 &amp;&amp; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*
</pre>
<ul>
 	<li>Flatten an image</li>
</ul>
<pre>ID=$(docker run -d image-name /bin/bash)
docker export $ID | docker import – flat-image-name
</pre>
<ul>
 	<li>For backup</li>
</ul>
<pre>ID=$(docker run -d image-name /bin/bash)
(docker export $ID | gzip -c &gt; image.tgz)
gzip -dc image.tgz | docker import - flat-image-name
</pre>
<h3><a id="user-content-monitor-system-resource-utilization-for-running-containers" class="anchor" href="https://github.com/wsargent/docker-cheat-sheet#monitor-system-resource-utilization-for-running-containers"></a>Monitor system resource utilization for running containers</h3>
To check the CPU, memory, and network i/o usage of a single container, you can use:
<pre>docker stats &lt;container&gt;
</pre>
For all containers listed by id:
<pre>docker stats $(docker ps -q)
</pre>
For all containers listed by name:
<pre>docker stats $(docker ps --format '{{.Names}}')
</pre>
For all containers listed by image:
<pre>docker ps -a -f ancestor=ubuntu
</pre>