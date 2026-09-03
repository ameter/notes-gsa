Test certs:

curl -sS -o /dev/null -w "%{http_code}\n" https://rubygems.org

Will get 200 if trusted.


```


curl: (60) SSL certificate OpenSSL verify result: unable to get local issuer certificate (20)
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the webpage mentioned above.
000
```

## Get Zscalar root CA cert from host:

openssl crl2pkcs7 -nocrl -certfile /etc/ssl/certs/ca-certificates.crt \
  | openssl pkcs7 -print_certs \
  | awk '/Zscaler Root CA/,/END CERTIFICATE/' > zscaler-root.pem


## Copy Zscaler root CA cert into sandbox

sbx cp zscaler-root.pem \[your sandbox name\]:/usr/local/share/ca-certificates/zscaler-root.crt


## Install the Zscaler root CA cert in the sandbox

sbx exec idp sudo update-ca-certificates