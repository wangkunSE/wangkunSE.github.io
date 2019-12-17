---
title: Nginx location匹配规则
date: 2019-09-25 18:38:43
categories: [编程]
tags:
- nginx
---



#### 语法规则

> ```shell
> location [=|~|~*|^~] /uri/ { … }
> ```

| 模式                | 含义                                                         |
| ------------------- | :----------------------------------------------------------- |
| location = /uri     | = 表示精确匹配，只有完全匹配上才能生效                       |
| location ^~ /uri    | ^~ 开头对URL路径进行前缀匹配，并且在正则之前。               |
| location ~ pattern  | 开头表示区分大小写的正则匹配                                 |
| location ~* pattern | 开头表示不区分大小写的正则匹配                               |
| location /uri       | 不带任何修饰符，也表示前缀匹配，但是在正则匹配之后           |
| location /          | 通用匹配，任何未匹配到其它location的请求都会匹配到，相当于switch中的default |

- 首先精确匹配 `=`
- 其次前缀匹配 `^~`
- 其次是按文件中顺序的正则匹配
- 然后匹配不带任何修饰的前缀匹配。
- 最后是交给 `/` 通用匹配
- 当有匹配成功时候，停止匹配，按当前匹配规则处理请求



### 遇到的问题

> 想要在非根目录下部署静态文件，但是一直显示404。 配置如下
>
>  ```
> location ^~ /admin {
> 	  root  /a/n/admin;
> 	  index index.html;
> 	}
>  ```
>
> 意思就是将根目录指向nginx所在的admin下，而admin目录下有index.html文件。想要通过访问http://localhost/admin 访问到index.html。但是这样写会一直报404。
>
> **根源**
>
> 查了下nginx的error日志，发现有如下一句话
>
> 2019/09/25 16:13:48 [error] 53456#57324: *74 CreateFile() "D:\develop\nginx-1.16.1/admin/admin" failed (2: The system cannot find the file specified), client: 127.0.0.1, server: localhost, request: "GET /admin HTTP/1.1", host: "localhost"
> 
>
> 可以看到nginx尝试到/admin/admin下寻找我们的文件，这说明location后的前缀会在寻找文件时起作用！！！所以需要把它失效掉。配置如下
>
> ```
> location ^~ /admin {
> 		  root  /a/b;
> 		  index index.html;
> 		}
> ```
>
> 将root定位到父级目录，问题解决！
>
> 
