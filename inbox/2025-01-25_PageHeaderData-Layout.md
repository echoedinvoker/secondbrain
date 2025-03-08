---
date: 2025-01-25
type: fact
aliases:
  -
hubs:
  - "[[psql]]"
---

# PageHeaderData Layout

In [[2025-01-25_Overall-Page-Layout|Overall Page Layout]], we used the real sample data to show where the `PageHeaderData` is placed in the page. In this article, we will show the layout of the `PageHeaderData` itself.


Here is the text content from the image converted to Markdown format:

| Field | Type | Length | Description |
| --- | --- | --- | --- |
| pd_lsn | PageXLogRecPtr | 8 bytes | LSN: next byte after last byte of WAL record for last change to this page |
| pd_checksum | uint16 | 2 bytes | Page checksum |
| pd_flags | uint16 | 2 bytes | Flag bits |
| pd_lower | LocationIndex | 2 bytes | Offset to start of free space |
| pd_upper | LocationIndex | 2 bytes | Offset to end of free space |
| pd_special | LocationIndex | 2 bytes | Offset to start of special space |
| pd_pagesize_version | uint16 | 2 bytes | Page size and layout version number information |
| pd_prune_xid | TransactionId | 4 bytes | Oldest unpruned XMAX on page, or zero if none |


```js
00000000: 00 00 00 00 B8 29 B8 22 00 00 04 00 E4 00 28 01  .....)."......(.
//        ^^^^^^^^^^^^^^^^^^^^^^^ ^^^^^ ^^^^^ ^^^^^ ^^^^^
//                pd_lsn      pd_checksum   pd_lower 
//                                    pd_flags     pd_upper
00000010: 00 20 04 20 00 00 00 00 A8 9F A2 00 30 9F E2 00  . . ........0...
//        ^^^^^^^^^^^^^^^^^^^^^^^ 
//        others that we don't interest

// (...omitted)

```

With the `pd_lower` and `pd_upper` values, we can locate the free space in the page.

```js
const pd_lower = 'E4 00';
const freeSpaceStart = parseInt(pd_lower, 16); // 0x00E4 = 228
const pd_upper = '28 01';
const freeSpaceEnd = parseInt(pd_upper, 16); // 0x0128 = 296
```

```js
00000000: 00 00 00 00 B8 29 B8 22 00 00 04 00 E4 00 28 01  .....)."......(.
//                                            ^^^^^ ^^^^^ pd_lower and pd_upper
00000010: 00 20 04 20 00 00 00 00 A8 9F A2 00 30 9F E2 00  . . ........0...
00000020: 80 9E 5C 01 E0 9D 36 01 70 9D D2 00 C0 9C 56 01  ..\...6.p.....V.
00000030: 60 9C B2 00 A8 9B 6E 01 C8 9A C0 01 60 9A C6 00  `.....n.....`...
00000040: 68 99 EC 01 C0 98 46 01 10 98 54 01 B0 97 B2 00  h.....F...T.....
00000050: F0 96 72 01 98 96 A4 00 D8 95 7C 01 48 95 12 01  ..r.......|.H...
00000060: C8 94 FE 00 28 94 3A 01 C8 93 B8 00 10 93 6C 01  ....(.:.......l.
00000070: 70 92 34 01 A8 91 88 01 28 91 FC 00 98 90 1A 01  p.4.....(.......
00000080: F8 8F 3E 01 60 8F 22 01 70 8E DA 01 C8 8D 42 01  ..>.`.".p.....B.
00000090: 10 8D 66 01 B0 8C B8 00 C0 8B D2 01 68 8B AE 00  ..f.........h...
000000a0: A8 8A 76 01 A8 89 F2 01 48 89 BE 00 70 88 AC 01  ..v.....H...p...
000000b0: F8 87 E2 00 28 87 98 01 C8 86 B4 00 60 86 C2 00  ....(.......`...
000000c0: 08 86 AC 00 98 85 D6 00 D0 84 8E 01 10 84 7A 01  ..............z.
000000d0: 70 83 3A 01 E0 82 14 01 40 82 3A 01 80 81 7C 01  p.:.....@.:...|.
000000e0: 28 81 AC 00 00 00 00 00 00 00 00 00 00 00 00 00  (...............
//                   ^ start of free space
000000f0: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000100: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000110: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000120: 00 00 00 00 00 00 00 00 8D 03 00 00 00 00 00 00  ................
00000130: 00 00 00 00 00 00 00 00 33 00 0A 00 03 09 20 4F  ........3..... O
00000140: 02 00 00 00 00 00 00 00 CB 00 00 00 00 00 00 00  ................
//                               ^ end of free space
00000150: B8 C7 08 27 FF 31 01 00 B8 C7 08 27 FF 31 01 00  ...'.1.....'.1..
00000160: 0F 47 65 6E 65 37 36 1F 31 30 31 35 35 35 37 37  .Gene76.10155577
00000170: 34 32 38 33 35 34 11 6F 66 66 6C 69 6E 65 00 00  428354.offline..
00000180: 8D 03 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000190: 32 00 0A 00 03 09 20 7F 02 00 00 00 00 00 00 00  2..... .........
000001a0: 32 00 00 00 00 00 00 00 E8 87 32 90 80 87 01 00  2.........2.....
000001b0: E8 87 32 90 80 87 01 00 0F 41 6C 74 61 34 39 53  ..2......Alta49S
000001c0: 49 6E 63 69 64 75 6E 74 20 65 6E 69 6D 20 71 75  Incidunt enim qu
000001d0: 69 20 73 75 6E 74 20 76 65 6E 69 61 6D 20 65 6E  i sunt veniam en

// (...omitted)

```
