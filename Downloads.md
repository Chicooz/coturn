## Latest official recommended version - 4.4.4.2: ##

  * SCTP fixes: [Issue 10](https://code.google.com/p/coturn/issues/detail?id=10) .

[4.4.4.2 Version Downloads (full source and platform builds)](http://turnserver.open-sys.org/downloads/v4.4.4.2/)

[Main Downloads Page (with all recent versions)](http://turnserver.open-sys.org/downloads/)

[Backup Downloads Page](http://coturn.net/turnserver/)

[Extra Docs ](http://turnserver.open-sys.org/downloads/extradocs/)

## Older versions ##

### 4.4.4.1: ###

  * 'native' SCTP support (experimental);
  * option of encrypted stored passwords for web admin users;
  * option of encrypted stored password for CLI user;


[v4.4.4.1](http://turnserver.open-sys.org/downloads/v4.4.4.1/)

### 4.4.2.3: ###

  * bandwidth control fixed;
  * STUN/TURN control traffic counted separately from data traffic, for the sake of the bandwidth control;
  * higher bandwidth limit capacity on 64 bits systems;
  * redis operations with the realm options fixed;


[v4.4.2.3](http://turnserver.open-sys.org/downloads/v4.4.2.3/)

### 4.4.2.2: ###

  * admin\_user SQLite table schema fixed;
  * REST API docs fixed;
  * Amazon AWS uses syslog;


[v4.4.2.2](http://turnserver.open-sys.org/downloads/v4.4.2.2/)

### 4.4.2.1: ###

  * (HMAC-)SHA-512 and -384 algorithms added;
  * TOS (DiffServer) and TTL IP header field handling fixed ([Issue 9](https://code.google.com/p/coturn/issues/detail?id=9) fixed);
  * updates according to the new third-party-auth draft (oauth);
  * peer logging added.


[v4.4.2.1](http://turnserver.open-sys.org/downloads/v4.4.2.1/)

### 4.4.1.2: ###

  * TURN-bis draft experimental implementation;
  * TRANSPORT attribute handling fixed;
  * hostname-to-IP-address resolution fix;
  * updates for Solaris (name resolution libraries);

[v4.4.1.2](http://turnserver.open-sys.org/downloads/v4.4.1.2/)

### 4.4.1.1: ###

  * HTTPS web admin server;
  * SSLv2 support cancelled (security concern fixed);
  * The server-side short-term credentials mechanism support cancelled;
  * OpenSSL 1.1.0 supported;
  * Shared secrets fixed in MongoDB: multiple secrets per realm allowed;
  * Shared secrets admin fixed in Redis;

[v4.4.1.1](http://turnserver.open-sys.org/downloads/v4.4.1.1/)

### 4.3.3.1: ###

  * multiple authentication threads;
  * database code cleaned;

[v4.3.3.1](http://turnserver.open-sys.org/downloads/v4.3.3.1/)

### 4.3.2.2: ###

  * Redis read message queue bug fixed;
  * STUN/TURN ALPN supported (when compiled with OpenSSL 1.0.2+ );
  * DTLS v1.2 supported (when compiled with OpenSSL 1.0.2+ );
  * Auto optimal ECDH parameters (when compiled with OpenSSL 1.0.2+ );
  * TLS/DTLS code cleaning.

[v4.3.2.2](http://turnserver.open-sys.org/downloads/v4.3.2.2/)

### 4.3.1.3: ###

  * Reliability fixes ([Issue 141](https://code.google.com/p/coturn/issues/detail?id=141) from rfc5766-turn-server).
  * HTTP/HTTPS echo fixed.
  * External address mapping fixes for Amazon EC2.
  * Minor docs improvements.

[v4.3.1.3](http://turnserver.open-sys.org/downloads/v4.3.1.3/)

### 4.3.1.2: ###

  * SQLite supported as the default user database.
  * Support of the flat-file user database removed (no longer supported).
  * TLS connection procedure improved in uclient test program.
  * CentOS 6.5 binary build in the downloads section upgraded to CentOS 6.6.
  * We do not provide 32-bits binary download images since this release. We do support the 32-bits platforms, but you either have to compile it yourself, or you have to use the Coturn package that is packed with your OS (if available).

[v4.3.1.2](http://turnserver.open-sys.org/downloads/v4.3.1.2/)

### 4.2.3.1: ###

  * Request re-transmission implemented in uclient test program.
  * TLS connection procedure improved in uclient test program.

[v4.2.3.1](http://turnserver.open-sys.org/downloads/v4.2.3.1/)

### 4.2.2.2: ###

  * Black- and white- IP lists are divided per realm (the DB schema for those two tables changed);
  * Updated Redis database schema.
  * Debian UFW file added ([Issue 1](https://code.google.com/p/coturn/issues/detail?id=1) fixed).
  * TCP/TLS tests extended.
  * Relay RTCP sockets ports allocation fixed.
  * List of libraries cleaned.
  * SSL renegotiation callback fixed.

[v4.2.2.2](http://turnserver.open-sys.org/downloads/v4.2.2.2/)


### 4.2.1.2: ###

  * oAuth security experimental implementation;
  * The "TLS renegotiation" DoS attack prevention implemented;
  * FQDN as relay-ip and listener-ip parameters ([issue 6](https://code.google.com/p/coturn/issues/detail?id=6)) (patch provided by Iñaki Baz Castillo);
  * redis user key operation fixed.
  * redis, mysql and psql db operations fixed.
  * SHA-256 memory leak fixed.
  * Mobility ticket retransmission fixed.
  * Move debian package from SVN to GIT.
  * Move secondary download area to coturn.net.
  * Quota allocation fixed.
  * Core dump fixed.
  * Bandwidth allocation fixed.
  * Memory code cleaning.
  * Logging fixed.

[v4.2.1.2](http://turnserver.open-sys.org/downloads/v4.2.1.2/)

### 4.1.2.1: ###

  * The origin attribute is verified in the subsequent session messages (server flag --check-origin-consistency).
  * MySQL SSL connection support.
  * Crash fixed when the DB connection string is incorrect.
  * Minor docs fixes.

[v4.1.2.1](http://turnserver.open-sys.org/downloads/v4.1.2.1/)

### 4.1.1.1: ###

  * Forceful server-side session cancellation implemented (in telnet console).

[v4.1.1.1](http://turnserver.open-sys.org/downloads/v4.1.1.1/)

### 4.1.0.2: ###

  * SSODA (double allocation) draft support added.
  * DB "driver" abstraction and MongoDB support (by Federico Pinna).
  * multiple origins supported per request.
  * "allocation mismatch" condition fixed (merged from rfc5766-turn-server).
  * STUN BINDING response fixed in the case of -X (external address) option.
  * "pu" CLI command fixed.
  * session cleaning fixed in TCP relay functionality (RFC 6062).
  * some crash conditions fixed.
  * working on compilation warnings.

[v4.1.0.2](http://turnserver.open-sys.org/downloads/v4.1.0.2/)

### 4.0.1.3: ###

  * Redis DB connection status fixed ([Issue 129](https://code.google.com/p/coturn/issues/detail?id=129)).
  * SIGHUP for logfile reset implemented (Gustavo Garcia suggestion).
  * log reset command in CLI added.
  * Some error code corrections:
    1. "Mobility forbidden" error changed, to value 405.
    1. "Wrong credentials" situation is now treated as error 441.

[v4.0.1.3](http://turnserver.open-sys.org/downloads/v4.0.1.3/)

### 4.0.1.2: ###

  * Bandwidth draft implemented.
  * Issues 126, 127 and 128 fixes merged from rfc5766-turn-server.
  * Amazon EC2 image fixed (test db).

[v4.0.1.2](http://turnserver.open-sys.org/downloads/v4.0.1.2/)

### 4.0.0.2: ###

  * Channel management callbacks for the custom libraries.
  * Separate header file for the new spaces data.

[v4.0.0.2](http://turnserver.open-sys.org/downloads/v4.0.0.2/)

### 4.0.0.0: ###

  * Multi-tenant TURN server.

[v4.0.0.0](http://turnserver.open-sys.org/downloads/v4.0.0.0/)