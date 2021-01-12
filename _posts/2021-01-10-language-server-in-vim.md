---
layout: post
title: Language Server Client in neovim
comments: true
tags: [neovim, plugin, lsp]
---

## What is language server protocol?

Development becomes a lot easier if all languages support features like autocomplete, linting, go-to-definition etc. within the editor of your choice. But this is a difficult task for editor devs. Each editor has to build support or integrate with tools that provide language support. Each of these tools behave in different ways making integration difficult. Language server protocol was defined by microsoft to put and end to this. LSP defines a standard protocol for tools(called language servers) to communicate with your editor.

![Why lsp](https://code.visualstudio.com/assets/api/language-extensions/language-server-extension-guide/lsp-languages-editors.png "Why lsp?")
*Image source: [Visual Studio Code](https://code.visualstudio.com/api/language-extensions/language-server-extension-guide)*

## What is a language server client?

A language server client is the part of your editor that interacts with a language server - Sometimes implemented as plugins, sometimes built-in.

## What language clients are available for vim?

1. [vim-lsp](https://github.com/prabirshrestha/vim-lsp)
1. [LanguageClient-neovim](https://github.com/autozimu/LanguageClient-neovim)
1. [coc.nvim](https://github.com/neoclide/coc.nvim)
1. [ale](https://github.com/dense-analysis/ale)
1. Built-in language server in [neovim](https://neovim.io/doc/user/lsp.html)

We'll see how to setup the built-in language server in neovim in this blog post.

## First, setup a decent looking theme

[Onebuddy](https://github.com/Th3Whit3Wolf/onebuddy) is an atom one inspired theme. [Install](/2020/11/07/vimming-in-2020/) the theme with `vim-plug`.
Add this to your vimrc:

```vim
Plug 'tjdevries/colorbuddy.vim'
Plug 'Th3Whit3Wolf/onebuddy', { 'branch': 'main' }
```

Enable the theme by adding this to vimrc:

```vim
set termguicolors
colorscheme onebuddy
```

## What features should we expect?

1. Show errors in line
1. Show warnings in line
1. Show errors/warnings in a list
1. Format file
1. Jump to definition
1. See documentation
1. Code actions - Actions like *"Extract to function", "Import this module"* - Yes, you can get that in vim.
1. Autocomplete

The first 7 are really easy to achieve. Let us handle autocomplete in a separate post.

## How to setup built-in lsp client for neovim?

neovim's lsp client is built in lua. The configuration is also in lua. Usable configurations for common language servers are collected in [neovim/nvim-lspconfig](https://github.com/neovim/nvim-lspconfig/blob/master/CONFIG.md).

Install the plugin with:

```vim
Plug 'neovim/nvim-lspconfig'
```

Let us create a new file in `.vim` directory with all the lsp configurations. Name it `lsp_config.lua`. Now in your `vimrc`, add the following:

```vimrc
luafile ~/.vim/lsp_config.lua
```

In this file, add some configuration for common language servers

```lua
lspconfig = require'lspconfig'

lspconfig.pyls.setup{}
lspconfig.tsserver.setup{}
lspconfig.rust_analyzer.setup{}
```

That is it! You should start seeing diagnostics now. Ensure you have the right language servers installed.
For example, for python language server, do

```bash
pip3 install 'python-language-server[all]'
```

## Add some signs to the gutter

It would be nice to see some symbols to the left of the code when there are errors.
Add these lines to your vimrc.

```vim
sign define LspDiagnosticsSignError text=🔴
sign define LspDiagnosticsSignWarning text=🟠
sign define LspDiagnosticsSignInformation text=🔵
sign define LspDiagnosticsSignHint text=🟢
```

## Add some keybindings

We need some keybindings for actions like **jump to definition**. Add the following to your vimrc.

```vim
nnoremap <silent> gd    <cmd>lua vim.lsp.buf.definition()<CR>
nnoremap <silent> gi    <cmd>lua vim.lsp.buf.implementation()<CR>
nnoremap <silent> gr    <cmd>lua vim.lsp.buf.references()<CR>
nnoremap <silent> gD    <cmd>lua vim.lsp.buf.declaration()<CR>
nnoremap <silent> ge    <cmd>lua vim.lsp.diagnostic.set_loclist()<CR>
nnoremap <silent> K     <cmd>lua vim.lsp.buf.hover()<CR>
nnoremap <silent> <leader>f    <cmd>lua vim.lsp.buf.formatting()<CR>
nnoremap <silent> <leader>rn    <cmd>lua vim.lsp.buf.rename()<CR>

nnoremap <silent> <leader>a <cmd>lua vim.lsp.buf.code_action()<CR>
xmap <silent> <leader>a <cmd>lua vim.lsp.buf.range_code_action()<CR>
```

This will create the following mappings

| Keybindings  | Action                                              |
| :----------- | -----------                                         |
| `gd`         | Go to definition                                    |
| `gi`         | Go to implementation                                |
| `gr`         | See references                                      |
| `gD`         | Go to declaration                                   |
| `ge`         | Show errors in loclist                              |
| `gd`         | Go to definition                                    |
| `space f`    | FOrmat file                                         |
| `space r n`  | Rename variable under cursor                        |
| `space a`    | Code actions - On normal mode and visual selections |

Your end result should look like this:
![lsp screenshot](/public/lsp_python.png "Screenshot of lsp python")

