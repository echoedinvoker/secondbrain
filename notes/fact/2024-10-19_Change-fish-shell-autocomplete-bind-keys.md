---
date: 2024-10-19
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Change fish shell autocomplete bind keys

```bash
# ~/.config/fish/config.fish
bind --erase --preset \t  # remove the default binding for Tab
bind \t complete # let Tab to change the completion hint

bind \ck 'commandline -f forward-word' # let Ctrl+K move the cursor to the end of the word
bind \cl 'commandline -f accept-autosuggestion' # let Ctrl+L accept the autosuggestion

```

Then, restart the fish shell.
