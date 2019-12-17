---
title: nginx-http 模块
published: true
category: nginx
---


## http core 模块
http core模块有3个主要的块：http,server,location。

## 模块指令
### socket和host配置

* listen

listen指令用于指定监听的ip地址和端口。例子如下：
```
listen 192.168.1.1:80;
listen 127.0.0.1;
listen 80 default;
listen 443 ssl;
```
也可以监听sockets:
```
listen unix:/tmp/nginx.sock;
```

* server_name

指定server的主机名，当nginx接收一个请求后，它会将Header中的host和server的server_name进行匹配，第一个匹配项会被选择。
如果没有匹配的server，Nginx会选择匹配 listen 参数的第一个server（如listen *:80会处理80端口接收的所有请求）。例子如下：
```
server_name www.website.com website.com;
server_name *.website.com;
server_name .website.com; # combines both *.website.com and website.com
```
可以使用""匹配所有的没有host Header的请求，但是前面必须有一个主机名，或者使用"_"表示一个虚拟主机名。例子如下：
```
server_name website.com "";
server_name _ "";
```
* sendfile

使用系统内核的sendfile处理文件传输。默认禁用，Nginx使用自己的文件传输。值为on/off。

### path和documents

* root

root可配置在http,sever,location,if中，定义文件的根目录。默认值html。

* alias

alias可配置在location中，必须以“/”结束。和root的主要区别是nginx如何解析location后面的url，root的处理结果是：root路径+location路径。
alias的处理结果是：使用alias路径替换location路径。如：
```
location /t/ {
    root /www/root/html/;
}
```
如果请求访问的是`/t/a.html`，服务器将返回`/www/root/html/t/a.html`。
```
location ~^/download/(.*)$ {，
   root /home/webdata/www/$1
}
```
如果请求的是`/download/test.tar.gz`,服务器将返回`/home/webdata/www/download/test.tar.gz`。 
```
location /t {
    alias /www/root/html/new_t/;  #这里必须以/结尾。
}
```
如果请求访问的是`/t/a.html`，服务器将返回`/www/root/html/new_t/a.html`。

所以，一般情况下，在nginx配置中的良好习惯是：
 在location `/`中配置root目录； 在location `/path`中配置alias虚拟目录。alias不用校验路径是否存在。

* error_page

指定代替http错误的页面。
```
error_page 404 /not_found.html;
error_page 500 501 502 503 504 /server_error.html;
error_page 403 http://website.com/;
error_page 404 @notfound; # jump to a named location block
error_page 404 =200 /index.html; # 如果发生404，重定向到index.html并返回200。
```
* if_modified_since

定义如何处理if_modified_since header。if_modified_since多用于搜索引擎爬虫，如果请求文件在if_modified_since
时间后没有改变，那么服务器会返回304。值有三个：1，off:忽略if_modified_since header;2，exact 默认，当该header和请求文件
的修改日期相等时，返回304。3，before,当该header晚于请求文件的修改日期时，返回304。

* try_files

按顺序依次尝试处理服务。直到成功为止。例子如下：
```
location / {
try_files $uri $uri.html $uri.php $uri.xml @proxy;
}
# the following is a "named location block"
location @proxy {
proxy_pass 127.0.0.1:8080;
}
```

### 客户端请求

* keepalive_requests

一个keep-alive连接处理的最大请求数。

* keepalive_timeout

keep-alive超时时间，默认为75，第二个参数表示通过header发送给客户端的keep-alive。
```
keepalive_timeout 75 60;
```

* send_timeout

关闭不活动状态连接的时间，当客户端停止传送数据时，连接会变为不活动状态。默认60。

* client_body_in_file_only

该指令启动，那么客户端的HTTP请求body会在磁盘中存储为真实的普通文本文件。client body是客户端请求的数据减去http header。
值有：1,off，不存储为文件，默认。2, clean，存储为文件，请求处理完成后删除。3，on，总是存储为文件，适合dubbug，不推荐。

* client_body_in_single_buffer

是否将client body在内存中存储为单个buffer。值为on/off，默认off。

* client_body_buffer_size

