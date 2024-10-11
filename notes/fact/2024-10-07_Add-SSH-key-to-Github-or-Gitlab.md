---
date: 2024-10-07
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Add SSH key to Github or Gitlab

## Generate SSH key
```bash
ssh-keygen -t ed25519 -C "mattchang@mirle.com.tw" -f ~/.ssh/id_ed25519_gitlab
```
It will generate a pair of keys in ~/.ssh: id_ed25519_gitlab and id_ed25519_gitlab.pub.
If you don't use -f option, the default key name will be id_ed25519. (we prefer to use different keys for different services)

## Add public key to Github or Gitlab

add the content of id_ed25519_gitlab.pub to the SSH keys in your Github or Gitlab account. (key with extension .pub is the public key)
This step is on the web interface of Github or Gitlab.

## If ssh or git clone is not working

1. make sure the agent is running
```bash
mattc@x ~/Mirle> eval (ssh-agent -c) # this is for fish shell, if you are using bash, you can use eval $(ssh-agent -s)
Agent pid 121831
```
If the agent is running:

2. add the key to the agent
```bash
ssh-add ~/.ssh/id_ed25519_gitlab
```
