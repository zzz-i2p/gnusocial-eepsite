Configuring a GNU Social instance as a Hidden Service on Tor and i2p
====================================================================

*WARNING: This is not ready yet.*

This tutorial is a step-by-step walkthrough of how I set up a GNU Social
as an eepSite, a Tor Hidden Service, and allow it to communicate with i2p, Tor,
and Clearnet GNU Social instances.

Background/Goals
----------------

For the purposes of this tutorial, we're trying to set up an otherwise normal,
federated GNU Social node that participates fully in the network, but making
itself available as an eepSite and a hidden service by default. In this guide,
the service you host **will not be anonymous**, it's purpose is to provide
anonymity to users. In order to be hidden-only but also be a secure participant
in the Fediverse, it would be necessary to get a certificate for an onion-only
domain which isn't possible yet <sup>1</sup>.

Preparation
-----------

Far and away the easiest part of this to figure out is the actual hidden service
configuration.

### Docker

It's probably a good idea for you to use a network intended only for your GNU
Social application and it's dependencies, so before doing anything else, create
your docker network:

```
docker network create --subnet 172.82.82.0/24 gnusocial
```

### i2p

The i2p tunnel configuration for the GNU Social web service is one of the
simplest we've had so far. It's just a plain-old http service with 3 hops.
Nothing terribly special to see here.

```
[GITHUB]
type = http
host = gnusocial-eepsite
port = 80
inbound.length = 3
outbound.length = 3
keys = gnusocial.dat
```

However, because we'll need to make contact with other eepSite-based GNU Social
instances, the i2pd.conf will need to have the http proxy re-activated thusly:

```
[httpproxy]
enabled = true
address = 0.0.0.0
port = 4444
```

Obviously that's listening on all interfaces. Since we're using Docker to
isolate the network and privoxy to route requests, this should be fine.

### Tor

With the Tor configuration for this system, we'll also need to create both a
hidden service *and* a SocksPort(Which, of course, we'll also be routing with
privoxy. We'll also be deactivating the ControlPort so it can't be used, even
by accident. This makes for a very simple torrc.

The hidden service configuration looks like this:

```
HiddenServiceDir /var/lib/tor/gnusocial-onion/
HiddenServicePort 80 172.82.82.5:80
```

And the SocksPort configuration looks like this:

```
SOCKSPort 172.82.82.3:9150
SOCKSPolicy accept 172.0.0.0/8
DataDirectory /var/lib/tor
```

### privoxy

```
user-manual /usr/share/doc/privoxy/user-manual/
confdir /etc/privoxy
logdir /var/log/privoxy
filterfile default.filter
logfile privoxy.log
listen-address  0.0.0.0:8118
toggle  1
enable-remote-toggle  0
enable-remote-http-toggle  0
enable-edit-actions 0
enforce-blocks 0
buffer-limit 4096
enable-proxy-authentication-forwarding 0
forwarded-connect-retries  0
accept-intercepted-requests 0
allow-cgi-request-crunching 0
split-large-forms 0
keep-alive-timeout 5
tolerate-pipelining 1
socket-timeout 300
forward-socks5t / 172.82.82.3:9150 .
forward .i2p 172.82.82.2:4444
forward 172.82.82.4 172.82.82.4
```

### Dynamic DNS



```
```

### Let's Encrypt



```
```

Patching the GNU Social Defaults
--------------------------------

Here's where we make sure that the proxy configuration is pre-set by default to
make sure that they are used even when configuring the instance.


Setting up MariaDB
------------------

Initial GNU Social Configuration
--------------------------------


Footnote:
---------

<sup>1</sup> Originally, I had intended for this to be hidden-only. In order to
do this, it's possible to set GNU Social configuration settings proxy\_host,
proxy\_port to direct traffic to an HTTP proxy server, but since i2p's http
proxy can only be used to access other eepsites, we'll have to use Privoxy to
route requests for i2p resources to i2p, and clearnet resources to Tor. But
since I also want to run MariaDB in a separate container, Privoxy will need an
exception for the MariaDB container. Since we're running a service, we're only
really concerned with obfuscating our physical location, not making ourselves
unidentifiable to other GNU Social nodes. Unfortunately, for now, it's
impossible to get an EV certificate for a .onion-only domain from Let's Encrypt,
so it's not possible to identify yourself to the other GNU Social instances in
that way, or communicate in an encrypted way with the rest of the Fediverse.
That's limiting to us, as we will only be able to communicate with nodes that
allow HTTP connections, which is bad on the clearnet. It's not ideal on darknets
either, but it's less bad.
