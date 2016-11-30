---
layout: post
title: tmux cheatsheet
---

Some really basic commands:

```
C-b ?                       # get help

tmux ls                     # list sessions
tmux new -s NAME            # create a session
tmux attach -t NAME         # attach to a session
C-b d                       # detach from a session
```

Window management:

```
C-b %                       # vertical split
C-b "                       # horizontal split
C-b <arrow>                 # switch to a pane
```

Slightly more advanced stuff:

```
# rename a session
tmux rename-session -t OLDNAME NEWNAME
```