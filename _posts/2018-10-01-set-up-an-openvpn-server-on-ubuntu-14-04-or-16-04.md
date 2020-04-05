---
id: 763
title: Set Up an OpenVPN Server on Ubuntu 14.04|16.04
date: 2018-10-01T00:15:52+00:00
author: Prabuddha
layout: post
guid: https://prabuddha.me/?p=763
permalink: /set-up-an-openvpn-server-on-ubuntu-14-04-or-16-04/
categories:
  - DevOps
  - security
---
<h2 id="step-1-install-openvpn">Step 1: Install OpenVPN</h2>
To start off, we will install OpenVPN onto our server. OpenVPN is available in Ubuntu's default repositories, so we can use <code>apt</code> for the installation. We will also be installing the <code>easy-rsa</code> package, which will help us set up an internal CA (certificate authority) for use with our VPN.

To update your server's package index and install the necessary packages type:
<pre class="wp-block-preformatted">sudo apt-get update
sudo apt-get install openvpn easy-rsa
</pre>
The needed software is now on the server, ready to be configured.
<h2 id="step-2-set-up-the-ca-directory">Step 2: Set Up the CA Directory</h2>
OpenVPN is an TLS/SSL VPN. This means that it utilizes certificates in order to encrypt traffic between the server and clients. In order to issue trusted certificates, we will need to set up our own simple certificate authority (CA).

To begin, we can copy the <code>easy-rsa</code> template directory into our home directory with the <code>make-cadir</code>command:
<pre class="wp-block-preformatted">make-cadir ~/openvpn-ca
</pre>
Move into the newly created directory to begin configuring the CA:
<pre class="wp-block-preformatted">cd ~/openvpn-ca
</pre>
<h2 id="step-3-configure-the-ca-variables">Step 3: Configure the CA Variables</h2>
To configure the values our CA will use, we need to edit the <code>vars</code> file within the directory. Open that file now in your text editor:
<pre class="wp-block-preformatted">nano vars
</pre>
Inside, you will find some variables that can be adjusted to determine how your certificates will be created. We only need to worry about a few of these.

Towards the bottom of the file, find the settings that set field defaults for new certificates. It should look something like this:~/openvpn-ca/vars
<pre class="wp-block-code"><code>. . .

export KEY_COUNTRY="US"
export KEY_PROVINCE="CA"
export KEY_CITY="SanFrancisco"
export KEY_ORG="Fort-Funston"
export KEY_EMAIL="me@myhost.mydomain"
export KEY_OU="MyOrganizationalUnit"

. . .
</code></pre>
Edit the values in red to whatever you'd prefer, but do not leave them blank:
<pre class="wp-block-preformatted">~/openvpn-ca/vars</pre>
While we are here, we will also edit the value<code>KEY_NAME</code> just below this section, which populates the subject field. To keep this simple, we'll call it <code>server</code> in this guide:~/openvpn-ca/vars
<pre class="wp-block-code"><code>export KEY_NAME="server"
</code></pre>
When you are finished, save and close the file.
<h2 id="step-4-build-the-certificate-authority">Step 4: Build the Certificate Authority</h2>
Now, we can use the variables we set and the <code>easy-rsa</code> utilities to build our certificate authority.

Ensure you are in your CA directory, and then source the <code>vars</code> file you just edited:
<pre class="wp-block-preformatted">cd ~/openvpn-ca
source vars

