`su`、`sudo su`、`sudo -s` 和 `sudo -i` 的区别
============================================

The Differences between Su, Sudo Su, Sudo -s and Sudo -i

在 Linux 命令行中有多种方式在终端中切换 root 会话（session）。这可能让人很迷惑，很多初学者可能不熟悉每个命令如何获得 root 访问权限、它们有何差异以及这些差异影响到什么场景。

正因为如此，下文将分析可以在终端中获得 root 访问权限的每个命令。准确解释它们如何获得 root 权限、何时使用它们以及它们之间的区别。


### su

`su` 命令替换 shell 中的当前用户。可以在 `su` 后面跟特定用户名（如：`su normal-username`）来切换到任何用户。这个命令告诉系统切换（并退出）当前用户到特定用户。如果 `su` 后面不提供任何参数，这个命令可以获取 root 权限。

当用户想直接使用系统的 root 账户时 `su` 是最好的选择。它不会涉及 `sudo` 或类似的操作。相反，必须知道 root 账户的密码用于登录操作。另外，`su` 之外的命令无法获得 root home 目录和 root 环境。(译注：这里描述有误，见下面注视。)

 > 根据 man su 上的介绍：
 > 
 > `su` 后什么也不带时，是直接切换用户，只会设置 `HOME` 和 `SHELL`（如果目标用户不是 root，还会设置 `USER` 和 `LOGNAME`）。 
 > `su` 后带 `--login`（简写：`-`）时，会类似于真正的用户登录：清除所有除了 `TERM` 外的环境变量；初始化 `HOME`，`SHELL`，`USER`，`LOGNAME`，`PATH` 变量；转到目标用户 home 目录。
 > 所以不存在退出当前用户。
 > 推荐使用 `--login`（简写：`-`）避免环境混乱。

### sudo su

这个命令类似于直接运行 `su`。但是不是直接告诉系统“切换用户”，而是告诉系统以 `root` 用户权限执行 `su`。`sudo su` 执行的时候，“.profile,” “.bashrc” 和 “/etc/profile” 将会被加载，就像执行 `su`（或是 `su root`）。这是因为在命令前加 `sudo` 执行时，将会以 root 权限执行它。

尽管跟 `su` 区别不大，`sudo su` 仍是一个非常重要的命令，因为当用户使用 `su` 获取 root 权限时，必须知道 root 的密码。而 `sudo su` 需要输入的是当前用户的密码。优点是无需知道 root 密码即可获取 root 权限，安全性有所提高。

### sudo -i

使用 `sudo -i` 实际上与 `sudo su` 命令相同。用户可以通过 `sudo` 获取 root 权限，并且不需要切换成 root 用户。跟 `sudo su` 类似，`-i` 标记表示还将获取 root 环境（译注：参考 su 节的注释）。

`sudo -i` 与 `sudo su` 的不同之处在于 `sudo -i` 是一种更简洁的获取 root 和 root 环境的方式。如何做到的？使用 `sudo su` 时，将会不止一次使用 setuid 命令。这会导致识别当前环境哪些变量保留、哪些变量会被重置（当切换到 root 环境时）变得困难。`sudo -i` 则不会，所以这是大部分人在不直接登录时倾向于使用 `sudo -i` 获取 root 权限的原因。

### sudo -s

`sudo` 的 `-s` 标记将会读取当前用户的 `$SHELL` 变量。这个命令类似于执行 `sudo /bin/bash`。`sudo -s` 是“non-login”的 shell。这意味着跟 `sudo -i` 与 `sudo su` 不同，切换用户时不会读取任何环境变量文件。这表示当用户执行 `sudo -s`，将会切换到 root，但是不会改变用户或是用户环境。

这个命令最好是用于用户不像使用 root 环境，只是想在一个 root shell 中简单执行一些命令。上面提到过的命令将会获取 root 权限，但是也会加载 root 环境变量，并允许更全面的 root 访问（可能导致安全问题）。


### 结论：我该使用哪个命令？

每个命令都有特定的使用场景。重要的是理解每个命令干的事情，及何时使用它们。从表现来看，如果想同时切换到 root 环境，`sudo -i` 是最好的方式。而 `sudo -s` 只会获取 root shell，不会加载 root 环境，可能提高一些安全性。

没有一个 100% 好的命令。随着用户对命令行越来越熟悉，他们需要考虑获取 root 的每一种方式（并且有很多），综合考虑使用合适的命令。希望这篇文章可以帮到你。

你更倾向于使用哪种获取 root 访问的方式？告诉我们吧！

**原文：**[The Differences between Su, Sudo Su, Sudo -s and Sudo -i](https://www.maketecheasier.com/differences-between-su-sudo-su-sudo-s-sudo-i/)
