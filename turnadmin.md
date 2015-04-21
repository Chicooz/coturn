## turnadmin application: a TURN relay administration tool ##

### Usage: ###
```
       $ turnadmin [command] [options]
       $ turnadmin [ -h	| --help]
```
### Commands: ###

  * **-P,** **--generate-encrypted-password** Generate and print to the standard output an encrypted form of a password (for web admin user or CLI). The value then can be used as a safe key for the password storage on disk or in the database. Every invocation for the same password produces a different result. The for mat of the encrypted password is: ` $5$<...salt...>$<...sha256(salt+password)...> `. Salt is 16 characters, the sha256 output is 64 characters. Character 5 is the algorithm id (sha256). Only sha256 is supported as the hash function.
  * **-k,** **--key** Generate key for a long-term credentials mechanism user.
  * **-a,** **--add** Add or update a long-term user.
  * **-d,** **--delete** Delete a long-term user.
  * **-l,** **--list** List all long-term users.
  * **-s,** **--set-secret=...** Set secret in the database.
  * **-S,** **--show-secret** Show secret stored in database.
  * **-X,** **--delete-secret** `<secret>`	Delete a shared secret.
  * **--delete-all-secrets**	Delete all shared secrets for REST API.
  * **-O,** **--add-origin**	Add origin-to-realm relation.
  * **-R,** **--del-origin**	Delete origin-to-realm relation.
  * **-I,** **--list-origins** List origin-to-realm relations.
  * **-g,** **--set-realm-option** Set realm params: **_max-bps_**, **_total-quota_**, **_user-quota_**.
  * **-G,** **--list-realm-options** List realm params.

### Options: ###

  * **-b,** **--db**, **--userdb** `<sqlite-file-name>` SQLite database file name (default - /var/db/turndb or /usr/local/var/db/turndb or /var/lib/turn/turndb, depending on platform).
  * **-e,** **--psql-userdb,** **--sql-userdb** `<connection-string>` PostgreSQL database connection string.
  * **-M,** **--mysql-userdb** `<connection-string>` MySQL database connection string.
  * **-N,** **--redis-userdb** `<connection-string>` Redis database connection string.
  * **-J,** **--mongo-userdb** `<connection-string>` MongoDB user database connection string.
  * **-u,** **--user** `<user>`   User name.
  * **-r,** **--realm** `<realm>`    Realm.
  * **-p,** **--password** `<password>` Password.
  * **-o,** **--origin** `origin` Fully-formed origin, for example `https://crinna.org:443`.
  * **--max-bps** Set value of realm's max-bps parameter. _Setting to zero value means removal of the option._
  * **--total-quota** Set value of realm's total-quota parameter. _Setting to zero value means removal of the option._
  * **--user-quota** Set value of realm's user-quota parameter. _Setting to zero value means removal of the option._
  * **-h,** **--help** Help.

### Generate an encrypted form of a password: ###
```
       $ turnadmin -P -p <password>
```
### Generate a key: ###
```
       $ turnadmin -k -u <username> -r <realm> -p <password>
```
### Add/update a user (and realm) in the database: ###
```
       $ turnadmin -a [-b <sqlite-db-file> | -e <db-connection-string> | -M <db-connection-string>  | -N <db-connection-string> ] -u <username>	-r <realm>  -p	<password>
```
### Delete a user from the database: ###
```
       $ turnadmin -d [-b <sqlite-db-file> | -e <db-connection-string> | -M <db-connection-string>  | -N <db-connection-string> ] -u <username>
```
### List all long-term users in MySQL database: ###
```
$ turnadmin -l --mysql-userdb="<db-connection-string>"
```
### List all long-term users in Redis database: ###
```
$ turnadmin -l --redis-userdb="<db-connection-string>"
```
### Set secret: ###
```
$ turnadmin -s <secret> --mysql-userdb="<db-connection-string>"
```
### Show secret(s): ###
```
$ turnadmin -S --psql-userdb="<db-connection-string>"
```
### Set origin-to-realm relation in MySQL database: ###
```
$ turnadmin --mysql-userdb="<db-connection-string>" -r <realm> -o <origin>
```
### Delete origin-to-realm relation from Redis DB: ###
```
$ turnadmin --redis-userdb="<db-connection-string>" -o <origin>
```
### List all origin-to-realm relations in Redis DB: ###
```
$ turnadmin --redis-userdb="<db-connection-string>" -I
```
### List the origin-to-realm relations in PostgreSQL DB for a single realm: ###
```
$ turnadmin --psql-userdb="<db-connection-string>" -I -r <realm>
```
### Help: ###
```
       $ turnadmin -h
```