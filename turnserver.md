## turnserver application: a TURN relay server implementation ##

### Usage: ###
```
       $ turnserver [flags] [-n | -c <config-file>] [ --db=<sqlite-db-file> | --userdb=<sqlite-db-file> | --psql-userdb=<db-conn-string> | --mysql-userdb=<db-conn-string> | --mongo-userdb=<db-conn-string>  | --redis-userdb=<db-conn-string> ] [options]
       $ turnserver -h
```

### Flags: ###

  * **-v,** **--verbose** 'Moderate' verbose mode.
  * **-V,** **--Verbose** 'Extra' verbose mode, very annoying and not recommended.
  * **-o,** **--daemon** Run server process as daemon.
  * **-f,** **--fingerprint** Use fingerprints in the TURN messages. If an incoming request contains fingerprint, then TURN server will always add fingerprints to the messages in this session, regardless of the per-server setting.
  * **-a,** **--lt-cred-mech** Use long-term credentials mechanism (this one you need for WebRTC usage).
  * **-z,** **--no-auth** Do not use any credentials mechanism, allow anonymous access. Opposite to **-a** and **-A** options.
  * **--use-auth-secret** TURN REST API flag. Flag that sets a special WebRTC authorization option that is based upon authentication secret. This feature purpose is to support "TURN Server REST API" as described in TURNServerRESTAPI.pdf in the docs. This option is used with long-term credentials mechanism.
  * **--oauth** Support oAuth authentication, as in the third-party TURN specs document: http://tools.ietf.org/html/draft-ietf-tram-turn-third-party-authz-11. The oAuth keys must be stored in the database, and handled by an external program. The TURN server expects the keys to be present in the database, and the TURN server does not handle the keys by itself. In the specs document, section 4.1, several key-management schemes are proposed and to be followed by the external key-management program.
  * **--dh566**	Use 566 bits DH TLS key. Default size of the key is 1066.
  * **--dh2066**	Use 2066 bits DH TLS key. Default size of the key is 1066.
  * **--no-sslv3**  Do not allow SSLv3 protocol.
  * **--no-tlsv1**  Do not allow TLSv1 protocol.
  * **--no-tlsv1\_1**  Do not allow TLSv1.1 protocol.
  * **--no-tlsv1\_2**  Do not allow TLSv1.2 protocol.
  * **--no-udp** Do not start 'plain' UDP listeners.
  * **--no-tcp** Do not start 'plain'TCP listeners.
  * **--no-tls** Do not start TLS listeners.
  * **--no-dtls** Do not start DTLS	listeners. This is recommended when you do not need DTLS. With this option, the plain UDP works faster.
  * **--no-udp-relay** Do not allow UDP relay endpoints defined in RFC 5766, use only TCP relay endpoints as defined in RFC 6062.
  * **--no-tcp-relay** Do not allow TCP relay endpoints defined in RFC 6062, use only UDP relay endpoints as defined in RFC 5766.
  * **--stale-nonce** Use extra security with nonce value having limited lifetime (600 secs).
  * **--no-stdout-log** Flag to prevent stdout log messages. By default, all log messages are going to both stdout and to the configured log. With this option everything will be going to the configured log file only (unless the log file itself is stdout).
  * **--syslog** Flag to redirect everything into the system log (syslog).
  * **--simple-log** This flag means that no log file rollover will be used, and the log file name will be constructed as-is, without PID and date appendage. This option can be used, for example, together with logrotate tool.
  * **--no-loopback-peers** Disallow peers on the loopback addresses (127.x.x.x and ::1).
  * **--no-multicast-peers** Disallow peers on well-known broadcast addresses (224.0.0.0 and above, and `FFXX:*`).
  * **--udp-self-balance** (_recommended for older Linuxes only_) balance the UDP traffic among the aux endpoints (for clients supporting 300 ALTERNATE-SERVER response). See **--aux-server** option.
  * **--secure-stun**	Require authentication of the STUN Binding request. By default, the clients are allowed anonymous access to the STUN Binding functionality.
  * **-S,** **--stun-only** Run as STUN server only, all TURN requests will be ignored. Option to suppress TURN functionality, only STUN requests will be processed.
  * **--no-stun**	Run as TURN server only, all STUN requests will be ignored. Option to suppress STUN functionality, only TURN requests will be processed.
  * **--mobility**    Mobility with ICE (MICE) specs support.
  * **--no-cli**      Turn OFF the CLI support. By default it is always ON, and the process turnserver accepts the telnet client connections on IP address 127.0.0.1, port 5766. See also options **--cli-ip**, **--cli-port** and **--cli-password**.
  * **--server-relay**   Server relay. **NON-STANDARD AND DANGEROUS OPTION**. Only for an application that want to run a server on the relay endpoints. This option eliminates the IP permissions check on the packets incoming to the relay endpoints. That makes the system vulnerable to DOS attack, for example, among other bad things. The rule is: if you do not understand what is this option about and why would you need it, **then you absolutely must NOT use it under any circumstances**. See http://tools.ietf.org/search/rfc5766#section-17.2.3 .
  * **--check-origin-consistency**	The flag that sets the origin consistency check: across the session, all requests must have the same main ORIGIN attribute value (if the ORIGIN was initially used by the session).
  * **-h** Help.

