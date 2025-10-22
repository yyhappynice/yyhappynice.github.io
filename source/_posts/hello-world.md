---
title: Hello World
date: 2025-10-12 15:04:40
categories:
  - 博客教程
tags:
  - Hexo
  - 教程
---
Welcome to [Hexo](https://hexo.io/)! This is your very first post. Check [documentation](https://hexo.io/docs/) for more info. If you get any problems when using Hexo, you can find the answer in [troubleshooting](https://hexo.io/docs/troubleshooting.html) or you can ask me on [GitHub](https://github.com/hexojs/hexo/issues).

## Quick Start

### Create a new post

``` bash
$ hexo new "My New Post"
```

More info: [Writing](https://hexo.io/docs/writing.html)

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)


---

---

<div style="text-align: center; color: red;">
这是居中的红色文字
</div>


<details>
<summary>点击展开</summary>
这里是隐藏的内容
</details>

这是一个脚注[^1]

[^1]: 这是脚注的内容

{% blockquote %}
这是一个引用块
{% endblockquote %}

{% codeblock lang:javascript %}
function hello() {
    console.log("Hello World!");
}
{% endcodeblock %}

{% link 链接标题 https://example.com %}

[TOC]  # 自动生成目录

- [x] 已完成的任务
- [ ] 未完成的任务
- [ ] 另一个任务
| 左对齐 | 居中对齐 | 右对齐 |
|:-------|:-------:|-------:|
| 内容1  |  内容2  |  内容3 |