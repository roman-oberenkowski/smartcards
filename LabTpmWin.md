# Lab TPM Windows
## Warmup
1. Boot Win11-Security-EN
1. Go to [webauthn demo site](https://webauthn.me/)
1. Select a desired username and press register
1. New window will pop-up (if minimized - find it on the taskbar)
1. What does it say?
1. What kind of hardware is required (which protocol has to be supported?, how to find such device when shopping? by which name/term?)
## Windows hello (FIDO2)
Here: <win hello introduction>
1. Go to Settings (WIN+I shortcut)
1. Accounts -> Sign-in options
1. PIN (Windows Hello) -> Set up
1. Why does Windows asks you for your password?
1. Go to [webauthn demo site](https://webauthn.me/) and try to register again
1. Did it work this time?
1. What was the flow of the data?
1. Try to login again, but provide incorrect pin as many times as possible (simulating a brute-force attack)
1. How many tries did it take you to block Windows hello?
1. How you can reset the retry counter?
1. <TPM lockout, screenshot>
1. Did user's Windows account login by PIN also got blocked?
## TPM virtual smartcard 
Note: TPM virtual smartcard is NOT connected with Win Hello
1. Open device manager (shortcut: WIN+X, M) look for 'Smart Card readers' are there any?
1. Open administrator command prompt and type
  ```
  tpmvscmgr create /name tpm-smartcard-test1 /adminkey random /pin prompt /generate
  ```
1. It asked you for a PIN, please remember it
1. Go back to the device manager, have something changed?
1. Download your student certificate from [elogin](https://elogin.put.poznan.pl/app/certificates/own)
1. Go to [eLogin settings](https://elogin.put.poznan.pl/app/settings) and check 'I want to be able to log in automatically' (using my certificate)
1. Open administrator command prompt and change directory to the one containing your certificate
  ```
  certutil -csp "Microsoft Base Smart Card Crypto Provider" -importpfx your.certificate.p12
  ```
1. Provide your cerificate password and selected PIN.
1. Try to login to eKursy using the certificate (please use Edge browser, firefox is misconfigured on our VM)
3. Did it work? If not, why?
4. If `Your security device is blocked` restart Windows and try again
5. Consult the device manager again to check the state of `tpm-smartcard-test1` is it ok?
6. Open run dialog (WIN+R), type `tpm.msc`
7. Click `Clear the TPM`, read (there won't be another chance), press apprioprate button.
8. The device restarts, how did it ask you to prove user presence? (VM-none)
9. Open device manager again, is something wrong? Why?
10. How to restore it?
