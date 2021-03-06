## Background
I have been using nginx as [reverse proxy](https://en.wikipedia.org/wiki/Reverse_proxy) for my daily development work. After upgrading nginx today, it somehow stops working correctly: a vendor.js file built from webpack has not been served properly. I get the following error from chrome console: 

```bash
GET http://nginx.example.com/dist/js/vendor.js net::ERR_CONTENT_LENGTH_MISMATCH 
```

I take some quick lookarounds and find out:

* Network tab shows the vendor.js request has a status code 200.
  * The response header seems to be fine. However, there is nothing to preview in chrome network tab.
  * The Content-Length of the vendor.js matches the real size of the file. 

    ```bash
    # ls -l : get the size in byte
    -rw-r--r--   1 michaelzheng  staff   4844863 Jul  2 17:00 vendor.js
      
    # ls -lh : get the size in human readable format 
    -rw-r--r--   1 michaelzheng  staff   4.6M  Jul vendor.js 
    ```
* Loading the vendor.js directly through browser or `curl` works perfectly.

## Debug

* Find the location of nginx config file(i.e. nginx.conf)

    ```bash
    nginx -t
    ```
    
* Find the location of error log, which is usually defined in nginx.conf
* Check out the error

    ```bash
    # tail -f nginx
    2018/07/02 22:29:27 [crit] 14586#0: *3641 open() "/usr/local/var/run/nginx/proxy_temp/1/08/0000000081" failed
    (13: Permission denied) while reading upstream, client: 127.0.0.1, server: nginx.example.com, request: "GET /dist/js/vendor.js HTTP/1.1", 
    upstream: "http://127.0.0.1:8080/dist/js/vendor.js", host: "nginx.example.com", referrer: "http://nginx.example.com/testabc"
    ```

    Now that we can clearly see it's related to permission problem of the buffer directory `proxy_temp`.

## Solutions

* Solution 1 - Grant permission

    ```bash
    chown -R nobody:admin proxy_temp
    ```

    You need to first run the command `ps aux | grep nginx` to find out the owner of the nginx process which is usually `nobody`

* Solution 2 - Disable buffering with [proxy\_buffering](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_buffering) set to `off`

* Solution 3 - Change the buffer directory to other directory the nginx process owner has permission to, with [proxy\_temp\_path](http://nginx.org/en/docs/http/ngx_http_proxy_module.html#proxy_temp_path) directive.

## Notice

* If you want to follow the latest news/articles for the series of my blogs, Please [「Watch」](https://github.com/n0ruSh/blogs/)to Subscribe.
