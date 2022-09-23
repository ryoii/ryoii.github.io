---
title: "VSCode 添加 Terminal"
subtitle: ""
date: 2022-09-23T11:30:00+08:00
lastmod: 2022-09-23T11:30:00+08:00
draft: false
authors: ['ryoii']
description: ""

tags: ['开发工具']
categories: []
series: []
---

## 打开 VSC 设置

打开 VSC 设置，检索对应的系统 ternimal 设置。在最新版本(本文未1.71)的 VSC 中，改设置路径和先前版本不同

+ Linux系统下: terminal.integrated.profiles.linux
+ Windows系统下: terminal.integrated.profiles.windows

选择 `在 settings.json 中编辑`

添加以下代码

```
{
    "terminal.integrated.defaultProfile.windows": "Command Prompt",
    "terminal.integrated.profiles.windows": {
        "PowerShell": {
            "source": "PowerShell",
            "icon": "terminal-powershell"
        },
        "Command Prompt": {
            "path": [
                "${env:windir}\\Sysnative\\cmd.exe",
                "${env:windir}\\System32\\cmd.exe"
            ],
            "args": [],
            "icon": "terminal-cmd"
        },
        "Git Bash": {
            "source": "Git Bash"
        },
        "Msys2 Mingw64": {
            "path": ["\\msys2\\usr\\bin\\bash.exe"],
            "args": ["--login"],
            "env": {
                "CHERE_INVOKING": "1",
                "MSYSTEM": "MINGW64"
            },
            "icon": "terminal-linux"
        }
    }
}
```

+ terminal.integrated.defaultProfile.windows 配置了 VSC 模式使用的 shell
+ terminal.integrated.profiles.windows 配置了若干 shell 的路径
 + source 会自动搜索 shell 的所在路径, 只针对常用特定的几个 shell 有效
 + 其他 shell 需要 使用 path 手动指定路径
 + 如额外添加的 `Msys2 Mingw64` shell, 手动指定了路径, 使用 `msys2` 的 shell 启动，并传入参数，使它以 `MINGW64` 模式打开