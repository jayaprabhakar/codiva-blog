---
title: "Update Response Headers on Cloudflare Workers"
pageTitle: "Update Response Headers on Cloudflare Workers | Codiva Online Compiler Blog"

date: 2018-09-07T00:47:02-07:00
draft: false
tags: [Cloudflare,ServiceWorker]
---

Do you have a static website and you want to change response headers, but the hosting provider does not provide a way to do that? Cloudflare workers makes it easy to do that.
<!--more-->

For example, if you want to disable the page from being included as a frame in another page, you can set content-security-policy.

```javascript

addEventListener('fetch', event => {

  event.respondWith(fetch(event.request)
      .then(function(response){
            response = new Response(response.body, response);
            // Set content-security-policy header to self and also include 
            // cloudflare workers dashboard to make it easy to preview
            response.headers.set("content-security-policy",
               "frame-ancestors 'self' https://dash.cloudflare.com;");

            return response;
        }

      ));
    

});
```

