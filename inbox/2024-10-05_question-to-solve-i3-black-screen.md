
我是arch linux用戶, 我之前在i3環境下配置NVIDIA Optimus以及安裝相關libries例如prime, nvidia, nvidia-utils, nvidia-optimus 為了使steam遊戲能夠運行順暢,但是重開機之後發現使用i3登入後就會呈現黑畫面,但改用gnome登入就不會,請問這是什麼問題呢?
下面是我在fish shell 中運行的命令history (最近到最久):
steam
steam-native
groups mattc
groups
sudo usermod -a -G games mattc
on "Permission denied when downloading the game with Steam on Arch Linux"
ls -ld ~/.steam ~/.local/share/Steam
ls -l $(which steam)
cd
killall steam-native
sudo pacman -S steam-native-runtime
sudo pacman -S vulkan-icd-loader lib32-vulkan-icd-loader
sudo pacman -S nvidia-prime
prime-run
sudo systemctl start optimus-manager.service
sudo systemctl enable optimus-manager.service
sudo systemctl status optimus-manager.service
sudo vim /etc/optimus-manager/optimus-manager.conf
yay -S optimus-manager
sudo pacman -S optimus-manager
sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils
lspci | grep -E "VGA|3D|Display"
lspci | grep -i vga

6. 現在嘗試卸載 NVIDIA 模塊：
   ```
   sudo modprobe -r nvidia_drm nvidia_modeset nvidia_uvm nvidia
   ```

7. 如果成功卸載，則切換到集成顯卡模式：
   ```
   sudo optimus-manager --switch integrated
   ```

8. 重新啟動系統：
   ```
   sudo reboot
