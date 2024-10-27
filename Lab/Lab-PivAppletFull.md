# Intro
The sections below, guide the user how to turn the blank SmartCafe Expert 4.0 into PIV-capable token and how to load user's certificate (and RSA2048 private key) from PUT eLogin service onto the card. Other certificates can also be used with presented procedure.
- The PIV applet used, is Yubico-compatible. That means, that the procedure described in 'Initialize PIV applet' can also be used with every Yubikey that supports PIV (e.g. Yubikey 5).
- At the end of the procedure the card can be used with OpenSC or any other PIV-supporting system. Windows will (by default) automatically imports certificates stored on PIV smartcard and allows to use them without any configuration/drivers needed.
- Used OS: SUSE Leap 15.6 (java and openssl were already instaled by default)
- This is only for educational purposes only, as the guide doesn't include changing all the keys/PINs/secrets, nor does it transition the card into SECURED state. 
- The result is not a secure product. You have been warned.
# Prepare a PIV card - guide
## Prerequsites - smartcard subsystem
- pcscd = PC SmartCard daemon
```
sudo zypper in opensc pcsc-ccid pcsc-lite pcsc-tools -y
sudo systemctl start pcscd
sudo systemctl enable pcscd
```
test pcscd using
```
sudo systemctl status pcscd
pcsc_scan
```
- Insert the card
- The tool should detect 'G&D StarSign Token'
    - If there are no detected readers - `sudo systemctl restart pcscd`

## Intro
The card has no applets installed yet - trying to upload a certificate or a private key would fail.
```
pkcs11-tool -L

Available slots:
Slot 0 (0x0): Lenovo Integrated Smart Card Reader 00 00
  (token not recognized)
```
## Download tool for applets management
- Download the gp.jar from https://javacard.pro/globalplatform/
- `java -jar gp.jar -i` should give you some card info

