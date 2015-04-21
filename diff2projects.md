# Introduction #

The two projects - rfc5766-turn-server and coturn - are closely related. Coturn was created as a next-generation TURN server. This document summarizes the differences between the two projects. The differences are supposed to grow with time, because rfc5766-turn-server is supported for the legacy purposes and its main goal is stability and performance. On the other hand, the coturn project is supposed to embrace all new things in the TURN world.

# Details #

## Multiple realms and ORIGIN field support in coturn. ##

Coturn is capable of handling multiple user realms in a single TURN server setup. From the database point of view, it is done by introducing a field "realm" in the database tables related to the long-term credentials; and by introducing a new table that maps the ORIGIN field in the ALLOCATE request to the realm (the relationship between realm and origin is one-to-many: many origins can correspond to the same realm).

If the ORIGIN field is not present in the ALLOCATE request, or no realm was found for the ORIGIN field, then the default realm is used.

The realms data items are independent. The same user name can be present in different realms with different passwords and those user names are used independently.

Different realms can have different performance settings (max-bps, total-quota and user-quota). There is a special database table that holds those settings, per realm. Those settings can be changed, per realm, in the telnet CLI, too.

The CLI interface shows the realm data. You can set the CLI session realm and then you will see only that realm data.

See the specs document here: http://tools.ietf.org/html/draft-johnston-tram-stun-origin-02 .

## Bandwidth draft specs support in coturn. ##

Bandwidth support was implemented to allow some control over the total bandwidth utilization by the server. The space are here: http://tools.ietf.org/html/draft-thomson-tram-turn-bandwidth-00.

This implementation of the specs document is fairly simple and straightforward. If the TURN server administrator wants to utilize the bandwidth functionality, then he/she must set the bps-capacity parameter of the TURN server. This value can be set statically (as the command-line parameter or as the config file setting) or dynamically (through the CLI interface). The max server bandwidth capacity must be determined by the administrator.

For correct functionality, the administrator must also set the max-bps TURN server parameter. The max-bps must be determined experimentally and/or by the administrative goals.

If the ALLOCATE request includes BANDWIDTH attribute (0x8010 attribute type, for experimental reasons; in the final document, the exact attribute type will be defined) then the server subtracts the value of the BANDWIDTH attribute (up to max-bps) from the total bandwidth capacity. If there is not enough bandwidth, then 486 error will be returned. If no BANDWIDTH attribute is included in the ALLOCATE request, then max-bps will be allocated. In the ALLOCATE response, the real allocated BANDWIDTH value is returned to the client.

## SSODA draft support in coturn. ##

Coturn supports the dual allocation specs: in a single TURN session, the client can request both IPv4 and IPv6 relay endpoints.

## MongoDB support in coturn. ##

Coturn can store data in Mongo.

## SQLite support in coturn. ##

Coturn can store data in SQLite.

## No flat files database support in coturn ##

Starting with version 4.3.1.1, coturn does not support turnuserdb.conf flat-file database. The default database for Coturn is SQLite.

## Coturn supports STUN/TURN ALPN ##

The details are in http://tools.ietf.org/html/draft-ietf-tram-alpn-08.
For ALPN support, Coturn has to be compiled with OpenSSL 1.0.2+.

## Authentication threading model ##

Coturn, starting with version 4.3.3.1, uses multiple authentication threads. rfc5766-turn-server uses a single authentication thread, always.

## Coturn does not support short-term credentials mechanism ##

Because there is no popular use case for the short-term credentials mechanism in the TURN Server, for simplification, we cancelled the short-term credentials support in Coturn.

## Coturn has HTTPS Web admin interface ##

Both rfc5766-turn-server and coturn provide telnet-based CLI admin interface. But only Coturn has web admin interface.

## Coturn supports SCTP ##

Experimental implementation of 'native' SCTP added to the client-side communications.