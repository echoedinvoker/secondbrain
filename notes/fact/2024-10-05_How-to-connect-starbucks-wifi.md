---
date: 2024-10-05
type: fact
aliases:
  -
hubs:
  - "[[]]"
---

# How to connect starbucks wifi

### 确保文件：

1. **确认安装了 NetworkManager：**

   检查你的系统上是否已经安装了 NetworkManager。如果没有，可以使用以下命令进行安装：

   ```bash
   sudo pacman -Syu networkmanager
   ```

2. **启用并启动 NetworkManager：**

   启动并启用 NetworkManager 服务，以便在启动时自动运行：

   ```bash
   sudo systemctl enable NetworkManager
   sudo systemctl start NetworkManager
   ```

### 使用命令行连接：

3. **查看可用的无线网络：**

   执行以下命令来查看你附近的无线网络：

   ```bash
   nmcli device wifi list
   ```

4. **连接到 Starbucks Wi-Fi：**

   找到 Starbucks 网络的 SSID，通常会显示为类似“Starbucks Wifi”，然后使用以下命令进行连接：

   ```bash
   nmcli device wifi connect "Starbucks Wifi"
   ```
   ```

