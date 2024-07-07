---
title: "Commandes utiles pour openssl"
date: 2024-07-07T19:43:17+02:00
draft: false
comment: false
---


## Commandes OpenSSL pour les conversions

Issues du lien suivant: [https://www.httpcs.com/fr/convertisseur-ssl](https://www.httpcs.com/fr/convertisseur-ssl)

### Conversion PEM
#### PEM en DER
```sh
openssl x509 -outform der -in certificate.pem -out certificate.der
```
#### PEM en P7B
```sh
openssl crl2pkcs7 -nocrl -certfile certificate.cer -out certificate.p7b -certfile CACert.cer
```
#### PEM en PFX
```sh
openssl pkcs12 -export -out certificate.pfx -inkey privateKey.key -in certificate.crt -certfile CACert.crt
```
### Conversion DER
#### DER(.crt .cer .der) en PEM
```sh
openssl x509 -inform der -in certificate.cer -out certificate.pem
```
#### DER en CER
```sh
openssl x509 -inform der -in certificat-ssl.der -out certificat-ssl.cer
```
### Conversion P7B
#### P7B en PEM
```sh
openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer
```
#### P7B en PFX
```sh
openssl pkcs7 -print_certs -in certificate.p7b -out certificate.cer
openssl pkcs12 -export -in certificate.cer -inkey privateKey.key -out certificate.pfx -certfile CACert.cer
```
#### P7B en CER
```sh
openssl pkcs7 -print_certs -in certificat-ssl.p7b -out certificat-ssl.cer
```
### Conversion PFX
#### PFX en PEM
```sh
openssl pkcs12 -in certificate.pfx -out certificate.cer -nodes
```
### Conversion CER
#### CER en P7B
```sh
openssl crl2pkcs7 -nocrl -certfile certificat-ssl.cer -certfile cert-intermediaire.cer -certfile cert-racine.cer -out certificat-ssl.p7b
```
#### CER en PFX
```sh
openssl pkcs12 -in certificat-ssl.cer -certfile cert-intermediaire.cer -certfile cert-racine.cer -inkey cle-privee.key -export -out certificat-ssl.pfx
```
#### CER en DER
```sh
openssl x509 -in certificat-ssl.cer -outform der -out certificat-ssl.der
```

## Autres conversions

#### Convertir un certificat et sa clé en pfx:
```sh
openssl pkcs12 -inkey domain.key -in domain.crt -export -out domain.pfx
```