# On 14.04 run below command.
sudo mv ~/openvpn-caopenssl-1.0.0.cnf ~/openvpn-caopenssl.cnf</pre>
You should see the following if it was sourced correctly:
<pre class="wp-block-code"><code>OutputNOTE: If you run ./clean-all, I will be doing a rm -rf on /home/sammy/openvpn-ca/keys
</code></pre>
Make sure we're operating in a clean environment by typing:
<pre class="wp-block-preformatted">./clean-all
</pre>
Now, we can build our root CA by typing:
<pre class="wp-block-preformatted">./build-ca
</pre>
This will initiate the process of creating the root certificate authority key and certificate. Since we filled out the <code>vars</code> file, all of the values should be populated automatically. Just press <strong>ENTER</strong> through the prompts to confirm the selections:
<pre class="wp-block-code"><code>OutputGenerating a 2048 bit RSA private key
..........................................................................................+++
...............................+++
writing new private key to 'ca.key'
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
...
...</code></pre>
We now have a CA that can be used to create the rest of the files we need.
<h2 id="step-5-create-the-server-certificate-key-and-encryption-files">Step 5: Create the Server Certificate, Key, and Encryption Files</h2>
Next, we will generate our server certificate and key pair, as well as some additional files used during the encryption process.

Start by generating the OpenVPN server certificate and key pair. We can do this by typing:

<strong>Note</strong>: If you choose a name other than <code>server</code> here, you will have to adjust some of the instructions below. For instance, when copying the generated files to the <code>/etc/openvpn</code> directroy, you will have to substitute the correct names. You will also have to modify the <code>/etc/openvpn/server.conf</code> file later to point to the correct <code>.crt</code> and <code>.key</code> files.
<pre class="wp-block-preformatted">./build-key-server server
</pre>
Once again, the prompts will have default values based on the argument we just passed in (<code>server</code>) and the contents of our <code>vars</code> file we sourced.

Feel free to accept the default values by pressing <strong>ENTER</strong>. Do <em>not</em> enter a challenge password for this setup. Towards the end, you will have to enter <strong>y</strong> to two questions to sign and commit the certificate:
<pre class="wp-block-code"><code>Output. . .

Certificate is to be certified until May  1 17:51:16 2026 GMT (3650 days)
Sign the certificate? [y/n]:y


1 out of 1 certificate requests certified, commit? [y/n]y
Write out database with 1 new entries
Data Base Updated
</code></pre>
Next, we'll generate a few other items. We can generate a strong Diffie-Hellman keys to use during key exchange by typing:
<pre class="wp-block-preformatted">./build-dh
</pre>
This might take a few minutes to complete.

Afterwards, we can generate an HMAC signature to strengthen the server's TLS integrity verification capabilities:
<pre class="wp-block-preformatted">openvpn --genkey --secret keys/ta.key
</pre>
<h2 id="step-6-generate-a-client-certificate-and-key-pair">Step 6: Generate a Client Certificate and Key Pair</h2>
Next, we can generate a client certificate and key pair. Although this can be done on the client machine and then signed by the server/CA for security purposes, for this guide we will generate the signed key on the server for the sake of simplicity.

We will generate a single client key/certificate for this guide, but if you have more than one client, you can repeat this process as many times as you'd like. Pass in a unique value to the script for each client.

Because you may come back to this step at a later time, we'll re-source the <code>vars</code> file. We will use <code>client1</code> as the value for our first certificate/key pair for this guide.

To produce credentials without a password, to aid in automated connections, use the <code>build-key</code>command like this:
<pre class="wp-block-preformatted">cd ~/openvpn-ca
source vars
./build-key client1
</pre>
If instead, you wish to create a password-protected set of credentials, use the <code>build-key-pass</code>command:
<pre class="wp-block-preformatted">cd ~/openvpn-ca
source vars
./build-key-pass client1
</pre>
Again, the defaults should be populated, so you can just hit <strong>ENTER</strong> to continue. Leave the challenge password blank and make sure to enter <strong>y</strong> for the prompts that ask whether to sign and commit the certificate.
<h2 id="step-7-configure-the-openvpn-service">Step 7: Configure the OpenVPN Service</h2>
Next, we can begin configuring the OpenVPN service using the credentials and files we've generated.
<h3 id="copy-the-files-to-the-openvpn-directory">Copy the Files to the OpenVPN Directory</h3>
To begin, we need to copy the files we need to the <code>/etc/openvpn</code> configuration directory.

