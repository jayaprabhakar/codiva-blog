---
title: "Host Saas Product Blog in Subdirectory Instead of Subdomain"
pageTitle: "Host Saas Product Blog in Subdirectory Instead of Subdomain | Codiva Online Compiler Blog"

date: 2018-06-13T23:48:26-07:00
draft: false
tags: []
---

You have a SAAS product. Now you want to add articles, blogs, tutorials, etc to support your product or for content marketing and SEO. Should you have these hosted as a subdomain (https://blog.example.com) or as a subdirectory (https://example.com/blog)?
<!--more-->

Google's official guideline is a bit vague (Do the right thing for your user and your long term plan.), but many SEO experts believe, subdirectory (/blog) is the right choice.

As a developer, I would prefer to have a subdomain. Because, I could simply add a *A record* or *CNAME entry* in the DNS and point to my blog host (Wordpress or GitHub pages).
It is simple and fast with negligible overhead. A DNS lookup for the first request, then everything is cached.

However, hosting as a subdirectory is a bit slow. You will have to configure on your webserver. If you use NGINX, it will be something like,

```
location /blog {
  proxy_pass etc..
}
```

Then, for each request, the request will first reach your server that will then be sent to the blog host. The response from the blog host will then get proxied back to the user.

This is unnecessarily slow. Codiva blog is hosted in GitHub pages, that is globally distributed superfast server. But the rest of my site is locaated only in the US. Global replication is expensive. With the subdirectory, approach, users' request anywhere in the world will reach the US servers, then get proxied to somewhere near my server.

As a developer sensitive to latency issues, I hate this.

But not any more. Enter *Cloudflare Service Worker*.

### Cloudflare Service Workers config

Codiva.io Blog is hosted in Github Pages with https. The github url looks something like https://username.github.io/codiva-blog. I want this to be served as https://www.codiva.io/blog

In cloudflare worker, intercept the request. If the url path starts with /blog/, fetch the request from github instead.

```
addEventListener('fetch', event => {
  var url = new URL(event.request.url);
  if (url.pathname.startsWith('/blog/') || url.pathname === '/blog') {
    handleBlog(event, url);
  } else {
    event.respondWith(fetch(event.request));
  }
})

async function handleBlog(event, url) {
  var originUrl = url.toString().replace(
    'https://www.codiva.io/blog',
   'https://example.github.io/codiva-blog');
  event.respondWith(fetch(originUrl)); 
}

```
This is very similar to the proxy_pass solution. Except, this runs in Cloudflare CDN close to the user and much more widely distributed than most blog hosting platforms. 

This solution is much faster than proxy_pass on your webserver.

