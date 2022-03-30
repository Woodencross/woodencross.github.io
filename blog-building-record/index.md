# 个人博客小站建设记录


## 为什么想建个人博客

一个重要的原因是因为想有一个地方可以自己记录一些东西，而各种现成的平台一是觉得自定义程度不够高，二是内容又会经过层层审核甚至被夹掉，所以到最后就选择了自建一个博客小站。看着小站在自己手中逐渐完善，就好像自己逐渐装修起来一个精致舒适的小屋一样，会真正有一种“属于我”的归属感，这也是这个小站叫做“Woodencross's Cyber **Home**”的原因。寄蜉蝣于天地，渺沧海之一粟，希望这个小站能够成为QC在赛博空间里的“锚”。

当然还有一个原因是忙完前一段突然得了点闲，就想折腾了:sweat_smile:。

## Hugo&Github Pages

自建博客当然要先选择一个框架啦，于是Google了一下目前主流的博客框架，再上Github看了一下:star:，Hugo的星最多，那么就决定是你了！

至于Github Pages，白嫖嘛，多是一件美事啊。

## 踩坑经历

### 框架问题

*Todo: config.yaml与content/*

### 图片插入问题

由于编译静态网站后文件结构不同，因此在文章的`.md`中使用相对路径插入图片是行不通的，如下：
```plaintext {linenos=false}
posts
    |-article
        |-article.md
        |-img.png
```
此时在`article.md`中使用`![](./img.png)`引用图片会404。

那么正确的图片引用姿势有这几种：

1. 使用图床，然后引用图片网址。这样的风险就是万一图床挂了或者网址变动就全完了。
2. 使用绝对路径。Hugo在构建静态网页时会将`content/`下的文件夹和`static`文件本身都拷贝到网址之下（应该是基于设定的`baseUrl`？），所以如果图片是这样放的：
    ```plaintext {linenos=false}
    posts
        |-article
            |-article.md
            |-img.png
    ```
    那么在`article.md`中可以使用`![](/post/article/img.png)`来引用图片。

    如果图片是这样放的：
    ```plaintext {linenos=false}
    static
        |-images
            |-img.png
    ```
    那么在`article.md`中可以使用`![](/images/img.png)`来引用图片。

不过在这个~~动荡的~~时代，资源还是自己掌握最好哇！说到这我又想起来以后自己搞一个家用NAS的Flag，想必又是一个大坑……

顺便一提，如果要调整插入图片的位置和大小的话，markdown自身语法支持并不好，而使用html的话Hugo好像支持也不太完善，因此使用Hugo提供的Shortcodes是一个不错的选择，回头专开一章写一写（挖坑不填警告）。

### 代码高亮问题

Hugo提供了Shortcodes支持[代码块高亮](https://gohugo.io/content-management/syntax-highlighting/)，但是Shortcodes我还没搞明白……

万幸的是，Hugo从v0.60.0开始支持如下写法：

```plaintext
    ```python {linenos=true, hl_lines=[1, "15-17"], linenostart=199}
    //code
    ```
```
这种写法好像只有goldmark渲染器支持。解释一下大括号中的元素：
- `linenos`：布尔值，是否显示行号。
- `hl_lines`：需要高亮渲染的行号。
- `linenostart`：行号开始显示的值。

### 搜索问题

[PaperMod主题内置了Fuse.js搜索功能](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-features/#search-page)，首先需要在`config.yaml`加入：
```yaml
outputs:
    home:
        - HTML
        - RSS
        - JSON # is necessary
```
之后需要在`content/`下新建一个`search.md`（我的是`search/_index.md`），表示一个新页面，并添加如下内容：
```yaml
---
title: "Search" # in any language you want
layout: "search" # is necessary
# url: "/archive"
# description: "Description for Search"
summary: "search"
---
```
其中`layout: "search"`是必要的，它指定了要以“search”的页面结构来渲染这一页。

其实主题作者基本把搜索都给弄好了，但是我一开始傻傻地把`.yaml`里要加代码的位置搞错了，把`outputs`加到了`param`参数下，人家应该在根下自成一个参数的。因此搜索不好使，我还把搜索页面的描述改成了“随时失灵……”。

### 分享按钮问题

一开始每篇文章最下面都会有一行分享按钮，我觉得不好看而且实用性不高（都是些Twitter、Facebook啥的），就想给它禁用掉。为此经历了三个阶段：
1. 在项目内搜索`twitter.com`，发现`themes/PaperMod/layouts/partials/share_icons.html`包含这一字段，于是新建`layouts/partials/share_icons.html`并置为空文件，这样可以覆盖主题里的对应文件。为防止误删我还特意写了一段注释（# 不要删除这段空循环否则后果自负.jpg）。
2. 可是马上我就发现，可以通过在文章`.md`的front matter（即前缀）中声明`disableShare: true`来禁用当前页面的分享按钮，于是我把第一步的空文件删掉，给目前的每篇文章都加上这个声明，并且给`archetypes/default.md`里也加了一下。这个文件是每次运行`Hugo new`新建文章时的模板，这样我的新文章也都能不显示分享了。
3. 一天后，我在翻阅`config.yaml`时发现一行
   ```yaml
   ShowShareButtons: true
   ```
   啊这，难道是【那个功能】！改成false试了一下果然……
   
大概这就是“认识的波浪式前进与螺旋式上升吧”。

### 评论问题

#### Disqus

Hogo对评论的支持与主题有关。我用的PaperMod虽然没有内置主题，但是它留下了自定义的接口，既然有接口那可不能浪费了啊。

首先按照PaperMod主题的[文档](https://adityatelange.github.io/hugo-PaperMod/posts/papermod/papermod-features/#comments)新建一个`layouts/partials/comments.html`文件，还要修改`config.yaml`：
```yaml
params:
    comments: true
```
接着按照Hugo的[文档](https://gohugo.io/content-management/comments/)去注册一个Disqus账号获得disqus-short-name，这是什么呢？原来Disqus会对我登记的每一个站点生成一个`disqus-short-name.disqus.com`的管理页面，这个short name就是里面的三级域名，当然这是可以在登记网站时自定义的。

获得short name之后，需要回`config.yaml`中把它记下来：
```yaml
disqusShortname: disqus-short-name
```
之后再根据Hugo官方的[这个文档](https://gohugo.io/templates/internal/#disqus)，复制里面的`layouts/partials/disqus.html`到我前面创建的`comments.html`里（不能改名，这个文件会按名字在`layouts/_default/single.html`中被调用渲染），之后注释掉里面检测localhost的if语句，就可以本地调试看到评论区出现啦。

PS：`disqus.html`的内容在Disqus的上手文档里也有提供，好像叫做Universal Code，两者大差不差，区别就是Hugo的代码调用了`.Site.DisqusShortname`来获取short name而Disqus的代码是直接把short name写在链接里了。

为了防止评论出bug，我还配置了一下`disqus_identifier`和`disqus_url`，具体是在`comments.html`中`script`一开始加入代码：
```html
var disqus_config = function() {
    this.page.title = '{{ .Title }}';
    this.page.url = '{{ .Permalink }}';
    this.page.identifier = '{{ .Permalink }}';
};
```
不过效果如何还有待验证，也可能就是个安慰剂，反正有备无患嘛。

还有，Disqus默认的评论区总感觉有种湾湾风格不知道为啥……回头看看能不能美化一点，或者看别人用的[Waline](https://waline.js.org/)挺好看的说不定换一下。

#### Gitalk

Push到网站上后才发现Disqus在国内被墙了，还是换一个吧……先试试Gitalk，这是一个基于Github issue的评论服务，正好可以使用网站的公开仓库issue存放评论，官方文档在[这里](https://github.com/gitalk/gitalk/blob/master/readme-cn.md)，但是对我来说写的不够傻瓜，于是又在别人的博客上找到了[这个](https://charmerkai.github.io/posts/2020-11/%E7%BB%99hugo%E5%8A%A0%E5%85%A5gitalk/)。

设置步骤如下：

1. 在Github`Settings->Developer settings->OAuth Apps`中新建一个授权，其中`Homepage URL`和`Authorization callback URL`都填主页的地址，并记住生成的`clientID`和`clientSecret`。
2. 更改`comments.html`为
   ```html
    <div id="gitalk-container"></div>
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.css">
    <script src="https://cdn.jsdelivr.net/npm/gitalk@1/dist/gitalk.min.js"></script>
    <script>
      const gitalk = new Gitalk({
        clientID: '{{ .Site.Params.Gitalk.clientID }}',
        clientSecret: '{{ .Site.Params.Gitalk.clientSecret }}',
        repo: '{{ .Site.Params.Gitalk.repo }}',
        owner: '{{ .Site.Params.Gitalk.owner }}',
        admin: ['{{ .Site.Params.Gitalk.owner }}'],
        id: location.pathname, // Ensure uniqueness and length less than 50
        distractionFreeMode: false // Facebook-like distraction free mode
      });
      (function() {
        if (["localhost", "127.0.0.1"].indexOf(window.location.hostname) != -1) {
          document.getElementById('gitalk-container').innerHTML = 'Gitalk comments not available by default when the website is previewed locally.';
          return;
        }
        gitalk.render('gitalk-container');
      })();
    </script>
   ```
3. 在`config.yaml`中加入相关参数：
   ```yaml
    params:
      Gitalk:
        clientID: your clientID
        clientSecret: your clientSecret
        repo: "Woodencross.github.io"
        owner: "Woodencross"
        admin: ["Woodencross"]
   ```
   并设置
   ```yaml
    comments: true
   ```

设置好之后，本地调试的话需要把检测localhost的代码删掉。建议是push到网站上再调试，因为评论需要登陆Github账号。Gitalk会给每一篇文章新建一个issue来存放评论，初始化时需要身份为admin的用户打开文章评论区才会自动新建一个issue。

效果如下：

{{< figure src="/images/screenshots/Gitalk亮.png" align="center" >}}

与主题契合的挺好！美中不足的就是在暗色模式下评论依旧是亮色，有些违和：

{{< figure src="/images/screenshots/Gitalk暗.png" align="center" >}}

只能说还是不够好呀。

#### Waline

> 同时 @waline/client 还带来了内置的暗黑模式支持。——Waline

于是我又来折腾辣。官方手册[在此](https://waline.js.org/guide/get-started.html)，这个写的很傻瓜基本上照做就没啥问题。

结束后我的`comments.html`是这样的：
```html
<script src="//cdn.jsdelivr.net/npm/@waline/client"></script>
<div id="waline-container"></div>
<script>
    Waline({
      el: '#waline-container',
      serverURL: 'my server URL',
    });
</script>
```
但是在网站暗色模式下依旧是亮色

{{< figure src="/images/screenshots/Waline亮.png" align="center" >}}

于是来看手册：

> 通常网站会通过两种方式启用暗黑模式支持:
> - 使用@media选择器通过prefers-color-scheme来根据设备颜色模式状态自动切换
> - 通过修改dom根元素(html或body)的属性与class来动态应用或取消暗黑模式的颜色样式。
> 如果你在第一种方式的站点上启用Waline，你只需将dark设置为'auto'。
> 对于第二种站点，你需要将dark设置为令暗黑模式生效的CSS选择器。

首先尝试在Waline()中添加
```html
dark: 'auto'
```
发现无效，说明我的网站应该是第二种情况，那就是说要填一个CSS选择器的名字？继续看手册的例子：

> vuepress-theme-hope: 它会在`<body>`上添加theme-dark class来开启暗黑模式。那么你需要将dark选项设置为body.theme-dark。

在`<body>`上添加？依照我贫瘠的html知识的指引，我打开了f12开发者模式，并尝试切换主题：

{{< figure src="/images/screenshots/body亮.png" caption="亮色" align="center" >}}

{{< figure src="/images/screenshots/body暗.png" caption="暗色" align="center" >}}

我超，还真有。于是添加
```html
dark: 'body.dark'
```

{{< figure src="/images/screenshots/Waline暗.png" align="center" >}}

大功告成！心里终于舒坦了~而且Waline还支持表情，感觉挺不赖，就先用着它了。