We can start with all of the files that we just generated. These were placed within the <code>~/openvpn-ca/keys</code> directory as they were created. We need to move our CA cert, our server cert and key, the HMAC signature, and the Diffie-Hellman file:
<pre class="wp-block-preformatted">cd ~/openvpn-ca/keys
sudo cp ca.crt server.crt server.key ta.key dh2048.pem /etc/openvpn
</pre>
Next, we need to copy and unzip a sample OpenVPN configuration file into the configuration directory so that we can use it as a basis for our setup:
<pre class="wp-block-preformatted">gunzip -c /usr/share/doc/openvpn/examples/sample-config-files/server.conf.gz | sudo tee /etc/openvpn/server.conf
</pre>
<h3 id="adjust-the-openvpn-configuration">Adjust the OpenVPN Configuration</h3>
Now that our files are in place, we can modify the server configuration file:
<pre class="wp-block-preformatted">sudo nano /etc/openvpn/server.conf
</pre>
<h4 id="basic-configuration">Basic Configuration</h4>
First, find the HMAC section by looking for the <code>tls-auth</code> directive. Remove the "<strong>;</strong>" to uncomment the <code>tls-auth</code> line. Below this, add the <code>key-direction</code> parameter set to "0":/etc/openvpn/server.conf
<pre class="wp-block-code"><code>tls-auth ta.key 0 # This file is secret
key-direction 0
</code></pre>
Next, find the section on cryptographic ciphers by looking for the commented out <code>cipher</code> lines. The <code>AES-128-CBC</code> cipher offers a good level of encryption and is well supported. Remove the "<strong>;</strong>" to uncomment the <code>cipher AES-128-CBC</code> line:/etc/openvpn/server.conf
<pre class="wp-block-code"><code>cipher AES-128-CBC
</code></pre>
Below this, add an <code>auth</code> line to select the HMAC message digest algorithm. For this, <code>SHA256</code> is a good choice:/etc/openvpn/server.conf
<pre class="wp-block-code"><code>auth SHA256
</code></pre>
Finally, find the <code>user</code> and <code>group</code> settings and remove the "<strong>;</strong>" at the beginning of to uncomment those lines:/etc/openvpn/server.conf
<pre class="wp-block-code"><code>user nobody
group nogroup
</code></pre>
<h4 id="optional-push-dns-changes-to-redirect-all-traffic-through-the-vpn">(Optional) Push DNS Changes to Redirect All Traffic Through the VPN</h4>
The settings above will create the VPN connection between the two machines, but will not force any connections to use the tunnel. If you wish to use the VPN to route all of your traffic, you will likely want to push the DNS settings to the client computers.

