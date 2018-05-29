---
layout: docs
title: Production Setup with Nginx
description: Production Setup with Nginx
permalink: /docs/tutorials/pm2-nginx-production-setup
---

## Nginx as a HTTP proxy

This method is the common way of handling a Node.js server in order to make it show up on the port `80`. Usually, your web server will run on a random port (for example `3001`). The benefits of using Nginx as a HTTP Proxy, is that the Node.js web server will run from a user of your choice instead of root. Nginx will then forward the `80` port to the one of your choice (here `3001`).

Just add a virtual host (best known as "server block" in the Nginx world):

```Nginx

upstream my_nodejs_upstream {
    server 127.0.0.1:3001;
    keepalive 64;
}

server {
    listen 80;
    server_name my_nodejs_server;
    root /home/www/project_root;
    
    location / {
    	proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    	proxy_set_header Host $http_host;
    	proxy_set_header X-NginX-Proxy true;
    	proxy_http_version 1.1;
    	proxy_set_header Upgrade $http_upgrade;
    	proxy_set_header Connection "upgrade";
    	proxy_max_temp_file_size 0;
    	proxy_pass http://my_nodejs_upstream/;
    	proxy_redirect off;
    	proxy_read_timeout 240s;
    }
}

```

Learn more on these options on the [Nginx docs](http://nginx.org/en/docs/http/websocket.html)! Once you have this, all you will need is a PM2-linked Node.js server running on port `3001` and you'll have a production-ready HTTP server!
