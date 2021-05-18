*** Know Issue ***
- Just support SSL with Cloudflare or same midle DNS 

*** Step Install ***
```
yum install -y python-sphinx graphviz`
yum install -y make autoconf automake jemalloc-devel libedit-devel libtool libunwind-devel ncurses-devel pcre-devel pkgconfig python3-docutils cpio python3`

wget https://varnish-cache.org/_downloads/varnish-6.5.1.tgz
tar -xvzf varnish-6.5.1.tgz
cd varnish-6.5.1
./configure --prefix=/opt/varnish
make && make check && make install
/opt/varnish/sbin/varnishd -V

vim /lib/systemd/system/varnish.service
```
Copy varnish.service to path

```
systemctl daemon-reload
systemctl start vanish
systemctl status vanish.service
systemctl enable vanish.service

```
Copy example.vcl to /opt/varnish/etc/

restart varnish, nginx service, make sure open port in firewalld

*** Testing ***
First, point domain on cloudflare, make sure turn on feature SSL on it.
Next, wait DNS update, using curl command to access to site:
`curl -I -k https://abc.com/`
The result as bellow:
```
HTTP/1.1 200 OK
Server: nginx
Date: Tue, 18 May 2021 02:29:32 GMT
Content-Type: text/html; charset=UTF-8
Content-Length: 204592
Connection: keep-alive
Keep-Alive: timeout=60
Vary: Accept-Encoding
X-Powered-By: PHP/7.2.18
Link: <https://mouldking.store/wp-json/>; rel="https://api.w.org/"
Link: <https://mouldking.store/wp-json/wp/v2/pages/6508>; rel="alternate"; type="application/json"
Link: <https://mouldking.store/>; rel=shortlink
Strict-Transport-Security: max-age=15768000
X-Frame-Options: SAMEORIGIN
Vary: Accept-Encoding
X-Varnish: 3906550
Age: 0
Via: 1.1 varnish (Varnish/6.5)
X-Cache: MISS
Accept-Ranges: bytes
Strict-Transport-Security: max-age=15768000
X-Frame-Options: SAMEORIGIN
```
attention `X-Cache`, state is MISS, 2nd visit, it will HIT.
To purge cache:
- restart varnish service
- or access url /purge/
```
[root@test-varnish-cache ~]# curl -I -k https://mouldking.store/purge/
HTTP/1.1 200 Purged
Server: nginx
Date: Tue, 18 May 2021 02:29:25 GMT
Content-Type: text/html; charset=utf-8
Content-Length: 241
Connection: keep-alive
Keep-Alive: timeout=60
X-Varnish: 372910
Retry-After: 5
Accept-Ranges: bytes
Strict-Transport-Security: max-age=15768000
X-Frame-Options: SAMEORIGIN
```

