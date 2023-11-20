# Manjaro折腾记录


## 为什么选择Linux&Manjaro

当然是因为Linux很酷:check_mark:

当然正经地说，酷确实是一个原因。除此之外，另一个原因是我觉得Linux在各种方面都比Windows更适合程序员：

1. 一切皆文件。没有C盘D盘等盘符让命令行cd起来很顺手（我的Windows电脑现在都是格成一个盘，数据放OneDrive就好啦）；
2. 优秀的包管理方式。统一的命令行安装卸载方式让**我觉得**自己的电脑很干净，而Windows下各种`ProgramData` `Program Files`（甚至路径里有一个空格！）`Program Files (x86)`还有注册表看的人头大。Windows下的Scoop和Choco？用起来总觉得治标不治本；
3. 高度自定义的外观。我对dock栏有一种莫名的执着，而Windows只能用它的资源管理器，第三方的dock总会有各种适配问题；
4. 越用Linux就越嫌弃Windows。个人经验。

那为什么不用Mac？因为穷。

当然Linux也有许多缺点，比如：

1. 不能打游戏！因此个人主力机肯定还得是Windows；
2. 驱动支持不好。比如我新买的Lenovo Yoga 13s，本来拿到第一件事就是想装个Linux，结果因为网卡比较新所以内核驱动不支持，联想官方放出的驱动也只有`.exe`的，加上触摸板用起来手感很奇怪，而且还有Office需求，因此随身轻薄本也得是Windows；

综上，我盯上了实验室的电脑：硬件旧，无Office需求，coding场合多，不打游戏不装QQ微信，而且只有一块物理硬盘，简直完美适合。

至于操作系统的选择，首选KDE桌面环境（GNOME的扩展性感觉还是稍弱了，而且还需要gnome-tweak结合浏览器插件来管理桌面插件？感觉不够Unix哲学！而且KDE的Yakuake下拉终端真的香），试过Manjaro与Kubuntu，体验下来还是Manjaro 更胜一筹。

顺带一提，大概两个月之前就是这台实验室电脑上的Manjaro刚崩了一次，警 钟 长 鸣。

## 资料站

由于Manjaro是ArchLinux系的，因此[ArchWiki](wiki.archlinux.org)是一个很有用的资料站。

## 安装记录

- windows.iso `shift+F10` `diskpart` `select disk 0` `clean`

### 换源

`sudo pacman-mirrors -i -c China -m rank`

**关于archlinuxcn源**

Manjaro为了稳定性，软件包版本会比archlinux低，在Manjaro里添加archlinuxcn源可能会导致依赖的版本错误，因此本篇不添加archlinuxcn源。

`sudo pacman -Syu`更新一下软件包及系统。

### 包管理

Manjaro使用`pacman`作为包管理工具，这个名字来源于老游戏“吃豆人”。这里记一些常用命令。

```bash
pacman -S pkg # 安装软件包pkg
  
pacman -R pkg # 删除软件包pkg
pacman -Rs pkg # 删除软件包pkg，及其所有未被其他软件包使用的依赖

pacman -Ss pkg # 在数据库中查找软件包pkg
pacman -Qs pkg # 在本地已安装中查找软件包pkg

pacman -Sc # 删除缓存
pacman -Scc # 清空缓存文件夹
```

为了启用AUR，我选择使用yay作为包管理工具。yay的使用与pacman几乎相同，并且能够搜索和安装AUR中的软件包。

```bash
sudo pacman -S yay # 安装yay
yay --aururl "https://aur.tuna.tsinghua.edu.cn" --save # 换源
```

**Note**

自2022-03-01后清华移除了AUR镜像，官方镜像为`https://aur.archlinux.org`，访问速度较慢。

### 输入法

输入法选择fcitx5。

```bash
yay -S fcitx5-im # 输入法框架
yay -S fcitx5-chinese-addons # 中文输入法
yay -S fcitx5-pinyin-zhwiki # 中文维基词库
yay -S fcitx5-material-color # 输入法皮肤
```

设置环境变量。

```plaintext
# /etc/environment

GTK_IM_MODULE=fcitx
QT_IM_MODULE=fcitx
XMODIFIERS=@im=fcitx
INPUT_METHOD=fcitx
SDL_IM_MODULE=fcitx
GLFW_IM_MODULE=ibus
```

重新登陆，并将fcitx5加入开机启动即可。

### 魔法

参照[魔法学院](https://archlinuxstudio.github.io/ArchLinuxTutorial/#/rookie/fxckGFW?id=%e9%ad%94%e6%b3%95%e5%ad%a6%e9%99%a2)，使用v2raya。

### 美化

#### 字体

中文可以安装文泉驿，等宽我选择JetBrains Mono。

```bash
yay -S wyq-microhei
yay -S nerd-fonts-jetbrains-mono
```


*Todo*
- [x] 输入法
- [x] 魔法
- [ ] 美化
  - [x]字体
  - [ ]oh-my-zsh
  - [ ]dock
