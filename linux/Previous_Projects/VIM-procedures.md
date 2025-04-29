---
title: "VIM Procedures"
date: 2023-04-07T07:14:26Z
draft: false
---

### Install vim
```
sudo apt install vim-nox
```
### Modes
```
[Esc] return to normal Mode 
: to enter commands in Normal Mode 
i to enter Insert Mode
V to enter Visual Mode
```

### Navigation Commands
```
h              to move left one char
j              to move up one line 
k              to move down one line
l              to move right one char


gg             to Move to top row
G              to move to bottom row
2G             to move two lines down
[->]           to move to right one char
2[->]          to move two chars to right
[<-]           to move left one char
3[<-]          to move left 3 chars
```

### Quit Commands
```
ZZ             to quit if no change made
:quit          to quit if no change made
:q             to quit if no change made
:wq            to write and quit
:q!            to quit and not save
```

### Delete Commands
```
u              to undo delete
[Ctrl]r        to redo change
x              to delete 1 char
2x             to delete 2 chars
dw             to delete a word
2dw            to delete 2 words
dd             to delete a line
3dd            to delete 3 lines
```
