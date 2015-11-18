title: 使用Nginx和PHP动态解析markdown文件
date: 2015-11-18 15:00:28
categories: 工具
tags: [nginx, php, markdown, parsedown]
---

在Windows上，前端使用nginx作为http server，后端使用php和[parsedown](http://www.parsedown.org)动态解析markdown文件。

<!-- more -->

## 安装PHP并运行php-cgi
下载安装PHP以及RunHiddenConsole.exe这个小工具（网上搜一下就有），
然后写一个小脚本来启动php-cgi，

```bat
@echo off

echo Starting PHP FastCGI
set php-root=D:\Program Files\php\php-5.6.15-nts-Win32-VC11-x86

"D:\Program Files\php\RunHiddenConsole.exe" "%php-root%\php-cgi.exe"  -b 127.0.0.1:9123  -c "%php-root%\php.ini"

echo %errorlevel%
```

## 配置nginx的fastcgi功能
主要的配置部分如下，

```
set $www_root_dir "path/to/your/root/dir";
location / {
	alias   $www_root_dir;			
	rewrite .* /md.php?file=$request_filename  last;
}

# fastcgi
location ~ \.php$ {
	root		   $www_root_dir/fastcgi;
	fastcgi_pass   127.0.0.1:9123;
	include        fastcgi.conf;
}
```

## 调用parsedown模块解析markdown文件
将`md.php`和`Parsedown.php`放到`$www_root_dir/fastcgi`目录下，

```php md.php
<html>
<head><title></title></head>

<body>
<?php
$md = @$_GET['file'];
if(is_dir($md)) {
	$md = rtrim($md, "/")."/index.md";
}

include 'Parsedown.php';
$Parsedown = new Parsedown();

if(file_exists($md)) {
	$content = file_get_contents($md) or '__Read file error__';
	echo $Parsedown->text($content);
} else {
	echo $Parsedown->text('__404 Error__');
}
?>
</body>
</html>
```

整个流程是这样的：
> 浏览器访问`xxx.md`，http server(nginx)将访问路径重写为`/md.php?file=xxx.md`，
> `md.php`调用`parsedown`将markdown内容转换为html并返回给http server，并由其
> 返回给浏览器。

(over)