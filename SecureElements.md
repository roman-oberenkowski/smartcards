## Mifare4Mobile
This seems to be the only way for a developer to **maybe** get their applet installed on the SE.
- https://www.nxp.com/design/design-center/software/rfid-developer-resources/mifare4mobile:MIFARE4MOBILE
- "Specifications are licensed free of charge to anyone who wishes to implement MIFARE4Mobile applet one a SE and agreeing to the terms & conditions (Robustness, security, self certification, Non-assert of IPR,...)" source: [NXP Mifare4Mobile 2016 video 00h27m00s](https://www.nxp.com/company/about-nxp/smarter-world-videos/MIFARE4MOBILE-WB-VIDEO)
- specifications for free, but what about rollout/etc?
## Other SE notes
- good introduction (old - Android ICS times)
    - https://web.archive.org/web/20231121015946/https://nelenkov.blogspot.com/2012/08/android-secure-element-execution.html
    - SE is just a smartcard integrated into the phone (usually in the NFC chip)
    - to install applets on the SE you need the keys (like in normal smartcards)
    - SE keys are not available to the end-user. You cannot install applets on your own
- https://developers.google.com/android/security/android-ready-se/supported-chipsets?ENABLE_SIGNIFICATIO=False#nxp
- https://developers.google.com/android/security/android-ready-se
- https://github.com/seek-for-android
  - Very old (2015)
  - "is for demonstration and test purposes only. Do not use in production environments!"
  - few interesing smartcard uses under [Wiki->Applications](https://github.com/seek-for-android/pool/wiki/GoogleOtpAuthenticator)
    - PCSC through bluetooth ("treat the Android phone like a normal smartcard reader"). Could be taken further - phone as bluetooth-connected smartcard.
  - used a smartcard embedded into microSD card - [SecureFileManager](https://github.com/seek-for-android/pool/wiki/SecureFileManager)
