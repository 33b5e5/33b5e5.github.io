---
layout: default
title: Secure DNS
date: 2017-07-17 12:27:21 -0700
---

I've been routing all the DNS queries from our house through Google's secure "DNS over HTTPS" service for the past 3 months. It has worked extremely well. As Google explains: "Traditional DNS queries and responses are sent over UDP or TCP without encryption. This is vulnerable to eavesdropping and spoofing. DNS-over-HTTPS greatly enhances privacy and security between a client and a recursive resolver, and complements DNSSEC to provide end-to-end authenticated DNS lookups." It feels good to secure DNS and keep all that sensitive data from the ISP (and from anyone else with a privileged position on the network).

There are a variety of DNS clients that have been created to interface with Google's DNS over HTTPS API; I chose one written in Go called <a href="https://github.com/pforemski/dingo" target="_blank">Dingo</a>.

For enhanced performance, I wanted to also use <a href="http://www.thekelleys.org.uk/dnsmasq/doc.html" target="_blank">dnsmasq</a>. I hoped to place it in front of Dingo, since dnsmasq provides a local cache, preventing unnecessary lookups to Google for common records.

However, instead of using vanilla dnsmasq, I decided to use the popular <a href="https://pi-hole.net/" target="_blank">Pi-hole</a> software. Pi-hole provides some additional features on top of dnsmasq, such as network-level ad-blocking, easy whitelist/blacklist capability, and a nice dashboard.

Following are some rough notes on how I set everything up. You can replicate this setup on any old Linux box on your local network. A Raspberry Pi is sufficient.

1. Install <a href="https://github.com/pi-hole/pi-hole#one-step-automated-install" target="_blank">Pi-hole</a>

2. Install <a href="https://github.com/pforemski/dingo#quick-start" target="_blank">Dingo</a>

3. Temporarily run Dingo as follows:
   <pre><code class="bash">sudo ./dingo-linux-amd64 -gdns:auto</code></pre>

4. You'll probably want to setup Dingo to start at boot. I launch it inside tmux, via a single line in ```/etc/rc.local``` like:
   <pre><code class="bash">tmux new-session -d -s dingo '/root/dingo-linux-amd64 -gdns:auto'</code></pre>

   Then you can easily "attach" to the tmux window to see the output at any time:
   <pre><code class="bash">sudo tmux attach -t dingo</code></pre>

5. I also added a pane to that same tmux window to show the pi-hole log. This works great in the same window since it's already running as root. Although we haven't configured Pi-hole yet, let's go ahead and add the log pane anyway, with something like:
   <pre><code class="bash">tail -f /var/log/pihole.log</code></pre>

   Now we can easily see Pi-hole and Dingo output in real-time.

6. Configure Pi-hole to query Dingo instead of your upstream DNS servers. Edit ```/etc/dnsmasq.d/01-pihole.conf``` to add the first line and comment out the other two, like:

   <pre><code class="bash">server=127.0.0.1#32000
   #server=8.8.8.8
   #server=8.8.4.4</code></pre>

   (Note: port 32000 is the default port for Dingo.)

   That should be sufficient.
   
7. Now you just need to configure the machines on your LAN to use the IP address of this Dingo/Pi-hole box for DNS. I chose to do this for all hosts at once by modifying the DHCP config on my router. I just hand out the IP of this Dingo/Pi-hole box as the primary DNS server, leaving my router/gateway IP as the secondary, just in case the Dingo/Pi-hole box goes offline.

<hr>

*Here's an optional modification to have the Pi-hole blackhole bad hosts to 0.0.0.0 instead of its default LAN IP. 0.0.0.0 is quicker and more reliable. Using the LAN IP was causing clients to have slow load times at news.google.com and other random sites.*

<pre><code class="bash">sudo vi /opt/pihole/gravity.sh</code></pre>

Look for the function "gravity_hostFormat()" (currently at line 302), and modify it like:

<pre><code class="bash"># Only IPv4
# First line is the original, second line is modified to use 0.0.0.0 for blocking.
#cat ${piholeDir}/${eventHorizon} | awk -v ipv4addr="$IPV4_ADDRESS" '{sub(/\r$/,""); print ipv4addr" "$0}' >> ${piholeDir}/${accretionDisc}
cat ${piholeDir}/${eventHorizon} | awk -v ipv4addr="0.0.0.0" '{sub(/\r$/,""); print ipv4addr" "$0}' >> ${piholeDir}/${accretionDisc}</code></pre>

<hr>

Unfortunately, edits to ```/etc/dnsmasq.d/01-pihole.conf``` and ```/opt/pihole/gravity.sh``` will be lost when the Pi-hole software is updated, so you'll want to avoid manually updating Pi-hole, or keep these notes handy.
