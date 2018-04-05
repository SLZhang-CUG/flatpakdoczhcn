[toc]

参考文章：[http://docs.flatpak.org/](http://docs.flatpak.org/)

flatpak是一种构建、发布、安装和运行应用的技术，它的桌面应用领域是linux桌面环境，尽管它也可以作为其他环境，例如嵌入式环境中进行应用程序的发布。

flatpak已经设计并且实现了以下几个目标：

 -  允许应用安装在任何一个Linux发行版中；
 -  给应用程序提供一致的环境，便于测试和减少bug；
 -  在桌面上完全集成应用程序;
 -  通过允许同时安装多个版本的运行时确保应用程序的前向兼容性;
 -  允许应用程序在Linux发行版中使用不可用的库（或不一致可用）;
 -  将应用程序与操作系统解耦，使应用程序不依赖于每个特定的发行版；
 -  通过在不同容器（sandboxes）来隔离不同的应用，来提高Linux桌面的安全性;
 -  分散的应用分发，没有一个单独的，特定的存储库。



# 基本概念
我们能够通过很少的关键概念来理解flatpak，~~这也是它不同于其他软件包的地方~~。
![](http://docs.flatpak.org/en/latest/_images/diagram.svg)
## 运行时(Runtimes)
运行时提供了应用程序使用的基本依赖关系。每个应用程序必须针对运行时构建，并且此运行时必须安装在主机系统上才能运行应用程序（flatpak可以自动安装应用程序所需的运行时）。可以同时安装多个不同的运行时，包括同一运行时的不同版本。
目前只有很少的运行时可用，包括小的和稳定的Freedesktop运行时，还有包含GNOME和KDE的运行时。
运行时是分布不可知的，不依赖于特定的分发版本。这意味着它们为应用程序提供了一个稳定的交叉分配基础，并且允许应用程序继续工作，而不管操作系统更新如何。
~~运行时提供了应用所需的基础环境，现在已经有很多可用的运行时。可以从 

## 绑定库(Bundled libraries)
如果应用程序需要运行时里面不存在的某些依赖项，那么它们可以与应用程序本身捆绑在一起。这为应用程序开发人员提供了他们使用的依赖关系的灵活性，包括使用：

 - 在分发或运行时中不可用的库;
 - 来自发行版或运行时库的不同版本的库
 - 库的补丁版本


## 沙盒
应用flatpak，每个应用程序是建立和运行在一个叫做sandbox的孤立的环境。每一个沙盒包含一个应用和他的运行时。默认情况下，应用程序只能访问其沙箱的内容。访问用户文件，网络，图形插座，总线和设备上的子系统必须明确授予。无法访问其他事物，比如其他进程。
根据需要，沙盒内的一些资源需要暴露在外，以供主机系统使用。这些被称为“导出”，因为它们是从沙盒中导出的文件，并包含应用程序的`.desktop`文件和图标等内容。

## Portals

Portals是应用程序可以通过它与沙箱内的主机环境进行交互的一种机制。他们能够与数据，文件和服务进行交互，而无需添加沙盒权限。

可以通过功能示例包括通过文件选择器对话框打开文件或打印实现Portals访问。接口工具包可以为门户实现透明支持，因此对沙箱外资源的访问将安全使用并开箱即用。

有关portals的更多信息可以在[沙箱权限](http://docs.flatpak.org/en/latest/sandbox-permissions.html)中找到。

## 存储库

通常使用存储库存储和发布flatpak应用程序和运行时，这些存储库的行为与git存储库非常相似。flatpak存储库可以包含单个对象或多个对象，并且每个对象都是版本控制的，允许升级甚至降级。

每个使用flatpak的系统都可以配置为访问任意数量的远程存储库。一旦系统被配置为访问“远程”，远程存储库的内容可以被检查和搜索，并且可以将其用作应用程序和运行时的源。

当执行更新时，将从相关remote下载已安装应用程序和运行时的新版本。就像使用git一样，只下载版本之间的差异，这使得该过程非常高效。


# 在hood下
flatpak使用一些预先存在的技术。通常没有必要为了使用flatpak熟悉这些，虽然在某些情况下它可能是有用的。他们包括：

 - [项目原子( Project Atomic)](http://www.projectatomic.io/)的[bubblewrap](https://github.com/projectatomic/bubblewrap)实用程序，它允许非特权用户使用以下内核特性设置和运行容器：
  - Cgroups
  - Namespaces
  - Bind mounts
  - Seccomp rules
 - [systemd](https://www.freedesktop.org/wiki/Software/systemd/)为沙箱设置cgroups
 - [D-Bus](https://www.freedesktop.org/wiki/Software/dbus/)是一种为应用程序提供高级API的完善方法
 - 来自[开放容器倡议](https://www.opencontainers.org/)的OCI格式，作为单一文件包的便捷传输格式
 - 用于版本控制和分配文件系统树的[OSTree](https://ostree.readthedocs.io/en/latest/)系统
 - [Appstream](https://www.freedesktop.org/software/appstream/docs/)元数据，允许flatpak应用程序在软件中心应用程序中很好地显示
