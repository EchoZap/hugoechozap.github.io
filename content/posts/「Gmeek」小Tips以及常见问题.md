---
title: "「Gmeek」小Tips以及常见问题"
date: 2024-10-21T21:56:15.533772
categories: ['Docs']
author: "Ronan"
---
# 修改文章发布时间
如需修改发布时间，可以在文章 `最后一行(后面不能有空行)` 添加如下代码。里面的时间是采用时间戳的形式，可以用如下 [网站](https://tool.lu/timestamp) 转换。

```
<!-- ##{"timestamp":1490764800}## -->
```


# 折叠代码块

```
<details>
    <summary>点我展开看代码</summary>
    <pre><code>
# 这里空一行，下面开始写代码
# 在这里写折叠的代码
# 最后这两行结束标签一定要顶格写且不能接在代码后面！！！
</code></pre>
</details>
```

- 示例
<details>
    <summary>点我展开看代码</summary>
    <pre><code>

```
echo "Just a test"      # 在这里写折叠的代码
# 最后这两行结束标签一定要顶格写且不能接在代码后面！！！
```
</code></pre>
</details>


# 书写公式

```
$$
E=mc^2
$$
```

- 示例

$$
E=mc^2
$$


# 强调关键信息
Github的语法里面有5中警报强调信息，分别是`NOTE`、 `TIP`、 `IMPORTANT`、 `WARNING`、 `CAUTION` 。在写文章的时候，适当使用可以提高文章的可读性，并且颜色也更加丰富。下面就简单描述一下使用方式，以及效果如何。

#### 使用方式
```
> [!NOTE]
> Useful information that users should know, even when skimming content.

> [!TIP]
> Helpful advice for doing things better or more easily.

> [!IMPORTANT]
> Key information users need to know to achieve their goal.

> [!WARNING]
> Urgent info that needs immediate user attention to avoid problems.

> [!CAUTION]
> Advises about risks or negative outcomes of certain actions.
```

#### 效果展示
> [!NOTE]
> Useful information that users should know, even when skimming content.
用户即使在浏览内容时也应该了解的有用信息。

> [!TIP]
> Helpful advice for doing things better or more easily.
有助于更好或更轻松地做事的有用建议。

> [!IMPORTANT]
> Key information users need to know to achieve their goal.
用户需要了解实现其目标的关键信息。

> [!WARNING]
> Urgent info that needs immediate user attention to avoid problems.
需要用户立即注意以避免出现问题的紧急信息。

> [!CAUTION]
> Advises about risks or negative outcomes of certain actions.
针对某些行动的风险或负面结果提出建议。


# 自定义单篇文章参数
自定义单篇文章页面的style和script

```
<!-- ##{"style":"<style>#postBody{font-size:20px}</style>"}## -->
```

```
<!-- ##{"script":"<script async src='//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js'></script>"}## -->
```

# 多种自定义参数
可同时一起添加多种自定义参数：

```
<!-- ##{"script":"<script async src='//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js'></script>","style":"<style>#postBody{font-size:20px}</style>","timestamp":1490764800}## -->
```

# 自定义全局文章参数
添加全局文章页面的style和script，在config.json文件中添加

```
"style":"<style>#postBody{font-size:20px}</style>",
"script":"<script async src='//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js'></script>",
```

# 置顶博客文章
只需要「Pin issue」即可。

# utteranc报错
如果在评论里面登录后评论报错，可直接按照提示安装utteranc app即可

```
Error: utterances is not installed on xxx/xxx.github.io. If you own this repo, install the app. Read more about this change in the PR.
```

# 删除文章
只需要「Close issue」或者「Delete issue」后，再手动全局生成一次即可。


<!-- ##{"timestamp":1722598446}## -->