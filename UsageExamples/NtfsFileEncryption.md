- to use GIDS/PIV/outher smartcard for certificated for signing files using NTFS EFS
    - type 'manage file encryption certificates' (or ControlPanel -> User Accounts -> Manage File Encryption Certificated - sidebar)
    - select the certificate that is on your smartcard
    - when trying to open encypted file, Windows should ask you for your pin (smartcard needs to be inserted when trying to open the file)
        - you can also do `cipher /flushcache` to remove cached encryption keys
    - trying to export the key (backup) will result in a `Encrypting File System` message `The certificate or key is not available for export on this machine. Error code: 8009000b`
    - It will not ask for a pin again when opening encrypted file after `cipher /flushcache`. Only if you remove the smartcard and insert it again