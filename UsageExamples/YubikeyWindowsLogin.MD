YubikeyWindowsLogin
- https://www.yubico.com/products/computer-login-tools/ (Yubico Login for Windows Configuration Guide)
- Replaces password authentication with 2-factor requiring the password and Yubikey
- Cannot login using only the security key
- Can still login using your fingerprint/PIN that was set-up with Windows Hello
- Uses the OTP (One Time Password) interface on the Yubikey. It needs to be enabled thru YubikeyManager/ykman 
- Using the factory preprogrammed secret (slot 1) didn't work for me. Had to populate the slot 2.
- Doesn't require to join the Domain


