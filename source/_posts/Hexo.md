---
title: Hexo
date: 2020-10-12 17:27:10
tags:
---

# Hexo's Blog

## Hexo’s Blog 搭建

### Hexo安装以及本地博客创建

1. 安装Node.js: 在Node.js官网下载文件安装即可, 安装完成后通过 node -v 查看node版本信息;

2. 安装Hexo: sudo npm install -g hexo;

3. 博客初始化: mkdir myblog & cd myblog & hexo init;

4. 生成本地网页文件并开启本地服务器: hexo g & hexo s;

以上内容为本地博客创建,下面将讲述本地博客与github关联

<!--more-->

***********************************************

5. 注册并登录github账号, 创建仓库, 仓库名称必须为<用户名>.github.io, 并关联ssh_key;

6. 打开myblog文件夹下的_config.yml文件: vim _config.yml;

7. 在 _config.yml 文件的最后部分, 将deploy配置如下:

  ```
    deploy:
    type: git
    repository: <github仓库地址>
    branch: master
  ```

8. 执行hexo g & hexo d; 若执行hexo g出错则执行npm install hexo –save，若执行hexo d出错则执行npm install hexo-deployer-git –save

### Hexo’s 小技巧

#### Hexo - 设置阅读全文:

1. 在文章中使用< !–more–> 手动进行截断 这种方法可以根据文章的内容，自己在合适的位置添加 < !–more–> 标签，使用灵活，也是Hexo推荐的方法。

2. 在文章中的front-matter中添加description，并提供文章摘录. 这种方式只会在首页列表中显示文章的摘要内容，进入文章详情后不会再显示.

3. 自动形成摘要，在主题配置文件中添加. 默认截取的长度为 150 字符，可以根据需要自行设定

  ```
    auto_excerpt:
    enable: true
    length: 150
  ```
建议使用 < !– more –>（即第一种方式），除了可以精确控制需要显示的摘录内容以外， 这种方式也可以让 Hexo 中的插件更好的识别

#### Next主题配置

1. 下载Next主题

    先走到blog的根目录，然后将next主题clone到themes目录下：
    `git clone https://github.com/next-theme/hexo-theme-next themes/next`；

2. 打开_config.yml文件，配置主题为Next；
    `theme: next`

3. 部署到服务器：
    `hexo g -d; hexo s`

### hexo博客中插入PDF

* 安装hexo-pdf插件  
    `npm install --save hexo-pdf --registry=https://registry.npm.taobao.org`
* 将next主题配置中的pdf enable 设置为true
* 插入pdf  
    `{% pdf ./bash_freshman.pdf %}`

### hexo增加tags、category

1. `hexo new page tags`
2. 编辑index.md，将type改为`tags`
3. 编辑主题配置文件，在menu中打开tags；

### hexo d命令报错 ERROR Deployer not found: git

`npm install hexo-deployer-git --save`

### hexo支持数学公式

本文主要使用NexT => MathJax => hexo-renderer-kramed的方式构建数学公式

* npm uninstall hexo-renderer-marked
* npm i hexo-renderer-kramed
* 修改next主题配置文件
    ```
    # Math Formulas Render Support
    math:
    # Default (true) will load mathjax / katex script on demand.
    # That is it only render those page which has `mathjax: true` in Front-matter.
    # If you set it to false, it will load mathjax / katex srcipt EVERY PAGE.
    per_page: true

    # hexo-renderer-pandoc (or hexo-renderer-kramed) required for full MathJax support.
    mathjax:
        enable: true
        # See: https://mhchem.github.io/MathJax-mhchem/
        mhchem: false

    # hexo-renderer-markdown-it-plus (or hexo-renderer-markdown-it with markdown-it-katex plugin) required for full Katex support.
    katex:
        enable: false
        # See: https://github.com/KaTeX/KaTeX/tree/master/contrib/copy-tex
        copy_tex: false


    vendors:
        # MathJax
        mathjax: //cdn.jsdelivr.net/npm/mathjax@3/es5/tex-mml-chtml.js
    ```
* 在写每篇博文的时候，都需要Front-matter部分还是要加上语句：mathjax: true.否则，数学公式无法被正常渲染.