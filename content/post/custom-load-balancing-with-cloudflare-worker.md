---
title: "Custom Load Balancing With Cloudflare Worker"
pageTitle: "Custom Load Balancing With Cloudflare Worker | Codiva Online Compiler Blog"

date: 2018-06-25T00:27:45+05:30
draft: false
tags: [Cloudflare,ServiceWorker,web,latency,scaling]
---
Launched your website? Getting lot of traffic? And you are planning to add more servers? You will need load balanacers for scalability and reliability of your website.

In this post, we will learn about load balancers and how to set them up at a low cost with Cloudflare Service Workers.
<!--more-->

This post assumes you know

*  JavaScript
*  ServiceWorker
*  Cloudflare Worker

## The basic pattern

Intercept a request, and send the request to a different host.

```
addEventListener('fetch', event => {
  var url = new URL(event.request.url);

  // https://example.com/path/ to https://myorigin.example.com/path
  url.hostname = 'myorigin.' + url.hostname
  
  event.respondWith(fetch(event.request));
});

```
This doesn't do anything useful yet, but this is the basic pattern that will be used in the rest of the examples.

## Load balancer with random routing

When you have a list of origin servers, pick a random host to route to.

This is a very basic load balancing technique to evenly distribute the traffic across all origin servers.

```
var hostnames = [
  0.example.com,
  1.example.com,
  2.example.com
];

addEventListener('fetch', event => {
  var url = new URL(event.request.url);

  // Randomly pick the next host 
  url.hostname = hostnames[getRandomInt(hostnames.length)];
  
  event.respondWith(fetch(event.request));
});

function getRandomInt(max) {
  return Math.floor(Math.random() * Math.floor(max));
}
```

## Load balancer with fallback

What to do when the host is down? A simple fallback strategy to route the request to a different host. Use this only if you know the request are idempotent. Mostly GET requests should be okay.

```

addEventListener('fetch', event => {
  var url = new URL(event.request.url);

  // Randomly pick the primary host
  var primary = getRandomInt(hostnames.length);
  url.hostname = hostnames[primary];

  var timeoutId = setTimeout(function() {
    var backup;
    do {
        // Naive solution to pick a backup host
        backup = getRandomInt(hostnames.length);
    } while(backup === primary);

  }, 2000 /* 2 seconds */);

  fetch(event.request)
    .then(function(response) {
        clearTimeout(timeoutId);
        event.respondWith(response);
    });
});

```

## Sticky routing

You may want to route the subsequent requests from the same user to the same host. This can reduce the latency, if the backend caches the data effectively.

## Geographic routing
Cloudflare adds [CF-IPCountry](https://support.cloudflare.com/hc/en-us/articles/200168236-What-does-Cloudflare-IP-Geolocation-do-) header to all requests once Cloudflare IP Geolocation is enabled.

You can access it using,

```
var countryCode = event.request.headers.get('CF-IPCountry');
```

You may need to provide a mapping of which countries to use which datacenter. If you are starting out, look at the places with most users for your site, and target only those countries.

In my case, India and the US contributes 80% of the traffic. So, for India and its neighbors, use India datacenter. For all others, use the US datacenters.

```
const US_HOST = "us.example.com"
const IN_HOST = "in.example.com"

var COUNTRIES_MAP = {
  IN: IN_HOST,
  PK: IN_HOST,
  BD: IN_HOST,
  SL: IN_HOST,
  NL: IN_HOST
}
addEventListener('fetch', event => {
  var url = new URL(event.request.url);

  var countryCode = event.request.headers.get('CF-IPCountry');
  if (COUNTRIES_MAP[countryCode]) {
    url.hostname = COUNTRIES_MAP[countryCode];
  } else {
    url.hostname = US_HOST;
  }
  
  event.respondWith(fetch(event.request));
});
```

## Geographic routing with random random loadbalancing within datacenter

```
const US_HOSTS = [
  0.us.example.com,
  1.us.example.com,
  2.us.example.com
];

const IN_HOSTS = [
  0.in.example.com,
  1.in.example.com,
  2.in.example.com
];

var COUNTRIES_MAP = {
  IN: IN_HOSTS,
  PK: IN_HOSTS,
  BD: IN_HOSTS,
  SL: IN_HOSTS,
  NL: IN_HOSTS
}
addEventListener('fetch', event => {
  var url = new URL(event.request.url);

  var countryCode = req.headers.get('CF-IPCountry');
  var hostnames = US_HOSTS;
  if (COUNTRIES_MAP[countryCode]) {
    hostnames = COUNTRIES_MAP[countryCode];
  }
  // Randomly pick the next host 
  url.hostname = hostnames[getRandomInt(hostnames.length)];
  
  event.respondWith(fetch(url, event.request));
});

function getRandomInt(max) {
  return Math.floor(Math.random() * Math.floor(max));
}
```
