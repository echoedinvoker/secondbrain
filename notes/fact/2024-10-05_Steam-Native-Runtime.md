---
date: 2024-10-05
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Steam Native Runtime

## 安裝 steam-native-runtime 有什麼作用？

   - 默認情況下，Steam 使用自己的運行時環境（Steam Runtime），這個環境包含了運行 Steam 和大多數 Steam 遊戲所需的庫。
   - steam-native-runtime 允許 Steam 使用系統自帶的庫，而不是 Steam 自帶的庫。

## 使用 steam-native-runtime 的原因：

   - 某些用戶可能會遇到使用 Steam 默認運行時環境時的兼容性問題。
   - 使用系統庫可能在某些情況下提供更好的性能或兼容性。
   - 對於某些特定的遊戲或系統配置，使用原生運行時可能會解決問題。

## 如何使用：

   - 安裝後，您可以使用 `steam-native` 命令來啟動使用系統庫的 Steam 版本。
   - 或者，您可以設置環境變量來始終使用原生運行時。

總的來說，steam-native-runtime 提供了一個替代選項，讓用戶可以在遇到問題時嘗試使用系統庫來運行 Steam。
每個遊戲和系統配置都可能有不同的表現，因此這是一個用於調試和優化的工具。
