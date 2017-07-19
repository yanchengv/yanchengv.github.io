---
layout: post
title:  "rails取消turbolinks 解决页面跳转后不加载js问题"
date:   2017-03-30 17:24
categories: rails
comments: true
---

```js
// rails 页面跳转后不加载js解决
// 页面切换 重新加载此js，取消turbolinks
$(document).on('turbolinks:load', function() {
    $('.selectpicker').selectpicker();

});





```
<a target= '_blank' href='http://stackoverflow.com/questions/17600093/rails-javascript-not-loading-after-clicking-through-link-to-helper?rq=1'>来源</a>