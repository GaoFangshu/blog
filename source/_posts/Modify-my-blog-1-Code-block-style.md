---
title: "Modify my Blog (1): Code Block Style"
date: 2016-10-24 23:59:43
tags: Blog
---
Can't stand the ugly color of code block, then I begin to customize my Hexo theme. Thanks to the [guide](http://blog.sunnyxx.com/2014/03/07/hexo_customize/), now let's begin:

* According to a [highlight theme](https://github.com/chriskempson/tomorrow-theme), change the highlight color (in `...\source\css\_partial\highlight.styl`), and set the background color as `#e5e5e5`.

<!--more-->

* Change the inline code style (in the same file as above):
```
.article-entry
    pre, code
      font-family: font-mono
    code
      background: #e5e5e5
      text-shadow: none
      padding: 0 0.3em
      border-radius: 3px
      border-width 1px
      border-color: color-border
```
* Change article base style in `...\source\css\_extend.styl`:
```
$block
    backgroun: color-background
    box-shadow: 1px 2px 3px #ddd
    border: 1px solid color-border
    border-radius: 10px
```

Other items still need to be modified, such as header, archives and logos. However, "Rome wasn't built in a day", leave it for next time.