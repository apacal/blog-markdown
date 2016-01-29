#如何高效实用Mac OS

转岗位做andorid有一段时间了，终于leader给我换了一台IMAC，做为一个客户端开发的同学，很有必要使用一个Unix like 系统，一套完整的工具（grep, find, sed, awk...）以及其的设计哲学，比如管道的设计（｜就是管道了），使用了有两个星期了。如何高效地使用Mac os是一门哲学。

首先Mac os是BSD的分支，是Unix like的一种，我使用当然是为了他的terminal.

##常用工具
###Homebrew

包管理工具，官方称之为The missing package manager for OS X。

类似apt-get, yun, 但是homebrew是去下载源代码，如何make，make install和apt-get, yum有本质的不同(brew-cask才和apt-get一样，下载的是二进制包）

安装步骤：先打开 Terminal 应用，输入：

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

有了 brew 以后，要下载工具，比如 MySQL、Gradle、Maven、Node.js 等工具，就不需要去网上下载了，只要一行命令就能搞定：

```
brew install mysql gradle maven node
```
PS：安装 brew 的时候会自动下载和安装 Apple 的 Command Line Tools。

###Homebrew Cask

brew-cask 允许你使用命令行安装 OS X 应用。比如你可以这样安装 Chrome：brew cask install google-chrome。还有 Evernote、Skype、Sublime Text、VirtualBox 等都可以用 brew-cask 安装。

brew-cask 是社区驱动的，如果你发现 brew-cask 上的应用不是最新版本，或者缺少你某个应用，你可以自己提交 pull request。

安装：

```
brew install caskroom/cask/brew-cask
```
应用也可以通过 App Store 安装，而且有些应用只能通过 App Store 安装，比如 Xcode 等一些 Apple 的应用。App Store 没有对应的命令行工具，还需要 Apple ID。倒是更新起来很方便。

几乎所有常用的应用都可以通过 brew-cask 安装，而且是从应用的官网上下载，所以你要安装新的应用时，建议用 brew-cask 安装。如果你不知道应用在 brew-cask 中的 ID，可以先用brew cask search命令搜索。

###iTerm2

iTerm2 是最常用的终端应用，是 Terminal 应用的替代品。提供了诸如Split Panes等一群实用特性。它默认的黑色背景让我毫不犹豫的抛弃了 Terminal。

安装：

```
brew cask install iterm2
```
感谢 brew-cask，我们可以通过命令行自动安装 iTerm2 了。

在终端里，除了可以用⌃E等快捷键（详见其他快捷键）之外，还可以使用⌥B、⌥F等快捷键（具体可以参考这里）。前提是这样设置一下：

选择Iterm菜单 > Preferences > Profiles，选择你在使用的 Profile（默认是Default），在Keys标签页中把Left option (⌥) key acts as和Right option (⌥) key acts as都设置成+ESC。

在打开新的窗口/标签页的时候，默认情况下新窗口总是 HOME 目录，还需要我每次敲命令才能进入工作目录。如果想要这个新窗口在打开的时候就自动进入工作目录，需要如下设置：

选择Iterm菜单 > Preferences > Profiles，选择你在使用的 Profile（默认是Default），在General标签页中的Working Directory部分中选择Reuse previous seesion's directory。

至此，Terminal 应用已经出色的完成了其历史使命。后面就交给 iTerm2 啦。

###Oh My Zsh

默认的 Bash 是黑白的，没有色彩。而 Oh My Zsh 可以带你进入彩色时代。Oh My Zsh 同时提供一套插件和工具，可以简化命令行操作。后面我们会看到很多介绍，你会看到我爱死这家伙了。

安装：

```
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```
目前我使用的插件有：git z sublime history rbenv bundler rake

Oh My Zsh 使用了 Z shell（zsh），一个和 Bash 相似的 Shell，而非 Bash。

在 Z shell 中，~/.zshrc是最重要的配置文件。Oh My Zsh 在安装的时候会把当前环境的$PATH写入~/.zshrc中。这并不是我期望的行为，因为使用了 brew，我们基本不再需要去定制$PATH，而 Oh My Zsh 提供的默认$PATH值$HOME/bin:/usr/local/bin:$PATH是非常合适的一个值，它把$HOME/bin加入了$PATH，可以让我们把自己用的脚本放到$HOME/bin下。

所以建议把~/.zshrc重置：

cp ~/.oh-my-zsh/templates/zshrc.zsh-template ~/.zshrc
Oh My Zsh 还有很多有价值的插件。

替代品有 Oh My Fish，使用了 Fishshell 作为基础。

Git 常用别名

几乎每个人都会使用一些方法比如 Git 别名来提高效率，几乎所有人都会把使用git st来代替git status。然而这需要手动设置，每个人也都不完全一样。

Oh My Zsh 提供了一套系统别名（alias），来达到相同的功能。比如gst作为git status的别名。而且 Git 插件是 Oh My Zsh 默认启用的，相当于你使用了 Oh My Zsh，你就拥有了一套高效率的别名，而且还是全球通用的。是不是棒棒哒？下面是一些常用的别名：

| Alias |	Command |
|:------------- |----------:|
|gapa	|git add --patch|
|gc!	|git commit -v --amend|
|gcl	|git clone --recursive|
|gclean	|git reset --hard && git clean -dfx|
|gcm	|git checkout master|
|gcmsg	|git commit -m|
|gco	|git checkout|
|gd	git |diff|
|gdca	|git diff --cached|
|glola	|git log --graph --pretty = format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit --all|
|gp	git |push|
|grbc	|git rebase --continue|
|gst	|git status|
|gup	|git pull --rebase|
|gwip	|git add -A; git rm $(git ls-files --deleted) 2> /dev/null; git commit -m "--wip--"|



完整列表请参考：[https://github.com/robbyrussell/oh-my-zsh/wiki/Plugin:git](https://github.com/robbyrussell/oh-my-zsh/wiki/Plugin:git)

###tmux
tmux是一个终端管理工具，是screen的加强版。

```
brew install tmux
```

为了和mac os的系统剪切板配合使用，需要在~/.tmux.conf加入

```
# # Working with the Clipboard on mac os
bind C-c run "tmux save-buffer - | pbcopy"
bind C-v run "tmux set-buffer \"$(pbpaste)\"; tmux paste-buffer"
```

可以安装一个tmux-resurrect的插件来保持tmux的状态，并且进行恢复状态。如下面.tmux.conf写的一样，需要先安装tmux-plugins/tmp然后才能安装tmux-resurrect


```
# install plugin
# git clone https://github.com/tmux-plugins/tpm ~/.tmux/plugins/tpm
#
# List of plugins
set -g @plugin 'tmux-plugins/tpm'
set -g @plugin 'tmux-plugins/tmux-sensible'
# Other examples:
# set -g @plugin 'github_username/plugin_name'
# set -g @plugin 'git@github.com/user/plugin'
# set -g @plugin 'git@bitbucket.com/user/plugin'

# Initialize TMUX plugin manager (keep this line at the very bottom of tmux.conf)
run '~/.tmux/plugins/tpm/tpm'
# type this in terminal
# $ tmux source ~/.tmux.conf
# Add new plugin to ~/.tmux.conf with set -g @plugin '...'
# Press prefix + I (capital I, as in Install) to fetch the plugin.
# end



set -g @plugin 'tmux-plugins/tmux-resurrect'
#prefix + Ctrl-s - save
#prefix + Ctrl-r - restore
```

###MacDown

MacDown 是 Markdown 编辑器。由于 Mou 一直不支持代码高亮，我就转向了 MacDown。完美支持GFM。

我特别喜欢 Markdown，我用 Makdown 来写文章（包括本文），写幻灯片（reveal.js）。Markdown 可以让我专注于内容本身，而无需花精力在排版和样式上。

安装：

```
brew cask install macdown
```
###z

在打开终端后，你是怎么进入项目的工作目录？是cd xxx，⌃R还是用别名？

z 工具可以帮你快速进入目录。比如在我的 Mac 上运行z cask就会进入/usr/local/Library/Taps/caskroom/homebrew-cask/Casks目录。

这货的安装非常方便，甚至都不需要下载任何东西，因为它已经整合在了 Oh My Zsh 中。编辑~/.zshrc文件，在plugins=(git)这行中加上z变成plugins=(git z)，然后运行source ~/.zshrc重新加载配置文件，就可以使用 z 了。

替代品有 autojump。autojump 需要使用 brew 安装。

###Vimium

Vimium 是一个 Google Chrome 扩展，模仿vim的操作方式来操作浏览器，让你可以纯键盘操作 Chrome，把你的 Chrome 变成“黑客的浏览器”。

安装方法请参考官方网站。

其他浏览器也有类似的工具，比如 FireFox 的 KeySnail。

###我的配置文件
[https://github.com/apacal/userprofile.git](https://github.com/apacal/userprofile.git)

##常用命令
```
open #用用程序打开指定的文件
```
