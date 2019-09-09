# Markdown 
## Move

### Simple

`h` move left

`j` move up

`k` move down

`l` move right

### Line

`$` move to end of the line

`^` move to begin of the line(excape white space)

`0` move to the begin of the line

`w` move to the next word

`b` move to the last word

### Screen

`<c-b>` scoll a screen backword

`<c-f>` scoll a screen forword

`H` move to the first line on curent screen

`M` move to the middle line on curent screen

`L` move to the end line on curent screen

### Paragraph

`{` move to last paragraph

`}` move to next paragraph

`%` move to next paranthese

### Marker

`ma` mark as "a"

`'a` skip tp mark "a"

### Whole

`gg` skip to the head of the file

`G` skip to the tail of the file

`ngg\nG\:n,` skip to the n-th line

## Basic Operator

`y` copy

`yy` copy one line

`p` paste

`d` delete

`dd` delete one line

`x` delete one character

`u` undo

`<c-r>` redo

## Visual Mode

`v` visual mode

`V` visual line mode

`<c-v>` visual block mode

## Replace Mode

`R` replace mode

## Advanced Operator

`.` repeat the last operator

### Search

`/str` search str

`*` search the next word which curent curson on

`#` search the last word which curent curson on

### Search and Replace

`:%s/old_word/new_word/g` search and replace all

`:s/old_word/new_word/g` search and replace sth which you select

`:%s/old_word/new_word/gc` search and replace which you want

### Split the screen

`<c-w>` basic command

`w` switch the windows

`c` close the windows but wouldn't close all

`q` quit the windows and would quit vim if the windows is last

`r` exchange the windows

`o` close other windows

`:sp` open the file in split the windows

`:vsp` open the file in vertical split windows

### File Operator ###

`:w file` save file

`:e` edit

`n` new one file
