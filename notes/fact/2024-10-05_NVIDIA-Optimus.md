---
date: 2024-10-05
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# NVIDIA Optimus

1. 混合顯卡系統：
   - 這是一個通用術語，指的是同時擁有集成顯卡和獨立顯卡的系統。
   - 這種配置在筆記本電腦中很常見，目的是在性能和電池壽命之間取得平衡。

2. NVIDIA Optimus：
   - Optimus 是 NVIDIA 開發的一種特定的混合顯卡技術。
   - 它專門用於配備 NVIDIA 獨立顯卡和（通常是）Intel 集成顯卡的系統。

3. 其他混合顯卡技術：
   - AMD 也有類似的技術，稱為 AMD Switchable Graphics 或 AMD Dynamic Switchable Graphics。
   - Apple 的 MacBooks 使用自己的混合顯卡切換技術。

4. Optimus 的工作原理：
   - 在低負載情況下使用集成顯卡以節省電力。
   - 在需要高性能時自動切換到 NVIDIA 顯卡。
   - 渲染由 NVIDIA 顯卡完成，但輸出通過集成顯卡傳輸到顯示器。

5. Linux 上的 Optimus：
   - 在 Linux 系統上，Optimus 的支持並不像在 Windows 上那麼原生和無縫。
   - 需要[[2024-10-05_Optimize-NVIDIA-Optimus-on-Arch-Linux|額外的工具和配置]]，如 PRIME、Bumblebee 或 Optimus Manager。


一般查詢險卡指令如下, 通常僅能夠查詢到集成顯卡:
```bash
lspci | grep -i vga
```
如果要更全面的查詢所有顯卡, 可以使用以下命令:
```bash
lspci | grep -E "VGA|3D|Display"
```

下面是我 Arch Linux 上的顯卡查詢結果:
```
mattc@x ~/dotfiles (arch-i3)> lspci | grep -E "VGA|3D|Display"
00:02.0 VGA compatible controller: Intel Corporation Iris Plus Graphics G1 (Ice Lake) (rev 07) # 集成顯卡，被識別為 VGA 兼容控制器。
02:00.0 3D controller: NVIDIA Corporation GP107M [GeForce MX350] (rev a1) # NVIDIA 獨立顯卡，但它被識別為 3D 控制器，而不是 VGA 控制器。
```
此為 NVIDIA Optimus 系統，同時擁有 Intel 集成顯卡和 NVIDIA 獨立顯卡。
