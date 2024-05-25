---
title: windows设置capslock为切换输入法
date: 2024-05-25 16:21:15
tags:
---

## 背景

最近实习，在使用公司的Mac，不得不说，Mac真的好用，切换输入法彻底废物Caps lock键太爽，经过查询和尝试，成功使用`PowerToys`试验出废弃windows的caps lock键

## 操作

### PowerToys

下载并安装[github链接](https://github.com/microsoft/PowerToys/releases)

下载自己对应的版本即可

### 设置

#### 第一步

首先确保以下事情：

- 确保你的windows没有开启大写锁定

- 确保你使用了两个语言的输入法，并且**没有**设置为“**允许我为每个应用窗口使用不同的输入法**”，具体设置参考下图。

  ![image-20240525163040147](https://lsp-1259035619.cos.ap-beijing.myqcloud.com/typora/image-20240525163040147.png)

  如果没有两种输入法：英文和中文，可以在下面位置安装你需要的输入法

  ![image-20240525163231054](https://lsp-1259035619.cos.ap-beijing.myqcloud.com/typora/image-20240525163231054.png)

- 确保你使用了比如**`win+space`**的方式切换你的输入法（**默认都是这个，可以自行尝试一下**）

- 确保你不使用shift来作为你切换中英文的方式

  ![image-20240525162832693](https://lsp-1259035619.cos.ap-beijing.myqcloud.com/typora/image-20240525162832693.png)

#### 第二步

打开你的powertoys，并在仪表板中开启键盘管理器

![image-20240525163407814](https://lsp-1259035619.cos.ap-beijing.myqcloud.com/typora/image-20240525163407814.png)

点击**`重新映射键`**，将你的capslock映射为你的快捷键即可

![image-20240525163529720](https://lsp-1259035619.cos.ap-beijing.myqcloud.com/typora/image-20240525163529720.png)

这里我的是win+space，当然也有上面我设置的alt+shift，这个无所谓，只要能映射为你需要的快捷键即可。

大功告成！！

## 最后

### 注意

- 这里设置后，你无法再通过简单的shift来切换全英文输入法，必须使用caps lock才行。
- 如果想要在微软拼音下切换中英文，那么还可以使用ctrl+space的方式来进行切换，但其实已经没啥用了，不如按一下caps lock进行切换，方便。
- 为什么不可以设置为不同应用一个输入法？这个主要是因为微软输入法中切换中英文的问题，因为有些软件，默认是微软输入法的英文输入，这时候想要输入中文就需要ctrl+space的方式，或者自己手动点击中英文切换才可以，比较麻烦。当然你不嫌弃麻烦，自己也可以开启。
