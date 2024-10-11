---
date: 2024-10-08
type: fact
aliases:
  -
hubs:
  - "[[Linux]]"
---

# Remove package from Arch linux completely and clearly


## Steps to Completely Remove a Package in Arch Linux

在 Arch Linux 上，如果你想完全刪除一個#AUR程序例如 #mov-cli，你可以按照以下步驟進行：

1. **確認程序名稱**：首先，確保你要刪除的程序名稱正確，可以用以下命令確認是否已安裝：

   ```bash
   pacman -Qs mov-cli
   ```

   如果 `mov-cli` 出現在列表中，說明它已安裝。

2. **卸載程序**：使用 #pacman 命令卸載程序：

   ```bash
   sudo pacman -Rns mov-cli
   ```

   - `-R` 表示刪除。
   - `-n` 用於刪除安裝時不是顯式安裝的依賴。
   - `-s` 刪除與包相關的所有文件，包括配置文件。

3. **清理系統緩存**：程序被刪除後，系統仍然可能保留一些緩存包，可以這樣清理：

   ```bash
   sudo pacman -Sc
   ```

   謹慎使用 `sudo pacman -Scc`，因為它會清理所有緩存。

4. **確認額外文件**：有時候，AUR包的文件可能存在於系統的不同位置。可以通過查找不再使用的孤立包來清理系統：

   ```bash
   sudo pacman -Qdtq | sudo pacman -Rns -
   ```

這應該可以幫助你完整地刪除 `mov-cli` 及其相關的文件。如果遇到任何錯誤或問題，可以提供更詳細的信息以便進一步協助。


## Dependency Issue While Removing a Package

💬:
mattc@x ~> sudo pacman -Rns mov-cli
[sudo] password for mattc: 
checking dependencies...
error: failed to prepare transaction (could not satisfy dependencies)
:: removing mov-cli breaks dependency 'mov-cli' required by python-mov-cli-youtube
mattc@x ~ [1]> 

🤖:[ChatGPT4o]
看來你遇到了一個依賴問題，`mov-cli` 是另一個包 #python-mov-cli-youtube 的依賴。如果你決定不需要 `python-mov-cli-youtube`，或者可以接受刪除它，那麼可以一起卸載這些包。

在進行下一步之前，請務必確定刪除這些包是否會影響你的其他需求，因為這可能會卸載 `python-mov-cli-youtube` 及與之關聯的所有依賴。

以下是如何同時卸載 `mov-cli` 和 `python-mov-cli-youtube` 的方法：

```bash
sudo pacman -Rns python-mov-cli-youtube mov-cli
```

這個命令會執行：

1. **刪除 'python-mov-cli-youtube' 與 'mov-cli'**：這會導致所有相關包被移除。
2. **'n' 和 's' 選項**確保移除的依賴包是由這些包提供且不被其他安裝的包需要的孤立依賴。

在此之前確保備份任何需要的數據，然後可以嘗試如上操作。確認確實不需要這些功能及配置。若仍有依賴關系困擾，也可以尋求進一步支援或查找可能的替代方案更新包管理流程。
