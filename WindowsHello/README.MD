# Windows Hello vs Windows Hello for Business
Standard Windows Hello (consumer version) doesn't not seem to be using any form of TPM-protection (or any other hardware protection) for the private keys.
source:
- https://learn.microsoft.com/en-us/windows/security/identity-protection/hello-for-business/
## Observations:
- after moving the hdd from one laptop to the other (same brand, same model), the Windows Hello's PIN-login became unavailable. User needed to provide the account password. Some documentation seems to suggest, that Windows Hello PIN mechanism uses the TPM (if available).