## Set the default master key
- Should be done only once (when card is taken out of the box)
- Changes the emv-scheme diversified master key to a static known value
- we're doing that to make our life easier later and to not risk bricking the card when someone forgets the '--emv' flag and issues several commands at once.
```
java -jar gp.jar --unlock --emv

Warning: no keys given, using default test key 404142434445464748494A4B4C4D4E4F
Default 404142434445464748494A4B4C4D4E4F set as master key for A000000003000000
```
## List currently installed applets
the following output is normal when the card is new (some errors, because some functions aren't supported, card is not locked):

```
java -jar gp.jar -l

Warning: no keys given, using default test key 404142434445464748494A4B4C4D4E4F
[WARN] GPSession - GET STATUS failed for 80F21000024F0000 with 0x6A81 (Function not supported e.g. card Life Cycle State is CARD_LOCKED)
ISD: A000000003000000 (INITIALIZED)
     Privs:   SecurityDomain, CardLock, CardTerminate, CardReset, CVMManagement
PKG: A0000000035344 (LOADED)
PKG: A0000000035350 (LOADED)
PKG: 534B544558544C4942 (LOADED)
PKG: A000000227011000 (LOADED)
PKG: D276000005AA040360010410 (LOADED)
```

## Install PIV applet
- source: https://github.com/arekinath/PivApplet
- the only version that works with SmartCafe Expert 4.0 card is `PivApplet-0.9.0-jc221-RESLD.cap`
- direct link to 0.9 https://github.com/arekinath/PivApplet/releases/download/v0.9.0/PivApplet-0.9.0-jc221-RESLD.cap

```
java -jar gp.jar --install PivApplet-0.9.0-jc221-RESLD.cap

Warning: no keys given, using default test key 404142434445464748494A4B4C4D4E4F
[WARN] GPSession - GET STATUS failed for 80F21000024F0000 with 0x6A81 (Function not supported e.g. card Life Cycle State is CARD_LOCKED)
CAP loaded
[WARN] GPSession - GET STATUS failed for 80F21000024F0000 with 0x6A81 (Function not supported e.g. card Life Cycle State is CARD_LOCKED)
```
- Check if the applet got installed
```
java -jar gp.jar -l

Warning: no keys given, using default test key 404142434445464748494A4B4C4D4E4F
[WARN] GPSession - GET STATUS failed for 80F21000024F0000 with 0x6A81 (Function not supported e.g. card Life Cycle State is CARD_LOCKED)
ISD: A000000003000000 (INITIALIZED)
     Privs:   SecurityDomain, CardLock, CardTerminate, CardReset, CVMManagement
APP: A000000308000010000100 (SELECTABLE)
     Privs:
PKG: A0000000035344 (LOADED)
PKG: A0000000035350 (LOADED)
PKG: 534B544558544C4942 (LOADED)
PKG: A000000227011000 (LOADED)
PKG: D276000005AA040360010410 (LOADED)
PKG: A000000308000010 (LOADED)
```
- applet's AID: A000000308000010000100
- PKG's AID: A000000308000010

```
pkcs11-tool -L

Available slots:
Slot 0 (0x0): Smart Card Reader 00 00
  token label        : PIV_II
  token manufacturer : piv_II
  token model        : PKCS#15 emulated
  token flags        : login required, rng, token initialized, PIN initialized
  hardware version   : 0.0
  firmware version   : 0.0
  serial num         : 0000000000000000
  pin min/max        : 4/8
```
Applet is now installed and has a default PIN (123456), PUK (12345678), and Card Administration Key (9b). Yubico-piv-tool assumes that the PIN is default if none is provided.

NOTE: the PIV's PIN,PUK,CAK are not connected in any way with the GlobalPlatform master key that we set to the default one before. If PIN and PUK and CAC are forgotten one can just uninstall the applet and install it again.

## (aditional) Reinstall the PIV Applet (for example to remove saved data)
Delete:
```
java -jar gp.jar --delete A000000308000010000100
```
Reinstall from PKG that was preserved on the card:
```
java -jar gp.jar -package A000000308000010 -applet A000000308000010000100 -create A000000308000010000100
```

## (aditional) Completly uninstall (making space for another applet)
```
java -jar gp.jar --uninstall PivApplet-0.9.0-jc221-RESLD.cap
```
or without the CAP file
```
java -jar gp.jar --delete A000000308000010000100
java -jar gp.jar --delete A000000308000010
```

# Initialize PIV applet
## Prerequsites
```
sudo zypper in yubico-piv-tool -y
```
## Procedure
Now we need to put the private key and certificate onto the card.
- You can list available readers using `yubico-piv-tool -r '' -a list-readers` and specify it as a reader in the subsequent commands. The yubikey-piv-tool will not work when the reader name is skipped. The empty string will match any/first reader.
```
yubico-piv-tool -r '' -a list-readers

Lenovo Integrated Smart Card Reader 00 00
```
Directly import the certificate and private key using the command:
```
yubico-piv-tool -r '' -a import-key -a import-cert -s 9a -KPKCS12 -i certificate.p12

Enter Password:
Failed to parse PKCS12 structure. (wrong password?)
Unable to import private key
```
If it fails like above please follow the next section to fix the certificate format.


## Prepare certificate and private key
The certificate from elogin uses legacy cryptography algorithm (RC2-40-CBC). The import fails.
### Investigate using openssl:
```
openssl pkcs12 -info < certificate.p12

Enter Import Password: 
MAC: sha1, Iteration 2048
MAC length: 20, salt length: 8
PKCS7 Encrypted data: pbeWithSHA1And40BitRC2-CBC, Iteration 2048
Error outputting keys and certificates
40878257D97F0000:error:0308010C:digital envelope routines:inner_evp_generic_fetch:unsupported:crypto/evp/evp_fetch.c:341:Global default library context, Algorithm (RC2-40-CBC : 0), Properties ()
```
After legacy support enable flag it works:
```
openssl pkcs12 -info -legacy < certificate.p12

Enter Import Password:
MAC: sha1, Iteration 2048
MAC length: 20, salt length: 8
PKCS7 Encrypted data: pbeWithSHA1And40BitRC2-CBC, Iteration 2048
Certificate bag
Bag Attributes
    localKeyID: 25 25 E4 6C 33 72 21 A9 C8 08 2D 98 D9 6B 70 C6 66 71 98 57
    friendlyName: test.name@put.poznan.pl
subject=C = PL, O = Poznan University of Technology, CN = test.name@put.poznan.pl
issuer=DC = pl, DC = edu, DC = put, DC = local, CN = EMPLOY-CA
-----BEGIN CERTIFICATE-----
MIIHUTCCBjmgAwXBAgITeQAA7yx1dl59ZJECcAAFAADrLDANBgkqhkiG9w0BAQsF
```

### Convert to a newer format:
"Unpack" the private key and the certificate.
```
openssl pkcs12 -nodes -legacy -in certificate.p12 > decryptedPrivateKeyAndCertificate.pem
```
"Pack" the private key and the certificate into newer format
```
openssl pkcs12 -export -in decryptedPrivateKeyAndCertificate.pem > newFormatCertificate.p12
```
Override the privateKey file (plain text) that was created above (openssl doesn't support loading both certificate and private key at once from stdin)
```
shred -u decryptedPrivateKeyAndCertificate.pem
```

## Write new certificate and private key to the card
```
yubico-piv-tool -r '' -a import-key -a import-cert -s 9a -KPKCS12 -i newFormatCertificate.p12
```
confirm that the procedure succeeded by using:
```
yubico-piv-tool -r '' -a status
pkcs11-tool -L # lists slots/cards
pkcs11-tool -O # lists card's objects
pkcs11-tool --login --test
```
## (cleanup) Remove the certificate from the smartcard
```
yubico-piv-tool -r '' -a delete-certificate -s 9a
```
WARNING: the private key is not deleted in the process. See next section
## (cleanup) Override the current private key with a new one
```
# after providing PIN, input 'test', press enter, press CTRL+D
yubico-piv-tool -r '' -a verify-pin --sign  -s 9a

# generate new key in the same slot
yubico-piv-tool -r '' -a generate -s 9a -A RSA2048

# after providing PIN, input 'test', press enter, press CTRL+D
yubico-piv-tool -r '' -a verify-pin --sign  -s 9a
```
Compare if both signatures are the same. If not - the key got overwritten

## Additional commands
### Change PIN
```
yubico-piv-tool -r '' -a change-pin
```
### Unblock (reset) PIN using PUK
```
yubico-piv-tool -r '' -a unblock-pin
```
### Complete factory reset
- https://support.yubico.com/hc/en-us/articles/360013645480-Resetting-the-Smart-Card-PIV-Application-on-Your-YubiKey
    - tested, doesn't work - "Reset failed, are pincodes blocked?" resets some info, but the applet gets unusable
- The workaround is to remove and install an applet again
