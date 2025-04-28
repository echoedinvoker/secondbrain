---
date: 2025-03-30
type: fact
aliases:
  -
hubs:
  - "[[terminal_ghostty]]"
---

# Change font family and size in Ghostty

## Configuration
```ini
# ~/.config/ghostty/config

# 設定基本字體（主要用於英文）
font-family = Hack

# 為中文字符設定特定字體
# 使用 font-codepoint-map 來指定中文字符範圍使用的字體
# 中文字符範圍大致為 U+4E00-U+9FFF
font-codepoint-map = U+4E00-U+9FFF=WenQuanYi Micro Hei

# 其他字體設定
font-size = 14
font-style = regular # regular(正常), italic(斜體),
                     # bold(粗體), bold italic(粗斜體)

```

## Some popular front-family

### 英文等寬字體：
- **JetBrains Mono** - 現代、清晰，有連字功能
- **Source Code Pro** - Adobe 開發的開源字體，非常適合編程
- **Fira Code** - 有連字功能，提高代碼可讀性
- **Hack** - 專為源代碼設計的字體
- **Inconsolata** - 簡潔現代的等寬字體
- **Cascadia Code** - 微軟開發，有連字功能

### 中文字體：
- **Noto Sans CJK** (思源黑體) - Google 和 Adobe 合作開發，支援多種中文變體
- **Source Han Sans** (思源黑體) - 與 Noto Sans CJK 相同的字體，Adobe 版本
- **WenQuanYi Micro Hei** (文泉驛微米黑) - 開源中文字體，適合螢幕顯示
- **Sarasa Gothic** (更紗黑體) - 結合了日文、中文和拉丁文的等寬字體，特別適合終端使用

For using these fonts, you need to install them first.

```bash
# 安裝英文字體
sudo pacman -S ttf-jetbrains-mono ttf-fira-code ttf-hack ttf-inconsolata

# 安裝中文字體
sudo pacman -S noto-fonts-cjk adobe-source-han-sans-cn-fonts wqy-microhei
```
