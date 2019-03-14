---
title: Windows系统下的Linux开发环境（WSL+zsh+tmux）
date: 2018-08-17 15:16:00
tags:
  - linux
  - windows
  - wsl
---


## 安装 WSL（Windows Subsystem for Linux）

非常建议您阅读官方英文文档 [Windows Subsystem for Linux Documentation](https://docs.microsoft.com/zh-cn/windows/wsl/about)， 本人也是参考该文档安装成功的。此处暂时不再赘述。

## wsl-terminal

安装成功并成功启动以后，你会发现自带终端好丑。配色也很难看并且不支持自定义。

这时候就需要 [wsl-terminal](https://github.com/goreliu/wsl-terminal) 帮忙了。此工具还有非常详细的[中文文档](https://goreliu.github.io/wsl-terminal/README.zh_CN.html)。

`wsl-terminal` 不仅允许你自定义终端主题（因为它基于`mintty`）。 还提供了很多工具，主要是在右键菜单添加入口，创建快捷方式，设置环境变量等。你可以参照文档根据自己的需求使用相应工具。

另外，本人觉得[Dracula](https://github.com/dracula/mintty/tree/8b951d742d7c2070cde63a879e7bd6e85506c2c5)这个主题不错。需要的话可以按照文档下载并更换主题。


## tmux

  `tmux` 同样是一个很常用的工具，可以让我们更随意的使用终端。

1. 安装`tmux`

        sudo apt-get install tmux

2. 你可以通过 `man tmux` 查看各种操作

3. `tmux` 一般都需要进一步配置才能使用的更加顺手，此处推荐一个配置 [.tmux](https://github.com/gpakosz/.tmux)

    ```
    $ cd
    $ git clone https://github.com/gpakosz/.tmux.git
    $ ln -s -f .tmux/.tmux.conf
    $ cp .tmux/.tmux.conf.local .
    ```

4. 在`wsl-terminal`下使用`tmux`需要进一步配置，请参考：[使用 tmux](https://github.com/goreliu/wsl-terminal/blob/master/README.zh_CN.md#%E4%BD%BF%E7%94%A8-tmux)


## zsh 和 oh-my-zsh

用过`mac`的同学肯定深有同感，`zsh + oh-my-zsh`实在太好用了。有种离不开的感觉。怎么在`Ubuntu`上使用`zsh`呢？

1. 安装`zsh`

        sudo apt-get install zsh

2. 安装[oh-my-zsh](https://github.com/robbyrussell/oh-my-zsh)，参考[Basic Installation](https://github.com/robbyrussell/oh-my-zsh#basic-installation)

3. 第2部自动安装脚本的最后一步，更改默认`shell`，可能会失败，这时候可以手动更改一下。

       sudo chsh -s /bin/zsh

4. 根据需求启用插件

        vi ~/.zshrc
    修改 `plugins` 部分，以下是本人启用的插件：
    ```
     plugins=(
      git
      history-substring-search
      vi-mode
      nvm
    )
    ```

## 在 `VSCode` 中使用 `WSL` 

  如果我们平时开发都是在 `WSL` 中运行项目，那使用 `VSCode` 开发时，终端默认使用 `powershell` 一定很难受。同样 `debug` 时也很不方便。可以通过以下方法修改：

* 终端使用 `WSL`

  修改 `vscode` 配置， 将 `terminal.integrated.shell:windows` 改为 `"C:\\WINDOWS\\System32\\wsl.exe"`

* debug时使用 `WSL`

  只需要在 `launch.json` 中增加 

      "useWSL": true


## 参考资料

* [Windows Subsystem for Linux Documentation](https://docs.microsoft.com/zh-cn/windows/wsl/about)
* [How to setup a nice looking terminal with WSL in Windows 10 Creators Update](https://medium.com/@Andreas_cmj/how-to-setup-a-nice-looking-terminal-with-wsl-in-windows-10-creators-update-2b468ed7c326)
* [Running Node.js on WSL from Visual Studio Code](https://blogs.msdn.microsoft.com/commandline/2017/10/27/running-node-js-on-wsl-from-visual-studio-code/)
