﻿[toc]


本教程提供了对flatpak的快速介绍。在这篇文章中，您将学习如何创建一个可以安装并运行的基本Flatpak应用程序。
要完成本教程，您应该按照[指南](https://flatpak.org/getting.html)安装flatpak。您还需要拥有`flatpak-builder`工具，该工具通常作为一个包提供。


# 1. 添加manifest
``flatpak-builder``的输入是一个JSON文件，它描述了构建应用程序的参数。这被称为清单。以下示例是gnome字典应用程序的清单：
```json
{
  "app-id": "org.gnome.Dictionary",
  "runtime": "org.gnome.Platform",
  "runtime-version": "3.26",
  "sdk": "org.gnome.Sdk",
  "command": "gnome-dictionary",
  "finish-args": [
     "--socket=x11",
     "--share=network"
  ],
  "modules": [
    {
      "name": "gnome-dictionary",
      "sources": [
        {
          "type": "archive",
          "url": "https://download.gnome.org/sources/gnome-dictionary/3.26/gnome-dictionary-3.26.0.tar.xz",
          "sha256": "387ff8fbb8091448453fd26dcf0b10053601c662e59581097bc0b54ced52e9ef"
        }
      ]
    }
  ]
}
```
可以看出，该manifest包含有关应用程序的基本信息，其中包括：

 - `app-id` - 应用 ID
 - `runtime` - 运行时提供了运行应用程序的环境，以及它可以使用的基本依赖关系
 - `sdk` - 该软件开发工具包是具有开发文件和头文件的运行时版本;这是构建应用程序所必需的
 - `command` - 用于运行应用程序的命令
 - `finish-args` - 配置选项，使应用程序可以访问沙盒外的资源;在这种情况下，应用程序正在接入网络并访问x11显示服务器

manifest的下一部分是`modules`列表。这描述了要作为构建过程的一部分构建的每个模块。其中一个模块始终是应用程序。其他可以是作为应用程序的一部分捆绑在一起的库和其他资源。

模块源可以有几种类型，包括`.tar`或`.zip`压缩文件，Git或Bzr库。在这个案例中，只有一个模块，它是一个将被下载和构建的`.tar`文件。

字典manifest的模块部分很短，因为只建立一个模块：应用程序本身。

为字典创建manifes，创建一个名为`org.gnome.dictionary.json`的文件，并把上面的JSON代码粘贴进去。

# 2.运行构建

要使用manifest构建字典应用程序，请运行以下命令：

```shell
$ flatpak-builder --repo=tutorial-repo dictionary org.gnome.Dictionary.json
```

这个命令执行以下操作：

 - 创建一个名为dictionary的新文件夹
 - 下载并验证字典源代码
 - 在SDK中而不是主机系统中构建并安装源代码
 - 通过设置权限来完成构建（在这个案例中，可以访问x11和网络）
 - 创建一个名为repo的新存储库（如果它不存在）并将生成的构建导出到其中

flatpak-builder还会做一些其他有用的事情，比如创建一个可单独安装的调试运行时（本例中称为`org.gnome.dictionary.debug`）和一个可单独安装的翻译运行时（称为`org.gnome.dictionary.locale`）。

# 3.创建一个新的存储库

要测试已构建的应用程序，需要添加已创建的新存储库：

```shell
$ flatpak --user remote-add --no-gpg-verify --if-not-exists tutorial-repo tutorial-repo
```

# 4.安装应用

下一步是从存储库安装字典应用程序。要做到这一点，运行:

```shell
$ flatpak --user install tutorial-repo org.gnome.Dictionary
```

要检查应用程序是否已成功安装，可以将安装的应用程序的sha256提交与`flatpak-builder`打印的提交ID进行比较：

```shell
$ flatpak info org.gnome.Dictionary
$ flatpak info org.gnome.Dictionary.Locale
```

# 5.运行应用

最后，你可以运行你构建的应用程序：

```shell
$ flatpak run org.gnome.Dictionary
```

其余文档提供了使用`flatpak-builder`的完整指南。如果是flatpak的新手，建议先看[flatpak介绍](../Flatpak 介绍.md)。









