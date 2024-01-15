---
layout: post
title: Moving to NixOs
comments: true
tags: [nixos]
---

The idea of a declartive reproducible operating system is really enticing. [NixOs](https://nixos.org) has been popping up in my reddit feeds recently. I had an option to install Nix as a package manager on my existing linux installation. But I decided to jump in the deep end. So, this post is being written on NixOS.

There is abundant documentation on Nix. Even after ignoring all of that, I was able to setup a working installation with most of my setup from the old Arch installation. Xmonad works. Firefox, telegram, zsh and neovim works too.

Setting up development environments is a bit tricky. It looks like there are different methods to set up isolated dev environments - `nix shell`, `nix flakes`, `devenv` etc. I got a flake based setup for python working. For android studio based android development, I had to install it globally. Flutter - there is no luck so far.

The next step is to learn the nix language and how the package management works.
