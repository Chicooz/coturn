## turnutils\_uclient application (for test purposes only) ##
> It was designed to simulate multiple clients. It uses asynch IO API in  libevent to  handle  multiple  clients. A  client connects to the relay, negotiates the session, and sends	multiple  (configured  number) messages	to  the	 server	 (relay), expecting the	same number of replies. The length of the messages is configurable.   The  message is an arbitrary octet stream, but it	can be configured as a string.  The number of the messages to send is configurable.

When an unreliable protocol is used for the client-TURN-server communications (UDP or DTLS), then the uclient program performs a retransmission actions, if necessary.

### Usage: ###
```
       $ turnutils_uclient [-tSvsyhcxg] [options]	<TURN-Server-IP-address>
```
### Flags: ###

  * **-t**     Use TCP for communications between client and TURN server (default is UDP).
  * **-b**     Use SCTP for communications between client and TURN server (default is UDP).
  * **-T**     Use TCP for the relay transport (default - UDP). Implies options -t, -y, -c, and ignores options -s, -e, -r and -g.
  * **-P**     Passive TCP (RFC6062 with active peer). Implies -T.
  * **-S**     Secure connection: TLS for TCP, DTLS for UDP.
  * **-U**     Secure unencrypted connection (suite eNULL): SSL/TLS for TCP, DTLS for UDP.
  * **-v**     Verbose.
  * **-s**     Use "Send" method	in TURN; by default, it	uses TURN Channels.
  * **-y**     Use  client-to-client  connections: RTP/RTCP pair	of channels to another RTP/RTCP pair of channels.  with this  option  the  _turnutils\_peer_ application  is  not used, as the allocated relay endpoints	are talking to each other.
  * **-h**     Hang on indefinitely after the last sent packet.
  * **-c**     Do not create rtcp connections.
  * **-x**     Request IPv6 relay address (RFC6156).
  * **-X**     Explicitly request IPv4 relay address.
  * **-g**     Set DONT\_FRAGMENT	parameter in TURN requests.
  * **-D**     Mandatory channels padding (like in **pjnath**).
  * **-N**     Negative tests (some limited cases only).
  * **-R**     Negative protocol tests.
  * **-O**     "DOS attack" intense mode.
  * **-M**	Use TURN ICE Mobility.
  * **-I**	Do not set permissions on TURN relay endpoints (for testing the non-standard server relay functionality).
  * **-G**    Generate extra requests (create permissions, channel bind).
  * **-Z**    Dual allocation (as in TURN-bis). Automatically turns on option -c.

### Options: ###

  * **-l** `<number>`     Message length (Default: 100 Bytes).
  * **-i** `<file-name>`     Certificate file (for secure connections only, optional).
  * **-k** `<file-name>`     Private key file (for secure connections only).
  * **-E** `<CA-file-name>`   CA file for server certificate verification, if the server certificate to be verified.
  * **-p** `<port>`     **TURN** **Server** port (Default: 3478 unsecure,	5349 secure).
  * **-n** `<number>`     Number of	messages to send (Default: 5).
  * **-d** `<device-name>`     Local interface device (optional, Linux only).
  * **-L** `<ip>`     Local IP address (optional).
  * **-m** `<number>`     Number of	clients	(default is 1, 2 or 4,	depending  on  options below).
  * **-e** `<ip>`     Peer address.
  * **-r** `<port>`     Peer port	(default 3480).
  * **-z** `<number>`     Per-session packet interval in milliseconds (default is 20 ms).
  * **-u** `<user>`     STUN/TURN username.
  * **-w** `<password>`     STUN/TURN user password.
  * **-W** `<secret>`     TURN REST API secret. Is not compatible with -A flag.
  * **-C** `<symbol>`     This is the timestamp/username separator symbol (character) in TURN REST API. The default value is ':'.
  * **-F** `<cipher-suite>`   Cipher suite for TLS/DTLS. Default value is DEFAULT.
  * **-o** `<origin>` the ORIGIN STUN attribute value.
  * **-a** `<bytes-per-second>` Bandwidth for the bandwidth request in ALLOCATE. The default value is zero.

> See the _turnutils\_uclient_ usage examples	in the "examples/scripts" directory.