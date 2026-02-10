---
layout: post
title: "Decrypt all PFX files in a directory"
date: 2018-09-15 12:39:00 -0600
---

I recently needed to decrypt multiple PFX files, extract their keys, and obtain server certificates. Below is a Bash script solution for batch processing PFX files in a directory.

**Key Requirements:**

- All PFX files must share the same password
- Execute the script in the directory containing the PFX files
- Replace "somepass" with the actual password

```bash
for f in *.pfx;
do
 pemout="${f}.pem";
 keyout="${pemout}.key";
 crtout="${pemout}.crt";
 openssl pkcs12 -in $f -out $pemout -nodes -password pass:somepass;
 openssl rsa -in $pemout -out $keyout;
 openssl x509 -in $pemout -out $crtout;
done
```

**What It Does:**

- Converts each PFX file to PEM format
- Extracts the private key
- Extracts the certificate
