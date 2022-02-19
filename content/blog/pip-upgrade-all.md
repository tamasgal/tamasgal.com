+++
title = "pip update-all"
date = "2019-03-15"
author = "Tamás Gál"
description = "A one-liner to update all pip packages"
categories = ["python"]
tags = ["pip","python", "one-liner"]
+++

## Problem to solve

Update all packages which are managed by `pip`.

## Fix

```shell
pip list --outdated --format=freeze | grep -v '^\-e' | cut -d = -f 1  | xargs -n1 pip install -U
```

## Alternatively

```shell
pip freeze|grep '=='|awk -F= '{print $1}' | xargs pip install -U
```