You can do this, uncomment a few directives that will configure client machines to redirect all web traffic through the VPN. Find the <code>redirect-gateway</code> section and remove the semicolon "<strong>;</strong>" from the beginning of the <code>redirect-gateway</code> line to uncomment it:<code>/etc/openvpn/server.conf</code>
<pre class="wp-block-code"><code>push "redirect-gateway def1 bypass-dhcp"
</code></pre>
Just below this, find the section<code>dhcp-option</code>. Again, remove the "<strong>;</strong>" from in front of both of the lines to uncomment them:<code>/etc/openvpn/server.conf</code>
<pre class="wp-block-code"><code>push "dhcp-option DNS 208.67.222.222"
push "dhcp-option DNS 208.67.220.220"
</code></pre>
This should assist clients in reconfiguring their DNS settings to use the VPN tunnel for as the default gateway.
<h4 id="optional-adjust-the-port-and-protocol">(Optional) Adjust the Port and Protocol</h4>
By default, the OpenVPN server uses port 1194 and the UDP protocol to accept client connections. If you need to use a different port because of restrictive network environments that your clients might be in, you can change the <code>port</code> option. If you are not hosting web content your OpenVPN server, port 443 is a popular choice since this is usually allowed through firewall rules.<code>/etc/openvpn/server.conf</code>
<pre class="wp-block-code"><code># Optional!
port 1194
</code></pre>
Often if the protocol will be restricted to that port as well. If so, change <code>proto</code> from UDP to TCP:<code>/etc/openvpn/server.conf</code>
<pre class="wp-block-code"><code># Optional!
proto tcp
</code></pre>
If you have no need to use a different port, it is best to leave these two settings as their default.
<h4 id="optional-point-to-non-default-credentials">(Optional) Point to Non-Default Credentials</h4>
If you selected a different name during the command<code>./build-key-server</code> earlier, modify the <code>cert </code>and lines <code>key</code> that you see to point to the appropriate and<code>.crt</code><code>.key</code> files. If you used the default,<code>server</code> this should already be set correctly:<code>/etc/openvpn/server.conf</code>
<pre class="wp-block-code"><code>cert server.crt
key server.key
</code></pre>
When you are finished, save and close the file.
<h2 id="step-8-adjust-the-server-networking-configuration">Step 8: Adjust the Server Networking Configuration</h2>
Next, we need to adjust some aspects of the server's networking so that OpenVPN can correctly route traffic.
<h3 id="allow-ip-forwarding">Allow IP Forwarding</h3>
First, we need to allow the server to forward traffic. This is fairly essential to the functionality we want our VPN server to provide.

We can adjust this setting by modifying the <code>/etc/sysctl.conf</code> file:
<pre class="wp-block-preformatted">sudo nano /etc/sysctl.conf
</pre>
Inside, look for the line that sets <code>net.ipv4.ip_forward</code> Remove the "<strong>#</strong>" character from the beginning of the line to uncomment that setting:/etc/sysctl.conf
<pre class="wp-block-code"><code>net.ipv4.ip_forward=1
</code></pre>
Save and close the file when you are finished.

To read the file and adjust the values for the current session, type:
<pre class="wp-block-preformatted">sudo sysctl -p</pre>
Save and close the file when you are finished.
<h3 id="adjust-the-ufw-rules-to-masquerade-client-connections">Adjust the IPTABLE Rules to Masquerade Client Connections</h3>
We need to modify the rules file to set up masquerading, an <code>iptables</code> concept that provides on-the-fly dynamic NAT to correctly route client connections.

Before we open the firewall configuration file to add masquerading, we need to find the public network interface of our machine. To do this, type:
<pre>ip route | grep default</pre>
Your public interface should follow the word "dev". For example, this result shows the interface named <code>eth0</code>, which is highlighted below:
<div class="secondary-code-label " title="Output">Output</div>
<pre class="code-pre "><code>default via 203.0.113.1 dev eth0  proto static  metric 600
</code></pre>
Next, you need to enable a forwarding rule for your iptables firewall so that traffic on your 10.8.0.0 network used (by default) on your VPN connection gets routed through from the tun0 interface to the eth0 interface. Allow traffic from OpenVPN client to eth0 (change to the interface you discovered!)
<pre>iptables -t nat -A POSTROUTING -s 10.8.0.0/24 -o eth0 -j MASQUERADE</pre>
Our server is now configured to correctly handle OpenVPN traffic.
<h2 id="step-9-start-and-enable-the-openvpn-service">Step 9: Start and Enable the OpenVPN Service</h2>
We're finally ready to start the OpenVPN service on our server. We can do this using systemd.

We need to start the OpenVPN server by specifying our configuration file name as an instance variable after the systemd unit file name. Our configuration file for our server is called <code>/etc/openvpn/server.conf</code>, so we will add <code>@server</code> to end of our unit file when calling it:
<pre class="wp-block-preformatted">sudo systemctl start openvpn@server


# For Ubuntu 14.04
sudo service openvpn start</pre>
Double-check that the service has started successfully by typing:
<pre class="wp-block-preformatted">sudo systemctl status openvpn@server


