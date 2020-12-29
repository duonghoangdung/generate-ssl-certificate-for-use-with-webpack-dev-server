# generate-ssl-certificate-for-use-with-webpack-dev-server
Generate SSL Certificate for use with Webpack Dev Server (OSX)

## Generate private key
```
$ openssl genrsa -out private.key 4096
```

## Generate a Certificate Signing Request
```
$ openssl req -new -sha256 \
    -out private.csr \
    -key private.key \
    -config ssl.conf 
```


## Generate the certificate
```
$ openssl x509 -req \
    -days 3650 \
    -in private.csr \
    -signkey private.key \
    -out private.crt \
    -extensions req_ext \
    -extfile ssl.conf
```

## Add the certificate to keychain and trust it
```
$ sudo security add-trusted-cert -d -r trustRoot -k /Library/Keychains/System.keychain private.crt
```

## Create a pem file from crt
```
$ openssl x509 -in private.crt -out private.pem -outform PEM
```

## Run webpack dev server
```
npm run webpack-dev-server -- --open --https --cert private.pem --key private.key
```

ssl.conf
```
[ req ]
default_bits       = 4096
distinguished_name = req_distinguished_name
req_extensions     = req_ext

[ req_distinguished_name ]
countryName                 = IL
countryName_default         = GB
stateOrProvinceName         = Center
stateOrProvinceName_default = England
localityName                = Tel Aviv
localityName_default        = Brighton
organizationName            = CA
organizationName_default    = Hallmarkdesign
organizationalUnitName      = BlazeMeter
commonName                  = localhost
commonName_max              = 64
commonName_default          = localhost

[ req_ext ]
subjectAltName = @alt_names

[alt_names]
DNS.1   = localhost
```
