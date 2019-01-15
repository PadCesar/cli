---
layout: post
title: '`grafana` speedup (update #1)'
tags: ubuntu 18.04 16.04 hw2018 server apache grafana
permalink: apache-gzip-grafana-faster.html
---

[grafana](/tag/grafana.html) uses the [`json`](https://en.wikipedia.org/wiki/JSON){:.external}
data format to transfer data to the browser. While `json` is easy to work with,
it is really bandwidth inefficient. This may not be a problem on a ~~local~~ fast
network, but I use grafana through a 10 Mbit/s internet access, so it can be
painfully slow.

But http traffic can be [compressed](https://en.wikipedia.org/wiki/HTTP_compression){:.external}
, and [apache](/tag/apache.html) obviously support that.

## apache configuration
0. Create the configuration file `/etc/apache2/conf-available/gzip.conf`:

    ```config
    <ifmodule mod_deflate.c>
    	BrowserMatch ^Mozilla/4 gzip-only-text/html
    	BrowserMatch ^Mozilla/4\.0[678] no-gzip
    	BrowserMatch \bMSIE !no-gzip !gzip-only-text/html
    
    	# compress content with type html, text, and css, json
    	AddOutputFilterByType DEFLATE text/html
    	AddOutputFilterByType DEFLATE text/plain
    	AddOutputFilterByType DEFLATE text/xml
    	AddOutputFilterByType DEFLATE application/xhtml+xml
    	AddOutputFilterByType DEFLATE application/xml
    	AddOutputFilterByType DEFLATE text/css
    	AddOutputFilterByType DEFLATE text/javascript
    	AddOutputFilterByType DEFLATE application/x-javascript
    	AddOutputFilterByType DEFLATE application/javascript
    	AddOutputFilterByType DEFLATE image/svg+xml
    	AddOutputFilterByType DEFLATE application/rss+xml
    	AddOutputFilterByType DEFLATE application/atom_xml
    	AddOutputFilterByType DEFLATE application/json
    	AddOutputFilterByType DEFLATE application/octet-stream
    
    	<ifmodule mod_headers.c>
    		# properly handle requests coming from behind proxies
    		Header append Vary User-Agent
    	</ifmodule>
    </ifmodule>
    ```

0. Enable the required modules:

    ```console
    sudo a2enmod deflate
    sudo a2enmod headers
    ```

0. Enable the configuration file:

    ```console
    sudo a2enconf gzip
    ```

0. Check the server configuration:

    ```console
    sudo apache2ctl configtest 
    Syntax OK
    ```

0. Restart apache:

    ```console
    sudo systemctl restart apache2
    ```

## Numbers
Here are some numbers for displaying 1 year of data, over my light-speed 10
MBit/s connection:

| Test condition | Uncompressed data size | Uncompressed duration | Compressed size | Compressed duration | Gain 
| - |
| browser cache disabled				| 50.5 MB | 52 s | 5.2 MB | 11 s | ***4.7x faster***
| browser cache active (second load)	| 45.5 MB | 41 s | 3.8 MB |  5 s | ***8.2x faster***

On the local network, the same page loads in ±4 seconds, with or without cache
and with or without compression.

Compression comes at the cost of an increase in CPU load and local access may be
a little bit slower (on simpler pages), but this is the price to pay to run
***8.2x faster***  remotely.