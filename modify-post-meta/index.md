# 修改文章post_meta信息


Hugo里使用post_meta描述文章的信息，比如发布时间、作者、标签、阅读时长等。这次受[这篇文章](https://www.sulvblog.cn/posts/blog/hugo_postmeta/)的启发来修改一下，使其更加美观。

将`themes/PaperMod/layouts/partials/post_meta.html`拷贝到`layouts/partials`下，并修改：
```html
{{- $scratch := newScratch }}
<!--创建时间-->
{{- if not .Date.IsZero -}}
{{- $scratch.Add "meta" (slice (printf "发表于<span title='%s'>%s</span>" (.Date) (.Date | time.Format (default "2006-01-02" .Site.Params.DateFormat)))) }}
{{- end }}
<!--添加：修改时间-->
{{- if (.Param "ShowLastMod") -}}
{{- $scratch.Add "meta" (slice (printf "修改于<span title='%s'>%s</span>" (.Lastmod) (.Lastmod | time.Format (default "2006-01-02" .Site.Params.DateFormat)))) }}
{{- end }}
<!--阅读时间-->
{{- if (.Param "ShowReadingTime") -}}
{{- $scratch.Add "meta" (slice (i18n "read_time" .ReadingTime | default (printf "%d min" .ReadingTime))) }}
{{- end }}
<!--字数统计-->
{{- if (.Param "ShowWordCount") -}}
{{- $scratch.Add "meta" (slice (i18n "words" .WordCount | default (printf "%d words" .WordCount))) }}
{{- end }}

{{- with (partial "author.html" .) }}
{{- $scratch.Add "meta" (slice .) }}
{{- end }}
<!--分隔符-->
{{- with ($scratch.Get "meta") }}
{{- delimit . "&nbsp;·&nbsp;" -}}
{{- end -}}
```

在`config.yaml`中：
```yaml
enableGitInfo: false # 关闭gitinfo，否则lastmod会显示为最后commit时间，有些bug
params:
  DateFormat: "2006-01-02" # 日期格式
  ShowLastMod: true # 显示lastmod
```

然后就可以在文章的front_matter中配置：
```yaml
lastmod: 2022-03-21T21:53:51+08:00
```
也可以写进archetypes中：
```yaml
lastmod: {{ .Date }}
```
后面再编辑时手动修改即可。
