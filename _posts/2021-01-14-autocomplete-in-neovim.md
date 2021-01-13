---
layout: post
title: Autocomplete in neovim with built-in LSP client
comments: true
tags: [neovim, plugin, lsp]
---

In the [last blog post](/2021/01/10/language-server-in-vim/), we saw how to setup the built-in lsp client in neovim for diagnostics and such. Now we'll see how to setup autocomplete.

First, install `completion-nvim`. Add this to your `vimrc` and run `PlugInstall`.

```vim
Plug 'nvim-lua/completion-nvim'
```

Now, in the `lsp_config.lua` file, you need to make some changes. The file should look like this:

```lua
lspconfig = require'lspconfig'
completion_callback = require'completion'.on_attach

lspconfig.pyls.setup{on_attach=completion_callback}
lspconfig.tsserver.setup{on_attach=completion_callback}
lspconfig.rust_analyzer.setup{on_attach=completion_callback}
```

This should give you completion. Now, we need a trigger to activate completion. I like the `tab` key. The config for this is available in the repo.

```vim
" Use <Tab> and <S-Tab> to navigate through popup menu
inoremap <expr> <Tab>   pumvisible() ? "\<C-n>" : "\<Tab>"
inoremap <expr> <S-Tab> pumvisible() ? "\<C-p>" : "\<S-Tab>"

" Set completeopt to have a better completion experience
set completeopt=menuone,noinsert,noselect

" Avoid showing message extra message when using completion
set shortmess+=c

let g:completion_enable_auto_popup = 0
imap <tab> <Plug>(completion_smart_tab)
imap <s-tab> <Plug>(completion_smart_s_tab)
```

Now, you can use `tab` and `shift + tab` to navigate through suggestions.
