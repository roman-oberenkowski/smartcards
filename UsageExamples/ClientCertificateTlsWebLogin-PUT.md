# PUT - client certificate login
1. https://elogin.put.poznan.pl/app/settings - enable Automatic Login
2. Enroll your certificate to a smartcard/Yubikey (can also be installed in system/Mozilla certificate store) 
3. https://elogin.put.poznan.pl/app/login - select certificate login
4. Provide your smartcard PIN
5. You are now logged-in
## Notes:
- Subsequent certificate use will not require the PIN - Windows seem to be caching it.
- Cancelling the certificate selection screen will cause the following to appear in firefox:
```
Secure Connection Failed

An error occurred during a connection to elogin.put.poznan.pl. A PKCS #11 module returned CKR_GENERAL_ERROR, indicating that an unrecoverable error has occurred.

Error code: SEC_ERROR_PKCS11_GENERAL_ERROR
```
In edge:
```
Certificate login error
An error occurred while trying to perform client certificate login. Possible causes:
 certificate login process was cancelled,
 no support for client certificate login in your browser,
 no proper client certificates installed in your browser or operating system,
 use of client certificate issued for an old account name.
If you recently changed your account name, please generate a new certificate
```