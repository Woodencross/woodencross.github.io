# Ubuntu折腾记录


## Why Linux & Ubuntu

因为Linux很酷:check_mark:

之前折腾过一阵Manjaro，是一个arch系的滚动更新的系统，结果被我滚挂过两次:sweat_smile:。因为要装在实验室电脑上，寒暑假可能一段时间不会碰它，这就又加大了滚挂的概率，所以这次捯饬一下Ubuntu。顺便换个口味，由KDE Plasma换成Gnome桌面环境体验一下。

P.S. 试了一下Pop!_OS，好看是确实好看，但是自带的商店应用会每次在联网打开时卡死，啊这。

## 安装过程

使用[ventoy](https://www.ventoy.net/cn/index.html)做启动盘，很方便，iso选择Ubuntu 20.04 LTS。

上来先用Windows11的cmd格一下盘，在安装界面`shift+F10`：

```cmd
diskpart
list disk
select disk 0
clean
```

之后开始安装，一路next。

### 换源

`软件和更新-->Ubuntu软件-->下载自`，我选了阿里云镜像。然后执行：
```bash
sudo apt update
sudo apt upgrade
```
来更新。

### 魔法

选择v2raya，不便多说。用Linux还是得有魔法，或者就用deepin那种，毕竟主要的生态还是在国外的。

### 家目录转英文

选择中文安装系统之后，家目录下默认为中文，这样使用终端很不方便，于是将其英文化。

1. 终端输入
   ```bash
   export LANG=en_US
   xdg-user-dirs-gtk-update
   ```
   在弹窗中同意。

2. 终端输入
   ```bash
   export LANG=zh_CN
   ```

3. 注销再登陆，选择保留旧名称，不再提醒。

### 输入法

Ubuntu自带的ibus使用体验不是很好，候选字体缩放等等都是问题，这次图省事就装了[搜狗输入法Linux版](https://pinyin.sogou.com/linux/help.php)，关掉悬浮窗还是很干净的。

总的来说，Ubuntu的缩放和字体渲染做的都很不错，至此新系统已经可以正常使用啦。

## 优化

虽然KDE桌面环境扩展度高，但是折腾到最后总是有一种零碎的感觉，gnome反而更像一个整体。

### Gnome Tweaks

这是Gnome桌面的优化软件，用来调整一些外观。
```bash
sudo apt install gnome-tweaks
```
在`扩展-->Desktop Icons`中关闭桌面文件夹和回收站图标。

### Dock

在[Gnome Extensions](https://extensions.gnome.org)里安装Dash to Dock，调整图标大小为64，避开所有窗口，使用内置主题。

### 字体

中文使用文泉驿米黑：
```bash
sudo apt install fonts-wqy-microhei
```

英文使用[JetBrains Mono](https://www.jetbrains.com/lp/mono/)，下载后解压到`~/.local/share/fonts`（或`/usr/share/fonts`，安装为系统字体），并执行`fc-cache -f -v`。

最后使用Gnome Tweaks来设置字体。

### 主题

由于辣鸡显卡，装了自定义主题后总会出现贴图丢失的情况，所以就不搞自定义主题了，只改一下颜色，使用[yaru-colors](https://github.com/Jannomag/Yaru-Colors)，clone下来之后`sudo bash ./install.sh`即可。我全部选择Aqua主题，比较清新。

之后还需要改一下终端配色，在配置文件首选项里改成`solarized-dark`，与Aqua主题色也比较配。

**Note**

我的环境安装yaru-colors后出现图标变形错位的现象，卸载无法解决问题，请谨慎安装。参见[Github issue](https://github.com/Jannomag/Yaru-Colors/issues/111)。

### zsh & oh-my-zsh

zsh，一个比默认的bash好用得多的shell，oh-my-zsh是zsh的一个框架，赋予其额外功能以及插件管理等。

首先安装并切换默认shell为zsh:
```bash
sudo apt install zsh
zsh # 进行初始设置，全按照默认即可
chsh -s $(which zsh) # 切换默认shell
```
注意设置完后注销重新登陆。

之后参照[oh-my-zsh](https://ohmyz.sh)的指令安装oh-my-zsh:
```bash
sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
这一步需要魔法助力，没有魔法的话可以搜索使用gitee镜像。

之后在`~/.zshrc`中更改配置，先改一下主题：
```plaintext
ZSH_THEME="eastwood"
```
这个主题非常简约，深得我心。然后更改插件，在`~/.zshrc`中
```
plugins=(git)
```
说明oh-my-zsh默认开启git插件，后面自己配置插件就是下载好后填进括号里就行。介绍一下我安装的插件：

1. z
   这个是默认装好的，需要自己开启，用处是根据历史快速跳转。比如之前访问过`~/Downloads/game/pacman`，再次访问的话只需`z pacman`即可。

2. zsh-syntax-highlighting
   终端命令语法高亮显示。

3. zsh-autosuggestions
   自动补全。

配置好后`.zshrc`是这样的：
```
plugins=(git z zsh-syntax-highlighting zsh-autosuggestions)
```

### fish shell

fish是一个开箱即用的shell，几乎不需要自行配置。之前设置自定义终端颜色之后，与oh-my-zsh的颜色好像有点冲突，有时光标会不闪烁，很难受，于是改用了fish。
```bash
sudo apt install fish
chsh -s $(which fish)
```

### Pop Shell

[Pop Shell](https://github.com/pop-os/shell)来源于基于Ubuntu的Pop!_OS，拥有非常好用的窗口自动平铺功能，会按照打开窗口的先后顺序以斐波那契数列形式在桌面上自动平铺。当然如果你愿意折腾，也可以使用awesome、i3wm等。

首先去下载它的release，之后安装TypeScript和Make：
```bash
sudo apt install node-typescript make
```
将release解压缩后在目录中执行
```bash
make local-install
```

安装完毕后，在右上角打开`Tile Windows`和`Show Active Hint`。

### 清理启动引导项

因为来回装了不少系统，因此efi启动引导项非常多，正好今天查了查怎么删除：使用`efibootmgr`。
```bash
efibootmgr # 列出所有引导项
efibootmgr -b xxxx -B # 删除序号为xxxx的引导项
```

## 后记

虽然说本文内容不多，但是前前后后也是折腾了好几天，其间遇到各种细节问题，不胜枚举。有句话说得好：“Linux is free when your time is free”，此番过后也是深以为然。话又说回来，虽然历经坎坷，但过程中获得的快乐与成就感不会骗人。我想这就值得了。:smile:
