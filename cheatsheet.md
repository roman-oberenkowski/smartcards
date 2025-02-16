# Cheatsheet
## Fix PIV cards in lab
1. Command for Linux
2. Command for Windows CMD
3. Command for Windows PowerShell
   
Prints card status, certificates and shows remaining PIN tries:
```
yubico-piv-tool -r '' -a status
"C:\Program Files\Yubico\Yubico PIV Tool\bin\yubico-piv-tool.exe" -r "" -a status
& "C:\Program Files\Yubico\Yubico PIV Tool\bin\yubico-piv-tool.exe" --reader="" -a status
```
If `PIN tries left: 0` then
```
yubico-piv-tool -r '' -a unblock-pin -P 12345678 --new-pin 123456
"C:\Program Files\Yubico\Yubico PIV Tool\bin\yubico-piv-tool.exe" -r "" -a unblock-pin -P 12345678 --new-pin 123456
& "C:\Program Files\Yubico\Yubico PIV Tool\bin\yubico-piv-tool.exe" --reader="" -a unblock-pin -P 12345678 --new-pin 123456
```

## Reinstall applets quickly
### GIDS
```
java -jar gp.jar --delete A000000397425446590201 -f
java -jar gp.jar -package A00000039742544659 -applet A000000397425446590201 -create A000000397425446590201
```

```
gp --delete A000000397425446590201 -f
gp -package A00000039742544659 -applet A000000397425446590201 -create A000000397425446590201
```
### PIV
```
java -jar gp.jar --delete A000000308000010000100
java -jar gp.jar -package A000000308000010 -applet A000000308000010000100 -create A000000308000010000100
```

```
gp --delete A000000308000010000100
gp -package A000000308000010 -applet A000000308000010000100 -create A000000308000010000100
```
### FIDO2
```
gp --delete A000000647
gp --install FIDO2.cap --params a7050506182007190400081820091904000a1904000b00
gp -package A000000647 -applet a0000006472f0001 -create a0000006472f0001 --params a7050506182007190400081820091904000a1904000b00
```
### JcAlgTest
```
APP:
4A43416C675465737431
PKG:
4A43416C6754657374
```
### NDEF (1)
```
APP:
D276000085304A434F900001
PKG:
D276000085304A434F9000
```
### IsoApplet
```
APP:
F276A288BCFBA69D34F31001
PKG:
F276A288BCFBA69D34F310
```
## Misc

### Wait for the card and do something
```
while true ; do
  opensc-tool --wait --atr &&
  pkcs11-tool -O && sleep 2 ;
done
```
