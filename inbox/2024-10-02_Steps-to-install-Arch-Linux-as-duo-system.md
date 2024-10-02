---
date: 2024-10-02
type: fact
hubs:
  - "[[Linux]]"
---

# Steps to install Arch Linux as duo system

以下是在已有 Ubuntu 系統的筆電上安裝 Arch Linux 作為雙系統的大致步驟：

1. 準備工作：
   - 確保您有足夠的硬碟空間用於 Arch Linux。
   - 下載並製作 Arch Linux 的 USB 安裝媒體。
   - 在 Ubuntu 中壓縮現有分區，為 Arch Linux 創建未分配空間。

2. 從 USB 啟動 Arch Linux 安裝環境：
  - 重啟電腦，進入 BIOS/UEFI 設置。(我的Acer Aspire 5是按F2)
   - 將啟動順序改為優先從 USB 啟動。
   - 保存設置並重啟，選擇從 USB 啟動。

3. 連接到互聯網：
   - 如果使用有線連接，應該自動連接。
   - 如果使用 Wi-Fi，使用 [[1727849366-CCEE|iwctl]] 命令連接。

4. 更新系統時鐘 [[1727888741-SNNL|timedatectl]]

5. [[1727849572-LTCP|分區]]

6. [[1727850171-KDQM|格式化]]分區

7. [[1727850261-TZQB|掛載]]分區

8. 安裝基本系統(使用 [[1727850583-OQVK|pacstrap]])

9. 生成 [[1727850792-AVTG|fstab]]

10. [[1727850914-LUIE|Chroot]] 到新系統

11. 配置系統：
    - [[1727889653-LYRH|設置時區]]
    - [[1727889734-VRXY|本地化設置]]
    - [[1727889805-NEJF|設置主機名]]
    - [[1727889892-QLSX|設置 root 密碼]]
    - [[1727889957-PJRU|創建新用戶]]
    - [[1727890418-JRPV|sudoer 設置]]

12. 安裝和配置[[1727890715-BKRP|引導加載程序]]：

13. 安裝其他好用的軟件：
    - [[1727891034-TFGP|網路管理軟件]]
    - [[1727891148-HMFX|CPU微碼更新]]

13. 退出 chroot 環境，卸載分區，重啟系統。
```bash
exit
umount -R /mnt
reboot
```
