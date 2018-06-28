---
layout: post
title: A webserver to rules them all
tags: ubuntu 18.04 hw2018 server apache
permalink: server-18.04-apache.html
#image: /data/img/wide/www.jpg
---
Many services I use have a web interface, so here is my *basic* [apache](/tag/apache.html)
setup. When I say *basic*, but I mean apache with https, mysql and phpmyadmin...

## Needs

There are plenty of opensource webservers, but I've choosen [apache httpd](/tag/apache.html)
mainly for two reasons:
 * I know how to use it for years.
 * I even don't know how to pronounce [`nginx`](https://nginx.org/){:.external}.

### Services
On my home server, a web interface is available for:

| Service       | Description                         | Type            | Related tag
| -             | -                                   | -               |
| backuppc      | rsync based on disk backup          | web-app/perl    | -
| grafana       | data visualisation and monitoring   | webserver       | [grafana](/tag/grafana.html)
| phpmyadmin    | web interface for mysql             | web-app/php     | [mysql](/tag/mysql.html)
| tvheadhend    | TV streaming and recording server   | webserver       | [tvheadend](/tag/tvheadend.html)
| weewx         | weather station software            | generated files | [weewx](/tag/weewx.html)
| zabbix        | network monitoring                  | web-app/php     | [zabbix](/tag/zabbix.html)

Apparently the grafana and tvheadend guys thinks it's a good idea to include a
webserver... Please don't, and here is why (IMHO):
 * The http/https ports are already used by another app.
 * There is already a lot of good opensource web servers, the time invested in
  your integrated web server could have been better spend.
 * A person that is able to setup grafana or tvheadend, is clearly able to install
 a webserver. Please have a look at phpmyadmin, it's so easy to setup on apache.

### https, http2, php, ...
For performance reasons I want to enable [http2](https://en.wikipedia.org/wiki/HTTP/2){:.external},
and this will affect which apache mpm to install.

# Setup

```
```