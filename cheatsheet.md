# Cheatsheet
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
