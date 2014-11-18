---
layout: post
title: "【已解决】perl: warning: Setting locale failed"
category: Technology
tags: Linux
---

错误提示如下：

    perl: warning: Setting locale failed.
    perl: warning: Please check that your locale settings:
    LANGUAGE = "en_US:",
    LC_ALL = (unset),
    LC_CTYPE = "zh_CN.UTF-8",
    LANG = "en_US.UTF-8"
        are Settingupported and installed on your system.
    perl: warning: Falling back to the standard locale ("C").


解决方案：

在`.bashrc`的最后加上`export LC_ALL=C`
执行`source /root/.bashrc`
