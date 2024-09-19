Yubikey 5 NFC has many interfaces that can be used for different purposes:
## FIDO (FIDO 1.0) U2F (Universal 2-factor) 
- addition for password login, second step
- uses public/private key and challenge-response concepts
- cannot login solely using a security key - no identification is possible
- (unconfirmed) each enrolled site doesn't take storage space on the security key
- (unconfirmed) website has to send all the necessary private-key metadata (handle) to the security key (that later does the signing)

## FIDO2
- passwordless login
- uses public/private key and challenge-response concepts
- generates/uses a key-pair that is unique app/website (two accounts on same site - two key-pairs)
- some data is saved on the security key
- list of enrolled sites can be read using `ykman` (from Yubikey Manager package)
```
PS C:\Program Files\Yubico\YubiKey Manager> ./ykman fido credentials list
Enter your PIN:
Credential ID  RP ID                Username                           Display name
eb9108a5...    apple.com                                               Apple ID
59186fff...    login.microsoft.com  exampleName@outlook.com            Example Name
```
- On Windows you can also use Yubikey's FIDO2 thru the NFC interface to login for example into microsoft account.
- If the passkey is enrolled in Windows Hello, the OS will not give the user the ability to choose external security key.
- If the passkey is not enrolled in Windows Hello, the OS will not give the user the ability to select the Windows Hello provider.
- Using the https://demo.yubico.com/webauthn-technical tool, the Windows hello can be confirmed to be using the TPM.

## PIV, OpenPGP
Yubikey 5 (NFC and non-NFC) supports both PIV and OpenPGP. 
It means, that its usb configuration can include 'smartcard reader'-like interface with one card containing both the PIV and OpenPGP applets, that can be used like a traditional smartcards.
- To make PIV smartcard writeable, you need the YubiKey Minidriver from https://www.yubico.com/support/download/smart-card-drivers-tools/
- Later (replug the device), you may use the following command:
`certutil -csp "Microsoft Base Smart Card Crypto Provider" -importpfx ./certificateWithPrivateKey.p12`
- Using above method, the Secuirty key will not enable the human presence detection (capacitive button)
For more details see dedicated PIV or OpenPGP sections.

## OTP, OATH
supported, not researched yet

## Frequently occuring terms connected with security keys:
See https://fidoalliance.org/specifications/ for a brief introduction to some of the terms.
- FIDO2 - Fast IDentity Online 2
- FIDO (FIDO 1.0) - Fast IDentity Online
	- FIDO U2F - Universal Second Factor (older specification)
	- FIDO UAF - Universal Authentication Framework (rather not widely used; only for mobile platforms)
- WebAuthn (Web Authentication) was designed for FIDO2, but has compatibility mode that allows U2F token to be used with FIDO2 web-server.
	- WebAuthn Relying Party is usually the role of a website in WebAuthn authentication process.
	- WebAuthn Client is usually the browser's role
	- In addition to U2F/FIDO2 features, the WebAuthn also allows for:
		- Use of platform authenticators (cryptographic modules built into the computing devices, such as the Trusted Platform Module (TPM) on a desktop/laptop computer, or a Secure Element built into a mobile phone) to generate and protect private keys
		- Use of external (or roaming) authenticators such as smart cards, Subscriber Identity Module (SIM) cards, or USB-based cryptographic hardware with HID and BLE transport support
- CTAP - Client to Authenticator Protocol - (as a transport layer) can use Bluetooth Low Energy (BLE) to verify physical proximity for a secure cross-device sign-in. Also the NFC and USB are supported.
	- CTAP2 - "CTAP2 allows the use of external authenticators (FIDO Security Keys, mobile devices) for authentication on FIDO2-enabled browsers and operating systems over USB, NFC, or BLE for a passwordless, second-factor or multi-factor authentication experience."
	- CTAP1 - "The new name for FIDO U2F, CTAP1 allows the use of existing FIDO U2F devices (such as FIDO Security Keys) for authentication on FIDO2-enabled browsers and operating systems over USB, NFC, or BLE  for a second-factor experience."
- Passkey - next generation authentication scheme meant to replace traditional passwords. Uses uses public/private key and challenge-response concepts.
	- https://www.yubico.com/resource/passkey-infographic/



 