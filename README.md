# Smartcards (+ other security devices)
Note: Work in progress... 

My notes and gathered information about smartcards and other security devices connected with smartcard uses. 
Intended to organize the knowledge and give a quick-start for newcommers using selected smartcards/mechanisms.
Other devices include things like TPM (that can be used like a smartcard using some popular solutions) or security keys (like Yubikey 5) that contain PIV/PGP features.
My goal it to include at least the following information:
- Specifications of the cards I worked with
- List of supported applets for a quick-start
- Short descriptions/guides concerning use-cases with commands for copy/paste
- Some technical terms necessary to understand the topic
- My experiences - like a note of something not working or requiring a particular parameter
- notes for both - Linux and Windows
  
## Identication of a smartcard
### Using ATR 
- ATR = Answer to Reset
- Find your card ATR for example using: 
    - [Smartcard Explorer](https://sourceforge.net/projects/jsmartcard/)
    - [Smartcard Suite](https://sourceforge.net/projects/smartcardsuite/)
    - EasyReader
    - [pcsc_scan.exe](https://pcsc-tools.apdu.fr/)
    - PyApduTool
- Use [atrParse](https://smartcard-atr.apdu.fr/) to try identify the card based on the aquired ATR.
- Note: The ATR that you get using contactless interface (ATS - Answer to Select) will probably be different then ATR from contact interface. You may look up both.
#### Linux
- `opensc-tool -n` to print card type (if supported by OpenSC)
- `pcsc_scan` (from pcsc-tools) to parse the ATR automatically
- `pcsc_scan -r` to list readers
- `systemctl stop pcdsd` and `pcscd -a --debug --foreground` to show all APDU commands being exchanged
- `gscriptor` (from pcsc-tools) as it is quite similar to EasyReader
- `opensc-tool -s "<APDU>"` to send specific APDU directly to the card

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
- Unlocking KeePass with a SmartCard [link](https://zerowidthjoiner.net/2019/02/24/unlocking-keepass-with-a-smartcard-keepass-certificate-shortcut-provider)

## OpenSC on Windows
- https://github.com/OpenSC/OpenSC/wiki/Windows-Quick-Start

## Sniff APDUs on windows
- https://github.com/robbatt/wireshark_iso7816_apdu_dissector/tree/master

## TPM virtual smartcards
- https://learn.microsoft.com/en-us/windows/security/identity-protection/virtual-smart-cards/virtual-smart-card-tpmvscmgr
- https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/setting-up-tpm-protected-certificates-using-a-microsoft/ba-p/1129063

## Misc
- APDU shell in python
    - https://github.com/grakic/apdu
- yubikey usage from the [article](https://blog.ctis.me/2022/12/yubikey-piv-gpg/#why-have-multiple-certificate-authorities) - notes 
    - Use PIV for
        - access control (in buildings)
        - login to OS (PKCS11 - pam_pkcs11)
        - SSL Client Authentication
        - SSH authentication
        - EAP-TLS auth for RADIUS
        - Unlocking bitlocker volumes ([link](https://nathanaelfrey.com/2021/01/09/setting-up-bitlocker-with-yubikey-as-smart-card/))
    - PGP
        - Signing, encrypting, and decrypting
        - Signing Git commits
        - Signing and encrypting emails
        - Signing and encrypting files
        - SSH authentication 
    - U2F/FIDO2
        - SSH authentication
        - Full-Disk Encryption (FDE)


## Smartcard simulation
- https://github.com/OpenSC/OpenSC/wiki/Smart-Card-Simulation

## Windows detection quirks
Microsoft and OpenSC loads the PIV applet before the GIDS applet. You cannot read the GIDS data if the PIV is installed on the same card.

If the PIV applet has been installed on a card (and the card read by Windows) with the same ATR, Windows add a cache entry in the registry in the “Calais” key making the link with the applet type (PIV, GIDS) and the ATR. Delete this entry or change the ATR to allows its load. This entry exists on x64 at 2 places (normal and Wow64 node).
- [source](https://www.mysmartlogon.com/generic-identity-device-specification-gids-smart-card/)
- [Windows Smartcard discovery process](https://learn.microsoft.com/en-us/windows-hardware/drivers/smartcard/discovery-process)
- Cannot use GPG or GIDS, because a card with the same ATR had PIV?
    - go to device manager, uninstall the smartcard reader (NOT smartcard itself) -> problem solved
- You can have a card that has both PGPCard and PIV. Set PGPCard applet as the default and both functions will work (no need to uninstall then). 

- If you get `The smart card requires drivers that are not present on this system.` find your card name in `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Cryptography\Calais\SmartCards\` and remove corresponding key
    - restart smartcard services using `services.msc`
    - it seems that it happens when middleware gets uninstalled, but leaves ATR assosiations behind. Maybe services restart is enough?
## MISC
### Install applet from the CAP already on the card
- ` ./gp.exe --emv --create D2760001240102000000000000010000 --package D27600012400 --applet D2760001240102000000000000010000` (example for JOPenPGP)
- Smartcard tools https://www.idrix.fr/Root/content/section/7/46/
- NFC FIDO Windows (Surface) https://learn.microsoft.com/en-us/surface/surface-pro-nfc
- `ssh-keygen -K` to generate resident keyfiles from the security key to the filesystem (new device)
- https://developers.yubico.com/SSH/Securing_git_with_SSH_and_FIDO2.html
- https://learn.microsoft.com/en-us/entra/identity/authentication/concept-fido2-compatibility
- https://learn.microsoft.com/pl-pl/entra/identity/authentication/how-to-register-passkey-authenticator?tabs=iOS
- good passkey demo test website (reset every 24h) https://passkey.org/
- https://www.idmanagement.gov/implement/scl-ssh/