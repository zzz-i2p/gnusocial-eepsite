Configuring a GNU Social instance as a Hidden Service on Tor and i2p
====================================================================

*WARNING: This is not ready yet.*

This tutorial is a step-by-step walkthrough of how I set up a GNU Social
as an eepSite, using Tor to obfuscate it's physical location to other members of
the federation, and also making it available as a hidden service. This
configuration is pretty complicated, and unlike the others, it runs dynamic code
on the server and allows user interaction. I've tried to reduce the possibility
of failure to the maximum extent I am currently able, however, I can't be sure
that it's impossible to bypass the hidden service/HTTP proxy configuration
as it stands. Turns out, it's kind of hard to keep track of all the ways the
swiss-army-chainsaw (PHP) has to connect to the internet. Some ways of setting
an HTTP proxy work for some things, some ways of setting an HTTP proxy work for
other things, and sometimes they work for both things but setting it both ways
breaks the things. But this way seems to be working right.

Background/Goals
----------------

For the purposes of this tutorial, we're trying to set up an otherwise normal,
federated GNU Social node that participates fully in the network, but only by
way of anonymous overlay networks from the very start. In order to do this, it's
possible to set GNU Social configuration settings proxy\_host, proxy\_port to
direct traffic to an HTTP proxy server, but since i2p's http proxy can only be
used to access other eepsites, we'll have to use Privoxy to route requests for
i2p resources to i2p, and clearnet resources to Tor. But since I also want to
run MariaDB in a separate container, Privoxy will need an exception for the
MariaDB container. Since we're running a service, we're only really concerned
with obfuscating our physical location, not making ourselves unidentifiable to
other GNU Social nodes. Unfortunately, for now, it's impossible to get an EV
certificate for a .onion-only domain from Let's Encrypt, so it's not possible
to identify yourself to the other GNU Social instances. That's limiting to us,
as we will only be able to communicate with nodes that allow HTTP connections,
which is bad on the clearnet. It's not ideal on darknets either, but it's less
bad.

Preparation
-----------

Far and away the easiest part of this to figure out is the actual hidden service
configuration

### i2p

### Tor

### privoxy

Patching the GNU Social Defaults
--------------------------------

Setting up MariaDB
------------------

Initial GNU Social Configuration
--------------------------------

