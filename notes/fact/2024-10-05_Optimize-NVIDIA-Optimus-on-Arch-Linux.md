---
date: 2024-10-05
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Optimize NVIDIA Optimus on Arch Linux

1. 首先，確保您已經安裝了正確的 NVIDIA 驅動程序。在 Arch Linux 上，您需要安裝以下包：

```
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils nvidia-prime
```

2. 安裝 Optimus 管理器：

```
yay -S optimus-manager
```

3. 配置 Optimus Manager：
   創建或編輯文件 `/etc/optimus-manager/optimus-manager.conf`，添加以下內容：

```conf
[optimus]
switching=none
pci_power_control=no
pci_remove=no
pci_reset=no

[intel]
driver=modesetting
accel=
tearfree=

[nvidia]
modeset=yes
pat=yes
dpi=96
options=overclocking
```

4. 啟動 Optimus Manager 服務：

```bash
sudo systemctl enable optimus-manager.service
sudo systemctl start optimus-manager.service
```

5. 對於 Steam 遊戲，您可以使用 `prime-run` 命令來運行遊戲。這將強制遊戲使用 NVIDIA GPU。您可以通過以下方式修改 Steam 遊戲的啟動選項：

  Steam interface -> Library -> Right-click on the game -> Properties -> Set Launch Options
   ```
   prime-run %command%
   ```

6. 如果您想要全局使用 NVIDIA GPU，可以在啟動 i3 之前切換到 NVIDIA 模式：
```
optimus-manager --switch nvidia
```
為了在 i3 之前啟動上面指令, 你可以選擇把它放在下面二者之一
1) ~/.xinitrc  # xinitrc 是用來啟動 X 會話的腳本
2) /usr/share/xsessions/i3-nvidia.desktop  # 這是 i3 的桌面文件，用於啟動 i3 會話


7. 為了獲得最佳性能，您可能還需要安裝 Vulkan 支持：

```
sudo pacman -S vulkan-icd-loader lib32-vulkan-icd-loader
```

