# GIDS
- Stands for Generic Identity Device Specification 
- Natively supported on Windows (no drivers/middleware needed)
## More info
- https://www.mysmartlogon.com/generic-identity-device-specification-gids-smart-card/
- https://www.mysmartlogon.com/knowledge-base/save-pfxp12-file-smart-card/
### Install applet on the card
- https://github.com/vletoux/GidsApplet
- `gp.exe/java -jar gp.jar --install gids.cap --default`
### Initialize GIDS
After installing an applet, you need to initialize the structure inside the applet itself. Set PIN, serial and admin key.
#### Linux
- Get the card status 
```
gids-tool
#-> [...] Is that a new card?
```
- Now initialize the card. It will ask for necessary information
```    
gids-tool -X -v
gids-tool
#-> [...] Dumping the content of the card [...]
```
- Alternatively you can also provide the data using commandline arguments:
```
gids-tool.exe -X -v --pin 12345 --serial-number 00000000000000000000000000000001
```
#### Windows
- [MySmartLogon's gidsInitialize Tool](http://download.mysmartlogon.com/gids/InitializeGids.exe) 
- gids-tool (from OpenSC project) - commands the same as on Linux. 

### Usage
- test in Windows (source: [link](https://techcommunity.microsoft.com/t5/core-infrastructure-and-security/manually-importing-keys-into-a-smart-card/ba-p/1128396))
```
certutil -csplist
# Note: Entering a PIN is not required for this operation. You can press ESC if you are prompted for a PIN.
certutil -scinfo

certutil -csp "Microsoft Base Smart Card Crypto Provider" -importpfx .\exported.pfx

To find the container value, type certutil.exe -scinfo.
To delete a container, type certutil.exe -delkey -csp "Microsoft Base Smart Card Crypto Provider" "<ContainerValue>".
certutil -scinfo
```
- Some examples of what can be enabled using a GIDS smart card include:
    - Smart card login to Windows
    - TLS client authentication
    - VPN authentication
    - source: [link](https://www.microcosm.com/blog/generic-identity-device-specification-gids-smart-card-authentication)

### Quick GIDS guide for new cards (Windows)
- Install JAVA
- Download [gp.exe](https://github.com/martinpaljak/GlobalPlatformPro/releases)
- Download [GidsApplet.cap](https://github.com/martinpaljak/GlobalPlatformPro/releases)
- Download [InitializeGids.exe](http://download.mysmartlogon.com/gids/InitializeGids.exe)
```
gp --emv --unlock
gp --install GidsApplet.cap
gp --secure-card
gp --lock <new 32hex key>
```
- Initialize the GIDS applet using the tool
  - choose your PIN
  - choose you admin key (used to reset if PIN is entered wrong 3 times in a row)
  - leave the serial as is or change it a little (it is random, different every time you open InitializeGids tool)
  - click initialize card
- Import your certificate and private key onto the card:
```
certutil -csp "Microsoft Base Smart Card Crypto Provider" -importpfx certificate.name@put.poznan.pl.p12
```
- provide the certificate password
- provide the card's PIN (Gids's PIN)
- test using `certutil -scinfo` (admin rights are NOT required)
#### Delete/Update the certificate
- option 1: just import a new certificate to the card (will be two)
- option 2: delete the old one (see below), import a new one (see above)
  - To find the container value, type certutil.exe -scinfo. A value shoul look like this `"certificate.name@put.poznan.pl--03071"`
  - To delete a container (and its certificate/private key), type certutil.exe -delkey -csp "Microsoft Base Smart Card Crypto Provider" "<ContainerValue>".
#### Manually import certificate from the card to the Windows OS
Sometimes the certificate gets imported automatically, sometimes it doesn't. The manual way of doing it is as follows:
- `certutil -scinfo`
- do not provide your PIN - click cancel/escape
- when 'Certificate List' appears select 'More choices' (if needed)
- select the certificate
- 'Click here to view certificate properties'
- 'Install certificate'
- 'Current user'
- 'Place certificate in the following store'
- 'Browse'
- 'Personal'
- 'Next', 'Finish', Done
- You may need to restart your web-browser to register the new certificate and offer it during login
- Certificate has to be valid (not expired) to show-up (at least in Edge) during web-login
