```txt
"设置文件编码
set encoding=utf-8
set fileencodings=utf-8
set termencoding=utf-8
set backspace=2
"设定leader键
let mapleader=','

"设置主题
let g:solarized_termcolors=256
syntax on
set background=dark
colorscheme industry
"常用主题onedark,xcodelight,xcodedark,xcodewwdc, gotham,gotham256,solarized

set cursorline

"设置保持历史记录10000
set history=10000

"打开文件时禁止折叠
set nofoldenable

"按照语法高亮进行折叠
set fdm=indent
set display=lastline

" scrolloff控制光标上下的边距为多少时翻页，emmm，说的有点抽象，改改这个数字就很清楚了
set scrolloff=5
set matchtime=2
set matchpairs+=<:>

" 显示行号可以通过在命令行中设置:set nonumber关闭行号。要想永久关闭或者开启就要在vimrc中设置了
set number
set showcmd
set showmatch

" 设置文件不备份，这里被注释掉；
set nobackup
set noundofile
set noswapfile
"set backupext=.bak
"set backupdir=~/.vim/vim_bak/
```