---
date: 2024-10-05
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Install libraries for VGA hardware on Arch Linux

1) lib32-amdvlk: 適用於 AMD 顯卡的開源 Vulkan 驅動。
2) lib32-nvidia-utils: 適用於 NVIDIA 顯卡的專有驅動。
3) lib32-vulkan-intel: 適用於 Intel 集成顯卡的 Vulkan 驅動。
4) lib32-vulkan-nouveau: 適用於 NVIDIA 顯卡的開源驅動。
5) lib32-vulkan-radeon: 適用於 AMD 顯卡的另一個開源驅動。
6) lib32-vulkan-swrast: 軟件渲染的 Vulkan 實現，通常用於測試或沒有硬件加速的環境。
7) lib32-vulkan-virtio: 用於虛擬化環境的 Vulkan 驅動。

使用下面指令查詢所使用的圖形硬件:

```bash
lspci | grep -i vga
```
這個指令通常應該顯示所有的顯卡，包括 NVIDIA 顯卡。但是有一些例外情況，例如筆電常使用的混合顯卡技術 (例如 [[2024-10-05_NVIDIA-Optimus|NVIDIA Optimus]])。