指定保存client body的buffer大小,如果超过这个大小，body的全部或者部分会被存储在磁盘中。默认为8k,或者16k。

* client_body_timeout

定义当读取一个客户端请求body时，不活动的超时时间，默认60。

* client_header_buffer_size

定义header buffer的大小，默认1k足够了。当header中有较大cookie时，需要用到large_client_header_buffers，超过大小返回413。

* large_client_header_buffers

在client_header_buffer_size不足的情况下会使用，值为amount * size。header的每一行需要满足单个buffer的大小（size）。如果请求的URI行
超过了单个buffer的大小，nginx返回414。如果还有header行超过单个buffer的大小，返回400 Bad Request错误。语法：
```
large_client_header_buffers amount size
```
默认为4\*8kilobytes

* client_header_timeout

定义一个读取客户端请求header时，不活动的超时时间，默认60。

* client_max_body_size

定义客户端body的最大尺寸，默认为1m，当客户端有传输文件的情况时，可能会超出，需要重新定义。

* lingering_time

客户端上传数据超过max_client_body_size，nginx会立即发送413，然而很多浏览器还会无视该错误继续上传文件。
该值定义了发送413后多久关闭连接。默认30s。

* lingering_timeout

定义两个读取操作之间等待多久会关闭连接。默认5s。

* ignore_invalid_headers

如果该指令为off，如果请求header格式错误，那么Nginx会400 错误。相当于严格的header检查，默认为on。

* chunked_transfer_encoding

启用或关闭HTTP1.1的块状传输，默认为on。

### MIME类型

* types

指定接收的请求类型和后缀名，关联header中的Content-Type
```
types {
mimetype1 extension1;
mimetype2 extension2 [extension3…];
[…]
}
```
mimetype影响浏览器的行为，如果检测到请求是application/pdf，可以使用pdf插件直接打开它，而不是下载。
Nginx将MIME 类型的基本设置保存在一个单独的文件中（mime.types），可以引入到配置文件中。
```
include mime.types
```
这个文件已经包括了大多数类型的扩展，如果在types列表中没有发现需要的扩展或者MIME类型，那么可以使用
default_type指令。也可以使用types块重新覆盖types列表。如下：强制某个文件夹中的文件使用下载而不是展示。
但是具体的结果还是要根据浏览器和后缀名决定。
```
http {
 include mime.types;
 […]
 location /downloads/ {
  # removes all MIME types
  types { }
  default_type application/octet-stream;
 }
 […]
}
```

* default_type

定义默认的MIME类型，如果某个后缀没有找到对应的MIME类型，那么使用default_type定义的类型。默认值为text/plain。

### 界限和限制

* limit_except

在location中可以排除某些客户端的访问，通过指定方法和ip。如下，只允许局域网内的ip地址通过get访问/admin。
```
location /admin/ {
	limit_except GET {
		allow 192.168.1.0/24;
		deny all;
	}
}
```

* limit_rate

限制客户端每个链接每秒传输数据的速度。如 500k。默认无限制。

* limit_rate_after

指定在limit_rate限速启动之前传输的数量，如10m。默认无限制。

* satisfy

指定客户端访问需要满足的条件，有两个值，any|all，any满足其中之一，all满足所有条件（默认）。
下面是一个访问条件的例子：
```
location /admin/ {
  allow 192.168.1.0/24;
  deny all;
  auth_basic "Authentication required";
  auth_basic_user_file conf/htpasswd;
}
```
上例中，指定了两个条件，第一个：通过allow和deny指定固定ip的请求通过，第二个：通过
auth_basic和auth_basic_user_file指定验证通过的请求可以通过。

* internal 

指定只有内部可以访问。不需要设置值。

### 文件访问和缓存

* directio

如果启用，表示大于指定值的文件使用DirectIO。DirectIO让Nginx从磁盘等设备中直接读取到内存中，不经过中间的缓存处理。
值：文件大小或者off，默认值为off。

* directio_alignment

使用directio时的字节对齐，默认为512。


* open_file_cache

