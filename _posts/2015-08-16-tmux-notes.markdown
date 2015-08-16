---
layout: post
title: "Tmux Notes"
category: Technology
tags: tmux linux vim
---

### Install
$ brew install tmux

### Prefix
Default: Ctrl-b
Customize: ~/.tmux.conf
```
  unbind C-b
  set -g prefix C-a
```

### Relation
* Session has_many Window
* Window has_many Pane

### Pane

Action  | Shortcut Key
------------- | -------------
vertical pane  | Prefix + %
horizontal pane  | Prefix + "
switch pane | Prefix + o
switch pane | Prefix + （direction key）
close current pane | Prefix + x
close all pane | Prefix + !


### Window

Action  | Shortcut Key
------------- | -------------
new window | Prefix + c
switch window | Prefix + (index)
close window | Prefix + &

### Session

Action  | Shortcut Key
------------- | -------------
create session from CLI | $ tmux new -s <session-name>
create session from tmux  | :new -s <session-name>
list sessions from CLI | $ tmux ls
list sessions from tmux | Prefix + s
attach session from CLI | $ tmux a -t <session-name>
detach session | Prefix + d

