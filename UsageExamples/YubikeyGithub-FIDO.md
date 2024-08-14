# YubikeyGithub-FIDO (Linux)
Use Yubikey to generate and store private key used for github authentication (like pushing commits).
- Generate the key directly on the Yubikey <https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent>
- Add the key like you would when adding a normal key.
## Notes
Instead of using
```
ssh-keygen -t ed25519 -C "example"
```
you use
```
ssh-keygen -t ed25519-sk -C "example"
```

## Usage example:
```
[user@host:~/test]$ git push
Enter passphrase for key '/home/user/.ssh/id_ed25519_sk':
Confirm user presence for key ED25519-SK SHA256: AAAAC3NzaC1lZDI1NTE5AAAAID4Ze32UvGGGGGGAAAAABBBBDDDDGGGRRRR2eab7w9Cz
User presence confirmed
Enumerating objects: 26, done.
[...]
```
You are required to "Confirm your presence" - the Yubikey starts blinking and you need to press it.