该指令启用对打开文件的信息的缓存，并不直接存储文件本身。包括如（__文件描述__,**文件和目录是否存在**,**文件错误，如权限错误，文件不存在等等**），
指令有两个参数1，max=X,X表示缓存存储对象的最大数量。如果这个数量达到，就的对象会被删除。2，inactive=Y,Y表示缓存存储对象的最大时间，默认情况下，Nginx
会在清除缓存之前等待60s。如果缓存被访问一次，那么缓存时间会重置。如果缓存对象被访问次数超过open_file_cache_min_uses，缓存将不会被清空（除非内存用完清除旧对象）。
语法：`open_file_cache max=X [inactive=Y] | off`，默认为off。

* open_file_cache_errors

打开文件的错误是否开启缓存，参考open_file_cache。

* open_file_cache_min_uses

参考open_file_cache，默认为1。

* open_file_cache_valid

文件缓存机制对应用来说十分重要，但是在文件改变频繁的场景下，缓存信息很快就会过时，文件信息需要在一段时间过后重新验证，该指令
指定多久时间对缓存对象重新验证。默认60（单位：秒）。


### 其他指令

* merge_slashes

当请求中有多个斜杠时，合并为一个，如果设置为on，当访问路径为http://website.com//document ，会将中间的//合并为一个。默认为off。

* resolver

指定DNS和缓存时间。如：
```
resolver 127.0.0.1; # use local DNS
resolver 8.8.8.8 8.8.4.4 valid=1h; # use Google DNS and cache results for 1 hour
```

* resolver_timeout

域名处理查询的超时时间，默认为30

* underscores_in_headers

值：on或者off，是否允许header name包含下划线：如test_header在off情况下是非法的。默认off。

* variables_hash_max_size

定义存放变量的hash table的最大数量。默认为512个。

* variables_hash_bucket_size

定义存放变量的 hash tables的块大小。

* post_action

在请求完成之后，需要执行的动作。如：
```
location /payment/ {
    post_action /scripts/done.php;
}
```

## 模块变量
HTTP模块中定义了大量的变量，但是只有一部分指令接收支持变量。如果一个指令中不支持某个变量，程序并不会报错，变量只会显示为普通文本。
这些变量可以分为三种，第一：客户端请求header中的变量，第二：返回给客户端的响应header中的变量，第三：Nginx自己生产的变量。

### 请求Header变量
* $http_host 请求头中的"Host"，客户端请求的主机名。

* $http_user_agent 请求头中的"User-Agent",描述web浏览器信息

* $http_referer 请求头中的"Referer",描述客户端前一个页面的URL。

* $http_via 请求头中的"via",描述客户端可能使用的代理。

* $http_x_forwarded_for 请求头中的“X-Forwarded-For”，如果客户端使用了代理，该参数表示客户端真正的IP地址。

* $http_cookie 请求头中的“Cookie”。

* $http_... 

其他header中的属性，名称需要对应。名称需要小写。“-”会被替换为“_”。

### 响应返回中的Header变量
* $sent_http_content_type 响应头中的“Content-Type”。

* $sent_http_content_length 响应头中的“Content_length”。

* $sent_http_location 响应头中的“Location”，表示需要的资源location和请求中指定的location不同。

* $sent_http_last_modified 响应头中的“Last-Modified”，表示请求资源修改的日期。

* $sent_http_connection 响应头中的“Connection”，表示连接是否保持alive或者已经关闭。

* $sent_http_keep_alive 响应头中的“Keep-Alive”，表示连接保持alive的持续时间。

* $sent_http_transfer_encoding 响应头中的“Transfer-Encoding”，表明响应体的编码方式（如，gzip）。

* $sent_http_cache_control 响应头中的“Cache-Control”，表明浏览器是否应该缓存资源。

* $sent_http_... 

其他响应头中的属性，名称需要对应。名称需要小写。“-”会被替换为“_”。

### Nginx提供的指令
* $arg_XXX 获取查询字符串（Get 参数），XXX为参数名称。

* $args 所有的查询参数合并在一起。

* $binary_remote_addr 客户端的IP地址二进制表示（4字节）。

* $body_bytes_sent 响应body中字节的数量。

* $connection_requests 当前连接处理请求的数量。

* $content_length 和HTTP header中的“Content-Length”相同。

