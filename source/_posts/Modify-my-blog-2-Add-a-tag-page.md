---
title: "Modify my Blog (2): Add a Tag Page"
date: 2016-10-31 23:03:25
tags: Blog
---
If you want to modify source code corresponding to your Hexo blog, searching it in Github repository can be pretty efficient.

For example, when I plan to add a tag page to my blog, searching `nav-home-icon` in [theme "athena" repository](https://github.com/steven5538/hexo-theme-athena). It shows relative code in `hexo-theme-athena/_config.yml`:

<!--more-->

```
# Header
menu:
  Home: /
  Archives: /archives
  About: /about
# Header Icon
menu_icon:
  Home: nav-home-icon
  Archives: nav-archives-icon
  About: nav-about-icon
```
To add a tag page, create a new page in hexo, by `$ hexo new page tags`. Add a line `type: "tags"` in this new page and modify the `hexo-theme-athena/_config.yml`:
```
# Header
menu:
  Home: /
  tags: /tags
  Archives: /archives
  About: /about
# Header Icon
menu_icon:
  Home: nav-home-icon
  tags: nav-tags-icon
  Archives: nav-archives-icon
  About: nav-about-icon
```
Now, we already have a tag page, but its tag icon still doesn't exist. Therefore, search `nav-home-icon` again, and we can find following code in `hexo-theme-athena/source/css/_partial/header.styl`:
```
#nav-rss-link
  &:before
    content: "\f09e"

#nav-home-icon
  &:before
    content: "\f015"

#nav-archives-icon
  &:before
    content: "\f03a"

#nav-about-icon
  &:before
    content: "\f007"
```
That's interesting, icons are not in common image format like `.jpg` or `.png`, but UTF-8 icons. Then, we can add a new tag icon easily:
```
#nav-tags-icon
  &:before
    content: "\f02c"
```

Unfortunately, I still don't know how to sort and count my tags automatically and at present have to write tags manually in markdown.