### Config file setting: ###

  * **-n** Do not use configuration file, use only command line parameters.
  * **-c** `<file-name>`     Configuration file name (default - turnserver.conf).  The	format of  config  file can be seen in the supplied examples/etc/turnserver.conf example file. Long names of the options are used as the configuration  items names in the file. If not an absolute path is supplied, then the file is searched in the following	directories:
    * current directory
    * current directory etc/	subdirectory
    * upper directory level etc/
    * /etc/
    * /usr/local/etc/

### User database settings: ###

  * **-b,** **--db**, **--userdb** `<sqlite-file-name>`
> > SQLite database file name. If no database option is defined, then an SQLite database is implied by default, with default location /usr/local/var/db/turndb, or /var/db/turndb, or /var/lib/turn/turndb, depending on the platform.
  * **-e,** **--psql-userdb,** **--sql-userdb** `<connection-string>`
> > User database connection string for PostgreSQL.

> The connection string format is like that:
```
"host=<host> dbname=<dbname> user=<db-user> password=<db-user-password> connect_timeout=<seconds>"
```
(for 8.x or newer Postgres), or:
```
"postgresql://username:password@hostname:port/databasename"
```
> (for 9.x or newer Postgres).
> See the INSTALL file for more explanations and examples.

> Also, see http://www.PostgreSQL.org for full PostgreSQL documentation.
    * **-M,** **--mysql-userdb** `<connection-string>`
> > > User database connection string for MySQL.

> The (proprietary) connection string format is like that:
```
"host=<host> dbname=<dbname> user=<db-user> password=<db-user-password> connect_timeout=<seconds>".
```
> See the INSTALL file for more explanations and examples.

> Also, see http://www.mysql.org for full MySQL documentation.

> Optional connection string parameters for the secure
> communications (SSL): **ca**, **capath**, **cert**, **key**, **cipher**
> (see http://dev.mysql.com/doc/refman/5.1/en/ssl-options.html
> for the command options description).

  * **-N,** **--redis-userdb** `<connection-string>`
> > User database connection string for Redis.

> The (proprietary) connection string format is like that:
```
"ip=<ip-addr> dbname=<number> password=<db-password> connect_timeout=<seconds>".
```
> See the INSTALL file for more explanations and examples.

> Also, see http://redis.io for full Redis documentation.

  * **-J,** **--mongo-userdb** `<connection-string>`
> > User database connection string for MongoDB.

> The connection string format is like that:
```
"mongodb://username:password@host:port/database?options".
```
> See the INSTALL file for more explanations and examples.

> Also, see http://docs.mongodb.org/manual/
> for full MongoDB documentation.

### Options: ###

  * **-d,** **--listening-device** `<device-name>` **_NOT RECOMMENDED, USE IT ONLY AS THE LAST RESORT OPTIMIZATION & SECURITY OPTION, FOR LINUX SYSTEMS ONLY._** Listener interface device. The turnserver process must have root privileges to bind the	listening endpoint to a device. If turnserver must run as a	process without root privileges, then just do not use this setting.
  * **-L,** **--listening-ip** `<ip>` Listener IP address of relay server.  Multiple listeners can  be specified.  If no **IP**(s) specified, then all IPv4 and IPv6 system IPs will be used for listening.
  * **-p,** **--listening-port** `<port>` TURN listener port for UDP and TCP listeners (Default: 3478). Note: actually, TLS & DTLS sessions can connect to the "plain" TCP & UDP port(s), too - if allowed by configuration. For example, the default listening port can be set to 80 or 443, to go around some strict NATs. Remember that ports < 1024 may require superuser privileges to start the turnserver process.
  * **--tls-listening-port** `<port>`   TURN listener port for TLS and DTLS listeners (Default: 5349). Note: actually, "plain" TCP & UDP sessions can connect to the TLS & DTLS port(s), too - if allowed by configuration. For example, the default tls listening port can be set to 443, to go around some strict NATs. Remember that ports < 1024 may require superuser privileges to start the turnserver process. For secure TCP connections, we currently support SSL version 3 and TLS versions 1.0, 1.1, 1.2. For secure UDP connections, we support DTLS version 1.
  * **--alt-listening-port** `<port>`   Alternative listener port for UDP and TCP listeners; default (or zero) value means "listening port plus one". This is needed for CHANGE\_REQUEST attribute in STUN NAT behavior discovery functionality, like one defined in STUN RFC 3489 or in RFC 5780. The STUN/TURN Server supports CHANGE\_REQUEST only if it is started with more than one listening IP address of the same family (IPv4 or IPv6). If only single IP address is used, then the CHANGE\_REQUEST attribute in BINDING request will be rejected.
  * **--alt-tls-listening-port** `<port>`   Alternative listener port for TLS and DTLS listeners; default (or zero) value means "TLS listening port plus one".
  * **--aux-server** `<IPv4:port>|<[IPv6]:port>`	Auxiliary STUN/TURN server listening endpoint. Aux servers have almost full TURN and STUN functionality. The (minor) limitations are:
  1. Auxiliary servers do not have alternative ports and they do not support STUN RFC 5780 functionality (CHANGE REQUEST).
  1. Auxiliary servers also are never returning ALTERNATIVE-SERVER reply.