# For Ubuntu 14.04
sudo service openvpn status</pre>
If everything went well, your output should look something that looks like this:
<pre class="wp-block-code"><code> * VPN 'server' is running</code></pre>
You can also check that the OpenVPN <code>tun0</code> interface is available by typing:
<pre class="wp-block-preformatted">ip addr show tun0
</pre>
You should see a configured interface:
<pre class="wp-block-code"><code>Output4: tun0: &lt;POINTOPOINT,MULTICAST,NOARP,UP,LOWER_UP&gt; mtu 1500 qdisc noqueue state UNKNOWN group default qlen 100
    link/none 
    inet 10.8.0.1 peer 10.8.0.2/32 scope global tun0
       valid_lft forever preferred_lft forever
</code></pre>
If everything went well, enable the service so that it starts automatically at boot.
<h2 id="step-10-create-client-configuration-infrastructure">Step 10: Create Client Configuration Infrastructure</h2>
Next, we need to set up a system that will allow us to create client configuration files easily.
<h3 id="creating-the-client-config-directory-structure">Creating the Client Config Directory Structure</h3>
Create a directory structure within your home directory to store the files:
<pre class="wp-block-preformatted">mkdir -p ~/client-configs/files
</pre>
Since our client configuration files will have the client keys embedded, we should lock down permissions on our inner directory:
<pre class="wp-block-preformatted">chmod 700 ~/client-configs/files
</pre>
<h3 id="creating-a-base-configuration">Creating a Base Configuration</h3>
Next, let's copy an example client configuration into our directory to use as our base configuration:
<pre class="wp-block-preformatted">cp /usr/share/doc/openvpn/examples/sample-config-files/client.conf ~/client-configs/base.conf
</pre>
Open this new file in your text editor:
<pre class="wp-block-preformatted">nano ~/client-configs/base.conf
</pre>
Inside, we need to make a few adjustments.

First, locate the <code>remote</code> directive. This points the client to our OpenVPN server address. This should be the public IP address of your OpenVPN server. If you changed the port that the OpenVPN server is listening on, change <code>1194</code> to the port you selected:~/client-configs/base.conf
<pre class="wp-block-code"><code>. . .
# The hostname/IP and port of the server.
# You can have multiple remote entries
# to load balance between the servers.
remote server_IP_address 1194
. . .
</code></pre>
Be sure that the protocol matches the value you are using in the server configuration:~/client-configs/base.conf
<pre class="wp-block-code"><code>proto udp
</code></pre>
Next, uncomment the <code>user</code> and <code>group</code> directives by removing the "<strong>;</strong>":~/client-configs/base.conf
<pre class="wp-block-code"><code># Downgrade privileges after initialization (non-Windows only)
user nobody
group nogroup
</code></pre>
Find the directives that set the <code>ca</code>, <code>cert</code>, and <code>key</code>. Comment out these directives since we will be adding the certs and keys within the file itself:~/client-configs/base.conf
<pre class="wp-block-code"><code># SSL/TLS parms.
# See the server config file for more
# description.  It's best to use
# a separate .crt/.key file pair
# for each client.  A single ca
# file can be used for all clients.
#ca ca.crt
#cert client.crt
#key client.key
</code></pre>
Mirror the <code>cipher</code> and <code>auth</code> settings that we set in the <code>/etc/openvpn/server.conf</code> file:~/client-configs/base.conf
<pre class="wp-block-code"><code>cipher AES-128-CBC
auth SHA256
</code></pre>
Next, add the <code>key-direction</code> directive somewhere in the file. This <strong>must</strong> be set to "1" to work with the server:~/client-configs/base.conf
<pre class="wp-block-code"><code>key-direction 1
</code></pre>
Finally, add a few <strong>commented out</strong> lines. We want to include these with every config, but should only enable them for Linux clients that ship with a <code>/etc/openvpn/update-resolv-conf</code> file. This script uses the <code>resolvconf</code> utility to update DNS information for Linux clients.~/client-configs/base.conf
<pre class="wp-block-code"><code># script-security 2
# up /etc/openvpn/update-resolv-conf
# down /etc/openvpn/update-resolv-conf
</code></pre>
If your client is running Linux and has an <code>/etc/openvpn/update-resolv-conf</code> file, you should uncomment these lines from the generated OpenVPN client configuration file.

