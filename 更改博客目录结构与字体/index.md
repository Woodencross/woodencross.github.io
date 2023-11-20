# 更改博客目录结构与字体


## 起因

之前所有的文章都是直接堆在`/posts`下，虽然用了`categories`和`tags`双分类，但是感觉还是不够好，比如无法从一篇文章导航到它所属的category。于是今天决定使用二级目录来储存文章，参考[这篇文章](https://www.sulvblog.cn/posts/blog/build_hugo/#5目录设置)。

## 二级目录

首先删去之前的`categories`分类。在`config.yaml`中
```yaml
taxonomies:
  #category: categories 注释掉
  tag: tags

menu:
  main:
    - identifier: posts
      name: 文章
      url: /posts/
      weight: 1
    #- identifier: categories 注释掉
      #name: 分类
      #url: /categories/
      #weight: 2
    - identifier: archives
      name: 时间线
      url: /archives/
      weight: 10
    - identifier: tags
      name: 标签
      url: /tags/
      weight: 20
    - identifier: search
      name: 搜索
      url: /search/
      weight: 30
    - identifier: about
      name: 关于
      url: /about/
      weight: 40
```
然后再删去`archetypes`里的相关内容。

这次暂且先将二级目录分为三类，分别是生活相关的**生活小窝**、技术相关的**技术图书馆**，还有建站专题**博客历史馆**。国有国史，站有站史，这很合理（有点赛博过家家的感觉了）。重排好的目录如图。

{{< image src="http://img.woodencross.cn/blog/更改博客目录结构与字体-2023-11-20-13-48-19.png" caption="目录" >}}
<!-- {{< image src="./img1.png" caption="目录" width=40% >}} -->

要注意每个二级目录下都要有一个`_index.md`，例如`/life`下的：
```yaml
---
title: 生活小窝 #标题
hidemeta: true #隐藏这篇md的信息，如时间作者等
weight: 20 #排序时的权重
---
```

## 修改字体

新建`assets/css/extended/blank.css`，在里面的修改会覆盖默认css。
```css
body{
    font-family: FangSong;
    font-size: 18px;
    line-height: 1.6;
}
```
修改为仿宋字体，目前感觉更好看点。

{{< image src="http://img.woodencross.cn/blog/更改博客目录结构与字体-2023-11-20-13-49-15.png" caption="原来的" >}}
<!-- {{< image src="./黑体1.png" caption="原来的" width=60% >}} -->

{{< image src="http://img.woodencross.cn/blog/更改博客目录结构与字体-2023-11-20-13-49-36.png" caption="现在的" >}}
<!-- {{< image src="./仿宋1.png" caption="现在的" width=60% >}} -->

{{< image src="http://img.woodencross.cn/blog/更改博客目录结构与字体-2023-11-20-13-50-03.png" caption="原来的" >}}
<!-- {{< image src="./黑体2.png" caption="原来的" width=60% >}} -->

{{< image src="http://img.woodencross.cn/blog/更改博客目录结构与字体-2023-11-20-13-50-21.png" caption="现在的" >}}
<!-- {{< image src="./仿宋2.png" caption="现在的" width=60% >}} -->
