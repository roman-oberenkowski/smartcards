# SSH lab - smartcard ssh login addition

## Prerequsites - smartcard subsystem (only for CLIENTs)
Students do this on their machines
- pcscd = PC SmartCard daemon
```
sudo zypper in opensc pcsc-ccid pcsc-lite pcsc-tools
sudo systemctl start pcscd
sudo systemctl enable pcscd
```
test pcscd using
```
sudo systemctl status pcscd
pcsc_scan
```
- Insert the card
- The tool should detect 'G&D StarSign Token'
    - If there are no detected readers - `sudo systemctl restart pcscd`
- exit the pcsc_scan using `CTRL+C`

## Client configuration
Edit ~/.ssh/config to add `PKCS11Provider /usr/lib64/opensc-pkcs11.so` line.
Shortcut:
```
 echo 'PKCS11Provider /usr/lib64/opensc-pkcs11.so' >> ~/.ssh/config
```

### Successfule example (card in the reader)
```
student@lab-net-4:~> ssh lab-net-3
******
Have a lot of fun...
Last login: Wed Dec  4 21:29:19 2024 from 150.254.32.133
******
student@lab-net-3:~> ******
If 'bin' is not a typo you can use command-not-found to lookup the package that contains it, like this:
    cnf bin
```
- the stars are the result of our pin-pad enabled smartcard readers (bin matches the wildcard first)

### Failed example (no card in the reader)
```
student@lab-net-4:~> ssh lab-net-3
(student@lab-net-3) Password: 
```

## Server configuration
- SSH servers has to have `PubkeyAuthentication yes` set in `/etc/ssh/sshd_config` (set by default)
- get pubkey from the card:
  ```
  ssh-keygen -D /usr/lib64/opensc-pkcs11.so
  ssh-keygen -D /usr/lib64/opensc-pkcs11.so > cardkeys.pub
  ssh-copy-id -f -i cardkeys.pub lab-net-99
  ```
- change the default password - that way students are forced to use the smartcards to log-in
  ```
  sudo passwd
  sudo passwd student
  ```
  
