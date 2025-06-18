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

5. 安裝 MySQL

```
~$ sudo apt-get install mysql-server
```

使用 `mysql -v` 檢視 MySQL 安裝的版本

```
~$ mysql --version
mysql  Ver 8.0.42-0ubuntu0.24.04.1 for Linux on x86_64 ((Ubuntu))
```

使用 `systemctl status mysql` 檢視該服務是否確實啟動

```
~$ sudo systemctl status mysql
● mysql.service - MySQL Community Server
     Loaded: loaded (/usr/lib/systemd/system/mysql.service; enabled; preset: enabled)
     Active: active (running) since Wed 2025-06-18 11:22:53 CST; 2min 3s ago
    Process: 19087 ExecStartPre=/usr/share/mysql/mysql-systemd-start pre (code=exited,>
   Main PID: 19095 (mysqld)
     Status: "Server is operational"
      Tasks: 38 (limit: 9435)
     Memory: 363.7M (peak: 377.8M)
        CPU: 4.129s
     CGroup: /system.slice/mysql.service
             └─19095 /usr/sbin/mysqld

Jun 18 11:22:52 NT200608-VM01 systemd[1]: Starting mysql.service - MySQL Community Ser>
Jun 18 11:22:53 NT200608-VM01 systemd[1]: Started mysql.service - MySQL Community Serv>
```

執行 `mysql_secure_installation` 提升 MySQL 安全性

```
~$ sudo mysql_secure_installation

Securing the MySQL server deployment.

Connecting to MySQL using a blank password.

VALIDATE PASSWORD COMPONENT can be used to test passwords
and improve security. It checks the strength of password
and allows the users to set only those passwords which are
secure enough. Would you like to setup VALIDATE PASSWORD component?

Press y|Y for Yes, any other key for No: y

There are three levels of password validation policy:

LOW    Length >= 8
MEDIUM Length >= 8, numeric, mixed case, and special characters
STRONG Length >= 8, numeric, mixed case, special characters and dictionary                  file

Please enter 0 = LOW, 1 = MEDIUM and 2 = STRONG: 2

Skipping password set for root as authentication with auth_socket is used by default.
If you would like to use password authentication instead, this can be done with the "ALTER_USER" command.
See https://dev.mysql.com/doc/refman/8.0/en/alter-user.html#alter-user-password-management for more information.

By default, a MySQL installation has an anonymous user,
allowing anyone to log into MySQL without having to have
a user account created for them. This is intended only for
testing, and to make the installation go a bit smoother.
You should remove them before moving into a production
environment.

Remove anonymous users? (Press y|Y for Yes, any other key for No) : y
Success.


Normally, root should only be allowed to connect from
'localhost'. This ensures that someone cannot guess at
the root password from the network.

Disallow root login remotely? (Press y|Y for Yes, any other key for No) : y
Success.

By default, MySQL comes with a database named 'test' that
anyone can access. This is also intended only for testing,
and should be removed before moving into a production
environment.


Remove test database and access to it? (Press y|Y for Yes, any other key for No) : y
 - Dropping test database...
Success.

 - Removing privileges on test database...
Success.

Reloading the privilege tables will ensure that all changes
made so far will take effect immediately.

Reload privilege tables now? (Press y|Y for Yes, any other key for No) : y
Success.

All done!
```

6. 安裝 Composer

先安裝 curl

```
~$ sudo apt-get install curl
```

下載 Composer 並將其安裝到指定路徑

```
~$ sudo curl -sS https://getcomposer.org/installer | sudo php -- --filename=composer --install-dir=/usr/bin
[sudo] password for lucas-chen: 
All settings correct for using Composer
Downloading...

Composer (version 2.8.9) successfully installed to: /usr/bin/composer
Use it: php /usr/bin/composer
```

> `-s`: slient
> `-S`: show error
> `php --`: 呼叫 PHP CLI 執行傳進來的安裝 script
> `--filename=composer`: 將 `composer.phar` 重新命名為 `composer`，安裝完之後就能直接在 CMD 輸入 `composer` 來使用，而不用加上 `.phar`
> `--install-dir=/usr/bin`: 指定要把 `composer` 檔案放到常作為全域可執行檔的預設搜尋路徑之一的 `/usr/bin`

使用 `composer --version` 檢視 Composer 安裝的版本

```
~$ composer --version
Composer version 2.8.9 2025-05-13 14:01:37
PHP version 8.3.6 (/usr/bin/php8.3)
Run the "diagnose" command to get more detailed diagnostics output.
```

7. 安裝 Elasticsearch

先安裝 `apt-transport-https`，允許使用透過 HTTPS 存取的儲存庫

```
~$ sudo apt-get install apt-transport-https
```

下載簽章公鑰檔(ASCII armor 格式)，將公鑰轉換成二進位 `.gpg` 格式方便 apt 讀取並輸出到 keyring 

```
~$ wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elasticsearch-keyring.gpg
```

接下來，apt 就能自動使用剛建立的 keyring 來驗證各套件簽章，確保來源是可信的

```
~$ echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.gpg] https://artifacts.elastic.co/packages/8.x/apt stable main
```

