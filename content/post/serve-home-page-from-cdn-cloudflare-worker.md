---
title: "Serve Home Page From CDN with Cloudflare Worker"
pageTitle: "Serve Home Page From CDN with Cloudflare Worker | Codiva Online Compiler Blog"

date: 2018-06-12T00:50:46-07:00
draft: false
tags: [Cloudflare,ServiceWorker]
---

Cloudflare Service Worker helped Codiva jump at least 2 positions in Google by speading up home page response. I did it by serving the new users (and search crawler) from the edge, and avoiding redirects for logged in users.

Let us take a look at the undocumented hack using Cloudflare workers. 
<!--more-->

You are building a SAAS product. When a user is logged in, the home page should show the user's personal content. If the user is not logged in, it should show the home page that convinces users' to sign up or login (typically static content).

Ideally, you want the static content to be served from CDN. At the same time, you would want to avoid redirects to the logged in user.

If you are a developer consious of latency, you would want to serve as much content as quickly as possible. Reducing latency improves engagement. Also, Google crawler, considers latency as a ranking parameter.

The ultimate latency reduction guideline is to


  >  Serve *less data* over *short distance* with an *efficient protocol*.


This post assumes, you already know
1.  Content Delivery Network
1.  Service Worker
1.  Basic web terminologies like Cookies, Cache, Requests, etc

Let us see the various scenarios for the traffic and see how to improve in each case.

1.  Existing user - logged in
1.  Existing user - not logged in
1.  First time user.


### Existing user (logged in or not logged in)

For existing users, the biggest optimization you could do is to minimize amount of data transferred.

You could do that with Service Worker to intercept the request, and fetch only the dynamic content and render using the prefetched template.

I will explain that technique in detain in a later post.

### First time user (Real user or web crawler)

For the first time user, there is not much optimization you could make. There is no data cached. Also, the request has to go to the server to determine if the user is logged in.
Required to decide whether to serve the static content or the dynamic personalized content.

This trick lies in identifying whether the user is a first time user or existing user. We do it by looking at the cookies. 

[Codiva](https://www.codiva.io/) uses Express with Node. Express sets 'connect.sid' cookie on every response. In Cloudflare Service Worker, we could simply check if this cookie is set. If not, respond with the static home page.

Otherwise, send the request to the server. (Optimizing this case is in later post).


```
  if (event.request.headers.has('Cookie')
        && event.request.headers.get('Cookie').indexOf('connect.sid')>=0) {
      // Existing user.  
      event.respondWith(fetch(event.request));
    } else {
      // You may need to set the Page Rule to cache .html files
      var newUrl = new URL('/static/homepage.html', event.request.url);

      event.respondWith(fetch(newUrl.toString())
          .then(function(response){
                // Remove cache headers
                response = new Response(response.body, response)
                response.headers.delete('expires')
                response.headers.delete('cache-control')
                return response  
            }

          ));
    }
```

If you use other technologies, find the correct cookie. This does not need any change on the downstream server. You could do some sophisticated processing here to identify the right cookie. If you want to modify the server, you could even set a separate cookie when a user logs in. (Remember, the cookie might get invalid. So cookie present just means the user may be logged in. But cookie not present clearly means the user is not logged in.)

The advantage of this solution are.

1.  No changes needed on the downstream server.
1.  Very simple to implement and debug
1.  Serves the first time user from the edge. Whether the user comes from search result, search ad, or from direct or other referrals 
1.  Search Crawler will see the same content and faster. This is not a blackhat technique. 
1.  No redirects for logged in user (Try [Gmail](https://www.gmail.com) to see the annoying redirects for logged in user.) 


