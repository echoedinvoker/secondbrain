---
date: 2024-10-02
type: fact
hubs:
  - "[[Linux]]"
---

# Reboot after setting up GRUB

重啟後，您應該會看到以下幾種可能的情況：

A. 如果一切正常，您應該能夠看到 GRUB 引導菜單，其中包含 Arch Linux 和 Ubuntu 的選項。

B. 如果沒有看到 GRUB 菜單，而是直接進入了 Arch Linux 或 Ubuntu：
   - 在啟動過程中，嘗試按住或反覆按 Shift 鍵或 Esc 鍵，這通常可以調出 GRUB 菜單。
   - 如果仍然看不到 GRUB 菜單，可能需要調整 GRUB 的配置以顯示菜單。

C. 如果系統直接啟動到了 Ubuntu，而沒有 Arch Linux 的選項：
   - 在 Ubuntu 中，運行 `sudo update-grub` 來更新 GRUB 配置，然後再次重啟。

D. 如果遇到錯誤或無法啟動：
   - 記下錯誤信息。
   - 使用 Live USB 啟動到 Ubuntu 或 Arch Linux 的 live 環境。

重啟後，請告訴我實際的結果。根據您遇到的具體情況，我們可以提供進一步的建議和故障排除步驟。如果一切順利，您應該能夠選擇並啟動 Arch Linux 了。
