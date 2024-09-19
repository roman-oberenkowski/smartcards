# Phone as a security key (passkey)
## Windows U2F/FIDO/FIDO2
### WebAuthn (for websites)
1. Visit https://demo.yubico.com/webauthn-technical/registration (https://demo.yubico.com/ -> "TRY WEBAUTHN")
1. Click 'Register'
1. Select 'Use another device' (if you have Windows Hello set-up)
1. Select 'iPhone, iPad, or Android device' (or your phone if you already agreed to skip the QR code next time)
1. Scan the QR code on your phone using your camera, or dedicated app like Microsoft Authenticator (not compatible with TOTP-only apps)
1. Allow bluetooth to be turned on.
1. The devies will connect
1. Select where to save the Passkey
#### Notes
- To change name or remove saved device edit the registry key at `HKEY_USERS\S-1-5-20\Software\Microsoft\Cryptography\FIDO\(Account SID)\LinkedDevices`
- You can use different apps to save the passkey on your device, like Google Password Manager (built-in) or Samsung Pass (on Samsung phones) 

### SSH
- `winget install Microsoft.OpenSSH.Beta`
	- see [here](https://www.openssh.com/txt/release-8.2) for details
- `ssh-keygen -t ed25519-sk`
	- or `ssh-keygen -t ecdsa-sk` 
	- more info [here](https://developers.yubico.com/SSH/Securing_SSH_with_FIDO2.html)