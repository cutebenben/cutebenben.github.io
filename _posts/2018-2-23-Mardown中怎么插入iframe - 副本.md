---
layout: post
title: 'Mardown中插入iframe!!!'
subtitle: '最近刚搭建的Jekyll静态博客，想在文章中直接插入网易云音乐的iframe外链，本以为mardown是支持HTML的原生语句的，但在测试中却发现了错误'
date: 2018-2-23
categories: 技巧
cover: 'https://warrest.github.io/wArrest.github.io/assets/img/2018-2-23/TIM截图20180223163607.png'
tags: Mardown iframe
typora-root-url: ..\assets\img\2018-2-23
---
#### 解决的过程不算麻烦，网上早就已经有大神碰到这个问题，不过照搬当然是不行的。
### 步骤：
1.在网易云的官网可以找到歌曲的外链iframe，我这里用《广东十年爱情故事》做示范
![TIM截图20180118100240](\assets\img\2018-2-23\TIM截图20180223164057.png)

源代码：
```html
<iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=513791211&auto=1&height=66"></iframe>
```
2.参考[大佬的教程](http://blog.shengbin.me/posts/iframe-in-markdown-of-jekyll)：

![TIM截图20180118100240](\assets\img\2018-2-23\TIM截图20180223164448.png)

我直接将代码中所有的& 替换成了 &amp;之后发现还是不行；需要将源代码做一点小改动：

```html
<iframe src="//music.163.com/outchain/player?type=2&amp;id=513791211&amp;auto=1&amp;height=66" frameborder="no" border="0" marginwidth="0" marginheight="0" width="330px" height="86px"> </iframe>
```
将frameborder属性放到src属性后面，将width 和 height 添加双引号，带上像素单位px；这样就OK了

### 播放器：
<iframe src="//music.163.com/outchain/player?type=2&amp;id=513791211&amp;auto=1&amp;height=66" frameborder="no" border="0" marginwidth="0" marginheight="0" width="330px" height="86px"> </iframe>

