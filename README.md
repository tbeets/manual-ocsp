# manual-ocsp

#### Essentials testing against a web server
```bash
# OCSP Validate the site www.akamai.com
./ocsp-request.sh "www.akamai.com"

# Above has pretty print result, but also saves the raw bytes of the request and response

###### Obtaining ASN.1 dump of the OCSP Response

# You can dump the ASN.1 of the HTTP response, although the OCSP Response document is nested in a asn.1 string
openssl asn1parse -in ocsp.resp -inform der

# Here you can cut out the nested OCSP Response (hex encoded) 
openssl asn1parse -in ocsp.resp -inform der | sed -n -e 's/^.*\[HEX DUMP\]://p'

# convert the hex encoded to bin (der) for next asn.1 decode
xxd -r -p nested.hex nested.bin
openssl asn1parse -in hack.bin -inform der

# All in one line
openssl asn1parse -in ocsp.resp -inform der | sed -n -e 's/^.*\[HEX DUMP\]://p' | xxd -r -p | openssl asn1parse -inform der
```

#### Test a NATS protocol listener (post-INFO TLS initiation not compatible with openssl)

```bash
# Run a test web server using the NATS Server's cert and secret
openssl s_server -key key.pem -cert cert.pem -accept 44300 -www

# You can validate your temporary web server as
openssl s_client -CAfile rootCA.pem -connect localhost:44300 -showcerts

# On an OCSP enabled cert with responder:
./ocsp-request.sh "localhost:44300"

# To get an ASN.1 output of OCSP Response body
openssl asn1parse -in ocsp.resp -inform der | sed -n -e 's/^.*\[HEX DUMP\]://p' | xxd -r -p | openssl asn1parse -inform der
```


