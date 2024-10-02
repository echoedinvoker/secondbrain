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
   - 下載並製作 Arch Linux 的 USB 安裝媒體（您已經準備好了）。
   - 在 Ubuntu 中壓縮現有分區，為 Arch Linux 創建未分配空間。

2. 從 USB 啟動 Arch Linux 安裝環境：
   - 重啟電腦，進入 BIOS/UEFI 設置。
   - 將啟動順序改為優先從 USB 啟動。
   - 保存設置並重啟，選擇從 USB 啟動。

3. 連接到互聯網：
   - 如果使用有線連接，應該自動連接。
   - 如果使用 Wi-Fi，使用 `iwctl` 命令連接。

4. 分區：
   - 使用 `lsblk` 查看現有分區。
   - 使用 `cfdisk` 或 `fdisk` 在未分配空間中創建新分區。
   - 通常需要創建 root 分區（/）和 swap 分區。

5. 格式化分區：
   - 格式化 root 分區：`mkfs.ext4 /dev/sdXY`
   - 創建 swap：`mkswap /dev/sdXZ`

6. 掛載分區：
   - 掛載 root 分區：`mount /dev/sdXY /mnt`
   - 啟用 swap：`swapon /dev/sdXZ`

7. 安裝基本系統：
   - 使用 pacstrap：`pacstrap /mnt base linux linux-firmware`

8. 生成 fstab：
   - `genfstab -U /mnt >> /mnt/etc/fstab`

9. Chroot 到新系統：
   - `arch-chroot /mnt`

10. 配置系統：
    - 設置時區
    - 本地化設置
    - 設置主機名
    - 設置 root 密碼
    - 創建新用戶

11. 安裝和配置引導加載程序（如 GRUB）：
    - 安裝 GRUB：`pacman -S grub efibootmgr`
    - 安裝 GRUB：`grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=GRUB`
    - 生成 GRUB 配置：`grub-mkconfig -o /boot/grub/grub.cfg`

12. 退出 chroot 環境，卸載分區，重啟系統。
