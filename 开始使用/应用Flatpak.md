﻿[toc]



此页面提供了使用flatpak所需的最常用命令的介绍,并说明必要的技术规范以及最常用的命令。

最终用户通常不需要使用此页面或flatpak命令行界面，因为可以通过图形软件管理工具轻松使用flatpak，尽管他们当然可以随意使用命令行！

# flatpak命令

`flatpak`是主要的flatpak命令，附加了特定的参数。例如，安装东西的命令是`flatpak install`，卸载命令是`flatpak unintall`。

# 身份标识

flatpak使用唯一的三元标识符（如`com.company.app`）标识每个应用程序和运行时。该地址的最后一部分是对象的名称，前面的部分标识开发人员，以便同一开发人员可以拥有多个应用程序，如`com.company.app1`和`com.company.app2`。

开发人员在创建自己的ID时应遵循标准的[D-Bus命名约定](https://dbus.freedesktop.org/doc/dbus-specification.html#message-protocol-names)。如果应用程序提供了D-Bus服务，则D-Bus服务名称应与应用程序标识相同。

## 确定三元组

通常使用它们的id来引用对象就足够了。但是，在某些情况下，有必要引用对象的特定版本或特定体系结构。例如，某些应用程序可能作为稳定版本和测试版本提供，在这种情况下，需要指定要安装哪一个。

flatpak允许使用对象的标识符triple来指定体系结构和版本。这采用`name/architecture/branch`的形式，例如`com.company.app/i386/stable`。（分支是指用于引用同一对象版本的术语。）三元组的第一部分是id，第二部分是体系结构，第三部分是分支。

标识符三元组也可用于通过将三元组的一部分留空而仅指定体系结构或分支。例如，`com.company.app//stable`只是指定分支，`com.company.app/i386//`只是指定了体系结构。

如果需要指定对象的体系结构或分支，则flatpak CLI会提供反馈。

# 系统与用户

flatpak命令可以在系统范围内运行，也可以按用户运行。系统上安装的应用程序和运行时可供系统上的所有用户使用。每个用户安装的应用程序和运行时仅适用于安装它们的用户。

相同的原则适用于存储库-系统范围内添加的存储库可供所有用户使用，而每个用户存储库只能由特定用户使用。

flatpak命令默认在系统范围内运行。如果您正在安装日常使用的应用程序，建议您坚持使用此默认行为。

但是，每个用户的运行命令对于测试和开发目的都很有用，因为以这种方式安装的对象不会被系统上的其他用户使用。要做到这一点，请使用`--user`选项，它可以与大多数`flatpak`命令结合使用。

如果按照用户运行而不是在系统范围内运行，命令的行为方式完全相同。

# 基本命令

本节介绍安装，运行和管理flatpak应用程序所需的基本命令。有关flatpak命令的完整列表，请运行`flatpak --help`或参阅[flatpak命令参考](http://docs.flatpak.org/en/latest/flatpak-command-reference.html)。


## remote列表
要列出您在系统上配置的remote，请运行：
```shell
$ flatpak remotes
```
这给出了已经添加的remote,指示每个remote是否已经被添加到每个用户或系统范围内。
## 添加remote
添加remote允许您搜索并列出其内容，并从中安装应用程序。添加remote最方便的方法是使用`.flatpakrepo`文件，其中包括remote的详细信息和gpg密钥：
```shell
$ flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo
```
在这里，`flathub`是给remote的本地名称。URL指向remote的`.flatpakrepo`文件。`--if-not-exist`如果远程已经存在，则会通过一个错误来终止命令。
## 删除remote
要删除remote，请运行：
```shell
$ flatpak remote-delete flathub
```
在这种情况下，`flathub`是remote的本地名称。



## 搜索
您可以使用`search`命令在任何remote中找到应用程序。例如：
```shell
$ flatpak search gimp
```
搜索将返回符合搜索条件的任何应用程序。每个搜索结果包括应用程序ID和应用程序所在的remote。在本例中，搜索项是gimp。
## 安装
要安装应用程序，请运行：
```shell
$ flatpak install flathub org.gimp.GIMP
```
在这里，`flathub`是应用程序从remote安装的名称，`org.gimp.gimp`是应用程序的标识。
有时，应用程序将需要特定的运行时，这将在应用程序之前安装。
要安装的应用程序的详细信息也可以由`.flatpakref`文件提供，该文件可以是远程文件，也可以是本地文件。要指定`.flatpakref`而不是手动提供远程和应用程序ID，请运行：
```shell
$ flatpak install https://flathub.org/repo/appstream/org.gimp.GIMP.flatpakref
```
如果`.flatpakref`文件指定要从尚未添加的remote安装应用程序，则会在安装应用程序之前询问是否添加应用程序。
## 运行
一旦安装了应用程序，就可以使用`run`命令及其应用程序ID来启动它：
```shell
$ flatpak run org.gimp.GIMP
```
## 升级
要将所有已安装的应用程序和运行时更新到最新版本，请运行：
```shell
$ flatpak update
```
## 列出已安装应用
要列出已安装的应用程序和运行时，请运行：
```shell
$ flatpak list
```
或者，只列出已安装的应用程序，请运行：
```shell
$ flatpak list --app
```
## 移除应用
要删除应用程序，请运行：
```shell
$ flatpak uninstall org.gimp.GIMP
``` 
