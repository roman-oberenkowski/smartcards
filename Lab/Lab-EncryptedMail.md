# Read encrypted email with smartcard
The goal of this lab is to read an encrypted email using a smartcard's private key to decrypt the message. That includes installing the necessary packages and configuring the email client (no email account required)
Used software:
- Email client: Mozilla Thunderbird (v128.3.1 ESR)
- OpenSUSE Leap (v15.6)
- OpenSC (v0.22)

## Prerequsites - smartcard subsystem
- pcscd = PC SmartCard daemon
```
sudo zypper in opensc pcsc-ccid pcsc-lite pcsc-tools thunderbird
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

## Student perspective:
1. Get one, already initialized smartcard (with a private-key already installed) from your course instructor
1. Install Thunderbird and OpenSC (commands above)
1. Configure Thunderbird to use OpenSC
    1. Open Thunderbird, skip the login prompt (no account is needed)
    1. `Settings` (bottom left corner, gear icon) -> `Privacy and security` (on the left ribbon) -> `Security devices`
    1. `Load`
    1. Module name: `OpenSC` (or a different name, if you wish)
    1. `Browse`
    1. `/usr/lib64/opensc-pkcs11.so` -> `Open`
    1. `OK`
    1. Ensure that you can see your smartcard reader under `OpenSC` entry
1. Disconnect the smartcard from your computer, put it on the desk
1. Open the message that was sent to you by the course instructor
1. Can you read your message?
1. Place the card into the reader, and try again, can you do it now?
1. What was required to use the smartcard's private-key?
1. Please write down the 'secret code' that was included in your message.
1. Please copy the private key from the smartcard to your computer's SSD
1. Give the smartcard back to the course instructor and request another message
1. Were you able to decrypt it?

## Course instructor preparations

### Create CA certs
```
mkdir ~/lab-cert
cd ~/lab-cert
openssl genrsa -out ca.key 2048
openssl req -new -x509 -days 3650 -key ca.key -out ca.crt -extensions v3_ca
```

### Generate private key and certificates (can be different for every smartcard)
```
openssl genrsa -out BSI.key 2048
openssl req -new -key BSI.key -out req_BSI.csr
openssl x509 -req -days 3650 -in req_BSI.csr -CA ca.crt -CAkey ca.key -set_serial 1 -out BSI.crt  -addtrust emailProtection -addrej  
ect clientAuth -addreject serverAuth -trustout  -extensions smime 
```

### Create pkcs12 file (.p12/.pfx) that can be imported onto the card
```
openssl pkcs12 -export -in BSI.crt -inkey BSI.key -out BSI.p12
```

### Import pkcs12 onto the GIDS smartcard
If the card is completly blank (fresh)
1. install the GIDS applet (separate guide)
1. Initialize GIDS using gids-tool (it is recomended to change the PIN, serial and the admin-key):
    ```
    gids-tool -X -v --pin 12345678 --serial-number 00000000000000000000000000000001 --admin-key 000000000000000000000000000000000000000000000001
    ```

Write the certificate and private key using pkcs15-init:
```
pkcs15-init --auth-id 80 --pin 12345678 --verify-pin -f PKCS12 -S BSI.p12 --passphrase "test"
```
- `--passphrase` - the password to .p12 file
- `--pin` - PIN selected during the initialization phase

### Check if the certificate got imported properly
- List certificates (and other objects)
    ```
    pkcs11-tool --login -O
    ```
    - note the id and use when signing by specifying --id
- show supported mechanisms:
    ```
    pkcs11-tool -M
    ``` 
- test what is really supported by the card:
    ```
    pksc11-tool --test
    ```
- to sign data from stdin using RSA:
    ```
    pkcs11-tool --sign --mechanism RSA-PKCS
    pkcs11-tool --sign --mechanism RSA-PKCS --id 01 
    ``` 

### Encrypt a message using a particular certificate
```
echo 'This is a test' > msg_plaintext.txt
openssl smime -encrypt -aes256 -in msg_plaintext.txt -out msg_enc.eml BSI.crt
```
or add the `From`, `To` and `Subject` fields:
```
{ echo -e 'From: someone\nTo: redacted\nSubject: something\nDate: Fri, 1 Sep 2024 09:23:00 +0200'; openssl smime -encrypt -aes256 -in msg_plaintext.txt BSI.crt; } > message_encrypted.eml
```
Add the resulting file as an attachment to an email that will be sent to a student.

## Bonus 
### Unlock the card PIN
Lock the card by providing the wrong PIN 3 times:
```
$ pkcs11-tool --login --test
Using slot 0 with a present token (0x0)
Logging in to "GIDS card (UserPIN)".
Please enter User PIN: 
error: PKCS11 function C_Login failed: rv = CKR_PIN_INCORRECT (0xa0)
Aborting.

$ pkcs11-tool --login --test
Using slot 0 with a present token (0x0)
Logging in to "GIDS card (UserPIN)".
WARNING: user PIN count low
Please enter User PIN: 
error: PKCS11 function C_Login failed: rv = CKR_PIN_INCORRECT (0xa0)
Aborting.

$ pkcs11-tool --login --test
Using slot 0 with a present token (0x0)
Logging in to "GIDS card (UserPIN)".
WARNING: final user PIN try
Please enter User PIN: 
error: PKCS11 function C_Login failed: rv = CKR_PIN_INCORRECT (0xa0)
Aborting.

$ pkcs11-tool --login --test
Using slot 0 with a present token (0x0)
Logging in to "GIDS card (UserPIN)".
WARNING: user PIN locked
Please enter User PIN: 
error: PKCS11 function C_Login failed: rv = CKR_PIN_LOCKED (0xa4)
Aborting.
```

Unlock using:
- WARNING: Entering an incorrect admin key can break your card (can block GIDS on the card forever)
```
$ gids-tool -U --admin-key 000000000000000000000000000000000000000000000001
Using reader with a card: OMNIKEY 5421 (...) 00 00
Administrator authentication successful
Setting the new PIN
Enter User-PIN (4 - 16 characters) : 

Unblock PIN done successfully
```

### Change PIN
To change the PIN if previous value is known use:
```
$ pkcs11-tool -c
Using slot 0 with a present token (0x0)
Please enter the current PIN: 
Please enter the new PIN: 
Please enter the new PIN again: 
PIN successfully changed
```
or 
```
$ pkcs11-tool -c --pin 12345678 --new-pin 87654321
Using slot 0 with a present token (0x0)
PIN successfully changed
```

### FAQ
- pkcs11-tool says CKR_TOKEN_NOT_RECOGNIZED
    ```
    $ pkcs11-tool -c
    Using slot 0 with a present token (0x0)
    error: PKCS11 function C_GetTokenInfo failed: rv = CKR_TOKEN_NOT_RECOGNIZED (0xe1)
    Aborting.
    ```
    - If the GIDS card is not initialized, the pkcs11-tool will not recognize it, you need to initialize the token using gids-tool