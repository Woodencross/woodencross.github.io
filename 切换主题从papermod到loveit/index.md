# 切换主题——从PaperMod到LoveIt


## 前言

之前一直在用 [PaperMod主题](https://adityatelange.github.io/hugo-PaperMod/) ，但是最近拉取了一下更新，主页中 profile 模式对于头像的读取方式发生了点变化，一是不能使用外链只能用本地图像，二是图变糊了。对问题一可以修改 layout 文件解决，但是二真的搞不懂:confused:。

于是乎，是时候打开新世界的大门了（指换主题）。

## 换新

[LoveIt主题](https://hugoloveit.com/zh-cn/) 是我早已中意的。它有简洁大方的布局，舒服的配色，合适的字体大小和间距。而且不得不提代码折叠和提醒框 shortcode ，真的是深深戳中我，可惜上篇文章刚准备介绍一个找到的提醒框代码，还没写好就夭折了:innocent:。

### 几个需要注意的点

1. 要使用 admonition 等功能，需要 hugo extended 版本。
2. fontawesome 需要用v5版的，v6试了一下无法引入。见 [fontawesome支持](https://hugoloveit.com/zh-cn/theme-documentation-content/#fontawesome) 。更新：好像需要使用 `solid` 版本，待验证。
3. `tags` `categories` `posts` 用的是 `_index.md` ，但是 `about` 页面需要用 `index.md` ，不然会不渲染内容。
4. 评论系统默认不支持 waline ，如果使用需要修改模板文件 `comment.html` `single.html` 。具体做法为：
   1. 新建 `layouts/partials/comment.html` ，复制 `themes/LoveIt/layouts/partials/comment.html` 的内容，然后在 Valine 的部分后面加入：
      ```html
        {{- /* Waline Comment System */ -}}
        {{- $waline := $comment.waline | default dict -}}
        {{- if $waline.enable -}}
            <div id="waline-container" class="comment"></div>
                <script src="{{ $waline.cdn }}"></script>
                <script>
                    new Waline({
                    el: '#waline-container',
                    serverURL: '{{ $waline.serverURL }}',
                    dark: 'body[theme="dark"]',
                    visitor: '{{ $waline.visitor }}',
                    avatar: '{{ $waline.avatar }}',
                    emoji: [
                        'https://cdn.jsdelivr.net/gh/walinejs/emojis@1.0.0/weibo',
                        'https://cdn.jsdelivr.net/gh/walinejs/emojis@1.0.0/tieba',
                        'https://cdn.jsdelivr.net/gh/walinejs/emojis@1.0.0/bilibili',
                    ],
                    login: '{{ $waline.login }}',
                    placeholder: '{{ $waline.placeholder }}'
                    });
                </script>
            <noscript>
                Please enable JavaScript to view the comments powered by <a href="https://waline.js.org/">Waline</a>.
            </noscript>
        {{- end -}}
      ```
      {{< admonition warning >}}其中 `dark` 字段是针对 LoveIt 主题进行的设置，不同主题间可能不通用。
      {{< /admonition >}}
   2. 在 `config.toml` 中，配置 waline：
      ```toml
      # Waline 评论
      [params.page.comment.waline]
        enable = true
        cdn = "//cdn.jsdelivr.net/npm/@waline/client"
        serverURL = "your-serverURL"
        visitor = true # 访问量统计
        avatar = "retro" # 默认头像
        login = "force" # 强制登陆后评论
        placeholder = "登陆后才能评论哦~"
      ```
   3. 此时访问量统计还没有真正生效。需要复制 `themes/LoveIt/layouts/posts/single.html` 到 `layouts/posts/single.html` 并修改，定位到：
      ```html
        {{- if $comment.enable | and $comment.valine.enable | and $comment.valine.visitor -}}
            <span id="{{ .RelPermalink }}" class="leancloud_visitors" data-flag-title="{{ .Title }}">
                <i class="far fa-eye fa-fw"></i>&nbsp;<span class=leancloud-visitors-count></span>&nbsp;{{ T "views" }}
            </span>&nbsp;
        {{- end -}}
      ```
      在其后面添加：
      ```html
        {{- if $comment.enable | and $comment.waline.enable | and $comment.waline.visitor -}}
            <span id="{{ .RelPermalink }}" class="leancloud_visitors" data-flag-title="{{ .Title }}">
                <i class="far fa-eye fa-fw"></i>&nbsp;<span class=leancloud-visitors-count></span>&nbsp;{{ T "views" }}
            </span>&nbsp;
        {{- end -}}
      ```
      其实就是把 valine 改成 waline 啦。
      {{< admonition warning >}}waline 说 2.x 版本会移除对 `leancloud_visitors`的支持，需注意。{{< /admonition >}}
5. 默认的 lunr 搜索的分词效果不好，如需要可以切换成 algolia 。~~这个是 Todo~~，lunr 也能用，只是如果你输入一个字或者半个单词，它就会傻掉搜不出结果，它只认词语和完整单词:sweat_smile:。

   [这篇文章](https://lucas-0.github.io/2020-06-30-hugo-and-loveit/#%E9%85%8D%E7%BD%AE-algolia)介绍了在 github action 中配置 algolia 搜索的方法，借鉴一下。
   1. 首先，注册一个 algolia 账号，并创建一个 `Index`。
   2. 其次，在 `overview-->API Keys` 中记住三个 Key。
   3. 然后在 `.github/warkflows/gh-pages.yaml` 中添加：
      ```yaml
      - name: Update Algolia Index
        env:
          ALGOLIA_APP_ID:     # 填写相应内容
          ALGOLIA_ADMIN_KEY:  # 填写相应内容
          ALGOLIA_INDEX_NAME: # 填写相应内容
          ALGOLIA_INDEX_FILE: # 填写相应内容
        run: |
          sudo apt-get -yqq install npm
          sudo npm install atomic-algolia -g
          atomic-algolia          
      ```
      关于配置 github actions 自动部署，可以参考我的[这篇文章](https://woodencross.github.io/deploy-website-via-github-actions/)。
   4. 同时在 `config.toml` 中设置搜索引擎为 `algolia` ，并填入 `appID` `index` `searchKey` （不一定必须，未测试）。
   5. 最后 push 你的代码，等待部署完成后就发现搜索变为 “Searched by Algolia” 啦！

## 美化

### favicon

使用 [realfavicongenerator](https://realfavicongenerator.net/) 来生成 favicon ，下载压缩包后放到 `/static` 目录下即可，这样 hugo 生成后这些图标会在网站根目录下。

{{< admonition tip >}}
修改完 favicon 后请重启浏览器刷新缓存后再观看效果。
{{< /admonition >}}

### header 字体

LoveIt 主题默认的字体是黑体，整体效果很不错，但是我想让左上角 header 的字体更个性一点。

{{< image src="http://img.woodencross.cn/blog/切换主题——从PaperMod到LoveIt-2023-11-20-13-54-16.png" caption="就是这个" >}}
<!-- {{< image src="./fig1.png" caption="就是这个" >}} -->

于是查阅文档，说到：

> 在 `assets/css/_override.scss` 中, 你可以覆盖 `themes/LoveIt/assets/css/_variables.scss` 中的变量以自定义样式.

于是新建 `assets/css/_override.scss` ，编辑内容为：

```scss
$header-title-font-family: Lucida Handwriting;
$header-title-font-size: 1.5rem;
```

此时再看 header：

{{< image src="http://img.woodencross.cn/blog/切换主题——从PaperMod到LoveIt-2023-11-20-13-54-46.png" caption="cooler" >}}
<!-- {{< image src="./fig2.png" caption="cooler" >}} -->

:cool::+1:

同时你也可以使用 [google fonts](https://fonts.google.com/) 的 API 来引入字体，比如在 `assets/css/_override.scss` 中：

```scss
@import url('https://fonts.googleapis.com/css2?family=Rock+Salt&display=swap');
$header-title-font-family: Rock Salt;
$header-title-font-size: 1.5rem;
```

显示效果为：

{{< image src="http://img.woodencross.cn/blog/切换主题——从PaperMod到LoveIt-2023-11-20-13-55-17.png" caption="also cooler" >}}
<!-- {{< image src="./fig3.png" caption="also cooler" >}} -->

{{< admonition info >}}
使用 API 引入字体可以使其跨平台显示，否则如手机端本地没有这个字体，则还是会以默认字体显示。
{{< /admonition >}}

### 图片相关

后来想了一下，还是决定把图片从 `static` 目录迁移到各文章自己的目录下。一开始放在 `static` 里的原因是考虑到图片可能会有复用的需求，经过这一段时间来看，本身写文章的频率就不高，一篇文章又附不了几张图，还是放在各文章的目录下方便管理和引用。

顺便，LoveIt 主题提供了 `image` shortcode 来插入图片，相比 `figure` 来说有以下优点：

1. 图片标题为浅色显示，与内容更为融洽。
2. 可以点开查看大图。
3. 支持画廊模式，该功能由 `config.toml` 中 `params.page.lightgallery` 控制是否启用，也可以在文章前置参数中单独设置。
4. 可以给图片设置链接。

但是 `image` 默认无法接收位置参数，~~使得图片都是左对齐，这在文章中不甚美观~~ 。为了使图片居中对齐，我们要用到 `style` shortcode：

```html
{{</* style "text-align:center" */>}}
{{</* image src="" */>}}
{{</* /style */>}}
```
这样图片就能居中对齐啦。

{{< admonition bug >}}
`image` 的左对齐现象触发原因不明，除了 `style` 方法外，观察到为图片添加 `caption` 字段也可以使其居中对齐。
{{< /admonition >}}

{{< admonition info >}}
`image` `style` shortcode 均需要 hugo extended 版本。
{{< /admonition >}}

## 后记

至此新主题基本收拾好了，看着不错！

