# Certificates
Certificates are generally used to indicate some kind of trust mechanism using a combination of cryptographic techniques.
Speaking in the context of web services any mention of certificates refers to certificates and/or a certificate chain required by a TLS service 99% of the time.
Other uses of certificates in web applications may refer to digitally signed documents or part of a key exchange required for messaging, such as key sharing for encrypted email.
This document will only cover certificates required for a TLS server, such as HTTPS.

## Trust
Before talking about anything technical let's start at the highest level.
Certificates solve a security problem not a technology problem.
Certificates are only required because secure web servers require them, but otherwise are completely unnecessary for encryption or service operation.
For example attempting to access a secure website with an invalid or missing certificate causes a web browser, or other application, to display a warning page, however the webserver will continue to function all the same.

In the common web scenario an anonymous user connects to a server with HTTP.
HTTP 1.1 is completely session-less, see my abridged description of [web transmission schemes](./Performance.md#http_1.1) for more information.
This means the server has absolutely no idea who the anonymous client is and the client only guesses at the server identity because of its domain name.
DNS spoofing allows attackers to violate domain names as forms of identity verification with very little effort.
We require more to ensure the server is exactly who they claim to be before we sacrifice all our financial and privacy details to a stranger.
Certificates provide the trust that other technologies are incapable of providing.

### Certificate Chain
Certificates use a process to ensure identity and manage risk, a process called a certificate chain.
All certificates must be signed by an issuing certificate.
Certificate chains start with a self-signed root certificate, because they are at the start of the chain there is nobody higher to sign them, so they sign themselves.
To ensure the integrity of the chain root certificates remain hidden from all public access and are only accessed by the intermediate certificates they signed.
Root certificates will sign intermediate certificates that then sign other intermediate certificates and/or server certificates.
Server certificates sit at the bottom of the chain.

```
self-signed root -> intermediate -> optional more intermediates -> server certificate
```

When a web browser accesses a secure website they will read the certificate associated with the web server.
The certificate will indicate who signed it and the web browser will then access the signing certificate.
The web browser will continue to climb that certificate chain until it finds a certificate it knows.
Known certificates exist as a part of an operating system certificate store, a browser certificate store, or certificates installed by an administrator/user.

### Certificate Revocation List
Certificate chains exist as a form of trusted authority, but also serve a management function.
It is rare, but occasionally somebody will compromise the encryption techniques that comprise a certificate which can allow an attacker to duplicate a certificate with their own data.
This is called certificate spoofing or SSL spoofing.
Certificate spoofing allows a browser to trust a fraudulent web site as though it were the intended website, which then allows attackers the ability to steal all sensitive data supplied by end users.

The two controls for certificate spoofing are expiration dates and certificate revocation lists.
A certificate is issue for a limited amount of time, typically 2 years.
The limited time span serves to invalidate a certificate before the expected time it takes an attacker to brute-force the cryptographic techniques comprising that certificate.
Let's Encrypt limits the time span of their certificates to only 3 months as a means to further mitigate risks because they issue so many certificates.

In the cases where an attacker does reverse engineer a certificate before the expiry data there must exist a mechanism to invalidate the certificate immediately and ensure everyone is informed.
That mechanism is a [certificate revocation list](https://en.wikipedia.org/wiki/Certificate_revocation_list).
An issuing authority will maintain a list of certificates signed through its chain that are revoked before their expiry date.
When a browser, or other application, attempts to resolve a certificate through the chain revoked certificates are known though the authority's revocation list, which will alert the accessing application the web site must not be trusted.

## Certificates for localhost
I will discuss how to create certificates with [OpenSSL](https://www.openssl.org/) and how to install them in your operating systems and/or browsers.
OpenSSL ships with a variety of other applications such as Node.js and Git Bash and ships natively with many Linux distributions, so it might already be installed on your computer.
Open a terminal and attempt to execute `openssl` to verify if its already installed.

### Creating certificates with OpenSSL
There are numerous ways to do this, so the following steps are the steps that worked for me.

#### Generate the key for a root certificate
`openssl genpkey -algorithm RSA -out ca.key`

This instruction executes the genpkey command which is a generic command for generating keys.
In this case the algorithm is RSA and the key is written to file named `ca.key`.

#### Generate a self-signed root certificate
`openssl req -x509 -key ca.key -days 16384 -out ca.crt -subj "/CN=Your Common Name/O=Your Organization Name"`

The req command generates our self-signed root certificate using the key created in the prior step and the x509 option.
The days option sets the expiry as number of days from now, which in this example is more than 44 years.
The certificate will be written to file named `ca.crt`.
The subject supplies meta data describing the certificate inline without an additional step or an external file.

#### Generate a server key
`openssl genpkey -algorithm RSA -out server.key`

Generates another key file exactly like our first step, but to a file named `server.key`.

#### Generate a signing request
`openssl req -new -key server.key -out server.csr -subj "/CN=Your Common Name/O=Your Organization Name"`

A signing request is a file with the instructions required to sign a certificate using an existing certificate/key combination.
The CSR uses the server key recently created, which will be needed to decode the server certificate created in the next step.

#### Generate and sign the server certificate
`openssl x509 -req -in server.csr -days 16384 -out server.crt -CA ca.crt -CAkey ca.key -CAcreateserial -extfile details.cnf`

This step reads in the signing request, root certificate, root certificate key, and writes a server certificate signed by the root certificate to file named `server.crt`.
Just like the root certificate this one sets an expiry more than 44 years out.
This step makes use of an additional configuration file `details.cnf`.
I found and configured this file about a year and a half ago and have no idea what it means, but this step will not work without it.
I am writing the config file here:

```
#
# OpenSSL example configuration file.
# This is mostly being used for generation of certificate requests.
#

# This definition stops the following lines choking if HOME isn't
# defined.
HOME			= .
RANDFILE		= $ENV::HOME/.rnd

# Extra OBJECT IDENTIFIER info:
#oid_file		= $ENV::HOME/.oid
oid_section		= new_oids

# To use this configuration file with the "-extfile" option of the
# "openssl x509" utility, name here the section containing the
# X.509v3 extensions to use:
# extensions		= 
# (Alternatively, use a configuration file that has only
# X.509v3 extensions in its main [= default] section.)

[ new_oids ]

# We can add new OIDs in here for use by 'ca', 'req' and 'ts'.
# Add a simple OID like this:
# testoid1=1.2.3.4
# Or use config file substitution like this:
# testoid2=${testoid1}.5.6

# Policies used by the TSA examples.
tsa_policy1 = 1.2.3.4.1
tsa_policy2 = 1.2.3.4.5.6
tsa_policy3 = 1.2.3.4.5.7

####################################################################
[ ca ]
default_ca	= CA_default		# The default ca section

####################################################################
[ CA_default ]

dir		        = ./demoCA       # Where everything is kept
certs		    = $dir/certs     # Where the issued certs are kept
crl_dir		    = $dir/crl       # Where the issued crl are kept
database	    = $dir/index.txt # database index file.
#unique_subject	= no             # Set to 'no' to allow creation of
					             # several certs with same subject.
new_certs_dir	= $dir/newcerts	 # default place for new certs.

certificate	= $dir/cacert.pem 	# The CA certificate
serial		= $dir/serial 		# The current serial number
crlnumber	= $dir/crlnumber	# the current crl number
					# must be commented out to leave a V1 CRL
crl		= $dir/crl.pem 		# The current CRL
private_key	= $dir/private/cakey.pem# The private key
RANDFILE	= $dir/private/.rand	# private random number file

x509_extensions	= usr_cert		# The extensions to add to the cert

# Comment out the following two lines for the "traditional"
# (and highly broken) format.
name_opt 	= ca_default		# Subject Name options
cert_opt 	= ca_default		# Certificate field options

# Extension copying option: use with caution.
# copy_extensions = copy

# Extensions to add to a CRL. Note: Netscape communicator chokes on V2 CRLs
# so this is commented out by default to leave a V1 CRL.
# crlnumber must also be commented out to leave a V1 CRL.
# crl_extensions	= crl_ext

default_days	= 365			# how long to certify for
default_crl_days= 30			# how long before next CRL
default_md	= default		# use public key default MD
preserve	= no			# keep passed DN ordering

# A few difference way of specifying how similar the request should look
# For type CA, the listed attributes must be the same, and the optional
# and supplied fields are just that :-)
policy		= policy_match

# For the CA policy
[ policy_match ]
countryName		= match
stateOrProvinceName	= match
organizationName	= match
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional

# For the 'anything' policy
# At this point in time, you must list all acceptable 'object'
# types.
[ policy_anything ]
countryName		= optional
stateOrProvinceName	= optional
localityName		= optional
organizationName	= optional
organizationalUnitName	= optional
commonName		= supplied
emailAddress		= optional

####################################################################
[ req ]
default_bits		= 2048
default_keyfile 	= privkey.pem
distinguished_name	= req_distinguished_name
attributes		= req_attributes
x509_extensions	= v3_ca	# The extensions to add to the self signed cert

# Passwords for private keys if not present they will be prompted for
# input_password = secret
# output_password = secret

# This sets a mask for permitted string types. There are several options. 
# default: PrintableString, T61String, BMPString.
# pkix	 : PrintableString, BMPString (PKIX recommendation before 2004)
# utf8only: only UTF8Strings (PKIX recommendation after 2004).
# nombstr : PrintableString, T61String (no BMPStrings or UTF8Strings).
# MASK:XXXX a literal mask value.
# WARNING: ancient versions of Netscape crash on BMPStrings or UTF8Strings.
string_mask = utf8only

# req_extensions = v3_req # The extensions to add to a certificate request

[ req_distinguished_name ]
countryName			= Country Name (2 letter code)
countryName_default		= AU
countryName_min			= 2
countryName_max			= 2

stateOrProvinceName		= State or Province Name (full name)
stateOrProvinceName_default	= Some-State

localityName			= Locality Name (eg, city)

0.organizationName		= Organization Name (eg, company)
0.organizationName_default	= Internet Widgits Pty Ltd

# we can do this but it is not needed normally :-)
#1.organizationName		= Second Organization Name (eg, company)
#1.organizationName_default	= World Wide Web Pty Ltd

organizationalUnitName		= Organizational Unit Name (eg, section)
#organizationalUnitName_default	=

commonName			= Common Name (e.g. server FQDN or YOUR name)
commonName_max			= 64

emailAddress			= Email Address
emailAddress_max		= 64

# SET-ex3			= SET extension number 3

[ req_attributes ]
challengePassword		= A challenge password
challengePassword_min		= 4
challengePassword_max		= 20

unstructuredName		= An optional company name

[ usr_cert ]

# These extensions are added when 'ca' signs a request.

# This goes against PKIX guidelines but some CAs do it and some software
# requires this to avoid interpreting an end user certificate as a CA.

basicConstraints=CA:FALSE

# Here are some examples of the usage of nsCertType. If it is omitted
# the certificate can be used for anything *except* object signing.

# This is OK for an SSL server.
# nsCertType			= server

# For an object signing certificate this would be used.
# nsCertType = objsign

# For normal client use this is typical
# nsCertType = client, email

# and for everything including object signing:
# nsCertType = client, email, objsign

# This is typical in keyUsage for a client certificate.
# keyUsage = nonRepudiation, digitalSignature, keyEncipherment

# This will be displayed in Netscape's comment listbox.
nsComment			= "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

# This stuff is for subjectAltName and issuerAltname.
# Import the email address.
# subjectAltName=email:copy
# An alternative to produce certificates that aren't
# deprecated according to PKIX.
# subjectAltName=email:move

# Copy subject details
# issuerAltName=issuer:copy

#nsCaRevocationUrl		= http://www.domain.dom/ca-crl.pem
#nsBaseUrl
#nsRevocationUrl
#nsRenewalUrl
#nsCaPolicyUrl
#nsSslServerName

# This is required for TSA certificates.
# extendedKeyUsage = critical,timeStamping

[ v3_req ]

# Extensions to add to a certificate request

basicConstraints = CA:FALSE
keyUsage = nonRepudiation, digitalSignature, keyEncipherment

[ v3_ca ]


# Extensions for a typical CA


# PKIX recommendation.

subjectKeyIdentifier=hash

authorityKeyIdentifier=keyid:always,issuer

basicConstraints = critical,CA:true

# Key usage: this is typical for a CA certificate. However since it will
# prevent it being used as an test self-signed certificate it is best
# left out by default.
# keyUsage = cRLSign, keyCertSign

# Some might want this also
# nsCertType = sslCA, emailCA

# Include email address in subject alt name: another PKIX recommendation
# subjectAltName=email:copy
# Copy issuer details
# issuerAltName=issuer:copy

# DER hex encoding of an extension: beware experts only!
# obj=DER:02:03
# Where 'obj' is a standard or added object
# You can even override a supported extension:
# basicConstraints= critical, DER:30:03:01:01:FF

[ crl_ext ]

# CRL extensions.
# Only issuerAltName and authorityKeyIdentifier make any sense in a CRL.

# issuerAltName=issuer:copy
authorityKeyIdentifier=keyid:always

[ proxy_cert_ext ]
# These extensions should be added when creating a proxy certificate

# This goes against PKIX guidelines but some CAs do it and some software
# requires this to avoid interpreting an end user certificate as a CA.

basicConstraints=CA:FALSE

# Here are some examples of the usage of nsCertType. If it is omitted
# the certificate can be used for anything *except* object signing.

# This is OK for an SSL server.
# nsCertType			= server

# For an object signing certificate this would be used.
# nsCertType = objsign

# For normal client use this is typical
# nsCertType = client, email

# and for everything including object signing:
# nsCertType = client, email, objsign

# This is typical in keyUsage for a client certificate.
# keyUsage = nonRepudiation, digitalSignature, keyEncipherment

# This will be displayed in Netscape's comment listbox.
nsComment			= "OpenSSL Generated Certificate"

# PKIX recommendations harmless if included in all certificates.
subjectKeyIdentifier=hash
authorityKeyIdentifier=keyid,issuer

# This stuff is for subjectAltName and issuerAltname.
# Import the email address.
# subjectAltName=email:copy
# An alternative to produce certificates that aren't
# deprecated according to PKIX.
# subjectAltName=email:move

# Copy subject details
# issuerAltName=issuer:copy

#nsCaRevocationUrl		= http://www.domain.dom/ca-crl.pem
#nsBaseUrl
#nsRevocationUrl
#nsRenewalUrl
#nsCaPolicyUrl
#nsSslServerName

# This really needs to be in place for it to be a proxy certificate.
proxyCertInfo=critical,language:id-ppl-anyLanguage,pathlen:3,policy:foo

####################################################################
[ tsa ]

default_tsa = tsa_config1	# the default TSA section

[ tsa_config1 ]

# These are used by the TSA reply generation only.
dir		= ./demoCA		# TSA root directory
serial		= $dir/tsaserial	# The current serial number (mandatory)
crypto_device	= builtin		# OpenSSL engine to use for signing
signer_cert	= $dir/tsacert.pem 	# The TSA signing certificate
					# (optional)
certs		= $dir/cacert.pem	# Certificate chain to include in reply
					# (optional)
signer_key	= $dir/private/tsakey.pem # The TSA private key (optional)
signer_digest  = sha256			# Signing digest to use. (Optional)
default_policy	= tsa_policy1		# Policy if request did not specify it
					# (optional)
other_policies	= tsa_policy2, tsa_policy3	# acceptable policies (optional)
digests     = sha1, sha256, sha384, sha512  # Acceptable message digests (mandatory)
accuracy	= secs:1, millisecs:500, microsecs:100	# (optional)
clock_precision_digits  = 0	# number of digits after dot. (optional)
ordering		= yes	# Is ordering defined for timestamps?
				# (optional, default: no)
tsa_name		= yes	# Must the TSA name be included in the reply?
				# (optional, default: no)
ess_cert_id_chain	= no	# Must the ESS cert id chain be included?
				# (optional, default: no)

[ x509_ext ]
basicConstraints = CA:FALSE
subjectKeyIdentifier = hash
authorityKeyIdentifier = keyid,issuer
subjectAltName = DNS:localhost
```

### Installing the Certificate to Windows 10 using PowerShell

#### Current User Trust Store
Windows will require an administrator terminal to add certificates to any of the machine trust stores, however a certificate may be added to the current user store without administrative access.
The result is the same, so I always just add certificates to the current user store.
The address to the current user trust store is `Cert:\CurrentUser\Root`.

#### Remove Old Certificates
Before adding certificates deleting existing certificates containing the same meta-data, because new certificates will no overwrite existing certificates of the same name.

`get-childItem Cert:\CurrentUser\Root -DnsName *your-certificate-organization-name* | Remove-Item -Force`

I have found that when executing that command directly from the terminal it will delete all certificates matching the supplied criteria.
I have also found that when executing that command as a child process in Node.js it will only remove the first certificate in the returned list.
In Node I then check for matching certificates every time I delete a certificate using this command: `get-childItem Cert:\CurrentUser\Root -DnsName *your-certificate-organization-name*`
Using a recursive loop I continue deleting certificates and checking for matching certificates until no more matching certificates are returned.

#### Installing the Certificates
I install both the server certificate and the root certificate into the trust store.
This way both certificates are trusted and the certificate chain is easily resolved.

`Import-Certificate -FilePath ca.crt -CertStoreLocation 'Cert:\CurrentUser\Root'`
`Import-Certificate -FilePath server.crt -CertStoreLocation 'Cert:\CurrentUser\Root'`

#### Firefox
At this point you have a custom created certificate chain that is trusted for the current user across Windows 10 and all installed web browsers, except Firefox.
Firefox has its own internal certificate trust store.
There are steps to access and modify the Firefox certificate store just like accessing the Windows trust stores, but I find it more convenient to tell Firefox to use the Windows trust stores.

1. Open Firefox and in the address bar go to `about:config`.
1. At the warning screen accept the risk and continue.
1. Search for `security.enterprise_roots.enabled` and double-click it to set the value to true.
1. Close out all instances of Firefox.

### Installing the Certificate to Debian and Ubuntu Linux Distributions
All steps in Linux require use of `sudo`.

Ubuntu/Debian use a staging location for certificates at `/usr/local/share/ca-certificates`.
For ease of management I recommend creating a directory there:
`sudo mkdir /usr/local/share/ca-certificates/extra`

Once the directory is created copy the two certificate files there:
`sudo cp ca.crt /usr/local/share/ca-certificates/extra`
`sudo cp server.crt /usr/local/share/ca-certificates/extra`

To install the certificates execute the command:
`sudo update-ca-certificates --fresh`

This command migrates certificates from the staging area to `/etc/ssl/certs/` and updates the manifest at `/etc/ssl/certs/ca-certificates.crt`.
The fresh option performs an audit such that custom certificates no longer present in the staging area are deleted and any new certificates at the staging area installed.

At this point the certificates are fully installed in Ubuntu/Debian, but the browsers will not trust them.
In Linux all browsers contain their own internal certificate store, just as Windows Firefox.
We can fix this with a script.
Save the following to a file named `linux.sh`:

```
#!/bin/bash

### Script installs root.cert.pem to certificate trust store of applications using NSS
### (e.g. Firefox, Thunderbird, Chromium)
### Mozilla uses cert8, Chromium and Chrome use cert9

###
### Requirement: apt install libnss3-tools
###


###
### CA file to install (CUSTOMIZE!)
###

certfile="./share-file-ca.crt"
certname="share-file-ca"


###
### For cert8 (legacy - DBM)
###

for certDB in $(find ~/ -name "cert8.db")
do
    certdir=$(dirname ${certDB});
    certutil -A -n "${certname}" -t "TCu,Cu,Tu" -i ${certfile} -d dbm:${certdir}
done


###
### For cert9 (SQL)
###

for certDB in $(find ~/ -name "cert9.db")
do
    certdir=$(dirname ${certDB});
    certutil -A -n "${certname}" -t "TCu,Cu,Tu" -i ${certfile} -d sql:${certdir}
done
```

The `certutil` command in those instructions comes from a package named libnss3-tools, so run these two commands to execute this script:

```
sudo apt install libnss3-tools
sudo chmod +x ./linux.sh
sudo ./linux.sh
```

#### All Ubuntu/Debian commands
```
sudo mkdir /usr/local/share/ca-certificates/extra
sudo cp ca.crt /usr/local/share/ca-certificates/extra
sudo cp server.crt /usr/local/share/ca-certificates/extra
sudo update-ca-certificates --fresh
sudo apt install libnss3-tools
sudo chmod +x ./linux.sh
sudo ./linux.sh
```

The browsers will continue to not trust the new certificates until they are fully closed out and reopened.
The result is exactly like Windows, a custom created certificate chain trusted in the browser.

### Installing the Certificate to Fedora Linux Distributions
I know the steps above for Ubuntu/Debian work because I have tested the steps myself.
I have not tested these steps for Fedora and so they may not work.

The steps for Fedora exactly mirror those for Ubuntu/Debian with differences to locations, trust command, and nss3 package name.

* Fedora certificate staging area - `/etc/pki/ca-trust/source/anchors`
* Fedora certificate installation command - `update-ca-trust`
* Fedora package installation of nss3 - `sudo yum install nss-tools`

### Installing the Certificate to OSX
Likewise, I have not tested this on OSX yet and so this step may not work.

`sudo security add-trusted-cert -d -r trustRoot -k "/Library/Keychains/System.keychain" "ca.crt"`