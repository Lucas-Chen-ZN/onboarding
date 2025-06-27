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
