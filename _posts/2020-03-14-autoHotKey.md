---
layout: post
title:  "autoHotKey"
categories: tools
tags: hotkey
author: joshua
---

### [Win下最爱效率神器:AutoHotKey](https://www.jeffjade.com/2016/03/11/2016-03-11-autohotkey/)

**[AutoHotkey](https://autohotkey.com/)**是一个windows下的开源、免费、自动化软件工具。它由最初旨在提供键盘快捷键的脚本语言驱动(称为：**热键**)，随着时间的推移演变成一个完整的脚本语言。但你不需要把它想得太深，你只需要知道它可以简化你的重复性工作，一键自动化启动或运行程序等等；以此提高我们的**工作效率**，改善**生活品质**；通过按键映射，鼠标模拟，定义宏等。

可以将其看作是一个热键增添器，也可以当成改键器/屏幕录制器，或者是游戏热键外挂等

写入相应的映射代码然后右击选择”**reload this script**“执行它就可以开始使用AutoHotkey里面设置好的功能了

想在其他地方放置脚本怎么办呢？很简单，只要新建一个文本文档，将其后缀名改为.ahk然后执行它就行了。所以，在同一台电脑中，你甚至可以存放多个脚本。当用不到该脚本了只需要，鼠标移到该图标处，右键选择**exit**即可

```text
# 号代表 Win 键；
! 号代表 Alt 键；
^ 号代表 Ctrl 键；
+ 号代表 shift 键；
:: 号(两个英文冒号)起分隔作用；
run，非常常用 的 AHK 命令之一;
; 号代表 注释后面一行内容；
```

想按下“Alt + e” 启动everything：

> !e::run "C:\Program Files\Everything\Everything.exe"