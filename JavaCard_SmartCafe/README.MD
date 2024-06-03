# SmartCafe/Sm@rtCafe 
## Info
- Atr: 3B F8 18 00 00 80 31 FE 45 00 73 C8 40 13 00 90 00 92 
- G&D StarSign Token
- Supports contact and contactless interface
- NXP Tag info: SmartMX (P5CD072 or P5CT072) - G&D Sm@rtCafe 
- Info from OpenSC: [link](https://github.com/OpenSC/OpenSC/wiki/StarSign-Token)
- After driver installation - Windows reports it as "SafeSign IC's read-write minidriver for G&D Sm@rtCafe Expert 4.0"
- JavaCard version 2.2.1
- GlobalPlatform version 2.1.1
- JCAlgTest results: [link](https://github.com/crocs-muni/jcalgtest_results/blob/main/javacard/Profiles/results/G%2BD_Smart_Cafe_Expert_4.x_V2_ICFabDate_2007_079_ALGSUPPORT__3b_f8_18_00_00_80_31_fe_45_00_73_c8_40_13_00_90_00_92_(provided_by_PetrS).csv)
- Some benchmarks: A comparison of Java Cards : state-of-affairs 2006 [Link](https://pure.tue.nl/ws/files/1889673/627238.pdf)
  - B_221 seems to be this card

## Drivers/middleware
- A.E.T Europe B.V. SafeSign: [Link](https://aeteurope.com/safesign/)
- SafeSign identity client for ArchLinux: [Link](https://aur.archlinux.org/packages/safesignidentityclient?O=20)
- Drivers for Windows/Ubuntu: [Link](https://safesign.gdamericadosul.com.br/download)
- PKCS#11 for those cards is provided via 'The SafeSign IC PKCS #11 Library' - called ‘libaetpkss.so’ - can be found in: /usr/lib/
- cards have default transport keys diversified using emv (use --emv in GlobalPlatformPro).
## Setup
Cards are not initialized - no applets are initialized/selectable by defalt. One can list/install new applets using [GlobalPlatformPro](https://github.com/martinpaljak/GlobalPlatformPro) or install from the packages provided in the card memory by the manufacturer.
### Keys
If you want to skip writing `--emv` everytime, you can change the card to use the default key without diversification:
```
java -jar gp.jar --emv --unlock
```

### OpenPGP
One can install OpenPGP applet on the card to make it compatible with gpg command (or GUI programs like Kleopatra). More info about OpenPGPCard on [OpenSC's wiki page](https://github.com/OpenSC/OpenSC/wiki/OpenPGP-card) or [GnuPG's page](https://www.gnupg.org/howtos/card-howto/en/smartcard-howto-single.html#id2505522)

The only OpenPGP card applet, that I managed to install was [JCOpenPGP](https://sourceforge.net/projects/jcopenpgp/) (OpenPGP Card v2 implementation)
It seems that the applet needs to be the default to work using `--default` for GlobalPlatformPro (maybe using a different AID would also work?). The JCOpenPGP.jar provided in the release is really a .CAP file and can be used as such.

To install JCOpenPGP applet, please run:
```
java -jar gp.jar --emv --install JCOpenPGP.jar --default
```
- --emv => use emv diversification (skip if you changed the key to the default one)
- --install -> installs an applet from .CAP file
- --default => sets the installed applet to be selected when the card resets - software doesn't need to select the correct AID.

Other OpenPGP implementations:
- OpenPGP-Card https://github.com/FluffyKaon/OpenPGP-Card
  - JavaCard 2.2.2 ("2.2.1 untested")
- SmartPGP https://github.com/github-af/SmartPGP
  - JavaCard 3.0.4
- https://sourceforge.net/projects/openpgp/files/

## To test
### GIDS
- https://github.com/JavaCardOS/GidsApp/tree/master
- https://github.com/vletoux/GidsApplet

### NDEF
- https://github.com/petrs/hotp_via_ndef
- https://github.com/OpenJavaCard/openjavacard-ndef