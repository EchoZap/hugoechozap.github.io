---
title: "个人自用vim配置文件"
date: 2024-10-21T21:56:27.406303
categories: ['Docs']
author: "Ronan"
---
将以下内容写入 `.vimrc` 中


```
" 开启系统剪切板
set clipboard=unnamedplus


" 将 jk 映射为 Esc
inoremap jk <Esc>


" 开启行号显示
set nu


" 开启自动缩进并将 tab 键设置为四个空格长度
set autoindent
set shiftwidth=4
set tabstop=4
set expandtab


" 启用自动补全
set omnifunc=syntaxcomplete#Complete

" 设置自动匹配括号
" inoremap ( ()<Left>
inoremap { {}<Left>
inoremap [ []<Left>


" 设置代码语法高亮
syntax on
```

<!-- ##{"timestamp":1723621841}## -->
