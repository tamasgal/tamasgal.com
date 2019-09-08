+++
title = "st terminal support in tmux"
date = "2019-03-06"
author = "Tamás Gál"
cover = ""
description = "A code snippet to transfer the terminfo to another server"
categories = ["terminal"]
tags = ["st","terminal", "ssh","suckless"]
+++

## Symptoms

You try to attach to or create a new tmux session and you get the following
error message:

```shell
open terminal failed: missing or unsuitable terminal: st-256color
```

## Fix

Copy over your terminfo definition with this one-liner:

```shell
infocmp st-256color|ssh USER@TARGETHOST "mkdir -p .terminfo && cat >/tmp/ti && tic /tmp/ti"
```

That's it...
