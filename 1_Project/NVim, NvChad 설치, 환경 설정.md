---
tags:
  - NvChad
  - NVim
  - Rust
---
# # NVIM 최신 버전 설치

설치

```bash
$ curl -LO $ https://github.com/neovim/neovim/releases/latest/download/nvim.appimage
$ chmod u+x nvim.appimage
$ ./nvim.appimage
$ sudo mv nvim.appimage /usr/bin/nvim.appimage
```

문제 발생 시

```bash
$ ./nvim.appimage --appimage-extract
$ ./squashfs-root/AppRun --version

# Optional: exposing nvim globally.

$ sudo mv squashfs-root /
$ sudo ln -s /squashfs-root/AppRun /usr/bin/nvim
$ nvim
```

# NVIM 을 vim 명령으로 설정

```
alias vim='nvim.appimage'
```

# BackGround 작업(Vim, ctrl + z)을 ForeGround 작업으로 전환

```
alias 1='%1'
```

# NVIM 설정 초기화

NVIM 설치 과정 중 문제 발생 시 NVIM Cache 파일 제거 후 시도

```bash
$ rm -rf ~/.local/share
$ rm -rf ~/.config/nvim/
```

# FUSE 관련 문제 발생시

```bash
$ sudo add-apt-repository universe
$ sudo apt install libfuse2
```

https://github.com/AppImage/AppImageKit/wiki/FUSE

# NvChad

NvChad : 복잡하고 다양한 NVIM의 설정과 다른 플러그인을 조합하여 사용 편리성을 제공하는 플러그인.

NvChad를 설치하면 mason, vimtree, telescope등 다양한 플러그인을 미리 설치 해준다.

```bash
$ git clone https://github.com/NvChad/NvChad ~/.config/nvim --depth 1
$ nvim

초기 설정 파일 질문시 N
```

# NVIM 폰트 설정(NVimTree 아이콘)

![[Pasted image 20231112153335.png]]

WSL을 사용할 경우 우분투에 폰트를 설치하는 것이 아니라 윈도우에 폰트를 설치한 후 터미널에서 해당 폰드를 적용해주어야 한다.

NVimTree가 폴더, 파일 아이콘 표시를 원한다면 이런 아이콘을 지원하는 폰트롤 설정해야 한다.

Nerd Font : 개발자 친화적 폰트들을 다운로드 받을 수 있는 사이트.
- https://docs.rockylinux.org/books/nvchad/nerd_fonts/
- https://gist.github.com/matthewjberger/7dd7e079f282f8138a9dc3b045ebefa0?permalink_comment_id=3753594
- https://www.jetbrains.com/lp/mono/#how-to-install

# NVim, NvChad 설정 파일

NvChad 설정 파일(chadrc.lua)

```bash
$ ls ~/.config/nvim/lua/custom/
chadrc.lua
```

NVim 설정 파일(init.lua)

```bash
$ ls ~/.config/nvim
LICENSE  init.lua  lazy-lock.json  lua
```

# NvChad rust-analyzer 설정

lua/custom/chadrc.lua

```lua
---@type ChadrcConfig
local M = {}

M.ui = { theme = 'bearded-arc' }
M.plugins = 'custom.plugins'          <-- 추가

return M
```

lua/custom/plugins.lua

```lua
local plugins = {
  {
    "williamboman/mason.nvim",
    opts = {
      ensure_installed = {
        "rust-analyzer",
      },
    },
  },
  {
    "neovim/nvim-lspconfig",
    config = function()
      require "plugins.configs.lspconfig"
      require "custom.configs.lspconfig"
    end,
  },
}
return plugins
```

lua/custom/configs/lspconfig.lua

```lua
local on_attach = require("plugins.configs.lspconfig").on_attach
local capabilities = require("plugins.configs.lspconfig").capabilities

local lspconfig = require("lspconfig")
local util = require "lspconfig/util"

lspconfig.rust_analyzer.setup({
  on_attach = on_attach,
  capabilities = capabilities,
  filetypes = {
    "rust"
  },
  root_dir = util.root_pattern("Cargo.toml"),
  settings = {
    ['rust-analyzer'] = {
      cargo = {
        allFeatures = true,
      }
    }
  }
})
```

# NvChad rust-tools 설정

rust-tools는 Rust 코드 자동 완성, 디버깅 관련 다양한 기능을 제공한다.
대신 rust-tools가 기능을 수행할때 의존하는 플러그인들이 있어 같이 설치 해주어야 한다.

아래 그림과 같이 파일 추가/설정.

- ![[Pasted image 20231112155725.png]]
- ![[Pasted image 20231112155728.png]]

- plugins.lua

```lua
local cmp = require "cmp"

local plugins = {
  {
    "neovim/nvim-lspconfig",
    config = function()
      require "plugins.configs.lspconfig"
      require "custom.configs.lspconfig"
    end,
  },
  {
    "simrat39/rust-tools.nvim",
    ft = "rust",
    dependencies = "neovim/nvim-lspconfig",
    opts = function ()
      return require "custom.configs.rust-tools"
    end,
    config = function(_, opts)
      require('rust-tools').setup(opts)
    end
  },
  {
    "mfussenegger/nvim-dap",
  },
}
return plugins
```

- lspconfig.lua

```lua
local on_attach = require("plugins.configs.lspconfig").on_attach
local capabilities = require("plugins.configs.lspconfig").capabilities

local lspconfig = require("lspconfig")
local util = require "lspconfig/util"
```

- rust-tools.lua

```lua
local on_attach = require("plugins.configs.lspconfig").on_attach
local capabilities = require("plugins.configs.lspconfig").capabilities


local options = {
  server = {
    on_attach = on_attach,
    capabilities = capabilities,
  }
}

return options
```
