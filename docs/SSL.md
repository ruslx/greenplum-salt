http://www.openkb.info/2014/05/how-to-enable-ssl-connection-for.html

How to enable ssl connection for Greenplum

Greenplum Database has native support for SSL connections between the client and the master server. 
SSL connections prevent third parties from snooping on the packets, and also prevent man-in-the-middle attacks. 
SSL should be used whenever the client connection goes through an insecure link, and must be used whenever client certificate authentication is used.

1. Create a Self-signed Certificate without a Passphrase for Testing Only.
Note: "Common Name" is the local host name. "challenge password" is left blank.
gpadmin@mdw $ openssl req -new -text -out server.req
Generating a 1024 bit RSA private key
.++++++
.....++++++
writing new private key to 'privkey.pem'
Enter PEM pass phrase:1234
Verifying - Enter PEM pass phrase:1234
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [AU]:US
State or Province Name (full name) [Some-State]:CA
Locality Name (eg, city) []:San Jose
Organization Name (eg, company) [Internet Widgits Pty Ltd]:Test
Organizational Unit Name (eg, section) []:Test
Common Name (e.g. server FQDN or YOUR name) []:mdw
Email Address []:test@test.test

Please enter the following 'extra' attributes
to be sent with your certificate request
A challenge password []:
An optional company name []:Test2

2. To use this certificate with Greenplum Database, remove the passphrase with the following commands:
gpadmin@mdw $ openssl rsa -in privkey.pem -out server.key
Enter pass phrase for privkey.pem:1234
writing RSA key

gpadmin@mdw $ rm privkey.pem

3. Enter the following command to turn the certificate into a self-signed certificate and to copy the key and certificate to a location where the server will look for them.
openssl req -x509 -in server.req -text -key server.key -out server.crt

4. Change the permissions on the key with the following command
chmod og-rwx server.key

5. Copy the server.key and server.crt into $MASTER_DATA_DIRECTORY 
cp server.key $MASTER_DATA_DIRECTORY/
cp server.crt $MASTER_DATA_DIRECTORY/

6. Add "ssl=on" in postgresql.conf

7. Add entries in pg_hba.conf to make every remote connection use ssl.
hostssl     all         all         0.0.0.0/0    md5

8. Restart GPDB
gpadmin$ gpstop -r
`gpstop -u` not working

9. Test using root user, and you will see "SSL connection":
gpadmin@mdw $ psql -h localhost -U root template1
Password for user root:
Timing is on.
Pager usage is off.
psql (8.2.15)
SSL connection (cipher: DHE-RSA-AES256-SHA, bits: 256)
Type "help" for help.

template1=#
