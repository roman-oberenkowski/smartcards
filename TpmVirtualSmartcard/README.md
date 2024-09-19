# Virtual smartcard (Windows)
Creates a virtual smartcard using a TPM, that behaves almost exactly as the real, physical one. All smartcard-compatible services will work with TPM virtual smartcard.

- instruction source: https://gist.github.com/ddrinka/2f4bb5f8953ce911f5f5448ad7d2de2f#file-1-ssh-tpm-md

example command to create a smartcard:
```
tpmvscmgr create /name "{SMARTCARD NAME}" /pin prompt /adminkey random /generate /pinpolicy minlen 4 uppercase allowed lowercase allowed specialchars allowed
```
then you need to enroll a certificate onto the newly created virtual smartcard.
## Create a certificate
### request.inf
```
[NewRequest]
Subject = "CN={RECOGNIZABLE SUBJECT NAME}"
Keylength = 2048
Exportable = FALSE
UserProtected = TRUE
MachineKeySet = FALSE
ProviderName = "Microsoft Base Smart Card Crypto Provider"
ProviderType = 1
RequestType = CERT
KeyUsage = 0x80
```
### Create and load the certificate
```
certreq -new -f request.inf
```

## More info
https://learn.microsoft.com/en-us/windows/security/identity-protection/virtual-smart-cards/virtual-smart-card-overview
- private key is only accessible inside the TPM
- private key is never loaded into the main memory
- Requires the PIN for crypto operations (utilizes TPM's anti-hammering capabilities)
- some encrypted data is stored on the hard drive. HDD data loss/intentional erase prevents from using the virtual smartcard, as the encrypted (sealed) private key is lost. Resetting the TPM from the BIOS also makes the virtual smartcard unoperational.
### Anti-hammering differences
Additionally, although the anti-hammering functionality of the virtual smart card is equally secure to that of a physical smart card, virtual smart card users are never required to contact an administrator to unblock the card. Instead, they wait a period of time (depending on the TPM specifications) before they reattempt to enter the PIN. Alternatively, the administrator can reset the lockout by providing owner authentication data to the TPM on the host computer

source: 
- https://learn.microsoft.com/en-us/windows/security/identity-protection/virtual-smart-cards/virtual-smart-card-understanding-and-evaluating

