---
layout: post
title: MacOS and Windows Dual Install Tips
date: 2018-4-3
categories: blog
tags: [技术总结]
description: 在Mac 2011（带光驱）旧版mac上安装Windows
---

##我们开始吧

**方法一**：

“创建Windows7安装磁盘”选项被隐藏，因为您的系统的功能有限。例如，较旧的Mac无法从外部USB磁盘启动，因此在USB磁盘上创建安装程序将没有任何意义。

仍然，如果你想在外部磁盘上创建一个安装程序，无论如何，你所要做的就是编辑BootCamp助理的“Info.plist”文件：

打开BootCamp助理应用程序的Package包内容，将Info.plist文件备份到某处，然后使用XCode打开旧的。

编辑文件：

编辑版块的标题：PreUSBBootSupportedModels到USBSupportedModels（如果已经是USBBootSupportedModels - >跳过此步骤）

将系统的型号标识符（通过系统信息应用程序查找）添加到USBSupportedModels部分。

将您的Boot ROM版本（按系统信息查找应用程序）添加到DARequiredROMVersions部分

保存。

重新启动BootCamp

@dusanvf的评论：
如果你的Bootcamp在这之后崩溃，写入终端：

```sudo codesign -fs - /Applications/Utilities/Boot\ Camp\ Assistant.app/```
 
**方法二**：

如果未出现“创建Windows 7或更高版本的安装磁盘”复选框，则无法使用U盘在Mac上安装Windows。此复选框的示例如下所示。由于您的Mac没有显示此复选框，我假设你有一个光盘驱动器。您需要使用磁盘实用程序应用程序将iso映像刻录到空白DVD。

现在有办法使这个复选框出现，以便文件可以复制到U盘，但很可能在Mac的固件不允许从U盘启动Windows。即使您可以使用U盘引导到OS X，这并不意味着您可以使用U盘引导到Windows。您需要从光盘驱动器启动到Windows，以将其安装到内部驱动器。

将iso刻录到DVD的步骤：（在Mac OS X中从Burn ISO刻录）

打开“磁盘实用程序”应用程序，它位于/Applications/Utilities/

拉下文件菜单并选择“打开磁盘映像”

导航到要刻录的ISO映像文件，然后单击“确定”

插入空白DVD

选择“验证已刻录的数据”

单击“刻录”，然后等待直到映像完成刻录到光盘

至于Windows的驱动程序。您可以使用BootCamp助理下载它们，也可以从BootCamp下载：Microsoft Windows操作系统的系统要求。在完成安装Windows后安装驱动程序。

最后一个注意事项：不要使用Microsoft的任何内容更改分区。只能使用OS X软件成功更改分区。唯一的例外是Windows可以格式化BOOTCAMP分区。

## windows需要安装在gpt分区表中，而分区表是mbr格式

解决办法：

1、在MAC OS中用bootcamp把win10安装分区调整好。

2、用磁盘工具抹掉新分的区，把分区格式选为mac日志式（切记！我在这走了多次弯路）！

3、重启电脑按住option键选择U盘或光盘（事先创建好WIN10安装U盘或光盘）启动 。

4、进入安装，到选择磁盘时删除这个磁盘后再新建此磁盘！ 

5、然后按提示进行安装就可以了。

