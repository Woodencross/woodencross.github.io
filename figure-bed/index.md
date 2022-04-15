# 个人图床搭建历程——阿里云域名+七牛云对象存储+picgo


## 为什么要建立图床

考虑：

$$ \lim\limits_{文章 \rightarrow \infty} 文章图片 \rightarrow \infty $$

所以把图片单独存放在图床里是很有远见的行为（:thinking:）[^foot1]。

[^foot1]: 按一张图 100K 算，我要发一万张图才能破 1G ，whatever。

## 图床建立过程

### 准备

建立图床需要准备两样东西：

1. 购买一个域名，需要实名认证并进行 ICP 备案。我买了个阿里云的域名，备案时被告知需要阿里云服务器的备案服务码，否则无法备案。可是我并没有使用服务器的需要啊？无奈，某宝 2 元买了一个服务码。

    然后就是填写信息，网站名称写了个人文件存储，注意名称里不能有关键字（”博客“也是关键字:sweat_smile:）。服务类型选了文件服务，之后提交阿里云初审。我大概是下午三四点提交的初审，六点多的时候就接到了阿里云的电话，告知我网站名称需要再加点英文或数字，我说那帮我加个123吧，之后就提交工信部审核了。

    第二天上午九点多，收到了审批通过的短信和备案号。总的来说还挺快的。

    然后进入阿里云 ICP 备案界面可以看到备案成功，同时提醒你进行公安备案（但是单做图床使用的话好像不必须公安备案）。

2. 购买一个对象存储服务。这里选择了七牛云，注册并实名认证后可以享受每月免费 10G 对象存储和 10G CDN 加速额度，香。

### 开始搭建

首先需要新建一个对象存储实例。访问控制选择公开，否则无法通过链接访问图片。地区我选择了华东。

{{< image src="./pic1.png" caption="公开访问控制" width=60% >}}

{{< admonition bug >}}
这里地区选择 `华东-浙江2` 的话，会由于区域代码的问题，导致后面 picgo 上传失败，这是一个 picgo 的 bug ，见 [GitHub issue](https://github.com/Molunerfinn/PicGo/issues/863) 。
{{< /admonition >}}

之后绑定域名。在 `域名管理-->自定义 CDN 加速域名` ，选择绑定域名，这里可以写已备案域名的二级域名，别的设置都默认即可。

{{< image src="./pic2.png" caption="绑定域名" width=60% >}}

最下面的 `缓存配置` ，使用推荐配置即可。

设置好后，你会拿到一个 CNAME 字段，复制下来。之后去你的域名控制台里添加一条解析记录，记录类型 `CNAME` ，主机记录写二级域名，记录值粘贴七牛云生成的 CNAME 即可。

{{< image src="./pic3.png" caption="解析域名" width=60% >}}

回到七牛云控制台，稍作等待，看到域名状态成功，然后你就可以使用你的域名 `http://img.xxxx.com/img1.png` 来随意访问图片啦。

### 上传图片到图床

[picgo](https://picgo.github.io/PicGo-Doc/zh/) 是一款方便的图床上传工具。

{{< image src="./pic4.png" caption="picgo 设置" width=60% >}}

- AccessKey / SecretKey 可以在七牛云 `密钥管理` 中找到。
- 存储空间名就写你刚创建的的实例名。
- 访问网址写刚绑定的加速域名，注意添加 `http://` 。
- [存储区域代码](https://developer.qiniu.com/kodo/1671/region-endpoint-fq)。

---

就我个人而言，我希望在上传图片前先对其压缩，这样可以节省很多空间和流量。你既可以在本地手动压缩后再上传，又可以使用 picgo 插件 [picgo-plugin-compress](https://github.com/JuZiSang/picgo-plugin-compress) 。

安装此插件需要先安装 node.js ，python 以及 vs c++ build tool 。这些都可以在安装 node.js 时勾选 `安装额外构建工具` 来安装，如果你已经安装了 node.js ，那么可以在电脑应用列表里找到 `Install Additional Tools for Node.js` ，运行它即可。

我之前因为作业需要已经安装了 python (miniconda) ，于是我想卸载 node 为我装的 python 。使用管理员身份运行 powershell ，列出本地包：
```bash
$ choco list --local

Chocolatey v1.0.1
chocolatey 1.0.1
chocolatey-core.extension 1.3.5.1
chocolatey-dotnetfx.extension 1.0.1
chocolatey-visualstudio.extension 1.10.0
chocolatey-windowsupdate.extension 1.0.4
dotnetfx 4.8.0.20190930
KB2919355 1.0.20160915
KB2919442 1.0.20160915
KB2999226 1.0.20181019
KB3033929 1.0.5
KB3035131 1.0.3
python 3.10.4
python3 3.10.4
vcredist140 14.31.31103
vcredist2015 14.0.24215.20170201
visualstudio-installer 2.0.2
visualstudio2019-workload-vctools 1.0.1
visualstudio2019buildtools 16.11.11.0
18 packages installed.
```
然后 `choco uninstall python python3` 即可。

之后在picgo `插件设置` 中搜索 `compress` ，安装作者为 `juzisang` 的那个。

安装完成后，点击插件的 `设置-->启用transformer` 可以启用，`设置-->配置plugin` 可以设置上传时图片的压缩方式。

## 后记

1. 全过程中的决速步骤：备案。
2. 今后写博客可以放肆地放图了:yum:。

{{< admonition failure >}}
好吧，hugo 站点拉取图片好像用的是 https ，但是七牛云免费流量不包括 https ，这就导致图片无法正常显示，成了一个链接。

我还是先老实把图片和博客放在一个仓库吧。:cry:
{{< /admonition >}}
