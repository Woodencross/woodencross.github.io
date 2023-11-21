# 重启博客

<!--more-->

## 其一

简单写一下，就不弄格式了。

起因是看到了别人一个搞的不错的博客，突然就想起来“哦，好像我也有个博客！”，之前还立flag说周更真是惭愧，已经成年更了。

纠结过是否使用notion作为新后端，因为我平时笔记在用它，那样几乎是零代码写博客了，但一是老文章迁移成问题，二是还是想接触一下coding，感觉每次写完文章push一下还挺带感的，于是便重启hugo小站了。

这次好像hugo与模板有更新，这两天得抽时间看一下新东西。然后考虑使用picgo+sm.ms做图床？目前还没决定好。如果编辑器使用typora的话可以做成粘贴图片自动上传到图床，这样本地目录就清爽很多。但是这样又牵扯到图床存储，将来可能发生意外的环节又多了一环。

反正先这样，总觉得这次重启不会是最后一次……（反向flag？）

## 其二

差不多整理了两天，现在博客的功能基本都正常了，正好在这篇文章记录一下，权且当作一个新起点吧。

首先将本博客站使用工具列出：

| 功能 | 名称 |
|:---:|:---:|
| 写作 | vscode + markdown |
| 图床 | 七牛云 + vs-picgo |
| 发布 | github actions + github pages|
| CDN | 阿里云 |
| 建站 | hugo |
| 主题 | DoIt |
| 评论 | twikoo |

本次重启确定了写作工具为vscode+markdown，相比于notion，markdown个人用起来还是更顺手些。那为什么笔记在用notion呢？因为notion对笔记文件的组织比较舒服。也许以后开坑谈谈各种笔记软件的个人体验。

### 写作

编辑器在vscode、typora与marktext中选择了vscode，原因是hugo博文不时会用到shortcode，而shortcode在markdown编辑器是不能被正确渲染的，vscode则可以使用内置终端all-in-one。markdown编辑器曾经的优势是可以自定义图床上传，而在我发现了vs-picgo插件后，这个优势也不存在了，vscode完胜。

### 图床

图床是我纠结了很久的地方，思考后最终还是选择将图片放在图床上，保持本地工程文件的简洁。图床使用七牛云，免费的有sm.ms，但是服务器在境外让我不太放心。在[之前的博客](https://woodencross.cn/%E4%B8%AA%E4%BA%BA%E5%9B%BE%E5%BA%8A%E6%90%AD%E5%BB%BA%E5%8E%86%E7%A8%8B/)中我记录了建立图床的步骤，最终因为https站点不允许请求http图片的原因不了了之。这次我启用了七牛云的https流量服务（可能会因此负担一些费用，希望不会太贵）使得图片可以正常请求到，同时上传工具也从picgo软件替换成为了vs-picgo插件，真正的all-in-one，完美实现编辑器里直接粘贴图片的功能，具体配置与软件没有太大差别。

另外为了方便图片自动命名，我把文章的路径与文件名都改为了中文。

### 发布

发布继续使用github actions部署到公开仓库的github pages。由于我想使用自己的域名访问，所以要在阿里云控制台添加一条CNAME记录指向github pages的域名，同时在公开仓库根目录下添加一个CNAME文件指向我自己的域名。这两点都遇到了一些问题，下面记录一下。

**阿里云一侧** 我原本以为只需要添加一条`@`的CNAME记录指向`woodencross.github.io`就可以了，但是公开仓库提示我DNS检查不通过，于是看[帮助文档](https://docs.github.com/zh/pages/configuring-a-custom-domain-for-your-github-pages-site/managing-a-custom-domain-for-your-github-pages-site)，上面说如果要配置`@`指向域名的记录，记录类型就需要为ANAME或ALIAS，可惜阿里云没有这两条选项，于是只能配置四个指向ip地址的A记录。提示DNS检查还是不通过，于是又添加一条`www`的CNAME记录，成功通过检查。

{{< image src="https://img.woodencross.cn/blog/重启博客-2023-11-20-16-20-16.png" caption="不同方案" >}}

**公开仓库一侧** 可以在`settings - pages`中添加自定义域名，但本质其实还是在仓库根目录下新建一个内容是你填写的域名的CNAME文件。由于我的公开仓库是github actions自动推送的，就导致每次推送都会把CNAME文件删除，需要重新新建。解决方法也简单，在github actions里添加一项新建CNAME文件的任务即可，这样每次推送来的文件中就会自带CNAME文件了。
```yaml
...
- name: Setup CNAME
  run: echo "woodencross.cn" > ./public/CNAME
...
```

### CDN

github.io访问速度还是太慢了，需要CDN加速。cloudflare的SSL证书好像有问题，浏览器提醒网址不安全，最终还是用了阿里云的CDN，毕竟域名也是阿里云买的，方便一些，配置就按照指引就好。希望不会花很多钱。

### 评论

twikoo部署在vercel上，国内vercel被墙基本无法访问。这次重启给vercel项目分配了一个我的二级域名，应该是可以访问了。另外，github commit触发vercel自动部署之后需要将分支promote to production，否则生产环境部署的还是之前的代码。

{{< image src="https://img.woodencross.cn/blog/重启博客-2023-11-21-16-39-58.png" caption="promote to production" >}}

## 其三

还有些todo，比如看一下配置文件更新了什么内容，整理一下文章的分类和tag，评论系统是否需要更新和邮件提醒。
