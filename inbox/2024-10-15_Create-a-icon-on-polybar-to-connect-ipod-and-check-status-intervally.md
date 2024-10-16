---
date: 2024-10-15
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Create a icon on polybar to connect ipod and check status intervally


1. 建立用來連線以及檢查連線狀態的腳本

```bash
## ~/.config/polybar/scripts/connect_airpods.sh
#!/bin/bash

# 連接 AirPods 的函數
connect_airpods() {
    bluetoothctl power on
    bluetoothctl agent on
    bluetoothctl default-agent
    bluetoothctl scan on &
    sleep 5  # 給予掃描一些時間
    bluetoothctl connect C4:35:D9:23:D1:83
    killall bluetoothctl
}

# 檢查 AirPods 連接狀態的函數
check_airpods_status() {
    if bluetoothctl info C4:35:D9:23:D1:83 | grep -q "Connected: yes"; then
        echo "Connected"
    else
        echo "Disconnected"
    fi
}

# 主程序
if [ "$1" = "toggle" ]; then
    if [ "$(check_airpods_status)" = "Connected" ]; then
        bluetoothctl disconnect C4:35:D9:23:D1:83
    else
        connect_airpods
    fi
else
    check_airpods_status
fi

```

```bash
chmod +x ~/.config/polybar/scripts/connect_airpods.sh
```


2. 在 polybar 配置文件中（通常是 `~/.config/polybar/config`）添加一個新的模組:

```ini
[module/airpods]
type = custom/script
exec = ~/.config/polybar/scripts/connect_airpods.sh  # 以無參數輸入的方式呼叫腳本, 以檢查 AirPods 的連接狀態
interval = 5  # 每 5 秒會執行一次 exec
click-left = ~/.config/polybar/scripts/connect_airpods.sh toggle   # 以 toggle 參數輸入的方式呼叫腳本, 以切換 AirPods 的連接狀態
format = "iPod: <label>"   # <label> 會被腳本的輸出替換
```

3. 在 polybar 配置文件的 `modules-right`（或者你想要放置的位置）中添加這個新模組:

```ini
modules-right = ... airpods ...
```

4. 重新啟動 polybar ($mod) + Shift + r 重啟i3)