### Premade server commands (with a list of all card pubkeys):
Course instructor does it on one machine.
```
echo -e '
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC7Sy3FuwbfRSuN3zwCtFQtO6GrraMzHjIQXqDdhi9UignV4Dg9ZxXW31BVKxv2cIsUdXAT3NMn7TGfYzu1pgNPkeKhTYO/BSy51wLldODZH+irWWi5/JSyrtDr7dCtHUTzp0pLEKSF0dw6q1kce56kT2+pMVFP96C2refDxHhxk9z4Naorw7F9RP1momdvJ1ZKIxlXVmB+ArRhWAsIw8rlDbif9Rwh5jE0/CulGFik68rql6UIFYBexzkVPwCTVVXMDke6v2U2P0QqtOnthxAiHmW0m3hW6OBPoSjb/qM76eEaugmP84fJDLWhSsypR+ECACJUroBQItfh4NsF/X3P PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD4bLt0QD7KIXb5JH/ElQbKG2JUYIqk3zaajS7HyExJ3EdjmkO3iqso0gw6wcxkRoNEPtwJk5augau5S7c6n0KmCbSF+evpAB2HQ6eozuV4ptDrVpqchXZto7KZVMTpXo89+Eah5F0+kphu6JxtUln4P1fQQD8FfXFvSk89q5fn5ZzGSnV6Y5Q18FYbfKW7frQbz8YkGJYYs+pYoI2iH7I4aNvwAsi+kU0Tf7cVV0e2OzLqzL47m8HhLweZB8X2pBB/4BDBipvmSUwBSxoQSYQFSPtv48VQvrymRUdm2q4Yy4HctTC7R++Gve+FTJaV1EQsbTfo6SBvZ4GxOtPPY0gJ PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQD0qVJ10ITAvqON9LpbhgjpTBxhmImSHkIQ2GxyxnU+TRk6LzeeuJ52pXZkIBBRr3ZWS4wBy2JH5s3eb1I2jK9YD9SEgRAC1TQu/sGxegZ9aCpxq4rF/Sch4CI7bQlNXbP2jnAGDDiiX9/gutwi2bUCgUuHxUTCf1miFbinz3Tx8b0r90yT1Y6kl3p9s2FNL0aClaiIYmVat+Ng0kmthyw63UV3isS52TA8oOnevJEb/+StAiR56lMSRuBr7KTN3szK3GUAyd0FWiOaAskGmuMCjz7b5Y1Eugs4v4K2PmgF1sxGy9EEypCHzLzjMHSRIBGCFim6gDik7l7gTbahKDGx PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCgunemufi9fpy1Cz34PbRl7LHsWWrqFR4FKmFIVZc2pcrguFqWHb6kF5mEsRK4h5XEkUN35kCAbx/ICjEfthAfXi8Nk4XkaUckb/xMu+jOpDmqG33siaKDvI7tbOutBf2Vx8+c/uvYmAH9vFnXpPORLVb8IEF++W0p8JMr2J7bWwWECTTsKqxMOcXsZs6re0KqTcrxXXPM2v/e599xxGvhu+wFFfr5j5oP06s/5Zih6qEDjqaEXc8JfikeeXzhOwywUsUgGKrd3WnNyBomcYxEg0eFKNILP8RTwC+2JMaeTMwGlQwvMyXOTOIkyeIVL2vYCdB2urU8VvPs1KmCNxx3 PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCcXdNw4Ol6O9gXBTRbFummHCf0CjQr1eluCaPKvt3M6jQFVTtMBeNsmVxrdpF7mGC9Gxek5635XiKToUK8ZUvo46Kxqblj5Gp+/MMydd2SZ4hXKkDgdPCvFWWDJJBH0ZnGYpsdWfwJznK8o0/ZGI6rYg5WwPT847VBA4TzNxoQyxApG5JPrw2t8qY7JVI3qEavtZYzqjo9xCX6A4IPYMZaFBv9YpMF5ol0SGmDh3VIPUqUuzD61nkzDWKEk1/MTrEesDyDspaT2v9KyhfDmIJgZuix0rfL02lFQxCwBzCXyQtw4cKJiUE8iBBxzYT3z2scVqmNg2UlIIp2RESK70JD PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCTtmm7a1nPwm+ZtapsY2gHATD59DvnVOxRh8fpjlwvC77Uysxfl5PqKpeXvFhhJ07x8XhhNJWM72LjY4IxbisShNAvD4EUVj7lQSFOcvk9d0XIEobJROHdyoVQkUF4esGUffchHh1WvChVYHjeiWPJWaOzd0ckC72I8ZIZJ8GCNfvNTYnBPynQuC1BaIwhxZ7fki6IZFIQrQU3SCwLwRqVwmCs2BUXdc8XOzBMvEsKO0aR2Q0z64byI8GBza+/mLPcM6Ti1DijhyKD4hSdtpvcJ1/pxXC9neIqUa6ZXz6v0LwSlDMWGQDBNU7dvWREvTw5GeWDU5X7uJp8rw5bdfgz PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCWdIsaGJxJxwAmsZtD3isjJ/c07JTXgzFBJpxNSOC/oddmAoJ1XTlK7DUG76pG/Q0+F0zrv6FNnGSwDMHwulbElDN1wpeCQDBiXGS7Dzentkw2bxK0ayqQeVRsj8IfgXgM0Wc4ZGqR6h04Rd0acCxtRb4g2YHKNR5n8O+DY+L2oOnksPTRl2y49mj6LYojyyvgNVDLC3hzyBAkgUwqKY/nMRk67YwaKhSBskaORz04na1vR2fcg8Bk9TVVUdKPiR255Cws28IGBjQDvX6d1nSeo7mbv3aLVb98WkkTnOULXLGB7XFYDjvHZK/h8rKo+mZ6YdFFb503KeEH9cya+fxl PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDazOcakIE3q+QXge7W7D3dqXsWXMiOpY2iNkIUmjjRUdUt8eeZ/nVSzHaH/NQhyiZuEpOHOp0UHosamhpIunPsSs5W4I6Ldvjv3V/js4TmSTjrw86QC3Q0fjlFvRrRgGHPFF/l96B+O8Am0FEndzILda9V87NKUn1buYIhlzUzL5/uqByPJKH5mUC5lb4dx2vu1OBwSlMSDy//XjY6YUJe1FQH0WWy9X8jRjJLHzzMHfXD7e60GWoDF26lp/GgOoayAtxFiEa/G8jPTBwm6iBg17/uhn6CKPyxmp7ucGAoRp0eX/c3DNd9DfaOSg040P1xxaDmVsj4gfYEDv6AoQ6L PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQChXrrBX/WtTkeadpEO6YMrWZginTDcO0BfPtUMEXY8lvsPMhVP9oTdt66oKagISKd54IdL/pKZBw+K2kgHW6MHfsi0VZAcBMemNAM3iJUxwZLjsHYn9kl/R949pvoiqZLVl5iVejTenv96LylPgpKPy8Zyms5WfsUSjq1/N5FHc9wNvJrC6dYBQHKTK/GMcBQeIXaHWSufUHTs00Vf5seTYZt9IyfV+c7xe5NO3UFxFzJxRx/zZEcZSiynwIt40LbxQQ2Nnh3UkPY6ixhafbq3M6u6s4UryvQXSBrKZocxEUJfQpdT4EdpmTo+iEpkrhdzVtxO5UJ4JCzDZ6Azse6x PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8P01MApAiByB5MZdkdBaig+5V5BBFgAYnYODCkm2k/k2D8T88s86LtcZBWhkxNF0dLRbxUGBi/mPuP/MItA4pgRh6ag7TyCubplDhCp743g4Ptf7Pk4KDmbEn1bSbPsvmFVtGXRRnOKFkZeb4IDAuqR6pmW3ATTSx1Zb/UtkxTnWa0GnwN4xTguOFUgtBGEb+430SGsrmc+inTFKboDvMpYw9NPGE8pNcxhECAozKTfBuvGnOknDmuVTHc33AoD4/rn1Tn3tSvG08JaeAGz3qA2PBV/AHP1OFk2Xb0lOzofGsOhBPoc35XCrax+mus9mu1xWwd2cXYh9Jh33DdDXj PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDABItFpU6jK9fRQG7ElH499+9CkAgzHGpaQPkgfStCY/aXR7X7J7cYet3R79TZvfOij8Z8PJHQFvDCHBmUHutuyB4k2BTbJa7VWLChDLh2W3/FcDLGL8SByqGH6zcxp+Sz0JQAj+tX1tugSPbfi+5wx/sqljZwZe74c68khEWcWQW0o267q6gSHVVfi2FlMgbuT+EiLNxrdDNkWFUE0jpLUhkmuj7Wf6Kn4AKsq09UrRc1iiMMcZKYEr2JdqkRx8Cy4darcAsTSl3ApHcWZTqqrOznW0JBgXGIIcxf9VvFN+B3mHHKcWcPufKYEjlX3l5J6rpc0yEcrRc3LGMZtzeh PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCLTodvp7ypAZPUPioA3g+qrtpPmtY71k4oIWsWTmeDRX5ToPrik2TNgKAqrSdjpQFnZWNTfbTizU4LhnD4inEHmN6SG1YSorZxhpp6JdlEIQK2riqf5NKTkXtaoDm9AjQ4CN/UK0cb7pUnTeLkrVHOruqY1DDLbuV6gelXvNjs2q9GlTmQgN53VIzxMM7a6L1X0iu6WzY0bv1gFZjLvzpebjdpuRGR/HfI9LyTWndemQkwYDcSwh7TNImym16Yi3m5c5Qvyr8tjMYUqrctsamci8GEZsySKWpjzh6OZgMVG6nOxWXWjdhL2is+fFDi4MxAAFU8Xfv3WpkdqZgJdtlt PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDco7n1Gzq/XOfAd3yTShSW/LD+/KPDRSxBYpgXtqrR56/xVZQ/trGGX7DC/9Tsb5DCwRIoBQwrGEVZBJXxEUxsBl/gi/thldN5fMzWsgiDNJvlZEvsE3a7L+D+zAjTp3wkzzxRXXC0vXINJcYYs54VMCnprasR1H+y8FMapUrmXnGSJNg63+BTlcM/qhXHPXlGxph1P20DmdqbkhaG68pataXcvRxlBt/CVIAh+Ctn1OjT/k5UDZ/xDOOZRXunp47QVFUHR+Edg5qbMD7o65ruZh7E3Pe5nGL7vEmq+QqkcUlkfVUVbLDgAdWSGDkb4I7ESsvGYOCNu4N7Shv7Jy4N PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQCs4pKB5XByYvRI+/EDLwKF2r+7PnwNDkBM/KMgmF94RmGX0po//kEdU9ml995n1EMjTuLqndrHyAEA58BlHXwHqlDZBXBEJYcBlvicI1cr9cUcooc6RoI7TJ95wOcskqKrJueb4v5CfJP9esLGiB/jLjxhuqud3YnpXbQgQw9z0peqxbzXpmOZWC0Ojbzl3JXgh8Qc+QDfAKILJoTCNhzbLgrwHSoJXlvWGVKry7YB2PmGiuDOg9HPZ1X3Tuq2XUcVpTbu+7J8OVydoh+wwAzq30VxgWLAJg7LzCYCxcFq6LhR9q3sjkkrAmuR2tZC3N9ulEPakgIdpW7tCi3AaPrn PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC8ZkvMgBAv4/EBsfb6DCu76O/rv9KphD9ykNqWTdKMfwBrvZzFJ4UGBs5LCFE/H32h0UhX2Y/kxDSDVEwVko7XeGd5VK9/lcjMtP6CUkbyFkgPEbq1QJR9tiZatrweWpAc9mD5X2TihK8VaeRVLU2TcXxZWmtuhTb7ig2gxYohxN6+A8HyEB3oWp3J+2BBK2n/DS026o1lqDpZuup74qyHE0rxUOcI0qAi2cTRumHoIBJ4V/cA3AX34/7QzhMYIVb/zwyBYPKRp4yOk5SPB3Eo8VdITOxZ0SpAwbHQdkeY5QUuNk9JHB85yXObODNN5fbdehYGvwEmEtV5W8RA6Cnz PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQDVZDmWAJ0Lvd8tEugvlnPb2cE12L4qS9hhx1AFp2iIvs7j+WO62BwTN3g9Ee0KUsrfG/jdhdbD8YKTZOo25UNpx2Z43r5rZm1ld1gSCvlGQUyDj6XNoI4NzhwBBcu6wEvZET2Tb1Dt1jXucoJLX8gvEYrBc6y8Mk+Uy+MZMwNmp9L/7H9d+q5yhsXPLEpY6BhKBjAgvsTYHoySwC5Pda1J6gss8DXCtkpbbw/QjYjf2akqGDxl+w74TLeZq5t+lYlAfONCPIG/Gnl05e7WpVWXGcjjNCJERCmpO5dUX9N92bT1V3RtT8E2QpepIdS2hZUE0f+DGIkA2QoDz3dY5bpp PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC96Btg6Fj8KABhN7IrmEytrL5rDYMTaxUipnTwKUsUnZbgOstaFwESZkHOI53o0SVRzO8ERiKDwsgRxAl3x2GtHAbHUPS3YkdvAoOTIZiRUv22alch3/hhioHzjsRacH4DbbPHLTlL1HrfIM96a7Deg63GChLPP1y2nZ5aIOyG6Rlp76rZtYpM6gNR0+zIQhdCJnIcl75CglodNzulFPsMHtMjWdq904QLGKgyauDUYHM3GdUlv1AXPLDgjwOBfxb/j1xAhCOSYLpYich38Oe7E1Vp6UkHt13JYkxrPL2eKv2JPZnCZRW28IzBrE96fy9TTw5N7NsB0ttQoTONYs53 PIV AUTH pubkey
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC7/hLcwU+dK6zQtGpXF3566ePilzDA/G++sToC0UIMz21RUWYdFPZ2uIw4gNahyWVIg5M33gwZQ5ZL5eJgofF+LCbru3xEIcWTJYOrtG+mWZPElfeVr0BFN2HiY4aDuvER7nad0/VqIt/mLcgUozYT5f2tfCfhvk5sR4DvzFM9Il3OUfM7F84wuONykMbz9VStq10988bsFk53YCGe3Vz/OJYNuAQayaUy/+pb7ngOzTvKlF3imS7yvhAvPOwmDkvpOpy05mZsUKZfKPqPfZddzeQWv1kDSAtmvV+IJmJTYYgyysG7IjZIOAgZ4zYnwm6ifC8UYg2c0X7X6N8mvCzx PIV AUTH pubkey
' > ~/.ssh/authorized_keys

echo 'LOGIN WORKED, good job!' | sudo tee /etc/motd

#sudo passwd

#sudo passwd student

```


## Sources
- https://ubuntu.com/server/docs/smart-card-authentication-with-ssh

## Known problems
- When trying to use the 'Common' private key, the ssh-agent fails - KEY MAN pubkey slot probably doesn't allow for rsa signatures, but has some other intended use.
  ```
  Enter PIN for 'BSI Test Card 18': 
  C_SignInit failed: 99
  pkcs11_get_key failed
  sign_and_send_pubkey: signing failed for RSA "KEY MAN pubkey": error in libcrypto
  (student@lab-net-3) Password: 
  ```
