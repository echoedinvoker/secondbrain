---
date: 2024-10-07
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Let #SSH agent work on all terminals automatically


## Install keychain

```bash
sudo pacman -S keychain
```


## Add this to your shell profile (here we use #fish shell as an example)

```bash
if type -q keychain
    keychain --eval --quiet -Q id_ed25519_gitlab | source
end

```

## Test it

Open a new terminal and run:
```bash
ssh-add -l
```
You should see your key listed.
```
