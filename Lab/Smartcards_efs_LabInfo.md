# EFS with smartcards - Lab (only Windows)
## In a nutshell
Lab goal is to decrypt a normal EFS-encrypted file using a smartcard (that contains the private key used for decryption).
- Windows supports GIDS/PIV smartcards out of the box. 
- Everything is very easy and automatic.
- Windows smartcard subsystem ignores the reader's PIN-pad. 
- PIV card pin is 123456
- GIDS card pin is 12345678
- Please do not mix the PIV/GIDS cards
    - if you do - uninstall the smartcard reader (not the card) in the device manager to fix

## Student perspective - standard scenario
1. Decompress the efs-sc.vhd and copy it (for example) to the desktop directory.
2. Attach the virtual disk image. Windows should detect a new 'removable drive' called 'efs-sc'
- If using the Win11-SecurityEN it is enough to double-click the .vhd file to mount the drive.
- If above doesn't work follow the procedure described in 'Manually attach a VHD in Windows' below
3. Double click any .txt file. 
4. Which files can you decrypt?
5. Can the included certificates (in Certificates directory) help you decrypt the files?
6. Can you copy/move the files?
    - Do admin-rights help?
    - Does the source/target location matter?
7. Can you imagine a way to retain access to a file when you give back the card to course instructor?

## Enhanced scenario - more work for course instructor
1. Mount the efs-sc using VirtualBox/VLAB commands on every computer (instructions in a separate file)
2. Tell the students, that they have a limited amount of time to copy the files to their SSD.
    - That simulates a bad actor having access to your pendrive for a short period of time.
    - a pretty well-educated attacker - almost an engineer!
    - you can hint and suggest using commandline tools like `copy`, `xcopy`, `robocopy`, etc...
    - of course they could just use DD or some other imaging software, but that would be only a partial solution (takes long time to image 64gb pendrive).
3. Disconnect the 'pendrives' remotely
4. Ask if anyone managed?
5. Give them the smartcards. (useless without the files)
6. The copy-resistance of EFS (probably) succesfully prevented the 'copy and crack later' attack.
7. Remotely connect the 'pendrives' again and continue with the standard scenario.

## Manually attach a VHD in Windows
Note: In Win11 there is a new 'Disk management' in modern setting, very similar, but please be aware, that currently, there are two places offering the same functionality
- Modern settings path: `System->Storage->Advanced storage settings->Disks and volumes`
1. Win+X
2. 'Disk Management'
3. Action->Attach VHD
    - If grayed-out - left click any disk/partition
4. Browse
5. Select the .vhd file
6. Leave the 'Read-only' checkbox unchecked.
7. OK

## Notes/solutions
Without the correct card/decryption certificate 
- you can delete and move the files (but only within the same 'disk')
- you cannot copy the file 
    - not even within the same disk 
    - not to a different drive
    - but you can do it using the `robocopy /efsraw`, but it requires Administrator rights. It doesn't decrypt the file and copies the cryptogram.
- EFS uses FEK to encrypt the file - one could read the file header, use a smartcard to unwrap the FEK and later decrypt that file - now without the card (hard, but still - possible)
- if Windows already knows, that a particular certificate is located on a smartcard, it will kindly ask a user to insert a specified smartcard.
- Cannot copy a private key from the smartcard, even though you can use it while having the card (and PIN)
- here, the PIN/PUK concepts are very similar to SIM-cards.
- limited PIN tries opens up a scary DoS attack posiibility (by trying the bad PIN and PUK many times - thus locking the card completly)
- one can also observe how the certificates get added automatically by windows whenether a supported smartcard is inserted.
    - you can remove that information by using 'Manage User Certificates' panel
    - can be disabled in settings
    - to manually reread the certificated from the card use `certutil -scinfo` (as admin) and later install the certificate into personal certificate store.
- EFS can be a good way to make it harder for someone to casually copy your encrypted files for later decryption (or brute-force cracking) - it is not that easy to copy a EFS file that you cannot decrypt right now.
- Security by obscurity, or hoping, that the attacker doesn't know a way/solution is a bad idea, but always - it makes it a bit harder for him!
- "Can the included certificates (in Certificates directory) help you decrypt the files?"
    - No, those only contain a public-key, some info and CA's signature - not a private-key!
    - .cer = certificate
    - .p12 = certificate PLUS the private-key

## Caching
- Windows caches the FEK and also the smartcard's PIN.
- flush cipher cache using `cipher /flushcache`
- to flush the PIN-cache - remove and reinsert the smartcard
- In Win11 (not SecurityEN) it has been observed that when removing the smartcard - Windows immediately flushes the cipher's cache.
- In Win10 (not SecuirtyEN) it has beed observed that unlocking and EFS-encrypted directory (containing Thunderbird's profile) with a smartcard (Yubikey 5 PIV) granted access to all files untill reboot.