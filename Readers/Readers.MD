# Readers
Some notes about used readers.
## Readers' lists
- https://ccid.apdu.fr/ccid/supported.html
  - https://ccid.apdu.fr/ccid/section.html
- nfc-tools recomended readers: https://www.wakdev.com/en/apps/nfc-tools-pc-mac/nfc-readers-for-nfc-tools-pc-mac.html
## Omnikey 5321 CL v2
If using default Windows 10 drivers - only the contact interface is available.

### Linux
To use the contact in Linux no additional drivers are required.
To use the contactless interface, the ifdokccid driver is required:
https://aur.archlinux.org/packages/omnikey_ifdokccid

## Omnikey 7121
Combo unit of a fingerprint and smartcard reader.
The reader inside is Omnikey Cardman 3x21. The tested unit was nonfunctional. Always reported "Card Mutes" in EasyReader, no lights blink when inserting/accessing the card. In Linux - "PowerUp failed" and "CmdPowerOn Card absent or mute" in pcscd debug mode.
The fingerprint reader is AES2501B (from AuthenTec). The drivers for Win10 are available from Windows update (as an optional update)
On Linux - fprintd supports that fingerprint reader, but something was wrong and most operations took way too long (like 30s to register a swipe). Tested on Manjaro and Fedora Asahi remix (both behave the same).

## SCM SDI010
### Linux
To use the contact in Linux no additional drivers are required.
To use the contactless interface, the scmccid driver is required:
https://aur.archlinux.org/packages/scmccid

In NixOS - requires a newer version then was provided by scmccid package - source url has to be updated

## ACR122U

### Linux:
seems you need to blacklist some kernel modules: "nfc" "pn533" "pn533_usb" and install acsccid package.

## Omnikey 5022CL
works out of the box on Linux

# Readers with PIN-pads
The PIN cannot be read by any app running on the PC. Couldn't get the PIN-pad to be recognized/used by Windows. On Linux, the PIN-pad can be tested for example using the `pkcs11-tool --login --test` command.
## SPR532
Has a built-in PIN-pad.
### Windows
Works both using Serial and USB interfaces.
### Linux
Works out of the box, PIN-pad works.

## HP (Liteon) keyboard with integrated smartcard reader (SK-2027)
- 03f0:164a HP, Inc HP SC Keyboard - Apollo (Liteon)
- Numpad can also be used as a PIN-pad.
