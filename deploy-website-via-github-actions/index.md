# 使用Github Actions部署私有仓库静态博客到Github Pages


## 起因

最初部署博客是在本地直接编译框架源码，然后把编译后的静态网页文件上传到Github Pages仓库。可是同时我又想管理框架源码，如果源码和静态网页托管到一个仓库的话，那么仓库的可见性又成了问题：Private的话网页不能互联网公开可见，Public的话源码又暴露了。试了一下分别本地管理两个仓库，可是这样每次都要commit两遍push两遍，怪麻烦的。于是秉承程序员“花六个小时写一个自动化脚本，做一个动手五分钟就能搞定的任务”的黄金精神，我又开始折腾Github Actions自动部署啦。

<!--{{< figure src="/images/memes/efishenci.png" width=50% align="center" >}}-->

## 过程

官方（也许？）手册在此：[GitHub Actions for Hugo GitHub Pages](https://github.com/peaceiris/actions-gh-pages)

Github通过检测仓库根目录下的`/.github/workflows/*.yaml`来执行Actions。因此需要新建一个`.yaml`文件，取名随意，之后复制手册里的默认配置并修改。折腾一下午后我的配置文件如下：
```yaml {hl_lines=[6, 31]}
name: GitHub Pages  # 为action取个名

on:
  push: # 设定当仓库收到push后执行action
    branches:   # 设定触发action的分支
      - master
  # pull_request:   # 我不需要pr后执行action，所以注释掉

jobs:
  deploy:
    runs-on: ubuntu-20.04   # 设定action的执行环境
    concurrency:
      group: ${{ github.workflow }}-${{ github.ref }}
    steps:  # 需要执行的步骤
      - uses: actions/checkout@v2   # 使用现成的action库
        with:   # 设定执行的环境变量
          submodules: true
          fetch-depth: 0

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: 'latest'
          extended: false

      - name: Build
        run: hugo   # 直接执行的指令

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        if: ${{ github.ref == 'refs/heads/master' }}
        with:
          deploy_key: ${{ secrets.GH_PAGES }}   # 改成自己deploy key的名字
          external_repository: Woodencross/Woodencross.github.io    # public库的地址
          publish_branch: master    # 要推送到public库的分支
          publish_dir: ./public     # 与源码的publishDir保持一致
          commit_message: ${{ github.event.head_commit.message }}   # 同步commit message

```
这里涉及到`deploy key`，这是什么呢？由于我的源码与action都在私有库里，静态网页在公开库里，而action自动部署的时候需要从私有库push到公开库，那么就需要一个像ssh key的东西（实际上就是ssh）来验证push者的身份。Github提供了三种验证方式，分别是`github_token` `deploy_key`与`personal_token`，其中只有`deploy_key`基于ssh，另两个都是基于https，那我自然选择最安全（理论上）的啦。

通过
```bash {linenos=false}
ssh-keygen -t rsa -b 4096 -C "$(git config user.email)" -f gh-pages -N ""
# You will get 2 files:
#   gh-pages.pub (public key)
#   gh-pages     (private key)
```
生成公钥私钥文件，将公钥加入**公开库**的`Settings->Deploy keys`中（随便取个名字），将私钥加入**私有库**的`Settings->Secrets`中，注意这个的取名要和`.yaml`文件中的一致，这样私有库就能通过身份验证向公开库push内容了。当在源码中更新好内容后，只需要向私有库push，剩下的编译部署交给Github Actions来完成！

当然纸面总结上看起来是很轻松的工作，但是实操起来踩的坑可不少，不然怎么花了一个下午？

一个是submodule的问题。因为我之前的主题模板是直接`git clone`到源码里的，这就导致action执行时报错说“找不到`.gitmodules`”，应该是使用的action库默认大家的模板都是submodule吧。但是submodule这个东西实在是不好管理，老是搞得我重建仓库，而我自己又不会写action库，所以就还是~~勉为其难地~~用`git submodule add`来添加模板了。

PS：好像在一些情况下不能用`git submodule add`而要用`git submodule--helper add`。

二是Github分支问题，我的仓库都是默认的`master`，而文档里copy来的分支默认是`main`，有一处在第六行（高亮）很明显也是直接改了，但还有一处在第31行（高亮）……这个是真的很难注意到好吧。听朋友说Github主分支由master改叫main是黑命贵的产物，因为master有奴隶主的意思:sweat_smile:。真是差不多得了嗷。

三是愚蠢的typo，`.github/workflows`少了个**s**！！我还寻思push之后Github action怎么不工作，一度以为是为了防挖矿设置了每小时最大工作次数。

## 结语

总而言之，网站的源码保护和自动化部署在经历了一个下午的折腾后也算是完美收官。我的评价是：值！（甚至老想手痒多提交几次欣赏成果~）