Valid formats are 1.2.3.4:5555 for IPv4 and [1:2::3:4]:5555 for IPv6. There may be multiple aux-server options, each will be used for listening to client requests.
    * **-i,** **--relay-device** `<device-name>` **_NOT RECOMMENDED, USE IT ONLY AS THE LAST RESORT OPTIMIZATION & SECURITY OPTION, FOR LINUX SYSTEMS ONLY._** Relay interface device to be used for the relay sockets.
    * **-E,** **--relay-ip** `<ip>` Relay  address  (the local IP address that will be used to relay the packets to the _peer_).	Multiple relay addresses may be	 used. The same IP(s) can be used as both listening IP(s) and relay IP(s). If no relay IP(s) specified, then the turnserver will apply the default policy (since v3.2.2.2): it will decide itself which relay addresses to be used, and it will always be using the client socket IP address as the relay IP address of the TURN session (if the requested relay address family is the same as the family of the client socket).
    * **-X,** **--external-ip** `<public-ip>[/private-ip]` TURN Server public/private address mapping, if the server is behind NAT. In that situation, if a -X is used in form `"-X <ip>"` then that ip will be reported as relay IP address of all allocations. This scenario works only in a simple case when one single relay address is be used, and no CHANGE\_REQUEST STUN functionality is required. That single relay address must be mapped by NAT to the 'external' IP. The "external-ip" value, if not empty, is returned in XOR-RELAYED-ADDRESS field. For that 'external' IP, NAT must forward ports directly (relayed port 12345 must be always mapped to the same 'external' port 12345). In more complex case when more than one IP address is involved, that option must be used several times, each entry must have form `"-X <public-ip/private-ip>"`, to map all involved addresses. CHANGE\_REQUEST NAT discovery STUN functionality will work correctly, if the addresses are mapped properly, even when the TURN server itself is behind A NAT. By default, this value is empty, and no address mapping is used.
    * **-m,** **--relay-threads** `<number>` Number of relay threads to handle the established connections (in addition to authentication thread and the listener thread). If explicitly set to 0 then application handles that traffic in a single thread. If not set, then the default policy is employed. The default thread number is the number of CPUs. In older Linux systems (before Linux kernel 3.9), the number of UDP threads is always one threads per network listening endpoint - including the auxiliary endpoints.
    * **--min-port** `<port>` Lower bound of the UDP port range	for  relay  endpoints  allocation.  Default value is 49152, according to RFC 5766.
    * **--max-port** `<port>` Upper  bound  of	the UDP	port range for relay endpoints allocation.  Default value is 65535, according to RFC 5766.
    * **-u,** **--user** `<user:password> or <user:0xkey>` Long-term credentials user account, in the column-separated form 'username:key'. Multiple user accounts can be used in the command line. The key is either the	user password, or the key is generated by _turnadmin_ command. In the second case, the key  must be  prepended with  '0x'  symbols. The key is calculated over the user name, the realm, and the user password. So, if the realm has been changed, then all user keys in the database have to be re-set. This option cannot be used with TURN REST API.
    * **-r,** **--realm** `<realm>` The default realm to be used for the users when no explicit origin/realm relationship was found in the database. Must be used with long-term credentials mechanism or with TURN REST API.
    * **-q,** **--user-quota** `<number>` Per-user allocation quota: how many concurrent allocations a user can create. _This option can also be set	through the database, for a particular realm._
    * **-Q,** **--total-quota** `<number>` Total allocations quota: global limit on concurrent allocations. _This option can also be set	through the database, for a particular realm._
    * **-s,** **--max-bps** `<number>` Max bytes-per-second bandwidth a TURN session is allowed to handle (input and output network streams are treated separately). Anything above that limit will be dropped or temporary suppressed (within the available buffer limits). _This option can also be set	through the database, for a particular realm._
    * **-B,** **--bps-capacity** `<number>` Maximum server capacity. Total bytes-per-second bandwidth the TURN server is allowed to allocate for the sessions, combined (input and output network streams are treated separately).
    * **--static-auth-secret** `<secret>` Static authentication secret value (a string), for TURN REST API. If not set, then the turn server will try to use the 'dynamic' value in turn\_secret table in user database (if present). The database-stored value can be changed on-the-fly by a separate program, so this is why that other mode is 'dynamic'. Multiple shared secrets can be used (both in the database and in the "static" fashion).
    * **--server-name**	Server name used for the oAuth authentication purposes. The default value is the realm name.
    * **--cert** `<file-name>` Certificate file, PEM format. Same file search rules applied  as for the configuration file. If both **--no-tls** and **--no-dtls** options are specified, then this parameter is not needed. Default value is turn\_server\_cert.pem.
    * **--pkey** `<file-name>` Private key file, PEM format. Same file search rules applied as for the configuration file. If both **--no-tls** and **--no-dtls** options are specified, then this parameter is not needed. Default value is turn\_server\_pkey.pem.
    * **--pkey-pwd** `<private-key-password>` Private key file password, if the key is encrypted.
    * **--CA-file**	CA file in OpenSSL format. Forces TURN server to verify the client SSL certificates. By default, no CA is set and no client certificate check is performed.
    * **--dh-file**	Use custom DH TLS key, stored in PEM format in the file. Flags **--dh566** and **--dh2066** are ignored when the DH key is taken from a file.
    * **--cipher-list**	Allowed OpenSSL cipher list for TLS/DTLS connections. Default value is "DEFAULT".
    * **--ec-curve-name** `<curve-name>` Curve name for EC ciphers, if supported by OpenSSL library (TLS and DTLS). The default value is prime256v1, if pre-OpenSSL 1.0.2 is used. With OpenSSL 1.0.2+,	an optimal curve will be automatically calculated, if not defined by this option.
    * **-l**, **--log-file** `<file-name> or "stdout" or "syslog"` Option to set the full path name of the log file. By default, the turnserver tries to open a log file in  /var/log, /var/tmp, /tmp and current directories directories (which open operation succeeds first that file will be used). With this option you can set the definite log file name. The special names are "stdout" and "-" - they will force everything to the stdout. Also, the file name "syslog" will redirect everything into the system log (syslog) as if the option --syslog was set. In the runtime, the logfile can be reset with the SIGHUP signal to the turnserver process.
    * **--alternate-server** `<ip:port>` Option to set the "redirection" mode. The value of this option will be the address of the alternate server for UDP & TCP service in form of  `<ip>[:<port>]`. The server will send this value in the attribute ALTERNATE-SERVER, with error 300, on ALLOCATE request, to the client. Client will receive only values with the same address family as the client network endpoint address family. See RFC 5389 and RFC 5766 for ALTERNATE-SERVER functionality description. The client must use the obtained IP value for subsequent TURN communications. If more than one --alternate-server options are provided, then the functionality can be more accurately described as "load-balancing" than a mere "redirection". If the port number is omitted, then the default port number 3478 for the UDP/TCP protocols will be used. Colon (:) characters in IPv6 addresses may conflict with the syntax of the option. To alleviate this conflict, literal IPv6 addresses are enclosed in square brackets in such resource identifiers, for example: [2001:db8:85a3:8d3:1319:8a2e:370:7348]:3478 . Multiple alternate servers can be set. They will be used in the round-robin manner. All servers in the pool are considered of equal weight and the load will be distributed equally. For example, if we have 4 alternate servers, then each server will receive 25% of ALLOCATE requests. A alternate TURN server address can be used more than one time with the alternate-server option, so this can emulate "weighting" of the servers.
    * **--tls-alternate-server** `<ip:port>`	Option to set alternative server for TLS & DTLS services in form of `<ip>[:<port>]`. If the port number is omitted, then the default port number 5349 for the TLS/DTLS protocols will be used. See the previous option for the functionality description.
    * **-C**, **--rest-api-separator** `<symbol>`	This is the timestamp/username separator symbol (character) in TURN REST API. The default value is colon (':').
    * **-O**, **--redis-statsdb**	`<connection-string>`	Redis status and statistics database connection string, if used (default - empty, no Redis stats DB used). This database keeps allocations status information, and it can be also used for publishing and delivering traffic and allocation event notifications. The connection string has the same parameters as redis-userdb connection string.
    * **--max-allocate-timeout** `<seconds>`	Max time, in seconds, allowed for full allocation establishment. Default is 60 seconds.
    * **--denied-peer-ip=**`<IPaddr[-IPaddr]>`, **--allowed-peer-ip=**`<IPaddr[-IPaddr]>` Options to ban or allow specific ip addresses or ranges of ip addresses. If an ip address is specified as both allowed and denied, then the ip address is considered to be allowed. This is useful when you wish to ban a range of ip addresses, except for a few specific ips within that range. This can be used when you do not want users of the turn server to be able to access machines reachable by the turn server, but would otherwise be unreachable from the internet (e.g. when the turn server is sitting behind a NAT). The allowed/denied addresses (white/black lists) functionality is very simple:
    1. If there is no rule for an address, then it is allowed;
    1. If there is an explicit allowed rule that fit an address then it is allowed - no matter what;
    1. If there is no explicit allowed rule for an address, and if there is a denied rule that fits the address, then it is denied.
