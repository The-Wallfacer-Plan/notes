---
title: Vim
description: 
published: true
date: 2023-01-31T16:08:51.628Z
tags: 
editor: markdown
dateCreated: 2023-01-31T15:07:16.066Z
---

### useful sites:
- [Linux vi and vim editor: Tutorial and advanced features](http://www.yolinux.com/TUTORIALS/LinuxTutorialAdvanced_vi.html)
- [zzapper - Best of Vim Tips](http://www.rayninfo.co.uk/vimtips.html)
- [Keep your vimrc file clean](http://vim.wikia.com/wiki/Keep_your_vimrc_file_clean)
- [Learn Vim Progressively](http://yannesposito.com/Scratch/en/blog/Learn-Vim-Progressively/)
- [vim-galore](https://github.com/mhinz/vim-galore)
- [learn-vim](https://github.com/dofy/learn-vim/)


### Normal Mode
```
`vim +?bug README` / `vim +/install README`
`g_` – go to the last non blank character of the line
`N%` – Go to the Nth percentage line of the file.
x>--增加以下x行的缩进。
x<--减少以下x行的缩进。
）--移动光标到下一个句子
(--移动光标到上一个句子
# function jump related
{ : jump to last space line
} : jump to next space line
[[ : jump to starting of last function
]] : jump to starting of next function
# window switch
Ctrl-w_p    # last accessed window
```

### Insert Mode
```
ctrl-d 删除一个 shiftwidth; shiftwidth 通常设置的和 tab 移动的宽度一样
ctrl-@ 插入上次插入的文字, 并推出 insert mode
ctrl-a 插入上次插入的文字
ctrl-e 插入光标下面的字符
ctrl-y 插入光标上面的字符
```

### Ex Mode
```
ctrl-c 退出 command-line 回到 normal mode
ctrl-b 光标到 command-line 的开始
ctrl-e 光标到 command-line 的结束
ctrl-d command-line 补全
ctrl-r ctrl-f 插入光标下的对象
ctrl-r ctrl-p
ctrl-r ctrl-w
ctrl-r ctrl-a
```