* $content_type 和HTTP header中的“Content-Type”相同。

* $cookie_XXX 获取cookie中的属性，XXX为属性名称。

* $document_root 返回当前请求中的root指令的值。

* $document_uri 返回当前指令的URI,如果做了内部的redirect，那么它和原请求的地址可能不同。和$uri变量相同。

* $host 和HTTP请求头中的“Host”相同。

* $hostname 服务器的系统主机名。

* $https HTTPS连接设置为on，其他为空。

* $is_args 如果$args有参数，那么$is_args等于？。如果$args为空，那么$is_args也为空。可以用这个参数结合$args构建URI。如：
`index.php$is_args$args`。

* $limit_rate 返回每个链接传输速率的限制。也可以使用set设置这个值，如：
```
set $limit_rate 128k
```
* $nginx_version 返回nginx版本。

* $pid 返回nginx的进程id。

* $query_string 和$args相同。

* $remote_addr 返回客户端ip地址。

* $remote_port 返回客户端ip端口。

* $realpath_root 返回客户端请求中的文档根路径。

* $request_body 客户端请求体。

* $request_body_file 如果设置了请求body存储（参考指令client_body_in_file_only）这个变量表示临时文件的存储路劲。

* $request_completion 如果请求完成，返回OK，否则返回空字符串。

* $request_filename 返回当前请求中全文件名。

* $request_method 请求方法。

* $request_uri 请求的原始url，和$document_uri/$uri不同，在所有处理过程中保持不变。

* $scheme http或者https。

* $server_addr 服务器的IP地址。每次使用会执行一次系统调用，会影响系统性能。

* $server_name 返回server_name属性的值。

* $server_port 返回服务器socket的端口。

* $server_protocol 返回协议和版本，如HTTP/1.1。

* $time_iso8601,$time_local 返回ISO8601和本地格式化的时间。

* $uri 和$document_uri一样。

## Location模块
### 修饰符
* =修饰符 

等号修饰符，表示精确匹配，可以允许参数。如`location = /abcd` 可以匹配如下情况

1. `/abcd`。
2. `/ABCD` 文件系统大小写不敏感的情况下支持。
3. `/abcd?param1&param2`
	
* 无修饰符 

匹配以设置的location开头的路径。
* ~修饰符 

大小写敏感匹配指定的正则表达式。如下正则表达式使用^$指定开头和结尾，并且大小写敏感，可以允许参数。
```
location ~ ^/abcd$ {}
```
* ~*修饰符

大小写不敏感匹配。
* ^~修饰符

和无修饰符类似，匹配以设置的location开头的路径，不同的是如果表达式匹配，Nginx停止搜索其他的匹配。
* @修饰符

该模块不能被客户端访问，但是只能通过其他指令由内部请求生成，如try_files或者error_page。

### 匹配顺序和优先级
nginx中一个请求可能会匹配到多个location，所以理解匹配的优先级非常重要。修饰符之间的优先级从高到低如下：
1. =修饰符。
2. 无修饰符，精确匹配。
3. ^~修饰符。
4. ~或者~*。
5. 无修饰符，以location开头。

使用如下三个例子来说明匹配优先级：
1.  
```
server {
  server_name website.com;
  location /doc {
    […] # requests beginning with "/doc"
  }
  location ~* ^/document$ {
    […] # requests exactly matching "/document"
  }
}
```
当访问`http://website.com/document `时，第二个location是优先级4，而第一个location是优先级5。所以会匹配第二个。
2. 
```
server {
  server_name website.com;
  location /document {
    […] # requests beginning with "/document"
  }
  location ~* ^/document$ {
    […] # requests exactly matching "/document"
  }
}
```
当访问`http://website.com/document `时，第一个location是优先级2，第二个是优先级4，所以第一个会匹配。
3. 
```
server {
  server_name website.com;
  location ^~ /doc {
    […] # requests beginning with "/doc"
  }
  location ~* ^/document$ {
    […] # requests exactly matching "/document"
  }
}
```
当访问`http://website.com/document `时，第一个location是优先级3，第二个优先级是4.所以会匹配第一个。