The "white" and "black" peer IP ranges can be dynamically changed in the database.
    * **--pidfile** File name to store the pid of the process. Default is /var/run/turnserver.pid (if superuser account is used) or /var/tmp/turnserver.pid .
    * **--proc-user**	User name to run the process. After the initialization, the turnserver process will make an attempt to change the current user ID to that user.
    * **--proc-group**	Group name to run the process. After the initialization, the turnserver process will make an attempt to change the current group ID to that group.
    * **--cli-ip** Local system IP address to be used for CLI management interface. The turnserver process accepts telnet client connections at this IP address and on the CLI port (see the next parameter). Default IP value is 127.0.0.1, port 5766. You can use telnet or putty (in telnet mode) to access the CLI management interface. See also the parameter **--no-cli**.
    * **--cli-port** CLI management interface listening port. Default is 5766.
    * **--cli-password** CLI access password. Default is empty (no password). For the security reasons, it is recommended to use the encrypted form of the password (see the -P command in the turnadmin utility). The dollar signs in the encrypted form must be escaped.
    * **--cli-max-output-sessions** Maximum number of output sessions in ps CLI command. This value can be changed on-the-fly in CLI. The default value is 256.
    * **--ne=[1|2|3]** Set network engine type for the process (for internal purposes).

### PERFORMACE OPTIMIZATION AND LOAD BALANCE ###

