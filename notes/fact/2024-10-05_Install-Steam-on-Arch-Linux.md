---
date: 2024-10-05
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Install Steam on Arch Linux

1. 更新系統：
```
sudo pacman -Syu
```

2. 安裝 Steam：
```
sudo pacman -S steam
```

```
mattc@x ~/dotfiles (arch-i3)> sudo pacman -S steam
[sudo] password for mattc: 
resolving dependencies...
:: There are 7 providers available for lib32-vulkan-driver:
:: Repository multilib
   1) lib32-amdvlk  2) lib32-nvidia-utils  3) lib32-vulkan-intel
   4) lib32-vulkan-nouveau  5) lib32-vulkan-radeon  6) lib32-vulkan-swrast
   7) lib32-vulkan-virtio

Enter a number (default=1):  
```

根據您的圖形硬件選擇適當的包


3. 可選：安裝 [[2024-10-05_Steam-Native-Runtime|Steam Native Runtime]]
   如果您遇到兼容性問題，可以嘗試安裝 Steam Native Runtime：
```
sudo pacman -S steam-native-runtime
```

注意事項：
- 確保您的圖形驅動程序是最新的。
- 某些遊戲可能需要額外的配置或使用 Proton 來運行。
- 如果遇到問題，檢查 Arch Wiki 上的 Steam 頁面以獲取更多故障排除信息。

