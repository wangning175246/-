#### nginx的缓存

清理缓存 ngx_cache_purge

生成的缓存的方式

​	如访问 `http://netexr.blog.51cto.com/1.png` 如果被缓存那他的路径就是 /usr/local/nginx/cache/9/ad/e0bd86606797639426a92306b1b98ad9

1. nginx先把请求地址/1.png用md5进行哈希，得到e0bd86606797639426a92306b1b98ad9，因为默认proxy_cache_key的值是*$scheme$proxy_host$request_uri*,会对key进行md5计算。

2. level=1:2就是把最后一位数9拿出来建一个目录，然后再把9前面的2位建一个目录，最后把刚才得到的这个缓存文件放到9/ad目录中。

    同样的方法推理，如果配置level=1:1，那么缓存文件的路径就是/usr/local/nginx/cache/9/d/e0bd86606797639426a92306b1b98ad9

Etag的开启与关闭：一般是会为静态资源开启ETag

​	etag on|off;

#### nginx的匹配规则 

​	= 精确匹配

​    最长匹配

​    ^~ 前缀匹配

​	其他匹配 

​		~ 大小写敏感匹配

​		~* 大小写不敏感匹配

​		!~ 忽略正则匹配

​		!~* 忽略正则大小不敏感匹配

匹配即停止



​	