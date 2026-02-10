---
layout: post
title: "Troubleshooting Cryptic NetScaler Internal Error When Installing or Replacing a Certificate Key Pair"
date: 2018-09-15 00:00:00 -0600
---

## Abstract

The purpose of this blog is to assist with troubleshooting the "Internal Error" error when installing or replacing certificate key pairs.

## Let's dive in

No matter how many times you click the "install" button the certificate key pair just will not install. Sound familiar? I've encountered this issue, many, many, many times. The root cause is always one of the following:

1. The certificate or private key are formatted improperly.
2. The certificate does not correspond to the correct private key.

Let's go into each of these.

## 1. The certificate or private key are formatted improperly

Sometimes extra spaces can throw off the import of a certificate/key pair. Personally, I try to put all certificates into a PEM format. NetScaler OpenSSL can be used to accomplish this. For keys use the following command. Use the `--inform DER` option for DER encoded keys.

```bash
openssl rsa -in <key> -out <key.pem>
```

Along the same lines, use the following command to reformat the certificate file:

```bash
openssl x509 -in <cert> -out <cert.pem>
```

Try installing the certificate/key pair again. If that doesn't work then...

## 2. The certificate does not correspond to the correct private key

I encounter this much more than people would think. If the certificate was generated from a CSR that was not signed by the correct private key, then the certificate will never install. Ever. How can we check if the certificate was generated from a CSR that was signed by the correct key? By checking the modulus of the private key, CSR, and certificate of course! The modulus of the private key and certificate must be 100% exact matches. For an explanation as to why this is the case, please see [RSA cryptosystem on Wikipedia](https://en.wikipedia.org/wiki/RSA_(cryptosystem)).

To view the modulus of a private key, use the following command:

```bash
openssl rsa -modulus -noout -in <key>
```

To view the modulus of a CSR:

```bash
openssl req -modulus -noout -in <csr>
```

To view the modulus of a certificate:

```bash
openssl x509 -modulus -noout -in <cert>
```

Below is a sample output for what the modulus will look like for my public certificate.

![NetScaler modulus output]({{ site.baseurl }}/assets/images/troubleshooting-cryptic-netscaler/netscaler_modulus.png)

## FIPS NetScaler Appliances

So what happens if you can't access your private key to run OpenSSL commands (FIPS NetScaler appliances)? Remember that the Key, CSR, and Cert MUST all use the same modulus if they are related. With this theory in mind, you can generate a bogus CSR off of the FIPS key to see what the modulus should be for the public certificate. If you generate a CSR off of a FIPS box and the modulus for that CSR does not match the modulus for the Public Certificate that was returned to you, then that certificate did not use a CSR that was generated off of that FIPS key.

I hope this information has helped. The OpenSSL commands I have listed are only a handful of my favorites. For a really good cheat sheet on useful commands (I have it bookmarked), check out the link below.

[Most Common OpenSSL Commands - SSL Shopper](https://www.sslshopper.com/article-most-common-openssl-commands.html)

Let me know if you have questions in the comments below!

BC
