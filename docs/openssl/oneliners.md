# openssl:oneliners

handy one-liners for using openssl to generate a self-signed cert with SANs

``` shell title="ed25519 cert with san"
$ openssl req -new -nodes -x509 -subj "/C=US/ST=NC/O=secunit/CN=*.secunit.lan" \
                   -addext "subjectAltName = DNS:*.secunit.lan" \
                   -addext "certificatePolicies = 1.2.3.4" \
                   -newkey ec:<(openssl ecparam -name secp384r1) \
				   -keyout key.pem -out req.pem -days 3650
```

``` shell title="rsa cert with san"
$ openssl req -new -nodes -x509 -subj "/C=US/ST=NC/O=secunit/CN=*.secunit.lan" \
                   -addext "subjectAltName = DNS:*.secunit.lan" \
                   -addext "certificatePolicies = 1.2.3.4" \
                   -newkey rsa:2048 \
				   -keyout key.pem -out req.pem -days 3650
```
