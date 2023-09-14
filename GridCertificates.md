---
title: "wLCG Grid Certificates"
layout: default
nav_order: 7
---

# wLCG Grid Certificates

## Background

After many many years, the German GridKa Certification Authority (CA) at KIT in Karlsruhe will cease operation at 11 June 2023 as the CA cert ends. 
As a successor the [GÉANT](https://geant.org/) Europe's leading collaboration on network and related infrastructure and services for the benefit of research and education offers the service Sectigo Certificate Manager to obtain Grid user certificates starting now.

## Introduction

On order to access global Grid resources, users must hold a valid personal Grid user certificate (authentication) AND users must be member of a Virtual Organization (VO) (authorization).
A valid Grid user certificate is a prerequisite to request membership in a VO. Users usually have one Grid user certificate. Multiple VO membership is possible.[^note]

A Grid user certificate (format X509) consists of a private key with a private password and a certified public key. The private key and the password is exclusively possessed by the user and is NOT known to the Registration Authority (RA) or Certification Authority (CA) at any stage.

A certificate is valid for one (1) year and can be renewed. Users get notified by the CA via email three (3) weeks before the expiration date. It is strongly recommended to renew the certificate before its expiration.

The certificate can/should be copied to all devices/browsers which need it.

[^note]: A Grid user certificate can be seen as an analogy to a passport, whereas the VO membership compares to a visa.

## Authorities

The [GÉANT](https://geant.org/) CA is part of The [International Grid Trust Federation (IGTF)](http://www.igtf.net/){:target="_blank" rel="noopener"}  hence Grid user certificates are accepted by all Grid sites in WLCG. In order to facilitate the request procedure, many institutions in Germany operated Registration Authorities (RA) which take over the necessary paper-work on behalf of the CA.

BUW employees may use their ZIM account to authenticate against GÉANT and request a Grid user certificate. Use the portal [Certificate Manager SSO Check](https://cert-manager.com/customer/DFN/ssocheck/){:target="_blank" rel="noopener"}  to test your account. 

Non-BUW users can not use the portal and should rather check with their home institution officials how to proceed.

## Procedure

**There seem to be problems with the GEANT User Certificates when using those with for example AMI. Please use the alternative way (see 2. "In case of problems") for the time being** 

1. Test your credentials (account_name / password) in [Certificate Manager SSO Check](https://cert-manager.com/customer/DFN/ssocheck/){:target="_blank" rel="noopener"} . If you encounter problems during the certificate request, please screenshot your status information in the SSO check portal for further debugging
2. Request a Grid user certificate in [GEANT User Cert](https://cert-manager.com/customer/DFN/idp/clientgeant){:target="_blank" rel="noopener"} .
   - profile: GÉANT IGTF-MICS Personal
   - choose Enrollment Method: Key Generation
   - Key Type: RSA-4096
   - more detailed information is available on the [DFN FAQ page](https://doku.tid.dfn.de/de:dfnpki:tcsfaq){:target="_blank" rel="noopener"} 
   - **In case of problems**, e.g. if you don't have an employee ZIM account and start the request as a student: Request a "Nutzerzertifikat"/"User certificate" at [pki.pca.dfn.de/](https://pki.pca.dfn.de/grid-root-ca/cgi-bin/pub/pki?cmd=getStaticPage&name=index&RA_ID=165)
3. after a short while the new cert can be downloaded from the page
4. on Linux machines with Grid setups, the certificate and key files are usually placed in the directory ~/.globus/ 
   - Download the `certs.p12` file the User Cert Manager offers you.
   - copy it to `~/.globus/certs.p12`
   - Extract a **certificate** from it with `openssl pkcs12 -clcerts -nokeys  -in certs.p12 -out usercert.pem`
      - the certificate is your passport, you "show" to services to authenticate yourself
   - Extract a **key** from it with `openssl pkcs12 -nocerts -in certs.p12 -out userkey.pem`. 
   - **Make sure the key can only be read by yourself:** `chmod 400 userkey.pem`.
      - the key file is your secret key, that unlocks your certificate. Protect the key with a good password, do not share the key with anyone and backup the key, as it cannot be recovered, if the file got lost or you forgot the password
   - **in addition** import the certs.p12 into your browser:
      - Firefox: Settings → Certificates → View Certificates → Your Certificates → Import
      - Chrome: Settings → Security → Manage Certificates → Import (depends on your operating system, that's why we stongly recommend Firefox!)
5. with your user certificate as "passport" you have to register at your experiment/VO - so that your experiment/VO accepts your certificate and you can use experiment resources.
   - if you have already registered a (previous) certificate, you can add another certificate DN (DN= text string in your certificate, that identifies you) to your experiment account
   -  for **ATLAS**, this [VOMS](https://lcg-voms2.cern.ch:8443/voms/atlas/user/home.action){:target="_blank" rel="noopener"}  server is the central point for your registration
      - more information about Grid Certificate and VO Membership from an ATLAS point of view is available [here](https://confluence.desy.de/display/grid/Grid+User+Certificates+New){:target="_blank" rel="noopener"} 
6. Depending on your browser version, it might be necessary to check in your browser's certificate trust settings → the `The USERTRUST Network` certificate authority needs to be trusted for all operations
   - Firefox:
      - Settings → Certificates → View Certificates → Authorities
in the `The USERTRUST Network` block, select `Edit Trust` if for `GEANT eScience Personal CA` and ensure, that all trust settings are enabled 
   - Chrome:
      - Settings → Security → Manage Certificates → Authorities
search for `org-The USERTRUST Network` and ensure, that for both entries under `⋮` → Edit all trust settings are selected
7. to avoid problems with previous certificates, restart your browser after importing and backing up the certificate and key has been done.
I.e., to quit Firefox or Chrome explicitly select `Quit` or `Exit`, respectively, from the browsers' menues.
   -  if everything works with your new certificate, you can optionally delete your previous certificate

## Technicalities

Technically a new private/public key pair is created with every renewal.

### Finding certificates in Firefox browser

Preferences -> Privacy & Security -> Certificates -> View Certificates -> Your Certificates (-> Backup)

### Extracting the cert Files

Download/export the file either form the browser or directly to the ~/.globus/usercert.p12 directory and make sure to safe the old files. Then use openssl to extract ~/.globus/usercert.pem and ~/.globus/userkey.pem. Have your export passphrase at hand!

```bash
cd ~/.globus
 
 > mv certs.p12 certs.p12.old
 > mv usercert.pem usercert.pem.old
 > mv userkey.pem  userkey.pem.old
 
 > ls -l
-r-------- 1 account group 8213 24. Jan 14:36 certs.p12
-r-------- 1 account group 2611 31. Jan 13:40 certs.p12.old
 
 > openssl pkcs12 -clcerts -nokeys  -in certs.p12 -out usercert.pem
 > openssl pkcs12 -nocerts          -in certs.p12 -out userkey.pem
 
 > ls -l
-r-------- 1 account group 8213 24. Jan 14:36 certs.p12
-r-------- 1 account group 8213 24. Jan 14:38 usercert.pem
-r-------- 1 account group 2611 31. Jan 13:42 userkey.pem
```

### Inspecting Grid user certificates

Please make sure your public (usercert.pem) and private (userkey.pem) keys are:

- in the correct directory,
- have the correct permissions,
- show your DN,
- are valid,
- match each other (have the same md5sum),
- you remember the password.

```bash
> cd ~/.globus
 
 > ls -l
...
-r--r--r-- 1 account group  1728  8. Apr 09:36 usercert.pem
-r-------- 1 account group  2012  8. Apr 09:36 userkey.pem
 
 > openssl x509 -subject -issuer -dates -noout -in usercert.pem
subject= /DC=org/DC=terena/DC=tcs/C=DE/O=Bergische Universitaet Wuppertal/CN=Harenberg, Torsten harenber@uni-wuppertal.de
issuer= /C=NL/O=GEANT Vereniging/CN=GEANT eScience Personal CA 4
notBefore=Apr 20 00:00:00 2022 GMT
notAfter=Apr 20 23:59:59 2023 GMT
 
 > openssl x509 -noout -modulus -in usercert.pem | openssl md5
 > openssl rsa -noout -modulus -in userkey.pem   | openssl md5
 ```
 
