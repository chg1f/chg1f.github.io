---
title: "Vim Note"
description: "Vim笔记"
date: 2021-07-17T21:35:41+08:00
lastmod: 2021-07-17T21:35:41+08:00
tags:
- vim
---

## 备忘

1. 在为了在多机器、环境下，最大限度上令体验一致。 使用了`bash`+`readline`+`tmux`+`vim`+`fzf`作为基本的工具链
2. 在Vim上，为了对大文件等编缉，尽可能减少非常用必要设置及插件，或者可以可以简易打开/关闭的，保持体验，或者使用`vim -u NONE {filepath}`用裸配置模式打开
3. 对于日常开发上，目标只补存上Linter（语法检测）、Fixer/Fomatter（格式化或自动校正）、Completer（代码补全）,及一些快捷辨识及工具

### 相关文章

1. 键盘映射
> http://yyq123.blogspot.com/2010/12/vim-map.html?m=1

- 设置键盘映射
| command | normal | visual | operator | insert only | command |
| - | - | - | - | - | - |
| :map,:noremap | 1 | 1 | 1 |  |  |
| :map!,:noremap! |  |  |  | 1 | 1 |
| :nmap,:nnoremap | 1 |  |  |  |  |
| :vmap,:vnoremap |  | 1 |  |  |  |
| :omap,:onoremap |  |  | 1 |  |  |
| :imap,:inoremap |  |  |  | 1 |  |
| :cmap,:cnoremap |  |  |  |  | 1 |

- 取消键盘映射
| command | normal | visual | operator | insert only | command |
| - | - | - | - | - | - |
| :unmap | 1 | 1 | 1 |  |  |
| :unmap! |  |  |  | 1 | 1 |
| :nunmap | 1 |  |  |  |  |
| :vunmap |  | 1 |  |  |  |
| :ounmap |  |  | 1 |  |  |
| :iunmap |  |  |  | 1 |  |
| :cunmap |  |  |  |  | 1 |

- 查看键盘映射`:help key-notation`

2. 关于quickfix list及location list
> https://stackoverflow.com/questions/20933836/what-is-the-difference-between-location-list-and-quickfix-list-in-vim

3. 关于`buffer`、`window`、`tab`
> https://dev.to/iggredible/using-buffers-windows-and-tabs-efficiently-in-vim-56jc

- `buffer`
  - `:n`
  - `:ls,:buffers`
  - `:bn,:bnext`
  - `:bp,:bprev`
  - `:bd,:bdelete`
- `window`
  - `:sp,:split,<C-w>s`
  - `:vs,:vsplit,<C-w>v`
  - `<C-w>c` - close the current window
  - `<C-w>o` - make the current window the only one on the screen, all other windows are closed
  - `<C-w>h/j/k/l`
- `tab`
  - `:tabnew`
  - `:tabn,:tabnext`
  - `:tabp,:tabprev`
  - `:tabc,:tabclose`

## 我的配置文件

- `https://github.com/chongiofai/dots/blob/main/.vimrc`

### 插件

- YouCompleteMe
代码补全

- ale
语法检查，格式化/修正
  - <leader>f - 格式化/修正
  - <leader>l - 启用/取消语法检查
  - <leader>j - 错误/警告跳转下一个
  - <leader>k - 错误/警告跳转上一个

- vim-colorschemes
配置方案

- vim-bookmarks
书签管理

- eregex.vim
本文件正则匹配

- ack.vim
多文件匹配

- fzf,fzf.vim

- editorconfig-vim

- nerdcommenter

- vim-go

- undotree
- tagbar
- nerdtree

- vim-gitbranch

