---
layout: post
title: tmux cheatsheet
---

Some really basic commands (note I rebound C-b to C-x, see below):

```
C-x ?                       # get help

tmux ls                     # list sessions
tmux new -s NAME            # create a session
tmux attach -t NAME         # attach to a session
C-x d                       # detach from a session
```

Window management:

```
C-x %                       # vertical split
C-x "                       # horizontal split
C-x <arrow>                 # switch to a pane
C-x {                       # swap panes}
```

Slightly more advanced stuff:

```
# rename a session
tmux rename-session -t OLDNAME NEWNAME

# configure prefix in ~/.tmux.conf
unbind-key C-b
set-option -g prefix C-x
```
