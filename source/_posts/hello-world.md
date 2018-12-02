---
title: # 文章标题
date: 2018/12/03 13:48:25
tags:
- 标签1
categories: hello
thumbnail: https://atom-morgan.github.io/images/hello-world/tutorial-hello-world.png
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

More info: [Deployment](https://hexo.io/docs/deployment.html)

```css
section h1 > a:hover:before {
    color: black;
    background: #f1ad26;
}
```

```js
if (!env.code || !env.grammar) {
  if (env.code) {
    _.hooks.run('before-highlight', env);
    env.element.textContent = env.code;
    _.hooks.run('after-highlight', env);
  }
  _.hooks.run('complete', env);
  return;
}
```