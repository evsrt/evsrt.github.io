Generate a Strong RSA Key Pair:
```bash
openssl genrsa -out keys.pem 4096
```
Create a Certificate Signing Request (CSR):
```bash
openssl req -new -key keys.pem -out cert.csr
```
Generate a Self-Signed Certificate Valid for 10 Years:
```bash
openssl x509 -req -days 3650 -in cert.csr -signkey keys.pem -out cert.cert
```
Convert the Private Key and Certificate to DER Format:
```bash
openssl rsa -inform PEM -in keys.pem -out keys.der -outform DER
openssl x509 -in cert.cert -out cert.der -outform DER
```
Write the Private Key and Certificate to the Hardware Token:
```bash
pkcs11-tool --module /usr/lib64/librtpkcs11ecp.so -l -y privkey -w keys.der --id 10 --label "SSHPrivateKey"
pkcs11-tool --module /usr/lib64/librtpkcs11ecp.so -l -y cert -w cert.der --id 10 --label "SSHCertificate"
```
Remove All Private Keys
```bash
pkcs11-tool --module /usr/lib64/librtpkcs11ecp.so -l --type privkey --delete-object
```
Remove All Certificates
```bash
pkcs11-tool --module /usr/lib64/librtpkcs11ecp.so -l --type cert --delete-object
```
Remove All Public Keys
```bash
pkcs11-tool --module /usr/lib64/librtpkcs11ecp.so -l --type pubkey --delete-object
```
List All Objects on the Token:
```bash
pkcs11-tool --module /usr/lib64/librtpkcs11ecp.so -l -O
```
Extract the Public Key Manually:
```bash
openssl x509 -inform DER -in cert.der -out cert.pem
openssl x509 -in cert.pem -pubkey -noout > pubkey.pem
ssh-keygen -i -m PKCS8 -f pubkey.pem > key.pub
```
