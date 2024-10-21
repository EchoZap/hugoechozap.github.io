---
title: "「Gmeek」文章插入html标签以及图片源地址渲染"
date: 2024-10-21T21:56:17.574650
categories: ['Docs']
author: "Ronan"
---
# 1.图片使用创建文章(issue)时输入的原始链接。

通过markdown添加的图片，确实包含data-canonical-src图片的真正地址。这个只需要通过一小段js插件就能实现，而且不会误判在code中的内容被替换。  
所有文章页实现替换Camo，在config.json中添加如下配置

```
"script":"<script>document.querySelectorAll('a').forEach(anchor => {const img = anchor.querySelector('img');if (img && img.hasAttribute('data-canonical-src')) {const canonicalSrc = img.getAttribute('data-canonical-src');anchor.setAttribute('href', canonicalSrc);img.setAttribute('src', canonicalSrc);img.removeAttribute('data-canonical-src');}});</script>",
```

单篇文章页替换Camo，在文章页最后一行添加

```
<!-- ##{"script":"<script>document.querySelectorAll('a').forEach(anchor => {const img = anchor.querySelector('img');if (img && img.hasAttribute('data-canonical-src')) {const canonicalSrc = img.getAttribute('data-canonical-src');anchor.setAttribute('href', canonicalSrc);img.setAttribute('src', canonicalSrc);img.removeAttribute('data-canonical-src');}});</script>"}## -->
```

# 2.通过html插入音乐、图片和视频等
### 使用方式
在需要添加html标签的位置使用 `行内代码` 方式，并且后面紧跟着 `Gmeek-html`，然后才是html标签。  
> [!note]
> 大白话：在创建文章（issue）时，写着写着想插入一张图片就可以通过两个反引号 `` 包裹住Gmeek-html以及html标签(也就是只需要将其中的src改为自己要插入的图片链接)

- 图片img

```
`Gmeek-html<img src="https://picsum.photos/200">`
```

- 内嵌框架iframe-网站

```
`Gmeek-html<iframe src="https://music.meekdai.com/#61" width="100%" height="460px" frameborder="0" allowfullscreen="true"></iframe>`
```

- 内嵌框架iframe-歌曲

```
`Gmeek-html<iframe style='border-radius:12px' src='https://open.spotify.com/embed/track/0U3fV7K4WFfVRgLGEAKh3g?utm_source=generator' width='100%' height='152' frameBorder='0' allowfullscreen='' allow='autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture' loading='lazy'></iframe>`
```

`Gmeek-html<iframe style='border-radius:12px' src='//music.163.com/outchain/player?type=2&id=545590290&auto=1' width='100%' height='152' frameBorder='0' allowfullscreen='' allow='autoplay; clipboard-write; encrypted-media; fullscreen; picture-in-picture' loading='lazy'></iframe>`

- 内嵌框架iframe-视频

```
`Gmeek-html<iframe src="//player.bilibili.com/player.html?isOutside=true&aid=1604800941&bvid=BV1qm421M7Xs&cid=1557311907&p=1&autoplay=0" scrolling="no" border="0" frameborder="no" framespacing="0" allowfullscreen="true" width="100%" height="460px"></iframe>`
```



<!-- ##{"timestamp":1722610298}## -->