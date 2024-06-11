# smartcards
This repo contains the notes and gathered information about smartcards. Intended to organize the knowledge and give a quick-start for newcommers using selected smartcards.

## Identication of a smartcard
### Using ATR 
- Find your card ATR for example using: [Smartcard Suite](https://sourceforge.net/projects/smartcardsuite/)
- Use [atrParse](https://smartcard-atr.apdu.fr/) to try identify the card based on the aquired ATR.
### Global Platform 
If the card uses GlobalPlatform (more info [here](https://github.com/martinpaljak/GlobalPlatformPro/tree/master/docs/pdfs)) you can use [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro/) to get some technical info about the card
- `gp.exe/java -jar gp.jar -i`

If the keys are default/known, you can list installed applets/packages and identify what is already on the card.
- `gp.exe/java -jar gp.jar -l` or `gp.exe/java -jar gp.jar -l --emv`

To interpret the AIDs on the card you can use 
- https://github.com/petrs/gpalist
- https://emv.cool/2020/12/23/Complete-list-of-application-identifiers-AID/

## Example usage (not tested yet)
- Protect private key
- Decrypt emails (OpenPGP)
- Use GIDS to log-in to Windows
- Pluggable authentication modules - use pam_p11 to authenticate using PKCS#11
- (possible?) connect to eduroam using certificate on the smartcard

## GIDS
- Stands for Generic Identity Device Specification 
- Natively supported on Windows (no drivers/middleware needed)
- https://www.mysmartlogon.com/generic-identity-device-specification-gids-smart-card/
- https://www.mysmartlogon.com/knowledge-base/save-pfxp12-file-smart-card/
### Install applet on the card
- https://github.com/vletoux/GidsApplet
- `gp.exe/java -jar gp.jar --install gids.cap --default`
### Initialize GIDS
After installing an applet, you need to initialize the structure inside the applet itself. Set PIN, serial and admin key.
#### Linux
- Get the card status 
```
gids-tool
#-> [...] Is that a new card?
```
- Now initialize the card. It will ask for necessary information
```    
gids-tool -X -v
gids-tool
#-> [...] Dumping the content of the card [...]
```
- Alternatively you can also provide the data using commandline arguments:
```
gids-tool.exe -X -v --pin 12345 --serial-number 00000000000000000000000000000001
```
#### Windows
- [MySmartLogon's gidsInitialize Tool](http://download.mysmartlogon.com/gids/InitializeGids.exe) 
- gids-tool (from OpenSC project) - commands the same as on Linux. 

### Usage
- test in Windows (source: [link](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/manually-importing-keys-into-a-smart-card/ba-p/1128396))
```
certutil -csplist
certutil -scinfo

certutil -csp "Microsoft Base Smart Card Crypto Provider" -importpfx .\exported.pfx

certutil -scinfo
```