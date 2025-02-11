# Cheatsheet
## Fix PIV cards in lab
Prints card status, certificates and shows remaining PIN tries:
```
yubico-piv-tool -r '' -a status
"C:\Program Files\Yubico\Yubico PIV Tool\bin\yubico-piv-tool.exe" -r "" -a status
```
If `PIN tries left: 0` then
```
yubico-piv-tool -r '' -a unblock-pin -P 12345678 --new-pin 123456
"C:\Program Files\Yubico\Yubico PIV Tool\bin\yubico-piv-tool.exe" -r "" -a unblock-pin -P 12345678 --new-pin 123456
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
## Misc
### Wait for the card and do something
```
while true ; do
  opensc-tool --wait --atr &&
  pkcs11-tool -O && sleep 2 ;
done
```