[PERFORMACE OPTIMIZATION AND LOAD BALANCE WIKI PAGE](http://code.google.com/p/coturn/wiki/turn_performance_and_load_balance)

### WEBRTC USAGE ###

This is a set of notes for the WebRTC users:

  1. WebRTC uses long-term credentials mechanism, so you have to use -a option (or --lt-cred-mech). WebRTC relaying will not work with anonymous access. With -a option, do not forget to set the default realm (-r option). You will also have to set up the user accounts, for that you have a number of options (see also the DATABASES section below):
    * command-line options (-u).
    * database table (if SQLite, PostgreSQL or MySQL used). You will have to set keys with turnadmin utility (see docs and wiki for turnadmin). You cannot use open passwords in the database.
    * Redis key/value pair(s), if Redis is used. You can use either keys or open passwords with Redis; see turndb/testredisdbsetup.sh file.
    * Mongo database; see turndb/testmongosetup.sh file.
    * you also can use the TURN REST API (see docs). You will need shared secret(s) set either	through the command line option, or through the config file, or through the database table (SQLite, PostgreSQL or MySQL), or through the Redis or Mongo database.
  1. Usually WebRTC uses fingerprinting (-f).
  1. -v option may be nice to see the connected clients.
  1. -X is needed if you are running your TURN server behind a NAT.
  1. --min-port and --max-port may be needed if you want to limit the relay endpoints ports number range.

### TURN REST API ###

In WebRTC, the browser obtains the TURN connection information from the web
server. This information is a secure information - because it contains the
necessary TURN credentials. As these credentials are transmitted over the
public networks, we have a potential security problem.

If we have to transmit a valuable information over the public network,
then this information has to have a limited lifetime. Then the guy who
obtains this information without permission will be able to perform
only limited damage.

This is how the idea of time-limited TURN credentials appeared. This security mechanism is based upon the long-term credentials mechanism. The main idea is that the web server provides the credentials to the client, but those credentials can be used only limited time by an application that has to create a TURN server connection.

It is assumed that a sort of REST API will be used between the WebRTC clients, WebRTC management console and WebRTC web server to handle the temporary passwords and the (relatively) persistent shared secret. The TURN Server provide support, from the TURN side, for that REST API. Strictly speaking, the TURN Server does not implement the REST API; it just provides a support for it.

The "classic" long-term credentials mechanism (LTCM) is described here:

http://tools.ietf.org/html/rfc5389#section-10.2

http://tools.ietf.org/html/rfc5389#section-15.4

For authentication, each user must know two things: the username and the
password. Optionally, the user may supply the ORIGIN value, so that the
server can figure out the realm to be used for the user. The nonce and the realm values are supplied by the TURN server. But LTCM is not saying anything about the nature and about the persistence of the username and of the password; and this is used by the REST API.

In the TURN REST API, there is no persistent passwords for users. A user has
just the username. The password is always temporary, and it is generated by
the web server on-demand, when the user accesses the WebRTC page. And,
actually, a temporary one-time session only, username is provided to the user,
too.

The temporary user is generated as:

`temporary-username="timestamp" + ":" + "username"`

where username is the persistent user name, and the timestamp format is just
seconds sinse 1970 - the same value as time(NULL) function returns. The timestamp is the expiration time of the temporary password.

The temporary password is obtained as HMAC-SHA1 function over the temporary
username, with shared secret as the HMAC key, and then the result is encoded:

`temporary-password = base64_encode(hmac-sha1(input = temporary-username, key = shared-secret))`

Both the TURN server and the web server know the same shared secret. How the
shared secret is distributed among the involved entities is left to the WebRTC
deployment details - this is beyond the scope of the TURN REST API.

So, a timestamp is used for the temporary password calculation, and this
timestamp can be retrieved from the temporary username. This information
is valuable, but only temporary, while the timestamp is not expired. Without
knowledge of the shared secret, a new temporary password cannot be generated.

This is all formally described in Justin Uberti TURN REST API draft specs document that can be obtained here: [TURN REST API BEHAVE DRAFT SPECS](http://tools.ietf.org/html/draft-uberti-behave-turn-rest-00)

Once the temporary username and password are obtained by the client (browser)
application, then the rest is just 'classic" long-term credentials mechanism.
For developers, we are going to describe it step-by-step below:

  1. a new TURN client sends a request command to the TURN server. Optionally, the client adds the ORIGIN field to it.
  1. TURN server sees that this is a new client and the message is not authenticated.
  1. the TURN server generates a random nonce string, and return the error 401 to the client, with nonce and realm included. If the ORIGIN field was present in the client request, it may affect the realm value that the server chooses for the client.
  1. the client sees the 401 error and it extracts two values from the error response: the nonce and the realm.
  1. the client uses username, realm and password to produce a key: `  key = MD5(username ":" realm ":" SASLprep(password)) ` (SASLprep is described here: http://tools.ietf.org/html/rfc4013)
  1. the client forms a new request, adds username, realm and nonce to the request. Then, the client calculates and adds the integrity field to the request. This is the trickiest part of the process, and it is described in the end of section 15.4: http://tools.ietf.org/html/rfc5389#section-15.4
  1. the client, optionally, adds the fingerprint field. This may be also a tricky procedure, described in section 15.5 of the same document. WebRTC usually uses fingerprinted TURN messages.
  1. the TURN server receives the request, reads the username.
  1. then the TURN server checks that the nonce and the realm in the request are the valid ones.
  1. then the TURN server calculates the key.
  1. then the TURN server calculates the integrity field.
  1. then the TURN server compares the calculated integrity field with the received one - they must be the same. If the integrity fields differ, then the request is rejected.

In subsequent communications, the client may go with exactly the same
sequence, but for optimization usually the client, having already
information about realm and nonce, pre-calculates the integrity string
for each request, so that the 401 error response becomes unnecessary.
The TURN server may use "--stale-nonce" option for extra security: in
some time, the nonce expires and the client will obtain 438 error response
with the new nonce, and the client will have to start using the new nonce.

In subsequent communications, the sever and the client will always assume
the same password - the original password becomes the session parameter and
is never expiring. So the password is not changing while the session is valid
and unexpired. So, if the session is properly maintained, it may go forever,
even if the user password has been already changed (in the database). The
session simply is using the old password. Once the session got disconnected,
the client will have to use the new password to re-connect (if the password
has been changed).

An example when a new shared secret is generated every hour by the TURN server
box and then supplied to the web server, remotely, is provided in the script `examples/scripts/restapi/shared_secret_maintainer.pl` .

A very important thing is that the nonce must be totally random and it must be
different for different clients and different sessions.

### THIRD-PARTY AUTHORIZATION/AUTHENTICATION SUPPORT (with oAuth) ###

Starting with version 4.2.1.2, the TURN server supports the third-party authorization (when the option --oauth is used), in the way how it is described in the document http://tools.ietf.org/html/draft-ietf-tram-turn-third-party-authz-11. The TURN server takes all oAuth key information from its database; it assumes that a separate program handles the keys. The keys can be handled by any algorithm described in the specs document - the only requirement is that the external program has to update the key database, correctly. The external program has to follow one or more key management schemes described in the section 4.1 of the specs document.

As IANA has not assigned the attribute numbers, yet, we are using the unassigned values 0x8031 and 0x0031 for the THIRD\_PARTY\_AUTHORIZATION and OAUTH\_ACCESS\_TOKEN attributes.

### DATABASES ###

For the user database, the turnserver has the following options:

  1. Users can be set in the command line, with multiple -u or --user options. Obviously, only a few users can be set that way, and their credentials are fixed for the turnserver process lifetime.
  1. Users can be stored in SQLite database, if the turnserver was compiled with SQLite support. Each time turnserver checks user credentials, it reads the database (asynchronously, of course, so that the current flow of packets is not delayed in any way), so any change in the database content is immediately visible by the turnserver. The schema for the SQL database can be found in schema.sql file:

```
# Table for long-term credentials mechanism authorization:
#
CREATE TABLE turnusers_lt (
    realm varchar(127) default '',
    name varchar(512),
    hmackey char(128),
    PRIMARY KEY (realm,name)
);

The field hmackey contains HEX string representation of the key.
We do not store the user open passwords for long-term credentials, for security reasons.
Storing only the HMAC key has its own implications - if you change the realm,
you will have to update the HMAC keys of all users, because the realm is 
used for the HMAC key generation.

The key must be 32 characters (HEX representation of 16 bytes) for SHA1.

# Table holding shared secrets for secret-based authorization
# (REST API). It can only be used together with the long-term 
# mechanism:
#
CREATE TABLE turn_secret (
	realm varchar(127) default '',
        value varchar(128),
	primary key (realm,value)
);

# Table holding "white" allowed peer IP ranges.
#
CREATE TABLE allowed_peer_ip (
	realm varchar(127) default '',
	ip_range varchar(256),
	primary key (realm,ip_range)
);

# Table holding "black" denied peer IP ranges.
#
CREATE TABLE denied_peer_ip (
	realm varchar(127) default '',
	ip_range varchar(256),
	primary key (realm,ip_range)
);

# Table to match origin to realm.
# Multiple origins may have the same realm.
# If no realm is found or the origin is absent
# then the default realm is used.
#
CREATE TABLE turn_origin_to_realm (
	origin varchar(127),
	realm varchar(127),
	primary key (origin,realm)
);

# Realm options.
# Valid options are 'max-bps',
# 'total-quota' and 'user-quota'.
# Values for them are integers (in text form).
#
CREATE TABLE turn_realm_option (
	realm varchar(127) default '',
	opt varchar(32),
	value varchar(128),
	primary key (realm,opt)
);

# oAuth key storage table.
#
CREATE TABLE oauth_key (
	kid varchar(128),
	ikm_key varchar(256) default '',
	timestamp bigint default 0,
	lifetime integer default 0,
	hkdf_hash_func varchar(64) default '',
	as_rs_alg varchar(64) default '',
	as_rs_key varchar(256) default '',
	auth_alg varchar(64) default '',
	auth_key varchar(256) default '',
	primary key (kid)
);

#The oauth_key table fields meanings are:
#  kid: the kid of the key;
#  ikm_key - (optional) base64-encoded key ("input keying material");
#	The ikm_key is not needed if the as_rs_key and auth_key are defined explicitly in the database;
#  timestamp - (optional) the timestamp (in seconds) when the key lifetime started;	
#  lifetime - (optional) the key lifetime in seconds; the default value is 0 - unlimited lifetime.
#  hkdf_hash_func - (optional) hash function for HKDF procedure; the valid values are SHA-1, SHA-256, SHA-384, SHA-512 with SHA-256 as default;
      The hkdf_hash_func is not needed if the as_rs_key and auth_key are defined explicitly in the database;
#  as_rs_alg - oAuth token encryption algorithm; the valid values are "AES-128-CBC" and "AES-256-CBC", , "AEAD-AES-128-GCM", "AEAD-AES-256-GCM". The default value is "AES-256-CBC";
#  as_rs_key - (optional) base64-encoded AS-RS key. If not defined, then calculated with ikm_key and hkdf_hash_func. The as_rs_key length is defined by as_rs_alg.
#  auth_alg - (optional) oAuth token authentication algorithm; the valid values are "HMAC-SHA-256-128", "HMAC-SHA-256", "HMAC-SHA-384", "HMAC-SHA-512" and "HMAC-SHA-1". The default value is "HMAC-SHA-256-128".
#  auth_key - (optional) base64-encoded AUTH key. If not defined, then calculated with ikm_key and hkdf_hash_func. The auth_key length is defined by auth_alg.
#

# Https access admin users.
# Leave this table empty if you do not want 
# remote https access to the admin functions.
# 
# The password may be stored in encrypted form
# $5$<...salt...>$<...sha256(salt+password)...>
# The encrypted form can be generated with turnadmin utility.
#
CREATE TABLE admin_user (
	name varchar(32),
	realm varchar(127),
	password varchar(127),
	primary key (name)
);

```

> For long-term credentials, you have to set the "keys" for the users; the "keys" are generated by the turnadmin utility. For the key generation, you need username, password and the realm. All users in the database must use the same realm value; if down the road you will decide to change the realm name, then you will have to re-generate all user keys (that can be done in a batch script). For security reasons, you will have to make sure that nobody can access the database outside of the TURN server box.

See an example of the database setup in the file testsqldbsetup.sql.

> 3. The same as previous is applicable to a PostgreSQL or to a MySQL database. The same schema file is used. The same considerations are valid.

> 4. The same is true for the Redis database, but the Redis database has a different schema - it can be found (in the form of explanation) in schema.userdb.redis. Example of a redis database setup commands (for two realms - north.gov and crinna.org):

```
$ redis-cli <<!

AUTH turn
SELECT 2

set turn/realm/north.gov/user/ninefingers/key "bc807ee29df3c9ffa736523fb2c4e8ee"
set turn/realm/north.gov/user/gorst/key "7da2270ccfa49786e0115366d3a3d14d"

set turn/realm/crinna.org/user/whirrun/key "6972e85e51f36e53b0b61759c5a5219a"
set turn/realm/crinna.org/user/stranger-come-knocking/key "d43cb678560259a1839bff61c19de15e"

sadd turn/realm/north.gov/secret "logen" "bloody9"
sadd turn/realm/crinna.org/secret "north" "library"

set turn/realm/north.gov/max-bps 500000
set turn/realm/north.gov/total-quota 12000
set turn/realm/north.gov/user-quota 10000
set turn/realm/crinna.org/max-bps 400000
set turn/realm/crinna.org/total-quota 10000
set turn/realm/crinna.org/user-quota 8000

set turn/origin/http://crinna.org:80 crinna.org
set turn/origin/https://bligh.edu:443 crinna.org

sadd turn/realm/north.gov/allowed-peer-ip "172.17.13.200" "172.17.13.201"
sadd turn/realm/crinna.org/allowed-peer-ip "172.17.13.202"

sadd turn/realm/north.gov/denied-peer-ip "172.17.13.133-172.17.14.56" "172.17.17.133-172.17.19.56" "123::45"
sadd turn/realm/crinna.org/denied-peer-ip "123::77"

hmset turn/oauth/kid/north ikm_key 'Y2FybGVvbg==' hkdf_hash_func 'SHA-256' as_rs_alg 'AES-256-CBC' auth_alg 'HMAC-SHA-256-128'
hmset turn/oauth/kid/oldempire ikm_key 'YXVsY3Vz' hkdf_hash_func 'SHA-256' as_rs_alg 'AEAD-AES-256-GCM'

hmset turn/admin_user/skarling realm 'north.gov' password '\$5\$6fc35c3b0c7d4633\$27fca7574f9b79d0cb93ae03e45379470cbbdfcacdd6401f97ebc620f31f54f2'
hmset turn/admin_user/bayaz password '\$5\$e018513e9de69e73\$5cbdd2e29e04ca46aeb022268a7460d3a3468de193dcb2b95f064901769f455f'

save

!
```

See the file schema.userdb.redis for the more detailed explanation.

> 5. MongoDB setup

The MongoDB setup is well documented on their site http://docs.mongodb.org/manual/.

Note: if your system has a "standard" plain vanilla UNIX "make" utility
(that is not a GNU make) then you will have to use the GNU make to compile
the Mongo driver, because the Mongo compilation process was written with
the "proprietary" GNU extensions. For example, in FreeBSD in will have to use
"gmake" command.

If the TURN server was compiled with MongoDB support (mongo-c-driver is the C client
library for MongoDB), then we can use the TURN server database parameter
--mongo-userdb. The value of this parameter is a connection string
for the MongoDB database. The format of the connection string is described at
http://hergert.me/docs/mongo-c-driver/mongoc_uri.html:

```
"mongodb://[username:password@]host1[:port1][,host2[:port2],...[,hostN[:portN]]][/[database][?options]]"
```

So, an example of the MongoDB database parameter in the TURN server command
line would be:

```
--mongo-userdb="mongodb://localhost:27017/turndb"
```

Or in the turnserver.conf file:

```
mongo-userdb="mongodb://localhost:27017/turndb"
```

See the files schema.mongo.sh and testmongosetup.sh for the more examples.

> 7. If a database is used, then users can be divided into multiple independent realms. Each realm can be administered separately, and each realm can have its own set of users and its own performance options (max-bps, user-quota, total-quota).

> 8. Of course, the turnserver can be used in non-secure mode, when users are allowed to establish sessions anonymously. Then you do not need a database at all. But some TURN client (like WebRTC clients) are not able to work in anonymous mode, without an authentication mechanism.

For the status and statistics database, there are two choices:

  1. The simplest choice is not to use it. Do not set --redis-statsdb option, and this functionality will be simply ignored.
  1. If you choose to use the statistics database, then set the --redis-statsdb option. This may be the same database as in --redis-userdb option, or it may be a different database. You may want to use different database for security or convenience reasons. Also, you can use different database management systems for the user database and for the status and statistics database. For example, you can use MySQL as the user database, and you can use Redis for the statistics. Or you can use Redis for both. The stats database is described in turndb/schema.stats.redis.

So, we have 6 choices for the user management, and 2 choices for the statistics management. These two are totally independent. So, you have overall 6\*2=12 ways to handle persistent information, choose any for your convenience.

You do not have to handle the database information "manually" - the turnadmin program can handle everything for you. For SQLite, PostgreSQL and MySQL you will just have to create an empty database with schema.sql SQL script. With Redis or Mongo, you do not have to do even that - just run turnadmin and it will set the users for you (see the turnadmin manuals).

### KEEP ALIVE ###

The "keep alive" functionality can be implemented in several ways:

  1. For the HTTP-style "keep-alive", the TCP listening endpoints (default ports 3478 and 5349) are able to accept HTTP GET request and reply with HTTP 200 OK response.
  1. On UDP listening endpoints, STUN BINDING indication or STUN BINDING request can be used as stateless "keep-alive" communication dialog.

### HTTPS MANAGEMENT INTERFACE ###

The turnserver process provides an HTTPS Web access as statistics and basic
management interface. The turnserver listens to incoming HTTPS admin
connections on the same ports as the main TURN/STUN listener. The Web admin
pages are basic and self-explanatory.

To make the HTTPS interface active, the database table admin\_user must be
populated with the admin user account(s). An admin user can be a superuser
(if not assigned to a particular realm) or a restricted user (if assigned to
a realm). The restricted admin users can perform only limited actions, within
their corresponding realms.

### TELNET CLI ###

The turnserver process provides a telnet CLI access as statistics and basic management interface. By default, the turnserver starts a telnet CLI listener on IP 127.0.0.1 and port 5766. That can be changed by the command-cline options of the turnserver process (see --cli-ip and --cli-port options). The full list of telnet CLI commands is provided in "help" command output in the telnet CLI.

### TURN connection with Proxy ###

In an Enterprise environment when a Proxy is used for the Internet connections, the TURN connection handling has some specifics. The discussion about the matter can be found here:

[TURN and Proxy](https://groups.google.com/forum/#!topic/discuss-webrtc/q0_RHvYNbf8)

### ALPN ###

The server supports ALPNs "stun.turn" and "stun.nat-discovery", when
compiled with OpenSSL 1.0.2 or newer. If the server receives a TLS/DTLS
`ClientHello` message that contains one or both of those ALPNs, then the
server chooses the first `stun.*` label and sends it back (in the `ServerHello`)
in the ALPN extension field. If no `stun.*` label is found, then the server
does not include the ALPN information into the `ServerHello`.

### TURN-bis draft ###

TURN-bis with dual allocation (http://tools.ietf.org/html/draft-ietf-tram-turnbis-01) is implemented. At the present moment, the two new dual-allocation-related STUN attributes have no assigned type (by IANA); so we are using tentative numbers: ADDITIONAL-ADDRESS-FAMILY attribute has type 0x8032, and ADDRESS-ERROR-CODE has type 0x8033.