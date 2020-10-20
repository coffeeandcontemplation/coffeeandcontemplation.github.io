---
layout: post
title: How to quit vim
---
Vim has multiple modes. You normally insert text in the `Insert` mode. `Normal` mode is where you move around and give commands to vim.

`Esc` will take you to normal mode(usually). You can enter a command starting with a `:`. Quit vim with the below command:


```
<Esc>:qa!
```

- `q` - quit
- `a` - all
- `!` - force do the operation.
This will quit all the buffers including the unsaved ones.
