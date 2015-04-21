## turnutils\_peer  application (for test purposes ony) ##
turnutils\_peer is a simple UDP-only echo backend server. This application is used for the test purposes only, as a '_peer_'  for the _turnutils\_uclient_ application.

### Usage: ###
```
       $ turnutils_peer [-v] [options]
```
### Options: ###

  * **-p** `<port>`     Listening	UDP port (Default: 3480).
  * **-d** `<device-name>`     Listening	interface device (optional, Linux only)
  * **-L** `<ip>`     Listening	address	of _turnutils\_peer_	server. Multiple listening addresses can be used, IPv4 and IPv6. If no listener address(es) defined, then it listens on all IPv4 and IPv6 addresses.
  * **-v**     Verbose