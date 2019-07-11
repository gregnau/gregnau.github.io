---
layout: post
title: 🔒 Create a certificate for multiple domains (subdomains)
---

Recently i've faced with challenges of getting multi-domain setup with HTTPS by default. This subject has been proven to be more tricky than one would expect. Searching gives a lot of results on creating self-signed certificates, but those are Root Certificate Authorities with a domain in the CommonName field and can't handle more than one domain OR a bunch of subdomains (i.e. domain.com OR \*.domain.com). Covering a larger number of domains with only one certificate require x509 Version 3 certificates (RFC 2459) properly signed with Root Certificate Authority (rootCA). So basically a proper chain of certificate and authority, ofcourse self signed. There is a downside also, the root certificate needs to be imported on every clients before use.

### Creating the Root Certificate Authority certificate (`rootCA`)
1. First step is to create the Root Certificate Authority key file, which is the private key to signing every certificates in the future. This is better to be kept private in a safe place.
The standard key sizes nowadays are `1024 bit`, `2048 bit`, and to a much lesser extent `4096 bit`. My advice to go with `2048 bit`, which is fine for now. `4096 bit` is usually overkill and the key length is 5 times more computationally intensive than `2048 bit`. The `-des3` option will protect this keyfile with a password. Nevertheless no need to say don't forget this password, otherwise the root key file will be unusable.
```shell
openssl genrsa -des3 -out rootCA.key 2048
```

2. Next step is to create and sign the rootCA certificate. This is actually what real certificate authorities are also doing, self-signing their root certificate:
```shell
openssl req -x509 -new -nodes -key rootCA.key -sha256 -days 3650 -out rootCA.pem
```

```
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [NL]:NL
State or Province Name (full name) [Some-State]:Nederland
Locality Name (eg, city) []: Amsterdam
Organization Name (eg, company) [Internet Widgits BV]:Example Certificate Authority
Organizational Unit Name (eg, section) []:IT Security
Common Name (eg, YOUR name) []:ExampleCA (root)
Email Address []:dont-complain@example.com
```

After this step the Root Certificate (`rootCA.pem`) is signed with the previously generated `rootCA.key` and it's valid for 3650 days. Usually these certificates are valid for 5 or 10 years, but can be changed to anything with the `-days` option.

The new `rootCA.pem` file needs to be imported on every client which intended to use this domain with HTTPS.
Furthermore there is no need to import certificates for every site, they just needs to be signed with the `rootCA.key` as above.

### Creating Certificate Requests
1. Every trusted certificate needs a fresh private key file:
```shell
openssl genrsa -out cert.key 2048
```

2. For the multiple domain names there is config file needed:

```
[req]
distinguished_name = req_distinguished_name
req_extensions = v3_req[req_distinguished_name]
countryName = Country Name (2 letter code)
countryName_default = NL
stateOrProvinceName = State or Province Name (full name)
stateOrProvinceName_default = Nederland
localityName = Locality Name (eg, city)
localityName_default = Den Haag
organizationalUnitName = Organizational Unit Name (eg, section)
organizationalUnitName_default = IT security
commonName = Common Name
commonName_default = Example Webserver
commonName_max = 64

[ v3_req ]
# Extensions to add to a certificate request
basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = example.com
DNS.2 = *.example.com
DNS.3 = *.*.example.com
IP.1 = 192.168.144.170
IP.2 = 192.168.144.144
```

Domain names and/or ip addresses have to be supplied in the `alt_names` section. Save it as `cert.cnf` for example.

3. Once the key is created, generate a Certificate Signing Request:
```shell
openssl req -new -out cert.csr -key cert.key -config cert.cnf
```

4. Then one can check the request file (`cert.csr`) to validate it before next the step:
```shell
openssl req -text -noout -in cert.csr
```

5. Then create and sign the certificate:
```shell
openssl x509 -req -in cert.csr -CA rootCA.pem -CAkey rootCA.key -CAcreateserial -out cert.crt -days 365 -sha256 -extensions v3_req -extfile cert.cnf
```

* Optional:
Convert the `.crt` to `.pem`:
```shell
openssl x509 -in cert.crt -out cert.pem -outform PEM
```

### Trust the new Certificate Authority
1. Copy your certificate in `PEM` format (the format that has `---BEGIN CERTIFICATE----` in it) into `/usr/local/share/ca-certificates` and name it with a `.crt` file extension.

2. Then update the certificate store:
```shell
sudo update-ca-certificates
```

* Downsides: This installation only affects products that use this certificate store. Some products may use other certificate stores; if you use those products, you'll need to add this CA certificate to those other certificate stores, too. ([Firefox Instructions](http://www.cyberciti.biz/faq/firefox-adding-trusted-ca/), [Chrome Instructions](http://blogmines.com/blog/2010/07/08/how-to-import-a-certificate-in-google-chrome/), [Java Instructions](http://www.windowsazure.com/en-us/documentation/articles/java-add-certificate-ca-store/))

### Testing The CA
1. You can verify if this worked by looking for the certificate that you just added in `/etc/ssl/certs/ca-certificates.crt` (which is just a long list of all of your trusted CA's concatenated together).

2. You can also use OpenSSL's `s_client` by trying to connect to a server that you know is using a certificate signed by the CA that you just installed:
```shell
openssl s_client -connect foo.whatever.com:443 -CApath /etc/ssl/certs
```

Example output:
```
CONNECTED(00000003)
depth=1 C = NL, ST = Nederland, O = "Internet Widgits BV", CN = "Example Webserver", emailAddress = dont-complain@example.com
verify return:1
depth=0 C = NL, ST = Nederland, L = Amsterdam, O = "Internet Widgits BV", CN = "Example Webserver"
verify return:1
---
Certificate chain
0 s:/C=NL/ST=Nederland/L=Amsterdam/O=Whatever, Inc./CN=foo.whatever.com
i:/C=NL/ST=Nederland/O=Internet Widgits BV/CN=Example Webserver/emailAddress=dont-complain@example.com
...
... (skip to interesting parts)
...
Key-Arg : None
PSK identity: None
PSK identity hint: None
SRP username: None
Start Time: 1392837700
Timeout : 300 (sec)
Verify return code: 0 (ok)
```

3. The first thing to look for is the certificate chain near the top of the output. This should show the CA as the issuer (next to i:). This tells you that the server is presenting a certificate signed by the CA you're installing.

4. Second, look for the verify return code at the end to be set to 0 (ok).

### Resources:
* [Creating your own ssl certificate authority](http://datacenteroverlords.com/2012/03/01/creating-your-own-ssl-certificate-authority/)
* [Multiple Names on One Certificate](http://apetec.com/support/GenerateSAN-CSR.htm)
* [How do you add a certificate authority (CA) to Ubuntu?](http://superuser.com/questions/437330/how-do-you-add-a-certificate-authority-ca-to-ubuntu#719047)
