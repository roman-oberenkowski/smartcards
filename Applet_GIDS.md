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
