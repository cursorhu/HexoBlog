---
title: VSCode配置笔记
date: 2022-12-08 11:50:24
tags: vscode
categories: vscode
---

# 基本配置

## 修改工作区存储目录workspaceStorage

VSCode会将每个工作区的一些配置、扩展、缓存等默认保存在C盘的AppData\Code\workspaceStorage，使用一段时间后数据能达到上十GB。

当C盘空间不足，用SpaceSniffer可以找到这些“数据垃圾”，但每隔一段时间清理也不是一劳永逸。

修改workspaceStorage存储路径到非系统盘：

1.首先选择VSCode在开始栏，状态栏，或桌面栏的快捷方式图标，常用哪个就修改哪个，右键属性：

添加启动的命令行选项，指定user-data-dir:

```
--user-data-dir "目标路径，例如F:\VSCodeWorkspaceStorage"
```

![image-20221208120051137](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202212081200216.png)

2.转移已有的workspaceStorage.

修改完成后，将%AppData%\Code下的所有内容拷贝到设置的目录中;  也可以删除%AppData%\Code，但是需要重新配置VSCode。

# 常用快捷键

## 代码注释

以双斜杠//注释和取消注释:

```
方法一：
注释：ctrl + / 
取消注释：ctrl + /
```

```
方法二：
注释：ctrl + k, ctrl + c 
取消注释：ctrl + k, ctrl + u
```

以星号/**/注释和取消注释:

```
注释：shift + alt + a 
取消注释：shift + alt + a
```

## 更改快捷键

File->Preference->KeyboardShortCuts

例如可以把块注释/**/快捷键改成`ctrl+Alt+/`，和行注释`ctrl+/`达成统一：

选择recording keys，直接录入要修改的快捷键

![image-20230220110133891](https://raw.githubusercontent.com/cursorhu/blog-images-on-picgo/master/images/202302201101988.png)

# 插件配置