安裝 Elasticsearch

```
~$ sudo apt update
~$ sudo apt install elasticsearch
```

使用 `systemctl enable elasticsearch` 讓 elasticsearch 服務成為開機啟動項目之一

```
~$ sudo systemctl enable elasticsearch.service
Created symlink /etc/systemd/system/multi-user.target.wants/elasticsearch.service → /usr/lib/systemd/system/elasticsearch.service.
```

啟動 elasticsearch 服務，然後使用 `systemctl status elasticsearch` 檢視該服務是否確實啟動

```
~$ sudo systemctl start elasticsearch.service
~$ sudo systemctl status elasticsearch.service
● elasticsearch.service - Elasticsearch
     Loaded: loaded (/usr/lib/systemd/system/elasticsearch.service; enabled; preset: e>
     Active: active (running) since Wed 2025-06-18 14:20:35 CST; 24s ago
       Docs: https://www.elastic.co
   Main PID: 22064 (java)
      Tasks: 98 (limit: 9435)
     Memory: 4.4G (peak: 4.4G)
        CPU: 32.855s
     CGroup: /system.slice/elasticsearch.service
             ├─22064 /usr/share/elasticsearch/jdk/bin/java -Xms4m -Xmx64m -XX:+UseSeri>
             ├─22125 /usr/share/elasticsearch/jdk/bin/java -Des.networkaddress.cache.t>
             └─22145 /usr/share/elasticsearch/modules/x-pack-ml/platform/linux-x86_64/>

Jun 18 14:20:20 NT200608-VM01 systemd[1]: Starting elasticsearch.service - Elasticsear>
Jun 18 14:20:35 NT200608-VM01 systemd[1]: Started elasticsearch.service - Elasticsearc>
```

```
# ---------------------------------- Cluster -----------------------------------
#
# Use a descriptive name for your cluster:
#
#cluster.name: my-application
cluster.name: magento 2.4.7
#
```

```
# ------------------------------------ Node ------------------------------------
#
# Use a descriptive name for the node:
#
#node.name: node-1
node.name: ubuntu
#
# Add custom attributes to the node:
#
#node.attr.rack: r1
#
```

```
# ---------------------------------- Network -----------------------------------
#
# By default Elasticsearch is only accessible on localhost. Set a different
# address here to expose this node on the network:
#
#network.host: 192.168.0.1
network.host: 127.0.0.1
#
# By default Elasticsearch listens for HTTP traffic on the first free port it
# finds starting at 9200. Set a specific HTTP port here:
#
#http.port: 9200
http.port: 9200
#
# For more information, consult the network module documentation.
#
```

在開發環境中暫時關閉安全驗證相關功能

```
# Enable security features
xpack.security.enabled: false
```

重新載入系統設定檔以套用前述數個步驟做的變更

```
~$ sudo systemctl daemon-reload
```

重新啟動 elasticsearch 服務

```
~$ sudo systemctl restart elasticsearch
```

測試 elasticsearch 是否確實執行

```
~$ curl -XGET 'http://localhost:9200'
{
  "name" : "ubuntu",
  "cluster_name" : "magento 2.4.7",
  "cluster_uuid" : "cAu8EGtLRUyGK-dCjfv2qg",
  "version" : {
    "number" : "8.18.2",
    "build_flavor" : "default",
    "build_type" : "deb",
    "build_hash" : "c6b8d8d951c631db715485edc1a74190cdce4189",
    "build_date" : "2025-05-23T10:07:06.210694702Z",
    "build_snapshot" : false,
    "lucene_version" : "9.12.1",
    "minimum_wire_compatibility_version" : "7.17.0",
    "minimum_index_compatibility_version" : "7.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

8. 設定 MySQL 資料庫

```
~$ sudo mysql -u root -p
...
mysql> CREATE DATABASE magentodb;
Query OK, 1 row affected (0.03 sec)

mysql> CREATE USER '<username>'@'localhost' IDENTIFIED BY '<passwords>';
Query OK, 0 rows affected (0.03 sec)

mysql> GRANT ALL ON magentodb.* TO '<username>'@'localhost';
Query OK, 0 rows affected (0.02 sec)

mysql> FLUSH PRIVILEGES;
Query OK, 0 rows affected (0.04 sec)

mysql> EXIT
Bye
```

9. 設定 `/var/www/html` 權限

將之後要安裝 Magento 的資料夾的權限變更成擁有者可讀/寫/執行，其他人只能讀取/執行

```
~$ sudo chown -R $USER:$USER /var/www/html
~$ sudo chmod -R 755 /var/www/html
~$ ll /var/www/html
total 12
drwxr-xr-x 2 lucas-chen lucas-chen 4096 Jun 18 09:55 ./
drwxr-xr-x 3 root       root       4096 Jun 18 09:55 ../
-rwxr-xr-x 1 lucas-chen lucas-chen  615 Jun 18 09:55 index.nginx-debian.html*
```

10. 建立 Magento 的 Access Keys

![Magento Access Keys](https://github.com/user-attachments/assets/60e17f00-39ee-4c82-81f7-644ecaa6e556)
