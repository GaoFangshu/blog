---
title: "Modify my Blog (3): Giving the Blog a Home Page"
date: 2017-02-14 21:07:22
tags: Blog
---
Utterly exhausted but deeply satisfied, I finished my work [today](https://en.wikipedia.org/wiki/Valentine's_Day#China). My blog has a home page from now on.

<!--more-->

I learned tutorials about HTML and CSS on [w3schools](http://www.w3schools.com/). Of course, they are just introductions and there exist more specific difficulties, such as iconfont, external font and some git problems.

For iconfont and external font, I found two solutions: `@font-face` in CSS and [Font Awesome API](http://fontawesome.io/). It seems that `@font-face` can't use absolute link (*[@font-face fonts only work on their own domain
](http://stackoverflow.com/questions/2892691/font-face-fonts-only-work-on-their-own-domain)*). I tried following code, but didn't work:

```
@font-face {
    font-family: "FontAwesome";
	src: url("gaofangshu.github.io/css/fonts/fontawesome-webfont.eot");
	src: url("gaofangshu.github.io/css/fonts/fontawesome-webfont.eot?#iefix") format('embedded-opentype')
	     url("gaofangshu.github.io/css/fonts/fontawesome-webfont.woff") format("woff"),
	     url("gaofangshu.github.io/css/fonts/fontawesome-webfont.ttf") format("truetype"),
	     url("gaofangshu.github.io/css/fonts/fontawesome-webfont.svg") format("svg");*/
    font-weight: normal;
    font-style: normal;
}
```

Thus, I had to use Font Awesome API, and `@font-face` worked this time:

```
<script src="https://use.fontawesome.com/YourEmbedCode.js"></script>

<style type="text/css">
    @font-face {
        font-family: "FontAwesome";
        src: url("css/fonts/fontawesome-webfont.eot");
        src: url("css/fonts/fontawesome-webfont.eot?#iefix") format('embedded-opentype')
             url("css/fonts/fontawesome-webfont.woff") format("woff"),
             url("css/fonts/fontawesome-webfont.ttf") format("truetype"),
             url("css/fonts/fontawesome-webfont.svg") format("svg");
        font-weight: normal;
        font-style: normal;
    }
    ...
...
```

Other references, including:
* [Icon Font for the Web](https://google.github.io/material-design-icons/#icon-font-for-the-web)
* [css content 如何自定义生成图标？](https://www.zhihu.com/question/22022905)
* [Font-Awesome Code](https://github.com/FortAwesome/Font-Awesome/blob/abb4a83c4386d0f552209a3b6fa18e46b93404da/src/assets/less/bootstrap-3.3.5/list-group.less)

Then, the problem was organizing HTML layout.
* Button & link in HTML:
 * [How do I make an html link look like a button?](http://stackoverflow.com/questions/710089/how-do-i-make-an-html-link-look-like-a-button)
 * [How to make `<a href=“”>` link look like a button?
](http://stackoverflow.com/questions/8357058/how-to-make-a-href-link-look-like-a-button)
* Centering:
 * [Centering page content vertically](http://stackoverflow.com/questions/5282758/centering-page-content-vertically)
 * [Centering a Div Both Horizontally And Vertically](http://tutorialzine.com/2010/03/centering-div-vertically-and-horizontally/)

When deleting old files in the blog repository, I found a trick from *[git remove all files except some files
](http://stackoverflow.com/questions/28306508/git-remove-all-files-except-some-files)*. In brief, it includes *cloning blog repository* → *modifing local repository* → *committing all changes* → *pushing changes to repository* → *removing all files in local repository* → *getting back the file you want to keep* → *committing again* → *pushing again*.

Of course, the last step was pushing the code to [GitHub](https://github.com/GaoFangshu) :)
* [HexoOverview](https://jacklightchen.github.io/blog/2016/10/27/HexoOverview/)
* [如何在 GitHub Pages 上传自己写的网页作为首页，Hexo 博客作为其子页？](https://www.zhihu.com/question/38125993)