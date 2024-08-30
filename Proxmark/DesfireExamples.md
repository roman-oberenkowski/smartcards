# Proxmark3 command examples for Desfire cards
(Proxmark3 easy with rrg-other firmware)
## Auth
- No diversification: `hf mfdes auth -n 0 -t AES --kdf <kdf function> -k <Key>`
  - `hf mfdes auth -n 0 -t AES --kdf none -k 000000000000000000000000000000000`

### Diversification
- Get UID using `hf search` or `hf mfdes info` (will not work if random UID is set)
  - like  `UID: 04 17 60 82 8F 11 90` 
  - remove spaces `041760828F1390`
  - that value can be used as key diversification input
- `hf mfdes auth -n 0 -t AES --kdf AN10922 --kdfi <UID> -k <diversification base>`
  - `hf mfdes auth -n 0 -t AES --kdf AN10922 --kdfi 041760828F1390 -k 00000000000000000000000000000000`

### Setup default parameters for auth
- set default auth values using auth --save
```
[usb] pm3 --> hf mfdes auth -n 0  -t AES -k 00000000000000000000000000000000  --kdfi 041760828F1390 --kdf AN10922 --save
[+] PICC selected and authenticated succesfully
[+] Context:
[=] Key num: 0 Key algo: aes Key[16]: 45 C0 74 66 01 A0 2C 37 A3 73 C5 B1 E3 25 A8 CA
[=] KDF algo: an10922 KDF input[7]: 04 17 60 82 8F 13 90
[=] AID: 000000 UID[0]:
[=] Secure channel: ev1 Command set: niso Communication mode: plain
[=] Session key [16]: 01 02 03 04 09 D4 40 37 13 14 15 16 44 AB 9E 67
[=]     IV [16]: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
[+] Context saved to defaults succesfully. You can check them by command hf mfdes default
```
See the saved parameters using
```
[usb] pm3 --> hf mfdes default
[=] -----------Default parameters---------------------------------
[=] Key Num     : 0
[=] Algo        : aes
[=] Key         : 45 C0 74 66 01 A0 2C 37 A3 73 C5 B1 E3 25 A8 CA
[=] KDF algo    : an10922
[=] KDF input   : [7] 04 17 60 82 8F 13 90
[=] Secure chan : ev1
[=] Command set : niso
[=] Comm mode   : plain
```
> NOTE: when using auth ... --save command, the diversified key will be saved as the base and subsequent commands will try to use that key as the diversification base. The solution is to then issue `hf mfdes default --kdf none` to use the saved key as is.

Or just use the default command to set the parameters (see the value difference in 'Key' entry despite the same parameters)
```
[usb] pm3 --> hf mfdes default -n 0  -t AES -k 00000000000000000000000000000000  --kdfi 041760828F1390 --kdf AN10922
[=] -----------Default parameters---------------------------------
[=] Key Num     : 0
[=] Algo        : aes
[=] Key         : 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
[=] KDF algo    : an10922
[=] KDF input   : [7] 04 17 60 82 8F 13 90
[=] Secure chan : ev1
[=] Command set : niso
[=] Comm mode   : plain
```

## List files
that are available publicly
- `hf mfdes lsfiles --aid 123456 --no-auth`

### With auth 
```
hf mfdes lsfiles --aid 123456 -n 0  -t AES -k 00000000000000000000000000000000  --kdfi 041760828F1390 --kdf AN10922
```
- or use the default auth parameters
```
hf mfdes lsfiles --aid 123456 
```

## Get real uid (when Random UID is set)
- `hf mfdes getuid` (of course with auth paramters/or use the default auth parameters)
- or `hf mfdes getuid --aid 123456` when authenticating to the application

## Read publicly accessible application files
- `hf mfdes dump --aid 123456 --no-auth`
