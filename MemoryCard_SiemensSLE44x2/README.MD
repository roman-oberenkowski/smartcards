# Siemens SLE 4432/42 card
- ATR: 3B 04 92 23 10 91
- [Datasheet PDF](http://www.smartcardsupply.com/PDF/DS_sle4432_42_0795.pdf)
- Only contact interface (no contactless - no antenna present)
- Uses ancient two-wire protocol
- Most readers doesn't work with this card (see below)

## Reader compatibility
Card doesn't work in most smartcard readers, bacause it uses ancient two-wire protocol. See [this thread](https://stackoverflow.com/questions/31850578/read-write-data-in-sle4442-smart-card-with-java-commandapdu)

### Tested readers 
Using EasyReader v5.24.00; Reset=>CardMutes=>fail, Reset=>ATR=>OK
- Omnikey 5x21 contact: OK
- Omnikey 4040 PCMCIA: OK
- Omnikey CardMan 3921: OK
  - Note: requires Omnikey 3x21 drivers, doesn't work with the generic driver
- ActivIdentity 3.0 USB: fail
- HP ExpressCard SmartCard: fail
- ActivIdentity/SCM SCR243 PCMCIA: fail
  - Note: it may be possible to interface with memory cards using SCM MCARD API (as mentioned in the reader's datasheet)
- HP (LiteOn) keyboard with integrated SC reader: fail

## More ATR information
```
Power up 1    PC/SC - OMNIKEY CardMan 5x21 0 - 5.00 V - 4.80 MHz
Atr           3B 04 92 23 10 91
Time          531 ms~ 
============================================
3B 04 92 23 10 91
TS=3B (1=Z direct convention)
T0=04
--------------- ISO 7816 - 3 ---------------
General parameters
   Fi (TA1)            : 372 (max 5.0MHz)
   Di (TA1)            : 1
   Min etu             : 372 cycles (Fi/Di)
   VPP param (TB1)     : 5V, 50mA
   N (TC1)             : 0 (Extra Guardtime)
   Def. protocol (TDx) : T=0
   Mode (TA2)          : negotiable
T=0 parameters
   WI (TC2)            : 10 (waiting interval)
   work waiting time   : 9600 etu (atr ones), 1000 ms (3.57MHz)
Historical bytes
   92 23 10 91
--------------- ISO 7816 - 4 ---------------
Category Indicator     : 92 (proprietary value) - ATR is not 7816-4 compliant
```