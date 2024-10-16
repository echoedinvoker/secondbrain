---
date: 2024-10-04
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Connect to IPOD Pro 2 on Arch Linux

每次戴上藍牙耳機之前都要重新連接一次, 有點麻煩, 所以記錄一下指令如下:

```
bluetoothctl # 進入 bluetoothctl prompt
power on
agent on
default-agent
scan on # 進入掃描前先讓 IPOD Pro 2 進入配對模式
pair C4:35:D9:23:D1:83  # 只有第一次 pairing 需要, 以後就不用這個了
connect C4:35:D9:23:D1:83
quit # 離開 bluetoothctl
```

C4:35:D9:23:D1:83 是我的 IPOD Pro 2 的 MAC address, 固定不變
