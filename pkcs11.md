- `pkcs11-tool -M` to show supported mechanisms
- `pksc11-tool --test` to show what is really supported on the card
- `pkcs11-tool --sign --mechanism RSA-X-509` to sign data from stdin using RSA
- `pkcs11-tool -L` to list objects (note the id and use when signing by specifying --id)
## PIV notes
if yubico-piv-tool returns
```
Failed to parse PKCS12 structure. (wrong password?)
Unable to import private key
```
then you know, that your cert is probably in a legacy p12/pfx format
- openssl pkcs12 -info -legacy < legacy_cert.p12                                                                                                                              
- openssl pkcs12 -nodes -legacy < legacy_cert.p12 > /tmp/certbag.pem
- openssl pkcs12 -export -in ~/tmp/certbag.pem > ~/tmp/good.p12
- yubico-piv-tool -r 'OMNIKEY' -a import-key -a import-cert -s 9a -KPKCS12 -i ~/tmp/good.p12

if you get `Unable to import private key` then probably your applet/card configuration is wrong (extended APDUs?)
- default yubikey's PIV PIN is 123456 - if you changed it - i'll ask you for it
