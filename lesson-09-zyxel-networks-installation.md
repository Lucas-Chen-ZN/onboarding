# Lesson 09: Zyxel Networks Installation

為了要下載 Zyxel Networks project Git 儲存庫的 code，必須先進行一些驗證相關的設定

## 1. 產生 SSH Key

使用 `ssh-keygen` 指令(`-t`：金鑰加密演算法；`-b`：金鑰長度，`-C`：幫助辨識金鑰的備註)產生金鑰對，其中包含一把公鑰和一把私鑰

記得要輸入 passphrase 較安全，這個 passphrase 之後需要在顯示是否啟用私鑰的時候輸入

```
~$ ssh-keygen -t rsa -b 4096 -C "<email>"
Generating public/private rsa key pair.

Enter file in which to save the key (/home/lucas-chen/.ssh/id_rsa): Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /home/lucas-chen/.ssh/id_rsa
Your public key has been saved in /home/lucas-chen/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:<hashes> lucas.chen@zyxel.com.tw
The key's randomart image is:
+---[RSA 4096]----+
|        o        |
| o  . o. =       |
|o .  +.=*.o .    |
|+  .o.=o=O.+     |
|.+ ....*S+*..    |
|o     +=*.+..o   |
| .   . .oE .o .  |
|  . .   .    .   |
|   .             |
+----[SHA256]-----+
```

將剛剛產生的公鑰複製起來

```
~$ cat ~/.ssh/id_rsa.pub
ssh-rsa AAA...== <email>
```

接下來，登入 Adobe Commerce Console，點下網頁右上角的 User name 開啟選單，選擇 SSH Keys

![Adobe Commerce Console](https://github.com/user-attachments/assets/ca871c48-fb35-4150-a88c-5e6efae5f9ee)

點下 + Add public key

![SSH keys](https://github.com/user-attachments/assets/aab11ade-5b25-41bb-acfa-03fff3f15471)

輸入 Title 為這把公鑰取名字，然後將公鑰貼上

![Title and Key](https://github.com/user-attachments/assets/83940b4f-0f92-4c08-a32a-63cc6ee91ed2)

確認無誤後，點下 Save

![Save Key](https://github.com/user-attachments/assets/98b41ed6-78c3-4922-8656-e16e6f9487a1)

測試 SSH 是否能連上 Zyxel Networks project 儲存庫

```
~$ ssh -T <git-username>@git.ap-3.magento.cloud

Lucas Chen <lucas.chen@zyxel.com.tw>, welcome to Magento Cloud, you successfully connected, but there is no shell here.

Version: 28.25
```

2. 安裝 Git

執行以下指令安裝 Git

```
~$ sudo apt-get install git
```

檢視 Git 安裝的版本

```
~$ git version
git version 2.43.0
```

設定全域使用者資訊: `user.name` 和 `user.email`

```
~$ git config --global user.name "Lucas Chen"
~$ git config --global user.email "Lucas.Chen@zyxel.com.tw"
```

設定完成後，就可以將 Zyxel Networks project 儲存庫 clone 至 `/var/www`

```
~$ cd /var/www/
/var/www$ git clone zne3kxtgzjvzi@git.ap-3.magento.cloud:zne3kxtgzjvzi.git Zyxel-Networks
Cloning into 'Zyxel-Networks'...
remote: counting objects: 42905, done.
Receiving objects: 100% (42905/42905), 40.67 MiB | 6.55 MiB/s, done.
Resolving deltas: 100% (20303/20303), done.
```

3. 安裝 Zyxel Networks project

於 `/var/www/Zyxel-Networks` 中執行 `composer install`，然後在依照 Lesson 8 設定資料庫、目錄、Web server 等。

Lesson 08
