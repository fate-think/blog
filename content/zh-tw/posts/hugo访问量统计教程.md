---
title: "hugo访问量统计教程"
keywords:
- statistics
- hugo
description: "hugo博客页脚添加网站访问次数和访问人数"
date: 2023-07-19T01:29:36Z
draft: false
---

hugo添加访问量统计教程 <br>
<!--more-->
日期 2023 年 07 月 19 日 <br>
时间 01 : 24

> <font color=#605DFF> 注意 </font> <br>
文中`hugo-theme-monochrome`是我用的主题名称，<br>
换成你的对应主题路径即可

## <font color=#66CC66> 〖 一 〗 改head.html </font>
博客主目录下，<br>
找到`themes/hugo-theme-monochrome/layouts/partials/`目录下的`head.html`，<br>
在`</head>`前添加以下代码(共2行)
```
<script async src="//busuanzi.ibruce.info/busuanzi/2.3/busuanzi.pure.mini.js">
</script>
```

<br/>

## <font color=#66CC66> 〖 二 〗 改footer.html </font>
博客主目录下，<br>
找到`themes/hugo-theme-monochrome/layouts/partials/`目录下的`footer.html`，<br>
在`</footer>`前添加以下代码 (一共8行)
```
<div class="busuanzi-footer">
<span id="busuanzi_container_site_pv">
    访问量&nbsp;-&nbsp;<span id="busuanzi_value_site_pv"></span>
</span>&nbsp;
<span id="busuanzi_container_site_uv">
    访客数&nbsp;-&nbsp;<span id="busuanzi_value_site_uv"></span>&nbsp;人
  </span>
</div>
```

- 如果你的博客已经能正常显示访问量，<br>
就不用看第( 3 )条了

<br/>

## <font color=#66CC66> 〖 三 〗 改params.toml </font>
如果你的博客不显示访问量，<br>
注意博客主目录下的<br>
`config/_default/params.toml`，<br>
这个文件里的 ` footer = " " `，不能为空，<br>
要填写任意内容，<br>
访客数才会显示
