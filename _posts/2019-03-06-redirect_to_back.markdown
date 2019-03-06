---
title: Rails 5 中 redirect_to :back
layout: post
date: '2019-03-06 11:46:00'
categories: rails
---

[原文](https://booox.github.io/2017/11/17/rails-5-redirect_to_back/)

Rails 5 中 redirect_to :back

先说Rails 5 中的最新用法
在 Rails 5 中用  `redirect_back(fallback_location: root_path) ` 来代替原先的 `redirect_to :back`

因为` redirect_to :back` 中` :back `即等于 `request.env["HTTP_REFERER"]`

但在网络的环境中请求来源是多样的，举一个例子

当你直接复制链接到地址栏里访问时，若页面中使用了` redirect_to :back` ，那么此时就会出错。

而最新的修订中，则指明了当 `request.env["HTTP_REFERER"] `不存在时，返回哪一个页面，即 `fallback_location `。

[具体见](https://stackoverflow.com/questions/16323632/rails-redirect-to-previous-page-back-after-delete)

# 源码中的代码及注解
```
# Redirects the browser to the page that issued the request (the referrer)
# if possible, otherwise redirects to the provided default fallback
# location.
#
# The referrer information is pulled from the HTTP `Referer` (sic) header on
# the request. This is an optional header and its presence on the request is
# subject to browser security settings and user preferences. If the request
# is missing this header, the <tt>fallback_location</tt> will be used.
#
#   redirect_back fallback_location: { action: "show", id: 5 }
#   redirect_back fallback_location: @post
#   redirect_back fallback_location: "http://www.rubyonrails.org"
#   redirect_back fallback_location: "/images/screenshot.jpg"
#   redirect_back fallback_location: posts_url
#   redirect_back fallback_location: proc { edit_post_url(@post) }
#
# All options that can be passed to <tt>redirect_to</tt> are accepted as
# options and the behavior is identical.
def redirect_back(fallback_location:, **args)
  if referer = request.headers["Referer"]
    redirect_to referer, **args
  else
    redirect_to fallback_location, **args
  end
end
```

# Link_to 如何返回上一个页面？
rails4的做法：
```
<%= link_to "Back", :back %>
<%= link_to "back", request.referer.present? ? request.referer : root_path %>
```
rails5的做法：
```
	<%= link_to url_for(request.env["HTTP_REFERER"].present? ? request.env["HTTP_REFERER"] : root_path) %>

<%= link_to url_for(request.env["HTTP_REFERER"] || root_path) %>

```