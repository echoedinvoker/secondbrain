---
date: 2024-10-04
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Connect to Wifi

使用下面指令取得當下可連線的 wifi 列表快照:

  ```bash
nmcli device wifi list  # 取得當下可連線的 wifi 列表 (snapshot)

IN-USE  BSSID              SSID                 MODE   CHAN  RATE        SIGNAL  BARS  >
        0A:BE:C5:A8:55:0F  iPhone               Infra  149   270 Mbit/s  100     ▂▄▆█  >
        A8:BD:27:11:C0:02  Mirle-Staff          Infra  6     130 Mbit/s  99      ▂▄▆█  >
        A8:BD:27:11:C0:00  Mirle-EMP            Infra  6     130 Mbit/s  97      ▂▄▆█  >
        8E:18:88:BF:DF:08  --                   Infra  6     130 Mbit/s  97      ▂▄▆█  >
        82:18:98:BF:DF:08  MIPC                 Infra  136   270 Mbit/s  95      ▂▄▆█  >
        A8:BD:27:11:C0:01  Mirle-OA             Infra  6     130 Mbit/s  90      ▂▄▆█  >
        8E:18:88:BF:DF:21  --                   Infra  11    130 Mbit/s  84      ▂▄▆█  >
        A8:BD:27:11:C0:12  Mirle-Staff          Infra  56    270 Mbit/s  82      ▂▄▆█  >
        A8:BD:27:11:C0:10  Mirle-EMP            Infra  56    270 Mbit/s  82      ▂▄▆█  >
        A8:BD:27:11:C0:11  Mirle-OA             Infra  56    270 Mbit/s  82      ▂▄▆█  >
        8E:18:88:BF:DE:D9  --                   Infra  1     130 Mbit/s  80      ▂▄▆_  >
        B8:3A:5A:DF:EA:94  3N1-PSK+MAC          Infra  44    540 Mbit/s  80      ▂▄▆_  >
        B8:3A:5A:DF:EA:91  test-mfp             Infra  44    540 Mbit/s  80      ▂▄▆_  >
        B8:3A:5A:DF:EA:90  Mac_test             Infra  44    540 Mbit/s  80      ▂▄▆_  >
        A8:BD:27:11:BF:21  Mirle-OA             Infra  11    130 Mbit/s  79      ▂▄▆_  >
        A8:BD:27:11:BF:22  Mirle-Staff          Infra  11    130 Mbit/s  77      ▂▄▆_  >

  ```

接著使用下面指令進行連線:

```bash
nmcli device wifi connect "My SSID" password "my password" # 雙引號的使用取決於中間是否有空格

```
