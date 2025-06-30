# Lesson 10: magento-cloud CLI Installation

## 1. 安裝 magento-cloud CLI

`curl -sS https://accounts.magento.cloud/cli/installer | php`

## 2. 將 magento-cloud CLI 新增至 bash 設定檔。

```
~$ export PATH=$PATH:$HOME/.magento-cloud/bin
```

## 3. 手動載入 Bash 設定檔的變更

> 在 Ubuntu 中，預設雖然也是 bash，但不像 macOS，Ubuntu 通常使用的是 `.profile` 或 `.bashrc`，而不是 `.bash_profile`

```
~$ . ~/.bash_profile
bash: /home/lucas-chen/.bash_profile: No such file or directory
~$ . ~/.bashrc
```

## 4. 執行 `magento-cloud` 指令以啟動 CLI

，並在系統提示時輸入您的Cloud帳戶認證。

```
~$ magento-cloud
Welcome to Magento Cloud!

Loading projects...
Authentication is required.

Log in via a browser? [Y/n] Y

Opened URL: http://127.0.0.1:5000
Please use the browser to log in.

Help:
  Leave this command running during login.
  If you need to quit, use Ctrl+C.

Login information received. Verifying...
You are logged in.

Generating SSH certificate...
A new SSH certificate has been generated.
It will be automatically refreshed when necessary.
Do you want to create an SSH configuration file automatically? [Y/n] Y
Configuration file created successfully: /home/lucas-chen/.ssh/config

Username: <Username>
Email address: lucas.chen@zyxel.com.tw
Your projects are:
+---------------+----------------+--------------------+
| ID            | Title          | Region             |
+---------------+----------------+--------------------+
| <###########> | Zyxel Networks | ap-3.magento.cloud |
+---------------+----------------+--------------------+

Get a project by running: magento-cloud get [id]
List a project's environments by running: magento-cloud environments -p [id]

You are logged in as <Username> (lucas.chen@zyxel.com.tw)

To view all commands, run: magento-cloud list
```

## 5. 列出可用的指令

若出現指令清單，表示 bash 知道要去哪裡搜尋指令了
也可以使用 `magento-cloud --version` 確認所安裝的 `magento-cloud` 的版本

```
~$ magento-cloud list
Magento Cloud CLI 1.46.1
...
~$ magento-cloud --version
Magento Cloud CLI 1.46.1
```
## 6. 取得與切換專案

```
~$ cd /var/www/
/var/www$ magento-cloud project:get
Enter a number to choose a project:
  [0] Zyxel Networks (zne3kxtgzjvzi)
 > 0

Selected default environment: master (type: development)
Directory (default: zyxel-networks): 
Downloading project Zyxel Networks (zne3kxtgzjvzi)
  Cloning into '/var/www/zyxel-networks'...
  remote: counting objects: 43259, done.        
  Receiving objects: 100% (43259/43259), 40.72 MiB | 3.38 MiB/s, done.
  Resolving deltas: 100% (20517/20517), done.

The project Zyxel Networks (zne3kxtgzjvzi) was successfully downloaded to: zyxel-networks
```

成功取得專案並儲存在 `zyxel-networks` 目錄

```
/var/www$ ll
total 24
drwxr-xr-x  6 lucas-chen lucas-chen 4096 Jun 30 08:30 ./
drwxr-xr-x 15 root       root       4096 Jun 18 16:25 ../
drwxrwxr-x 11 lucas-chen lucas-chen 4096 Jun 20 14:53 Zyxel-Networks/
drwxr-xr-x  2 lucas-chen lucas-chen 4096 Jun 18 16:25 html/
drwxr-xr-x 12 www-data   www-data   4096 Jun 19 14:09 magento2/
drwxrwxr-x 11 lucas-chen lucas-chen 4096 Jun 30 08:30 zyxel-networks/  # 這裡！
```

檢視雲端分支

```
/var/www$ cd zyxel-networks/
/var/www/zyxel-networks$ magento-cloud environment:list
Your environments are: 
+--------------------------------+--------------------------+----------+-------------+
| ID                             | Title                    | Status   | Type        |
+--------------------------------+--------------------------+----------+-------------+
| master*                        | Master                   | Active   | development |
|   production                   | Production               | Active   | production  |
|     staging                    | Staging                  | Active   | staging     |
|       integration              | integration              | Inactive | development |
|       integration-M247         | integration-M247         | Active   | development |
|       integration-no-customize | integration-no-customize | Active   | development |
|       integration2             | Integration2             | Inactive | development |
+--------------------------------+--------------------------+----------+-------------+
* - Indicates the current environment

Check out a different environment by running magento-cloud checkout [id]
Branch a new environment by running magento-cloud environment:branch [new-name]
```

切換到指定環境

```
/var/www/zyxel-networks$ magento-cloud checkout integration-M247
  From git.ap-3.magento.cloud:zne3kxtgzjvzi
   * branch              integration-M247 -> FETCH_HEAD
Creating local branch integration-M247 based on upstream magento/integration-M247
  Switched to a new branch 'integration-M247'
  branch 'integration-M247' set up to track 'magento/integration-M247'.
```

把雲端環境(staging、production...)裡的資料庫匯出成 `.sql` 檔，並直接下載到本機

```
/var/www/zyxel-networks$ magento-cloud db:dump
Creating SQL dump file: /var/www/zyxel-networks/zne3kxtgzjvzi--integration-m247-a4m6d3q--mysql--main--dump.sql
mysqldump: Deprecated program name. It will be removed in a future release, use '/usr/bin/mariadb-dump' instead
Warning: the dump file is not excluded by Git
  You should probably exclude these files using .gitignore:
    *--dump.sql
```

## 7. 建立 auth.json(Composer 認證用)

先在 GitHub 建立 Fine-grained personal access tokens

首先點下右上角的帳號圖片並選擇 Settings，進入 Settings 後，在左下角選擇 Developer settings

![Developer settings](https://github.com/user-attachments/assets/c372f2ea-bbc2-4a26-8d26-794f37d1c923)

於左上角的 Personal access tokens 選擇 Fine-grained tokens，再點下 Generate new token

![Fine-grained tokens](https://github.com/user-attachments/assets/c0f63527-0142-4fe9-a04e-76f7d913b997)

輸入 Token name、Description、Expiration 等等設定內容

![New fine-grained personal access token](https://github.com/user-attachments/assets/a9d2bdc3-ff26-44cb-a615-c61f62860483)

完成後就可以看到新增的 Fine-grained personal access token

![Lucas fine-grained personal access token](https://github.com/user-attachments/assets/996bc5c5-6a37-4227-b5c8-00b9b59cc849)

參考 [Authentication file](https://developer.adobe.com/commerce/contributor/guides/install/clone-repository/#authentication-file) 所示的格式建立 auth.json

```
/var/www/zyxel-networks$ vi auth.json
/var/www/zyxel-networks$ cat auth.json
{
    "http-basic": {
        "repo.magento.com": {
            "username": "<public-key>",
            "password": "<private-key>"
        }
    },
    "github-oauth": {
        "github.com": "<personal-access-token>"  # Fine-grained personal access token
    }
}
```

## 8. 執行 composer update

該指令會安裝 composer.json 中指定或最新版本的必要套件，然後更新 composer.lock，並將所安裝的套件版本記錄到裡面

