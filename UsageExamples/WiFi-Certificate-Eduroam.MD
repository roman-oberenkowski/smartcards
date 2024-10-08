# WiFi certificate in Windows
Connect to the eduroam while keeping the certificate (and private key) on the smartcard (or other secure medium).
- Windows will not let you select the certificate from the smartcard to connect to the WiFi network.
- You need to configure it manually
- Guide: https://docs.fortinet.com/document/fortiauthenticator/6.5.0/cookbook/905663/configuring-windows-10-wireless-profile-to-use-certificate
- When you want to connect, you need to insert your smartcard, provide the PIN and you're connected.
- You can remove the smartcard. Your connection will still work. 
- After removing the smartcard, you can reconnect whithout the smartcard. If you reboot, the above procedure needs to be conducted again.
