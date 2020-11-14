---
layout: post
title: Fuzzy finding in vim - vim + fzf
comments: true
tags: [vim, neovim, plugin]
---
FZF is the center-piece of my vim configuration. It is another excellent piece of software by [Junegunn Choi](https://github.com/junegunn).
> `fzf` is a command line fuzzy finder.

Now, that doesn't sound like much. But the way it can integrate with your other tools will blow your mind. The repository for FZF is located [here](https://github.com/junegunn/fzf) and has extensive documentation on how to use it. Follow [these instructions](https://github.com/junegunn/fzf#installation) to install it.

To see how it works, run this command:
``` bash
vim $(find . | fzf)
```
`find .` will list all the files in the directory recursively. You pipe that output into `fzf`.  Fuzzy search for the file of your choice and press enter. `fzf` will output that filename and that is passed as an argument to `vim`. Pretty neat, huh? `fzf` also has a multiselect mode which can be activated with `fzf -m`. You can select multiple entries by pressing `TAB`.

`fzf` has a [plugin](https://github.com/junegunn/fzf.vim) interface for using it in vim. Before installing and jumping into it, we need to lay some groundwork.


## RipGrep
[RipGrep](https://github.com/BurntSushi/ripgrep) is a faster replacement for grep written in rust. Output of some ripgrep commands will be piped into fzf to get our desired setup.
Now, go [install ripgrep](https://github.com/BurntSushi/ripgrep#installation). The binary is called `rg`.

## Vim leader key
Vim can map a sequence of keys to an action. Leader is just another key in that sequence. For example,
`map <leader>q :q<CR>` will map the sequence `<leader>` + `q` to `:q <enter>`. The default leader key is `\`. You can change the value of leader with `:mapleader`. I find `space` to be the best key for leader. It is accessible with both fingers and it isn't really doing anything else in the normal mode. So, go ahead and add this line in your `vimrc`.

```vim
let mapleader = "\<Space>"
```

## Install fzf.vim
We saw how to install plugins using `vim-plug` in [another post](/2020/11/07/vimming-in-2020/).

Add these 2 lines to your vimrc(Between `plug#begin` and `plug#end`).
```
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
Plug 'junegunn/fzf.vim'
```

Now, let us setup some config and keybindings for useful commands.
```vim
" use ripgrep to search for files
let $FZF_DEFAULT_COMMAND = 'rg -l ""'

map <C-p> :Files<CR>
map <C-b> :Buffers<CR>
map <Leader><Leader> :Commands<CR>
map <Leader>/ :execute 'Rg ' . input('Rg/')<CR>
map <Leader>l :BLines<CR>
map <Leader>gf :GF?<CR>
```

This will create the following mappings

| Keybindings   | Action                                                                                     |
| :-----------  | -----------                                                                                |
| `Ctrl + p`    | Fuzzy find files in the current working directory                                          |
| `Ctrl + b`    | Fuzzy find open buffers                                                                    |
| `space space` | Fuzzy find from a list of all available commands                                           |
| `space /`     | Search for some text within the working directory. Enter the text after pressing `space /` |
| `space l`     | Fuzzy search lines in the open file                                                        |
| `space g f`   | Fuzzy search for files that got added/changed after the last commit                        |

You can find a list of commands [here](https://github.com/junegunn/fzf.vim#commands). See if anything tickles your fancy.

## Searching for word under cursor
Something that I do pretty regularly is to search for the word under my cursor. This can be done by defining a vim function. I bind it to `space G`(That is an uppercase G).
```
command! -bang -nargs=* RgExact
  \ call fzf#vim#grep(
  \   'rg -F --column --line-number --no-heading --color=always --smart-case -- '.shellescape(<q-args>), 1,
  \   fzf#vim#with_preview(), <bang>0)

nmap <Leader>G :execute 'RgExact ' . expand('<cword>') <Cr>
```

## Example
<script id="asciicast-Q6G8YUsCbTETze0P07XFwmevF" src="https://asciinema.org/a/Q6G8YUsCbTETze0P07XFwmevF.js" async></script>
