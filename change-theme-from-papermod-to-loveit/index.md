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
5. 默认的 lunr 搜索的分词效果不好，如需要可以切换成 algolia 。这个是 Todo，lunr 也能用，只是如果你输入一个字或者半个单词，它就会傻掉搜不出结果，它只认词语和完整单词:sweat_smile:。
