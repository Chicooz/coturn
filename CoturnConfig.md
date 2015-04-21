# Introduction #

Coturn has multiple options and the configuration can be complex. The good thing is that most default values of the options are what most people need to use. Still, a simple introductory set-up guide is useful, and it is provided by this page.

(some portions of this document are taken from the Dialogic TURN server configuration guide http://www.dialogic.com/den/developer_forums/f/71/t/10238.aspx)

# Details #

## OS ##

A simple server-oriented configuration of the OS should suffice.

We assume that the system used for TURN resides at a public IP address, with no firewall involved. It is possible to set up a TURN server using a cloud service like AWS, but that is beyond the scope of these instructions. The coturn project download provides an example of Amazon AWS EC2 image that is set for the cloud service.

## Third-party libraries ##

Before installing and configuring the TURN server, the latest stable libevent library should be downloaded built and installed:

```
$ wget https://github.com/downloads/libevent/libevent/libevent-2.0.21-stable.tar.gz
```

As root, build and install the library with the usual:

```
$ tar xvfz libevent-2.0.21-stable.tar.gz
$ cd libevent-2.0.21-stable
$ ./configure
$ make
$ make install
```

Default setup assumes that you are using sqlite as the database engine, that is sufficient for most purposes. If your project requires a different database (mysql, postgresql, mongodb, redis) then check the INSTALL file for the instructions.

You will need, also, a relatively fresh OpenSSL version.

## Coturn installation ##

We assume that you are installing the Coturn from the sources.

Download the TURN server from https://code.google.com/p/coturn/wiki/Downloads. Build and install with:

```
$ tar xvfz turnserver-<...>.tar.gz
$ ./configure
$ make
$ make install
```

Check the INSTALL and README files with the server. They contain lots of useful information.

Add the long-term TURN users with the turnadmin utility. For example, this command adds user ninefinger with password youhavetoberealistic, realm north.gov, to the default sqlite database:

```
$ sudo turnadmin -a -u ninefingers -r north.gov -p youhavetoberealistic
```

Add the admin user(s) to the database, with turnadmin utility. If you added those users, then will be able to connect to the TURN server ports over HTTPS from your browser and perform admin tasks with the web interface.

For example, this command adds the admin user bayaz with password magi:

```
$ sudo bin/turnadmin -A -u bayaz -p magi
```

In your Javascript code where the RTCPeerConnection is created, reference the TURN server as follows:
```
        var pc_config = {"iceServers": [{"url": "stun:stun.l.google.com:19302"},
                        {"url":"turn:my_username@<turn_server_ip_address>", 
                                          "credential":"my_password"}]};
        pc_new = new webkitRTCPeerConnection(pc_config);
```

Embedding a visible password in your Javascript code may allow someone else to use your TURN server. It can be fixed by the TURN REST API usage (see the project wiki pages).

Since the server is publicly accessible, access to it should be restricted as much as possible. Minimally, port 3478 (STUN) should be opened for TCP and UDP. Assuming that we are using CentOS, the most convenient way to do this on CentOS is to use the firewall configuration option of "setup". Select Customize and Forward until the Other Ports screen is found.  There, add 3478:tcp and 3478:upd. Close and save, then restart the firewall with:

```
$ service iptables restart
```

Check the firewall with:

```
> service iptables status
Table: filter
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0           state RELATED,ESTABLISHED
2    REJECT     icmp --  0.0.0.0/0            0.0.0.0/0           icmp type 8 reject-with icmp-host-prohibited
3    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0
4    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
5    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:22
6    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0           state NEW tcp dpt:3478
7    ACCEPT     udp  --  0.0.0.0/0            0.0.0.0/0           state NEW udp dpt:3478
8    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited

Chain FORWARD (policy ACCEPT)
num  target     prot opt source               destination
1    REJECT     all  --  0.0.0.0/0            0.0.0.0/0           reject-with icmp-host-prohibited

Chain OUTPUT (policy ACCEPT)
num  target     prot opt source               destination

```

Note that this configuration has ICMP ping disabled to make the system harder to find and port 22 left open for SSH access.

Finally, for this relatively simple case that uses a system with a single Ethernet NIC and IP addres and no NAT firewall, start the TURN server with:

```
$ turnserver -L <public_ip_address> -a -f -r <realm-name>
```

Or, start it as a Linux daemon with:

```
$ turnserver -L <public_ip_address> -o -a -f -r <realm-name>
```

The TURN server should now be ready to use for media relay when ICE decides that it is needed for a WebRTC connection.