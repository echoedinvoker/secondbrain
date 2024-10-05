---
date: 2024-10-05
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Permission denied when downloading the game with Steam on Arch Linux

如果在 Steam 界面點擊遊戲 Install 卻在後台出現下面錯誤導致, 下載進度永遠為 0%:

```log
Couldn't write /.steam_exec_test.sh: Permission denied
Couldn't write /var/cache/pacman/pkg/.steam_exec_test.sh: Permission denied
Couldn't write /.snapshots/.steam_exec_test.sh: Permission denied
Couldn't write /var/log/.steam_exec_test.sh: Permission denied
Couldn't write /boot/.steam_exec_test.sh: Permission denied
```

1. 確定在家目錄下執行 steam 或 [[2024-10-05_Steam-Native-Runtime|steam-native]]

2. 執行 steam 的用戶必須屬於 group `games`, 若沒有則使用下面指令加入

```bash
sudo usermod -aG games $USER
group # 卻確定用戶已經加入 games 群組
```
```
