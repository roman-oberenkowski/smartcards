# Readers
Some notes about used readers.
## Omnikey 5321 CL v2
If using default Windows 10 drivers - only the contact interface is available.

## Omnikey 7121
Combo unit of a fingerprint and smartcard reader.
The reader inside is Omnikey Cardman 3x21. The tested unit was nonfunctional. Always reported "Card Mutes" in EasyReader, no lights blink when inserting/accessing the card. In Linux - "PowerUp failed" and "CmdPowerOn Card absent or mute" in pcscd debug mode.
The fingerprint reader is AES2501B (from AuthenTec). The drivers for Win10 are available from Windows update (as an optional update)
On Linux - fprintd supports that fingerprint reader, but something was wrong and most operations took way too long (like 30s to register a swipe). Tested on Manjaro and Fedora Asahi remix (both behave the same).
