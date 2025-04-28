---
date: 2025-03-13
type: fact
aliases:
  -
hubs:
  - "[[love2d_and_lua]]"
---

# Install Love2D and setup nvim LSP for it


## Install Love2D and Lua
```sh
# install love2d
sudo pacman -S love

# install lua and its package manager because love2d uses lua
sudo pacman -S lua luarocks

```

## Setup nvim LSP for Love2D

```lua
return {
  -- LSP 配置
  {
    "neovim/nvim-lspconfig",
    dependencies = {
      "folke/neodev.nvim",  -- 為 Neovim Lua API 提供補全
    },
    config = function()
      require("neodev").setup({}) -- 啟用 Neovim Lua API 補全
      
      local lspconfig = require("lspconfig")
      
      -- 配置 Lua Language Server
      lspconfig.lua_ls.setup({
        settings = {
          Lua = {
            runtime = {
              version = "LuaJIT",
            },
            diagnostics = {
              globals = {"love"},  -- 添加 love 全局變量
            },
            workspace = {
              library = {
                -- 添加 Love2D API 定義
                "/usr/share/lua/5.1/love",
                "/usr/share/love/",
              },
              checkThirdParty = false,
            },
            telemetry = {
              enable = false,
            },
          },
        },
      })
    end,
  },
}
```