Save the file when you are finished.
<h3 id="creating-a-configuration-generation-script">Creating a Configuration Generation Script</h3>
Next, we will create a simple script to compile our base configuration with the relevant certificate, key, and encryption files. This will place the generated configuration in the <code>~/client-configs/files</code> directory.

Create and open a file called <code>make_config.sh</code> within the <code>~/client-configs</code> directory:
<pre class="wp-block-preformatted">nano ~/client-configs/make_config.sh
</pre>
Inside, paste the following script:~/client-configs/make_config.sh
<pre class="wp-block-code"><code>#!/bin/bash

# First argument: Client identifier

KEY_DIR=~/openvpn-ca/keys
OUTPUT_DIR=~/client-configs/files
BASE_CONFIG=~/client-configs/base.conf

cat ${BASE_CONFIG} \
    &lt;(echo -e '&lt;ca&gt;') \
    ${KEY_DIR}/ca.crt \
    &lt;(echo -e '&lt;/ca&gt;\n&lt;cert&gt;') \
    ${KEY_DIR}/${1}.crt \
    &lt;(echo -e '&lt;/cert&gt;\n&lt;key&gt;') \
    ${KEY_DIR}/${1}.key \
    &lt;(echo -e '&lt;/key&gt;\n&lt;tls-auth&gt;') \
    ${KEY_DIR}/ta.key \
    &lt;(echo -e '&lt;/tls-auth&gt;') \
    &gt; ${OUTPUT_DIR}/${1}.ovpn
</code></pre>
Save and close the file when you are finished.

Mark the file as executable by typing:
<pre class="wp-block-preformatted">chmod 700 ~/client-configs/make_config.sh
</pre>
<h2 id="step-11-generate-client-configurations">Step 11: Generate Client Configurations</h2>
Now, we can easily generate client configuration files.

If you followed along with the guide, you created a client certificate and key called <code>client1.crt</code> and <code>client1.key</code> respectively by running the <code>./build-key client1</code> command in step 6. We can generate a config for these credentials by moving into our <code>~/client-configs</code> directory and using the script we made:
<pre class="wp-block-preformatted">cd ~/client-configs
./make_config.sh client1
</pre>
If everything went well, we should have a <code>client1.ovpn</code> file in our <code>~/client-configs/files</code>directory:
<pre class="wp-block-preformatted">ls ~/client-configs/files
</pre>
<pre class="wp-block-code"><code>Outputclient1.ovpn
</code></pre>
<h3 id="transferring-configuration-to-client-devices">Transferring Configuration to Client Devices</h3>
We need to transfer the client configuration file to the relevant device. For instance, this could be your local computer or a mobile device.

While the exact applications used to accomplish this transfer will depend on your choice and device's operating system, you want the application to use SFTP (SSH file transfer protocol) or SCP (Secure Copy) on the backend. This will transport your client's VPN authentication files over an encrypted connection.
<h2 id="step-12-install-the-client-configuration">Step 12: Install the Client Configuration</h2>
Now, we'll discuss how to install a client VPN profile on Windows, OS X, iOS, and Android. None of these client instructions are dependent on one another, so feel free to skip to whichever is applicable to you.

The OpenVPN connection will be called whatever you named the <code>.ovpn</code> file. In our example, this means that the connection will be called <code>client1.ovpn</code> for the first client file we generated.
<h3 id="windows">Windows</h3>
<strong>Installing</strong>

The OpenVPN client application for Windows can be found on <a href="https://openvpn.net/index.php/open-source/downloads.html">OpenVPN's Downloads page</a>. Choose the appropriate installer version for your version of Windows.NoteOpenVPN needs administrative privileges to install.

