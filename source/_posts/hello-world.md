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

#### 创建草稿
``` bash
$ hexo new draft "My Draft Post"
```

#### 发布草稿
``` bash
$ hexo publish "My Draft Post"
```

### Run server

``` bash
$ hexo server
```

More info: [Server](https://hexo.io/docs/server.html)

#### 本地预览选项
- `-p` 指定端口
- `-l` 只监听本地连接
- `-o` 自动打开浏览器

### Generate static files

``` bash
$ hexo generate
```

More info: [Generating](https://hexo.io/docs/generating.html)

#### 生成选项
- `-d` 生成后立即部署
- `-w` 监听文件变化
- `-b` 生成前清理缓存

### Deploy to remote sites

``` bash
$ hexo deploy
```

More info: [Deployment](https://hexo.io/docs/one-command-deployment.html)

## 高级功能

### 插件管理
Hexo 支持丰富的插件系统

#### 常用插件
- hexo-generator-feed：RSS 订阅
- hexo-generator-sitemap：站点地图
- hexo-deployer-git：Git 部署

### 主题定制
可以通过修改主题文件来自定义外观

#### 自定义样式
- 修改 CSS 文件
- 添加自定义 JavaScript
- 调整布局模板


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


- [x] 已完成的任务
- [ ] 未完成的任务
- [ ] 另一个任务
| 左对齐 | 居中对齐 | 右对齐 |
|:-------|:-------:|-------:|
| 内容1  |  内容2  |  内容3 |