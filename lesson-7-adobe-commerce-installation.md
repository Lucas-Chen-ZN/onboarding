# Lesson 7: Adobe Ccommerce Installation

## 

![On-premises installation overview](https://github.com/user-attachments/assets/43177272-2c6f-4e11-aa63-e6bfa8ca18e9)

## 參考範例

[System requirements](https://experienceleague.adobe.com/en/docs/commerce-operations/installation-guide/system-requirements)
[How to Install Magento on Ubuntu 24.04](https://greenwebpage.com/community/how-to-install-magento-on-ubuntu-24-04/)
[How to Install Magento 2.4.6 on Ubuntu 22.04?](https://www.mgt-commerce.com/tutorial/install-magento-2-4-6-on-ubuntu-22-04/)

## 1. 在 VirtualBox 建立 Ubuntu

```
~$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 24.04.2 LTS
Release:	24.04
Codename:	noble
```

## 2. 更新 OS

```
~$ sudo apt-get update
~$ sudo apt-get upgrade
```

## 3. 安裝 Nginx

```
~$ sudo apt-get install nginx
```

啟動 nginx 服務，然後使用 `systemctl status nginx` 檢視該服務是否確實啟動

```
~$ sudo systemctl start nginx
~$ sudo systemctl status nginx
● nginx.service - A high performance web server and a reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-06-18 09:12:49 CST; 2s ago
       Docs: man:nginx(8)
    Process: 6014 ExecStartPre=/usr/sbin/nginx -t -q -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
    Process: 6016 ExecStart=/usr/sbin/nginx -g daemon on; master_process on; (code=exited, status=0/SUCCESS)
   Main PID: 6017 (nginx)
      Tasks: 3 (limit: 9435)
     Memory: 2.4M (peak: 2.5M)
        CPU: 11ms
     CGroup: /system.slice/nginx.service
             ├─6017 "nginx: master process /usr/sbin/nginx -g daemon on; master_process on;"
             ├─6018 "nginx: worker process"
             └─6019 "nginx: worker process"

Jun 18 09:12:49 NT200608-VM01 systemd[1]: Starting nginx.service - A high performance web server and a reverse proxy server...
Jun 18 09:12:49 NT200608-VM01 systemd[1]: Started nginx.service - A high performance web server and a reverse proxy server.
```

開啟 Firefox 並在網址列輸入 localhost，就能看到如下圖網頁

![Welcome to nginx](https://github.com/user-attachments/assets/5b77eca2-e051-48b2-955a-048c4dc893a6)

使用 `systemctl enable nginx` 讓 nginx 服務成為開機啟動項目之一

```
~$ sudo systemctl enable nginx
Synchronizing state of nginx.service with SysV service script with /usr/lib/systemd/systemd-sysv-install.
Executing: /usr/lib/systemd/systemd-sysv-install enable nginx
```

4. 安裝 PHP

由於要使用 Nginx 作為 Web Server，所以不需要安裝 Apache。

從 Ubuntu Packages Search 網頁可以知道 `php` 套件會優先相依於 `libapache2-mod-php<version>` 或者 `php<version>-fpm`、或者 `php<version>-cgi`，
若先安裝 `php<version>-fpm`，
就不會再安裝 Apache 了

```
~$ sudo php-fpm
~$ sudo apt-get install php php-bcmath php-curl php-dev php-gd php-intl php-mbstring php-mysql php-soap php-xml php-zip
```

使用 `php -v` 檢視 PHP 安裝的版本

```
~$ php -v
PHP 8.3.6 (cli) (built: Mar 19 2025 10:08:38) (NTS)
Copyright (c) The PHP Group
Zend Engine v4.3.6, Copyright (c) Zend Technologies
    with Zend OPcache v8.3.6, Copyright (c), by Zend Technologies
```

使用 `php -m` 檢視 PHP 安裝的模組

```
~$ php -m
[PHP Modules]
bcmath
calendar
Core
ctype
curl
date
dom
exif
FFI
fileinfo
filter
ftp
gd
gettext
hash
iconv
intl
json
libxml
mbstring
mysqli
mysqlnd
openssl
pcntl
pcre
PDO
pdo_mysql
Phar
posix
random
readline
Reflection
session
shmop
SimpleXML
soap
sockets
sodium
SPL
standard
sysvmsg
sysvsem
sysvshm
tokenizer
xml
xmlreader
xmlwriter
xsl
Zend OPcache
zip
zlib

[Zend Modules]
Zend OPcache
```

接下來，先找出 php 設定檔的路徑

```
~$ php --ini | grep "Loaded Configuration File"
Loaded Configuration File:         /etc/php/8.3/cli/php.ini
```

再參考[PHP settings](https://experienceleague.adobe.com/en/docs/commerce-operations/installation-guide/prerequisites/php-settings)編輯以下設定：

* 設定 PHP「實際路徑(realpath)快取」可以佔用的最大記憶體

```
; Determines the size of the realpath cache to be used by PHP. This value should
; be increased on systems where PHP opens many files to reflect the quantity of
; the file operations performed.
; Note: if open_basedir is set, the cache is disabled
; https://php.net/realpath-cache-size
realpath_cache_size = 10M
```

* 設定 PHP「實際路徑(realpath)快取」的「存活時間」(Time-To-Live，TTL)，單位為秒

```
; Duration of time, in seconds for which to cache realpath information for a given
; file or directory. For systems with rarely changing files, consider increasing this
; value.
; https://php.net/realpath-cache-ttl
realpath_cache_ttl = 7200
```

* 啟用 `opcache.save_comments`

```
; If disabled, all PHPDoc comments are dropped from the code to reduce the
; size of the optimized code.
opcache.save_comments=1
```

* 啟用 PHP 標籤縮寫

```
; This directive determines whether or not PHP will recognize code between
; <? and ?> tags as PHP source which should be processed as such. It is
; generally recommended that <?php and ?> should be used and that this feature
; should be disabled, as enabling it may result in issues when generating XML
; documents, however this remains supported for backward compatibility reasons.
; Note that this directive does not control the <?= shorthand tag, which can be
; used regardless of this directive.
; Default Value: On
; Development Value: Off
; Production Value: Off
; https://php.net/short-open-tag
short_open_tag = On
```

* 延長每個腳本的最大執行時間

```
; Maximum execution time of each script, in seconds
; https://php.net/max-execution-time
; Note: This directive is hardcoded to 0 for the CLI SAPI
max_execution_time = 3600
```