After installing OpenVPN, copy the <code>.ovpn</code> file to:
<pre class="wp-block-code"><code>C:\Program Files\OpenVPN\config
</code></pre>
When you launch OpenVPN, it will automatically see the profile and makes it available.

OpenVPN must be run as an administrator each time it's used, even by administrative accounts. To do this without having to right-click and select <strong>Run as administrator</strong> every time you use the VPN, you can preset this, but this must be done from an administrative account. This also means that standard users will need to enter the administrator's password to use OpenVPN. On the other hand, standard users can't properly connect to the server unless the OpenVPN application on the client has admin rights, so the elevated privileges are necessary.

To set the OpenVPN application to always run as an administrator, right-click on its shortcut icon and go to <strong>Properties</strong>. At the bottom of the <strong>Compatibility</strong> tab, click the button to <strong>Change settings for all users</strong>. In the new window, check <strong>Run this program as an administrator</strong>.

<strong>Connecting</strong>

Each time you launch the OpenVPN GUI, Windows will ask if you want to allow the program to make changes to your computer. Click <strong>Yes</strong>. Launching the OpenVPN client application only puts the applet in the system tray so that the VPN can be connected and disconnected as needed; it does not actually make the VPN connection.

Once OpenVPN is started, initiate a connection by going into the system tray applet and right-clicking on the OpenVPN applet icon. This opens the context menu. Select <strong>client1</strong> at the top of the menu (that's our <code>client1.ovpn</code> profile) and choose <strong>Connect</strong>.

A status window will open showing the log output while the connection is established, and a message will show once the client is connected.

Disconnect from the VPN the same way: Go into the system tray applet, right-click the OpenVPN applet icon, select the client profile and click <strong>Disconnect</strong>.
<h3 id="os-x">OS X</h3>
<strong>Installing</strong>

<a href="https://tunnelblick.net/">Tunnelblick</a> is a free, open source OpenVPN client for Mac OS X. You can download the latest disk image from the <a href="https://tunnelblick.net/downloads.html">Tunnelblick Downloads page</a>. Double-click the downloaded <code>.dmg</code> file and follow the prompts to install.

Towards the end of the installation process, Tunnelblick will ask if you have any configuration files. It can be easier to answer <strong>No</strong> and let Tunnelblick finish. Open a Finder window and double-click <code>client1.ovpn</code>. Tunnelblick will install the client profile. Administrative privileges are required.

<strong>Connecting</strong>

Launch Tunnelblick by double-clicking Tunnelblick in the <strong>Applications</strong> folder. Once Tunnelblick has been launched, there will be a Tunnelblick icon in the menu bar at the top right of the screen for controlling connections. Click on the icon, and then the <strong>Connect</strong> menu item to initiate the VPN connection. Select the <strong>client1</strong> connection.
<h3 id="linux">Linux</h3>
<h4 id="installing">Installing</h4>
If you are using Linux, there are a variety of tools that you can use depending on your distribution. Your desktop environment or window manager might also include connection utilities.

The most universal way of connecting, however, is to just use the OpenVPN software.

On Ubuntu or Debian, you can install it just as you did on the server by typing:
<pre class="wp-block-preformatted">sudo apt-get update
sudo apt-get install openvpn
</pre>
On CentOS you can enable the EPEL repositories and then install it by typing:
<pre class="wp-block-preformatted">sudo yum install epel-release
sudo yum install openvpn
</pre>
<h4 id="configuring">Configuring</h4>
Check to see if your distribution includes a <code>/etc/openvpn/update-resolv-conf</code> script:
<pre class="wp-block-preformatted">ls /etc/openvpn
</pre>
<pre class="wp-block-code"><code>Outputupdate-resolve-conf
</code></pre>
Next, edit the OpenVPN client configuration file you transfered:
<pre class="wp-block-preformatted">nano client1.ovpn
</pre>
Uncomment the three lines we placed in to adjust the DNS settings if you were able to find an <code>update-resolv-conf</code> file:client1.ovpn
<pre class="wp-block-code"><code>script-security 2
up /etc/openvpn/update-resolv-conf
down /etc/openvpn/update-resolv-conf
</code></pre>
If you are using CentOS, change the <code>group</code> from <code>nogroup</code> to <code>nobody</code> to match the distribution's available groups:client1.ovpn
<pre class="wp-block-code"><code>group nobody
</code></pre>
Save and close the file.

Now, you can connect to the VPN by just pointing the <code>openvpn</code> command to the client configuration file:
<pre class="wp-block-preformatted">sudo openvpn --config client1.ovpn
</pre>
This should connect you to your server.
<h2 id="step-13-test-your-vpn-connection">Step 13: Test Your VPN Connection</h2>
Once everything is installed, a simple check confirms everything is working properly. Without having a VPN connection enabled, open a browser and go to <a href="https://www.dnsleaktest.com/">DNSLeakTest</a>.

The site will return the IP address assigned by your internet service provider and as you appear to the rest of the world. To check your DNS settings through the same website, click on <strong>Extended Test</strong> and it will tell you which DNS servers you are using.

Now connect the OpenVPN client to your Droplet's VPN and refresh the browser. The completely different IP address of your VPN server should now appear. That is now how you appear to the world. Again, <a href="https://www.dnsleaktest.com/">DNSLeakTest's</a> <strong>Extended Test</strong> will check your DNS settings and confirm you are now using the DNS resolvers pushed by your VPN.
<h2 id="step-14-revoking-client-certificates">Step 14: Revoking Client Certificates</h2>
Occasionally, you may need to revoke a client certificate to prevent further access to the OpenVPN server.

To do so, enter your CA directory and re-source the <code>vars</code> file:
<pre class="wp-block-preformatted">cd ~/openvpn-ca
source vars
</pre>
Next, call the <code>revoke-full</code> command using the client name that you wish to revoke:
<pre class="wp-block-preformatted">./revoke-full client3
</pre>
This will show some output, ending in <code>error 23</code>. This is normal and the process should have successfully generated the necessary revocation information, which is stored in a file called <code>crl.pem</code> within the <code>keys</code>subdirectory.

Transfer this file to the <code>/etc/openvpn</code> configuration directory:
<pre class="wp-block-preformatted">sudo cp ~/openvpn-ca/keys/crl.pem /etc/openvpn
</pre>
Next, open the OpenVPN server configuration file:
<pre class="wp-block-preformatted">sudo nano /etc/openvpn/server.conf
</pre>
At the bottom of the file, add the <code>crl-verify</code> option, so that the OpenVPN server checks the certificate revocation list that we've created each time a connection attempt is made:/etc/openvpn/server.conf
<pre class="wp-block-code"><code>crl-verify crl.pem
</code></pre>
Save and close the file.

Finally, restart OpenVPN to implement the certificate revocation:
<pre class="wp-block-preformatted">sudo systemctl restart openvpn@server
</pre>
The client should now longer be able to successfully connect to the server using the old credential.

To revoke additional clients, follow this process:
<ol>
 	<li>Generate a new certificate revocation list by sourcing the <code>vars</code> file in the <code>~/openvpn-ca</code> directory and then calling the <code>revoke-full</code> script on the client name.</li>
 	<li>Copy the new certificate revocation list to the <code>/etc/openvpn</code> directory to overwrite the old list.</li>
 	<li>Restart the OpenVPN service.</li>
</ol>
This process can be used to revoke any certificates that you've previously issued for your server.
<h2 id="conclusion">Conclusion</h2>
Congratulations! You are now securely traversing the internet protecting your identity, location, and traffic from snoopers and censors.

To configure more clients, you only need to follow steps <strong>6</strong>, and <strong>11-13</strong> for each additional device. To revoke access to clients, follow step <strong>14</strong>.