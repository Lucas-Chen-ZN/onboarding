# Lesson 02: `git` Diary

## 課程連結

* [Git/GitHub/GitLab完全教程(包括Git底层原理)](https://www.udemy.com/course/git-basic/?couponCode=ST16MT230625C)

> 本筆記主要為我在修讀 Udemy 課程《Git/GitHub/GitLab完全教程(包括Git底层原理)》(講師：Peng Xiao) 時的個人學習紀錄整理  
> 所有原課程內容之著作權屬原講師與 Udemy，若想獲得系統化教學與完整教材，請點課程連結購買課程支持原作者  
> 若此筆記有任何侵權疑慮，請來信，我會立即修正或下架相關部分  

## 補充：Adobe Commerce Pro 專案工作流程

![environments](https://github.com/user-attachments/assets/32d09713-88a7-4bad-a285-61065996e3d0)
> 圖片參考來源：Adobe Commerce Documentation - Pro project workflow  
> 官方文件連結：[https://experienceleague.adobe.com/zh-hant/docs/commerce-on-cloud/user-guide/architecture/pro-develop-deploy-workflow](https://experienceleague.adobe.com/zh-hant/docs/commerce-on-cloud/user-guide/architecture/pro-develop-deploy-workflow)  

* Pro 專案的三個主要環境：
    * Production (正式)：用於上線與維護網站
    * Staging (預備)：整合所有服務後進行全面測試
    * Integration (整合)：開發與功能測試專用

![development workflow](https://github.com/user-attachments/assets/9c37de39-3393-4a87-a904-93401c1c7e16)
> 圖片參考來源：Adobe Commerce Documentation - Pro project workflow  
> 官方文件連結：[https://experienceleague.adobe.com/zh-hant/docs/commerce-on-cloud/user-guide/architecture/pro-develop-deploy-workflow](https://experienceleague.adobe.com/zh-hant/docs/commerce-on-cloud/user-guide/architecture/pro-develop-deploy-workflow)  

> 假設：以 `origin` 為遠端儲存庫的參照名稱，目前正在開發一個名稱為 `moon-landing` 的新功能，而 Active Branch 的遠端分支為 `active`，Integration 為 `integration`，以此類推。

### I. 在本機先執行 Pull

#### i. 切換到最新的目標分支 Active Branch

```
git checkout active
```

#### ii. Pull 最新的遠端內容

```
git pull --rebase origin
```

* `--rebase` 表示先更新，再把自己的 commit 依序黏貼到最新的點之後

### II. 開發完成後 Push 到遠端

#### i. 從 Active Branch 切出新功能分支

```
git checkout -b moon-landing
```

#### ii. Add 然後 Commit

```
git add .
git commit -m "Apollo 11 is ready!"
```

#### iii. 首次 Push

```
git push -u origin moon-landing
```

* `-u` 表示建立關聯

### III. 將功能分支 Merge 回 Active Branch

```
# 確保 Active 分支是最新的狀態
git checkout active
git pull --rebase origin active

# 合併功能分支
git merge --no-ff moon-landing

# 推送回遠端
git push origin active
```

* `--no-ff` 表示保留 Merge commit，方便日後回溯
* 若有衝突必須先解決掉再 `git add <file>` 並 `git commit -m <message>` 後再繼續

### IV. Active Branch → Integration → ...

```
git checkout integration
git pull --rebase origin integration
git merge --no-ff active
git push origin integration
```

* 重複上述步驟，依序將 integration → staging → production → global-master，每一層都先 `pull` 再 `merge` 再 `push`。
* 例如：
    ```
    git checkout staging
    git pull --rebase origin staging
    git merge --no-ff integration
    git push origin staging
    ```

## Git Bash / Windows PowerShell 常用基本指令

1. 檢視目前目錄(directory)。

    ```
    $ pwd
    ```

2. 檢視目錄下所有內容。

    ```
    $ ls
    ```

    * 也可以使用 `ls -l` 顯示詳細資訊(權限、大小、修改時間等)：`d` 開頭的是目錄，`-` 開頭的是檔案。

        ```
        $ ls -l
        ```

3. 切換目錄。

    ```
    $ cd tmp
    $ cd .. # 返回上一層目錄
    ```

4. 建立檔案和檢視內容。

    ```
    $ echo "test" > test.txt
    $ more test.txt
    test
    $ cat test.txt
    test
    ```

## Windows PowerShell 獨有指令

1. 以樹狀結構顯示目錄和檔案。

    ```
    $ tree /F
    ```

2. 顯示隱藏目錄和檔案。
    
    ```
    $ dir -Force
    ```

## Git 環境設定與基本操作

1. 設定「全域」使用者資訊。

    ```
    $ git config --global user.name "name"
    $ git config --global user.email "e-mail"
    ```

    * 請檢視在目前使用者目錄下是否有生成一個 `.gitconfig` 的檔案。

    ```
    git config -l
    ```

2. 初始化 Git 儲存庫(Repository)。

    ```
    $ mkdir git-demo
    $ cd git-demo/
    $ git init
    $ watch -n 1 -d
    ```

3. (選擇性)移除 Git Hooks 範例。

    ```
    $ cd .git/hooks/
    $ rm -rf *.sample
    ```
    
    > 和許多其他版本控制系統(Version Control Systems)一樣，Git 也能在特定動作發生時觸發自訂腳本(Hooks)。這些 Hooks 分為兩類：client-side 與 server-side。Client-side hooks 會在如提交(commit)與合併(merge)等操作時執行；而 server-side hooks 則在如接收推送（push）等操作時觸發。
    
4. 檢視本機儲存庫設定。

    ```
    $ cat .git/config # 「區域」配置會優先於「全域」配置
    [core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
    ```

5. 修改預設分支(Branch)名稱(預設為 `main`，可以改成 `master`)。
   
   ```
    git config --global init.defaultBranch master
    ```

6. 建立檔案並檢視 Git 狀態。
    
    ```
    $ echo "Hello, World!" > HelloWorld.txt
    $ cat HelloWorld.txt
    Hello, World!
    ```
    
    ```
    $ git status
    On branch master
    
    No commits yet
    
    Untracked files:
      (use "git add <file>..." to include in what will be committed)
            HelloWorld.txt
    
    nothing added to commit but untracked files present (use "git add" to track)
    ```

7. 透過 `git show-ref` 列出本機儲存庫所有或特定 ref 和其對應的 commit 物件。

    ```
    $ git show-ref
    9f669f3f7fdaeafa78bb12f95934ee8556ac145b refs/heads/checkout
    6416b7bf6fabad0c57c09bda8904514dd7acd102 refs/heads/dev
    cc8dbb88f918fc335ed0456892d7057d62544cf7 refs/heads/main
    cc8dbb88f918fc335ed0456892d7057d62544cf7 refs/remotes/origin/HEAD
    6416b7bf6fabad0c57c09bda8904514dd7acd102 refs/remotes/origin/dev
    cc8dbb88f918fc335ed0456892d7057d62544cf7 refs/remotes/origin/main
    ```
    
    ```
    $ git show-ref dev
    6416b7bf6fabad0c57c09bda8904514dd7acd102 refs/heads/dev
    6416b7bf6fabad0c57c09bda8904514dd7acd102 refs/remotes/origin/dev
    ```
    
8. 

## Git 物件(Object)操作

1. `git add` 將檔案加到 Staging Area。
    
    * `git add` 會將修改後的檔案從 Working directory 加入到 Staging area (暫存區)，也就是告訴 Git「這個/些檔案應該被納入下一次的 `commit`」。

    ```
    PS D:\git-demo> tree .\.git\
    列出磁碟區 Data 的資料夾 PATH
    磁碟區序號為 42E8-56EA
    D:\GIT-DEMO\.GIT
    ├─hooks
    ├─info
    ├─objects
    │  ├─info
    │  └─pack
    └─refs
        ├─heads
        └─tags
    ```
    
    ```
    $ git add HelloWorld.txt
    warning: in the working copy of 'HelloWorld.txt', LF will be replaced by CRLF the next time Git touches it
    ```
    
    ```
    PS D:\git-demo> tree .\.git
    列出磁碟區 Data 的資料夾 PATH
    磁碟區序號為 42E8-56EA
    D:\GIT-DEMO\.GIT
    ├─hooks
    ├─info
    ├─objects
    │  ├─8a # 注意這裡！
    │  ├─info
    │  └─pack
    └─refs
        ├─heads
        └─tags
    ```
    
    ```
    $ git status
    On branch master

    No commits yet

    Changes to be committed:
      (use "git rm --cached <file>..." to unstage)
            new file:   HelloWorld.txt
    ```
    
    ```
    PS D:\git-demo> ls .\.git\objects\8a


    目錄: D:\git-demo\.git\objects\8a


    Mode                 LastWriteTime         Length Name
    ----                 -------------         ------ ----
    -ar---       2025/4/27  上午 09:18             30 b686eafeb1f44702738c8b0f24f2567c36da6d
    ```
    
    > 如果有兩個名稱不同但內容完全相同的檔案，Git 只會建立並共用同一個物件(object)，也就是用相同的 `blob` 來儲存檔案的類型與內容。
    > P.S.雜湊值(SHA-1)是透過 `blob (file size)\0(file content)\n` 格式計算而來。
    
    ```
    $ git cat-file -t 8ab686
    blob # 檔案類型
    $ git cat-file -p 8ab686
    Hello, World! # 檔案內容
    $ git cat-file -s 8ab686
    13 # 檔案大小
    ```
    
2. Homework

    • Initialize one empty git repository.
    • Create one file called `foo.txt`, add text "This is foo!" into this file.
    • Create one folder called bar, create a file called `bar.txt` inside of bar folder, and add text "This is bar" into this file.
    • Run command `git add *` to add all files and folder into git database
    • Use command `git cat-file` to check object files in `.git/objects`

    ```
    $ mkdir git-test
    $ cd git-demo/
    $ git init
    ```
    ```
    $ echo "This is foo!" > foo.txt
    $ mkdir bar
    $ cd bar/
    $ echo "This is bar!" > bar.txt
    ```
    ```
    $ git add *
    warning: in the working copy of 'bar.txt', LF will be replaced by CRLF the next time Git touches it
    warning: in the working copy of 'foo.txt', LF will be replaced by CRLF the next time Git touches it
    ```
    ```
    $ git ls-files -s # 列出目前在 Staging Area 中的檔案，包含每個檔案的權限(mode bits)、object name、stage number 和檔案名稱)
    100644 3dd47481d45a67a796a006e2cfe63011df2520a7 0       bar/bar.txt
    100644 cca4a4d4fc52054125f26097f7e1eba3481542ca 0       foo.txt
    
    $ git cat-file -p cca4a
    This is foo!
    $ git cat-file -p 3dd474
    This is bar!
    ```

3. modified 狀態

    ```
    $ git ls-files -s
    100644 e2129701f1a4d54dc44f03c93bca0a2aec7c5449 0       file1.txt
    100644 6c493ff740f9380390d5c9ddef4af18697ac9375 0       file2.txt
    ```
    
    > 此時 `file1.txt` 和 `file2.txt` 都已加入暫存區(stage 0)，準備提交。
    
    ```
    $ vim file1.txt
    $ git status
    On branch master

    No commits yet

    Changes to be committed:
      (use "git rm --cached <file>..." to unstage)
            new file:   file1.txt
            new file:   file2.txt

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
            modified:   file1.txt
    ```
    
    > 此時 `file1.txt` 的內容已被修改，但尚未重新加入暫存區，因此出現在「Changes not staged for commit」。
    
    ```
    $ git add file1.txt
    warning: in the working copy of 'file1.txt', LF will be replaced by CRLF the next time Git touches it
    
    $ git ls-files -s
    100644 135e1a54c3d0ac79e010741b164781101ed484bf 0       file1.txt
    100644 6c493ff740f9380390d5c9ddef4af18697ac9375 0       file2.txt
    ```
    
    > 執行 `git add` 後，Git 會根據修改後的內容重新產生一個新的 blob 物件(hash 值也會改變)。

4. 提交 commit
    
    > 執行 commit 後，Git 建立了一個新的 commit object `007e55c`。
    > 這個 object 會：
    > * 指向一個 tree object(紀錄目錄與檔案的狀態)
    > * 儲存作者、時間和訊息等資訊
    
    ```
    $ git commit -m "1st commit"
    [master (root-commit) 007e55c] 1st commit
     2 files changed, 2 insertions(+)
     create mode 100644 file1.txt
     create mode 100644 file2.txt
    ```
    
    > 檢視 commit object 的內容時，會發現有一個 tree object `d54714`。

    ```
    $ git cat-file -t 007e55c
    commit
    $ git cat-file -p 007e55c
    tree d54714132688218ada383d7bbc8032e430dcfaa9
    author Lucas Chen <lc20240328@gmail.com> 1745808340 +0800
    committer Lucas Chen <lc20240328@gmail.com> 1745808340 +0800

    1st commit
    ```
    > 檢視 tree object 的內容時，tree object 會列出目錄下的檔案與其對應的 blob (實際內容)。
    
    ```
    $ git cat-file -t d54714
    tree

    $ git cat-file -p d54714
    100644 blob 135e1a54c3d0ac79e010741b164781101ed484bf    file1.txt
    100644 blob 6c493ff740f9380390d5c9ddef4af18697ac9375    file2.txt
    ```
    
5. HEAD 的指向
    
    > HEAD 代表目前所在的分支位置，而該分支目前指向的是 `007e55c` 這個 commit。
    
    ```
    $ cat .git/HEAD # 指向目前操作的分支
    ref: refs/heads/master
    $ cat .git/refs/heads/master
    007e55cce52c9b4c4bd2119778c16ff98bc4095b # 指向 master 分支的 commit object
    ```
    
    > 修改 file2.txt 後重新加入暫存區，Git 會建立一個新的 blob (因內容改變，hash 值不同)。
    
    ```
    $ vim file2.txt
    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
            modified:   file2.txt

    no changes added to commit (use "git add" and/or "git commit -a")
    
    ```
    
    ```
    $ git add file2.txt
    warning: in the working copy of 'file2.txt', LF will be replaced by CRLF the next time Git touches it
    
    $ git ls-files -s
    100644 135e1a54c3d0ac79e010741b164781101ed484bf 0       file1.txt
    100644 7f1dfd6c2e5fbbd5fdac553945b9ab0c0492b74e 0       file2.txt
    ```
    
    > 第二次 commit 後會建立新的 commit object，並指向新的 tree object。
    
    ```
    $ git commit -m "2nd commit"
    [master 8b78f1b] 2nd commit
     1 file changed, 1 insertion(+), 1 deletion(-)
        $ git cat-file -p a7d8b5a
        tree 4fb09d8201963a598695b0bca722bc20e62b12e4
        parent c360ffb5724da9280212e7d84a24f932b655218f # 多了一個 parent，它會指向(繼承)上一個 commit object，也就是 root commit
        author Lucas Chen <lc20240328@gmail.com> 1745730340 +0800
        committer Lucas Chen <lc20240328@gmail.com> 1745730340 +0800

    2nd commit
    ```

6. Unchecked 狀態。
    
    > 執行 `git rm --cached test.txt` 會只從 Git 的暫存區移除 `test.txt`，但保留工作目錄中的實體檔案。
    
    ```
    $ git rm --cached test.txt
    rm 'test.txt'
    ```
    
    > 執行後的效果：
    > * `test.txt` 不再被 Git 追蹤(變為 Untracked 狀態)。
    > * 實體檔案仍然保留在工作目錄中。

7. Restore 取消暫存或還原檔案內容。
    
    > 將檔案從暫存區(staging area)移除，變成「已修改但未暫存」的狀態，不會改變已修改的檔案內容。
    
    ```
    $ git restore --staged file1.txt
    ```
    
    > 還原檔案內容(捨棄工作目錄的修改)，也就是將工作目錄中的檔案內容，還原為最後一次 commit 的狀態。修改會被覆蓋掉，無法復原！
    
    ```
    $ git restore file1.txt
    ```
    
## Git 日誌(Log/Reflog)

1. 檢視 commit 記錄
    
    ```
    $ git log
    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (HEAD -> master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

    3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

    2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

    1st commit
    ```
    
    > 讓每一筆 commit 顯示在單一列
    
    ```
    $ git log --oneline
    fde90e5 (HEAD -> master) 3rd commit
    8b78f1b 2nd commit
    007e55c 1st commit
    ```
    
    ```
    $ git log -2
    $ git log --oneline -2
    $ git log --after="2025-04-01"
    $ git log --before="2025-04-01"
    ```
    
    > 統計並分類 commit 紀錄，將 Git 紀錄依據作者分類，列出每位作者的 commit 數量與訊息摘要。
    
    ```
    $ git shortlog
    Lucas Chen (3):
      1st commit
      2nd commit
      3rd commit
    ```
    
    > 使用 `--stat` 參數，會列出每次 commit 的基本資訊，並統計每個檔案的異動情形(增加／刪除幾行)。
    
    ```
    $ git log --stat
    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (HEAD -> master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

    3rd commit

     folder1/file3.txt | 1 +
     1 file changed, 1 insertion(+)

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

    2nd commit

     file2.txt | 2 +-
     1 file changed, 1 insertion(+), 1 deletion(-)

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

    1st commit
    ```
    
2. HEAD

    > Related with **current active branch**.
    > Always point to the **latest commit**.

    ```
    $ cat .git/HEAD
    ref: refs/heads/master
    
    $ cat .git/refs/heads/master
    fde90e58a8a0d233e8060efae6685fdd6632bbff
    
    $ git cat-file -t fde90e
    commit
    
    $ git log
    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (HEAD -> master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit
    ```
    
## Branch (分支)管理

1. 列出本機端分支。

    ```
    $ git branch
    * master
    ```

2. 建立分支。

    ```
    $ git branch dev
    ```
    
    ```
    $ git branch
      dev
    * master
    ```
    
    > HEAD 目前指向 master 分支，而 master 與 dev 這兩個分支剛好都指向相同的 commit `fde90e5`。
    
    ```
    $ git log
    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (HEAD -> master, dev)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit

    $ cat .git/refs/heads/dev
    fde90e58a8a0d233e8060efae6685fdd6632bbff

    $ cat .git/refs/heads/master
    fde90e58a8a0d233e8060efae6685fdd6632bbff
    ```

3. 切換分支

    ```
    $ git checkout dev
    Switched to branch 'dev'
    ```

    ```
    $ git branch
    * dev
      master

    $ cat .git/HEAD
    ref: refs/heads/dev

    $ git log
    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (HEAD -> dev, master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit
    ```
    
4. 刪除分支
    
* 🚫 錯誤範例：目前仍在被刪除的分支上
    
    > 無法刪除目前正在使用的分支，必須先切換到其他分支(如 master)再刪除。
    
    ```
    $ git branch -D dev
    error: cannot delete branch 'dev' used by worktree at 'D:/Programming/Git/git-demo'
    ```

* ✅ 正確流程：切換後再刪除。

    > 使用 `-d` 只允許刪除已合併的分支。
    
    ```
    $ git checkout master
    Switched to branch 'master'

    $ git branch -d dev
    error: the branch 'dev' is not fully merged
    hint: If you are sure you want to delete it, run 'git branch -D dev'
    hint: Disable this message with "git config set advice.forceDeleteBranch false"
    ```
    
    > 若尚未合併，就需要使用 `-D` 強制刪除。
    
    ```
    $ git branch -D dev
    Deleted branch dev (was 7358b8f).
    ```

    > Git 刪除 `dev` 分支時，只是移除 `\refS\heads` 中指向 commit 的 dev 檔案和 `\logs\refs\heads` 中的 dev log 檔案刪除，不會立即刪除 object。

    ```
    $ git cat-file -t 7358b8f
    commit

    $ git cat-file -p 7358b8f
    tree 01ca37c988795d2ca053dba306e6bb5a865ad433
    parent fde90e58a8a0d233e8060efae6685fdd6632bbff
    author Lucas Chen <lc20240328@gmail.com> 1745902719 +0800
    committer Lucas Chen <lc20240328@gmail.com> 1745902719 +0800

    1st commit from dev branch

    $ git cat-file -p 01ca37
    100644 blob 38f8e886e1a6d733aa9bfa7282568f83c133ecd6    dev.txt
    100644 blob 135e1a54c3d0ac79e010741b164781101ed484bf    file1.txt
    100644 blob 7f1dfd6c2e5fbbd5fdac553945b9ab0c0492b74e    file2.txt
    040000 tree b4540ce0bad63a0f40de1619b97a4589a9259496    folder1
    ```

5. 在新的 `dev` 分支執行 commit

    ```
    $ echo "dev" > dev.txt
    $ git add dev.txt
    warning: in the working copy of 'dev.txt', LF will be replaced by CRLF the next time Git touches it
    ```
    
    ```
    $ git commit -m "1st commit from dev branch"
    [dev 7358b8f] 1st commit from dev branch
     1 file changed, 1 insertion(+)
     create mode 100644 dev.txt

    $ git log
    commit 7358b8f6141f51612434ebb7dd8832e490e730ba (HEAD -> dev)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Tue Apr 29 12:58:39 2025 +0800

        1st commit from dev branch

    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit

    ```
    
6. `checkout` 的進階操作。

    > 將 HEAD 切換到特定 commit，而不是指向分支。

    ```
    $ git log
    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (HEAD -> master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit

    $ cat .git/HEAD
    ref: refs/heads/master
    
    $ cat .git/refs/heads/master
    fde90e58a8a0d233e8060efae6685fdd6632bbff
    ```
    
    ```
    $ git checkout 007e55
    Note: switching to '007e55'.

    You are in 'detached HEAD' state. You can look around, make experimental
    changes and commit them, and you can discard any commits you make in this
    state without impacting any branches by switching back to a branch.

    If you want to create a new branch to retain commits you create, you may
    do so (now or later) by using -c with the switch command. Example:

      git switch -c <new-branch-name>

    Or undo this operation with:

      git switch -

    Turn off this advice by setting config variable advice.detachedHead to false

    HEAD is now at 007e55c 1st commit
    
    yungh@DESKTOP-LUCAS MINGW64 /d/Programming/Git/git-demo ((007e55c...))
    $ 
    ```
    
    > 進入 Detached HEAD 狀態時，HEAD 直接指向一個特定的 commit，而不是某個分支。此時雖然仍可自由修改檔案與提交(commit)，但這些新的 commit 不會被任何分支追蹤。
    > 換句話說，現在只是「暫時瀏覽」這個 commit 的狀態，並不是真的在某個分支上作業。
如果你在這個狀態下做出新的 commit，一旦切換回其他分支，那些 commit 就會變得孤立、難以尋找。
    
    > 💡 解法：立刻建立一個新分支來保存這些變更，讓這段 commit 紀錄有「依靠」，不會遺失。
    
    ```
    $ ls
    file1.txt  file2.txt

    $ git log
    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b (HEAD)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit
    ```
    
    > 先建立並切換到新的 `tmp` 分支以保存 Detached 狀態的 commit 紀錄。
    
    ```
    $ git checkout -b tmp
    Switched to a new branch 'tmp'

    $ git log
    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b (HEAD -> tmp)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit
    ```
    
    > 想還原之前的分支操作時，可以先使用 `git reflog` 追蹤 HEAD 或分支的紀錄，找回任何歷史上的 commit 狀態。

    ```
    $ git reflog
    fde90e5 (HEAD -> master) HEAD@{0}: checkout: moving from tmp to master
    007e55c (tmp) HEAD@{1}: checkout: moving from 007e55cce52c9b4c4bd2119778c16ff98bc4095b to tmp
    007e55c (tmp) HEAD@{2}: checkout: moving from master to 007e55
    fde90e5 (HEAD -> master) HEAD@{3}: checkout: moving from dev to master
    7358b8f HEAD@{4}: checkout: moving from master to dev
    fde90e5 (HEAD -> master) HEAD@{5}: checkout: moving from dev to master
    7358b8f HEAD@{6}: commit: 1st commit from dev branch
    fde90e5 (HEAD -> master) HEAD@{7}: checkout: moving from master to dev
    fde90e5 (HEAD -> master) HEAD@{8}: commit: 3rd commit
    8b78f1b HEAD@{9}: commit: 2nd commit
    007e55c (tmp) HEAD@{10}: commit (initial): 1st commit
    ```
    
    > 還原回特定 commit 並建立分支保存它。
    
    ```
    $ git checkout 7358b8f
    Note: switching to '7358b8f'.

    You are in 'detached HEAD' state. You can look around, make experimental
    changes and commit them, and you can discard any commits you make in this
    state without impacting any branches by switching back to a branch.

    If you want to create a new branch to retain commits you create, you may
    do so (now or later) by using -c with the switch command. Example:

      git switch -c <new-branch-name>

    Or undo this operation with:

      git switch -

    Turn off this advice by setting config variable advice.detachedHead to false

    HEAD is now at 7358b8f 1st commit from dev branch

    $ ls
    dev.txt  file1.txt  file2.txt  folder1/
    ```
    
    ```
    $ git checkout -b dev
    Switched to a new branch 'dev'
    ```
    ```
    $ git log
    commit 7358b8f6141f51612434ebb7dd8832e490e730ba (HEAD -> dev)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Tue Apr 29 12:58:39 2025 +0800

        1st commit from dev branch

    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b (tmp)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit
    ```

7. `checkout` 進階用法。
    
    > 還原檔案到最近一次加入暫存區的狀態。
    
    ```
    $ vim file1.txt

    $ git status
    On branch master
    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git restore <file>..." to discard changes in working directory)
            modified:   file1.txt

    no changes added to commit (use "git add" and/or "git commit -a")
    ```
    
    🧹 使用 `restore` 或 `checkout` 還原內容
    
    ```
    $ git restore file1.txt
    ```
    
    > 或
    
    ```
    $ git checkout file1.txt
    Updated 1 path from the index
    ```
    
    > 將暫存區的 `file1.txt` 恢復回工作目錄，也就是讓 `file1.txt` 回到最近一次 `add` 或 `commit` 的狀態。
    
    ```
    $ git status
    On branch master
    nothing to commit, working tree clean
    ```

8. 修改分支的名稱。

    > 將 tmp 分支的名稱修改為 test 分支。

    ```
    $ git branch -m tmp test
    ```
    
9. 分支合併(Merge) - Fast Forward

    > 從 master 建立 bugfix 分支。

    ```
    $ git checkout -b bugfix
    Switched to a new branch 'bugfix'
    ```

    > 此時 bugfix 和 master 分支是指向同一個 commit `fde90e5`。

    ```
    $ git log
    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (HEAD -> bugfix, master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit
    ```

    > 在 bugfix 分支新增一個 commit。

    ```
    $ echo "feile4" > file4.txt

    $ git add file4.txt
    warning: in the working copy of 'file4.txt', LF will be replaced by CRLF the next time Git touches it

    $ git commit -m "4th commit"
    [bugfix 3b9019f] 4th commit
     1 file changed, 1 insertion(+)
     create mode 100644 file4.txt
    ```

    > 現在 bugfix 前進了一步，但 master 沒有動：

    ```
    $ git log
    commit 3b9019f987517dbd2e5257d91180eb49c8bf454c (HEAD -> bugfix)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 12:53:40 2025 +0800

        4th commit

    commit fde90e58a8a0d233e8060efae6685fdd6632bbff (master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit
    ```

    > 回到 master 分支並執行合併，Git 會單純地將 master 的指標「快轉」到 bugfix 的位置，沒有產生新的 merge commit。

    ```
    $ git checkout master
    Switched to branch 'master'

    (master)
    $ git merge bugfix
    Updating fde90e5..3b9019f
    Fast-forward
     file4.txt | 1 +
     1 file changed, 1 insertion(+)
     create mode 100644 file4.txt
    ```

    > 在 Fast Forward 合併後，可以看到 Git 為了保留「合併前的位置」而自動紀錄的參考點（Original HEAD），也就是合併前、原本 master 所指向的 commit。

    ```
    $ cat .git/HEAD
    ref: refs/heads/master

    $ cat .git/refs/heads/master
    3b9019f987517dbd2e5257d91180eb49c8bf454c

    $ cat .git/ORIG_HEAD
    fde90e58a8a0d233e8060efae6685fdd6632bbff
    ```

    > 回到合併前的狀態。

    ```
    $ git reset ORIG_HEAD / git reset --mixed ORIG_HEAD
    ```
    
    > 之前合併進來的 `file4.txt`，還會存在於工作目錄中，但它會變成 Untracked 狀態。

10. 分支合併(Merge) - Three-way merge

    > master 分支：

    ```
    $ git log
    commit 087f04fdf9f8fd3f3b9955c7f76ad093ac8c4ed8 (HEAD -> master)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 13:31:50 2025 +0800

        5th commit

    commit fde90e58a8a0d233e8060efae6685fdd6632bbff
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit
    ```

    > bugfix 分支：

    ```
    $ git checkout bugfix
    Switched to branch 'bugfix'

    $ git log
    commit 3b9019f987517dbd2e5257d91180eb49c8bf454c (HEAD -> bugfix)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 12:53:40 2025 +0800

        4th commit

    commit fde90e58a8a0d233e8060efae6685fdd6632bbff
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit

    ```

    > 根據目前的 git log 結果，master 和 bugfix 分支已經分叉了，各自有獨立的最新 commit：
    > * master：最後一筆是 5th commit（087f04f）
    > * bugfix：最後一筆是 4th commit（3b9019f）
    > * 兩者的共同祖先是 3rd commit（fde90e5）

    ```
                   007e55c ← 1st commit
                      ↓
                   8b78f1b ← 2nd commit
                      ↓
                   fde90e5 ← 3rd commit
                   ↓     ↓
                   ↓     ↓
    3b9019f (bugfix)     087f04f (master)
           ↓                    ↓
        4th commit           5th commit
    ```

    > 進行 3-Way 合併後，會產生一個 merge commit。
    
    ```
    $ git merge bugfix
    hint: Waiting for your editor to close the file...
    ```
    
    ```
    Merge branch 'bugfix'
    # Please enter a commit message to explain why this merge is necessary,
    # especially if it merges an updated upstream into a topic branch.
    #
    # Lines starting with '#' will be ignored, and an empty message aborts
    # the commit.
    ```
    
    ```
    $ git merge bugfix
    Merge made by the 'ort' strategy.
     file4.txt | 1 +
     1 file changed, 1 insertion(+)
     create mode 100644 file4.txt
    ```
    
    > `c65e1d` 是新的 merge commit，
    > * 有兩個 parent：
    >   * `087f04f` 是 master 的 ORIG_HEAD（5th commit)
    >   * `3b9019f` 是 bugfix 的 HEAD（4th commit）
    
    ```
    $ git log
    commit c65e1d37112d8dc8d033359320a5fdad08dcd503 (HEAD -> master)
    Merge: 087f04f 3b9019f
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 14:54:45 2025 +0800

        Merge branch 'bugfix'

    commit 087f04fdf9f8fd3f3b9955c7f76ad093ac8c4ed8
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 13:31:50 2025 +0800

        5th commit

    commit 3b9019f987517dbd2e5257d91180eb49c8bf454c (bugfix)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 12:53:40 2025 +0800

        4th commit

    commit fde90e58a8a0d233e8060efae6685fdd6632bbff
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:29:35 2025 +0800

        3rd commit

    commit 8b78f1b8f9b0b0ce4e83f78b386ed9d46d94eedf
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 11:09:52 2025 +0800

        2nd commit

    commit 007e55cce52c9b4c4bd2119778c16ff98bc4095b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Mon Apr 28 10:45:40 2025 +0800

        1st commit
    ```

    ```
    $ git cat-file -p c65e1d
    tree 3794c2b99435cfe0cd57b3118f313b81ff9439a1
    parent 087f04fdf9f8fd3f3b9955c7f76ad093ac8c4ed8
    parent 3b9019f987517dbd2e5257d91180eb49c8bf454c
    author Lucas Chen <lc20240328@gmail.com> 1745996085 +0800
    committer Lucas Chen <lc20240328@gmail.com> 1745996085 +0800

    Merge branch 'bugfix'
    ```

    ```
                   007e55c ← 1st commit
                      ↓
                   8b78f1b ← 2nd commit
                      ↓
                   fde90e5 ← 3rd commit
                   ↓     ↓
                   ↓     ↓
    3b9019f (bugfix)     087f04f (master)
           ↓                    ↓
        4th commit           5th commit
                      ↓
                   c65e1d ← Merge branch 'bugfix'
    ```
    
    ```
    $ git log --oneline --graph --all
    *   c65e1d3 (HEAD -> master) Merge branch 'bugfix'
    |\
    | * 3b9019f (bugfix) 4th commit
    * | 087f04f 5th commit
    |/
    | * 7358b8f (dev) 1st commit from dev branch
    |/
    * fde90e5 3rd commit
    * 8b78f1b 2nd commit
    * 007e55c 1st commit
    ```

11. 分支合併(Merge) - Three-way merge with Conflic

> 1. 在目錄中初始化了一個 Git 儲存庫。
> 2. 建立 `test.txt` 並提交第一版(1st commit)。
> 3. 建立新分支 bugfix，並在該分支中修改 `test.txt` 後提交(2nd commit)。
> 回到 master 分支，進一步修改 `test.txt` 並提交(3rd commit)。

```
$ git init
Initialized empty Git repository in D:/Programming/Git/git-demo-3/.git/

(master)
$ echo "test" > test.txt

(master)
$ git add test.txt
warning: in the working copy of 'test.txt', LF will be replaced by CRLF the next time Git touches it

(master)
$ git commit -m "1st commit"
[master (root-commit) 742cc92] 1st commit
 1 file changed, 1 insertion(+)
 create mode 100644 test.txt

(master)
$ git checkout -b bugfix
Switched to a new branch 'bugfix'

(bugfix)
$ echo "adding from bugfix" >> test.txt

(bugfix)
$ git add test.txt
warning: in the working copy of 'test.txt', LF will be replaced by CRLF the next time Git touches it

(bugfix)
$ git commit -m "2nd commit"
[bugfix a5c3c6a] 2nd commit
 1 file changed, 1 insertion(+)

(bugfix)
$ git checkout master
Switched to branch 'master'

(master)
$ echo "adding from master" >> test.txt

(master)
$ git add test.txt
warning: in the working copy of 'test.txt', LF will be replaced by CRLF the next time Git touches it

(master)
$ git commit -m "3rd commit"
[master 63430c6] 3rd commit
 1 file changed, 1 insertion(+)

(master)
$ git log
commit 63430c67265b926ec84f77aca4f22d8d724b8714 (HEAD -> master)
Author: Lucas Chen <lc20240328@gmail.com>
Date:   Wed Apr 30 15:54:38 2025 +0800

    3rd commit

commit 742cc92f51bf471f1262c78451f0b2fef25f22d8
Author: Lucas Chen <lc20240328@gmail.com>
Date:   Wed Apr 30 15:52:16 2025 +0800

    1st commit
    
$ git ls-files -s
100644 f188f5379e6b25e76dce72c0f436972659134bc5 0       test.txt
```

> Git 嘗試自動合併 bugfix 分支到 master，但因為 `test.txt` 在兩邊分支都有不同的修改，所以發生了合併衝突(merge conflict)。

```
$ git merge bugfix
Auto-merging test.txt
CONFLICT (content): Merge conflict in test.txt
Automatic merge failed; fix conflicts and then commit the result.
```

```
$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Unmerged paths:
  (use "git add <file>..." to mark resolution)
        both modified:   test.txt

no changes added to commit (use "git add" and/or "git commit -a")
```

> 一旦 Git 發現 `test.txt` 中有衝突，Git 會將衝突的地方自動標記出來。開啟 `test.txt`，就會看到下列的內容：

```
$ cat test.txt
test
<<<<<<< HEAD
adding from master
=======
adding from bugfix
>>>>>>> bugfix
```

> 由於目前合併衝突尚未解決，Git 會將 `test.txt` 的三個版本放入暫存區中，用來協助解決衝突。
> 📘 這三個版本的意義：

| Stage | 說明                                  |
|:-----:|:------------------------------------- |
|   0   | Normal                                |
|   1   | Base (共同 ancestor)                  |
|   2   | Ours (HEAD，也就是 master 分支的版本) |
|   3   | Theirs (bugfix 分支的版本)            |

```
$ git ls-files -s
100644 9daeafb9864cf43055ae93beb0afd6c7d144bfa4 1       test.txt
100644 f188f5379e6b25e76dce72c0f436972659134bc5 2       test.txt
100644 e371aa5d62c212a64e26bbd93582a47f24e0fcb8 3       test.txt
```

```
$ git cat-file -p 9daeaf
test

(master|MERGING)
$ git cat-file -p f188f5
test
adding from master

(master|MERGING)
$ git cat-file -p e371aa
test
adding from bugfix
```

> 決定要保留哪些內容、將 `test.txt` 的標記移除並儲存該檔案，最後執行 `add` 和 `commit`。

```
$ git add test.txt

(master|MERGING)
$ git status
On branch master
All conflicts fixed but you are still merging.
  (use "git commit" to conclude merge)

Changes to be committed:
        modified:   test.txt

(master|MERGING)
$ git ls-files -s
100644 0f99f3ddcba9174137151fde9064df573ddd3a9a 0       test.txt
```

```
$ git commit
```

> commit 預設訊息為：

```
Merge branch 'bugfix'

# Conflicts:
#	test.txt
#
# It looks like you may be committing a merge.
# If this is not correct, please run
#	git update-ref -d MERGE_HEAD
# and try again.


# Please enter the commit message for your changes. Lines starting
# with '#' will be ignored, and an empty message aborts the commit.
#
# On branch master
# All conflicts fixed but you are still merging.
#
# Changes to be committed:
#	modified:   test.txt
#
```

```
$ git commit
[master 744577b] Merge branch 'bugfix'
```

```
(master)
$ git log
commit 744577b82413bcbbaf9967c0a984261d48957aff (HEAD -> master)
Merge: 63430c6 a5c3c6a
Author: Lucas Chen <lc20240328@gmail.com>
Date:   Wed Apr 30 16:48:25 2025 +0800

    Merge branch 'bugfix'

commit 63430c67265b926ec84f77aca4f22d8d724b8714
Author: Lucas Chen <lc20240328@gmail.com>
Date:   Wed Apr 30 15:54:38 2025 +0800

    3rd commit

commit a5c3c6afe439f57873ce46ad3a07f11cdf4f1c7b (bugfix)
Author: Lucas Chen <lc20240328@gmail.com>
Date:   Wed Apr 30 15:53:56 2025 +0800

    2nd commit

commit 742cc92f51bf471f1262c78451f0b2fef25f22d8
Author: Lucas Chen <lc20240328@gmail.com>
Date:   Wed Apr 30 15:52:16 2025 +0800
```

```
$ git log --oneline --graph --all
*   744577b (HEAD -> master) Merge branch 'bugfix'
|\
| * a5c3c6a (bugfix) 2nd commit
* | 63430c6 3rd commit
|/
* 742cc92 1st commit
```

## Git Rebase

> 假設在 dev 分支上開發，主分支是 master，想讓 dev 分支包含最新的 master 提交內容，可以執行 Rebase：

```
master: A---B---C
             \
dev:          D---E
```

```
master:    A---B---C
                    \
dev (new):           D'---E'
```

> 接下來，使用以下環境說明：

```
(master)
$ git log --oneline --graph --all
* 15f2bbd (HEAD -> master) add test3.txt
| * fd4cc39 (bugfix) add test2.txt
|/
* 384d519 add test1.txt
```

> 將 bugfix 分支的變更 rebase 到 master 分支的最前端 commit `15f2bbd` 之後。(把「目前所在的 bugfix 分支」的變更 rebase 到指定的 master 分支之後。)

```
(master)
$ git checkout bugfix
Switched to branch 'bugfix'

(bugfix)
$ git rebase master
Successfully rebased and updated refs/heads/bugfix.
```

> 原本的 bugfix commit `fd4cc39`被重新應用並建立為新的 commit `d553fa5`。
> bugfix 現在是建立在 master 最新 commit `15f2bbd` 之上。
> 但可以發現 commit `d553fa5` 的 Date 竟然比 commit `15f2bbd` 還要早！？

```
(bugfix)
$ git log
commit d553fa58ebde2aba2a9aa55dc795d1d30432483a (HEAD -> bugfix)
Author: Lucas Chen <lc20240328@gmail.com>
Date:   Wed Apr 30 17:51:16 2025 +0800

    add test2.txt

commit 15f2bbd639a992ee4414102cffdaf10d61b4e522 (master)
Author: Lucas Chen <lc20240328@gmail.com>
Date:   Wed Apr 30 17:51:44 2025 +0800

    add test3.txt

commit 384d519b221ef18b28ae1e706fd8872cd538d3ce
Author: Lucas Chen <lc20240328@gmail.com>
Date:   Wed Apr 30 17:50:28 2025 +0800

    add test1.txt
```

```
(bugfix)
$ git log --oneline --graph --all
* d553fa5 (HEAD -> bugfix) add test2.txt
* 15f2bbd (master) add test3.txt
* 384d519 add test1.txt
```

## Git 差異比較(`diff`)

1. 比較不同分支之間的差異。

    ```
    $ git diff master dev
    diff --git a/dev.txt b/dev.txt
    new file mode 100644 # working area
    index 0000000..38f8e88 # staging area
    --- /dev/null
    +++ b/dev.txt
    @@ -0,0 +1 @@
    +dev
    ```

## GitHub

1. 設定並驗證遠端儲存庫。

    > Push an existing repository from the command line.

    ```
    $ git remote add origin https://github.com/whsh99/github-demo.git
    ```
    ```
    $ cat .git/config
    [core]
            repositoryformatversion = 0
            filemode = false
            bare = false
            logallrefupdates = true
            symlinks = false
            ignorecase = true
    [remote "origin"]
            url = https://github.com/whsh99/github-demo.git
            fetch = +refs/heads/*:refs/remotes/origin/*
    ```
    
2. 建立 main 分支並推送(push)

    ```
    $ git branch -M main
    $ git push -u origin main
    info: please complete authentication in your browser...
    Enumerating objects: 6, done.
    Counting objects: 100% (6/6), done.
    Delta compression using up to 16 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (6/6), 356 bytes | 356.00 KiB/s, done.
    Total 6 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
    To https://github.com/whsh99/github-demo.git
     * [new branch]      main -> main
    branch 'main' set up to track 'origin/main'.
    ```

3. 驗證本機端和遠端的同步狀態。

    > 由下列輸出可以知道，HEAD 指向本機端 main 分支，而本機端 main 分支和遠端 origin/main 分支都指向同一個 commit。

    ```
    $ cat .git/refs/remotes/origin/main
    493da2ddb5e158e4ddd8a91cd3d430dddf03eef2

    $ git cat-file -t 493da2
    commit

    $ cat .git/refs/heads/main
    493da2ddb5e158e4ddd8a91cd3d430dddf03eef2

    $ git log
    commit 493da2ddb5e158e4ddd8a91cd3d430dddf03eef2 (HEAD -> main, origin/main)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 00:53:31 2025 +0800

        1st commit
    ```

4. 修改檔案後再次推送。

    > 修改本機端 `file1.txt`、執行 `add` 並 `commit` 後，需要再次 push 到遠端。

    ```
    $ vim file1.txt

    $ git add file1.txt
    warning: in the working copy of 'file1.txt', LF will be replaced by CRLF the next time Git touches it

    $ git commit -m "2nd commit"
    [main a86c295] 2nd commit
     1 file changed, 1 insertion(+), 1 deletion(-)
    ```
    
    ```
    $ git log
    commit a86c295e4e29eb052cfe43344c476be033bdf24b (HEAD -> main)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 01:06:27 2025 +0800

        2nd commit

    commit 493da2ddb5e158e4ddd8a91cd3d430dddf03eef2 (origin/main)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 00:53:31 2025 +0800

        1st commit
    ```
    
    > 推送以更新 origin/main：
    
    ```
    $ git push origin main
    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Delta compression using up to 16 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 308 bytes | 308.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
    To https://github.com/whsh99/github-demo.git
       493da2d..a86c295  main -> main
    ```
    
    > 推送後兩者再次同步：
    
    ```
    $ git log
    commit a86c295e4e29eb052cfe43344c476be033bdf24b (HEAD -> main, origin/main)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 01:06:27 2025 +0800

        2nd commit

    commit 493da2ddb5e158e4ddd8a91cd3d430dddf03eef2
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 00:53:31 2025 +0800

        1st commit
    ```

5. 從 GitHub Clone 遠端儲存庫。

    ```
    $ git clone https://github.com/whsh99/algorithm myalgo
    ```
    
    > 或
    
    ```
    $ git clone https://github.com/whsh99/algorithm
    Cloning into 'algorithm'...
    remote: Enumerating objects: 56, done.
    remote: Counting objects: 100% (56/56), done.
    remote: Compressing objects: 100% (54/54), done.
    remote: Total 56 (delta 19), reused 0 (delta 0), pack-reused 0 (from 0)
    Receiving objects: 100% (56/56), 239.49 KiB | 1.54 MiB/s, done.
    Resolving deltas: 100% (19/19), done.
    ```
    
    > 成功 clone 後的狀態：
    
    ```
    $ cd algorithm/
    $ git log
    commit 9266df86930c7adf91c4180ecbde38d9d6a44ed8 (HEAD -> main, origin/main, origin/HEAD)
    Author: Chen Yung Hsing <whsh99@gmail.com>
    Date:   Tue Jan 7 14:01:47 2025 +0800

        Update README-TIME_COMPLEXITY.md
    ...
    ```

6. 認識 Origin

    > 目前的本機端的 Git 儲存庫有連結到一個遠端(remote)儲存庫，名稱是 origin。

    ```
    $ git remote
    origin
    ```

    > 顯示每個遠端的 URL，以及它們是用來「抓取(fetch)」還是「推送(push)」的。

    ```
    $ git remote -v
    origin  https://github.com/whsh99/github-demo.git (fetch)
    origin  https://github.com/whsh99/github-demo.git (push)
    ```

    > origin 遠端儲存庫的設定。

    ```
    $ cat .git/config
    [core]
            repositoryformatversion = 0
            filemode = false
            bare = false
            logallrefupdates = true
            symlinks = false
            ignorecase = true
    [remote "origin"]
            url = https://github.com/whsh99/github-demo.git
            fetch = +refs/heads/*:refs/remotes/origin/*
    [branch "main"]
            remote = origin
            merge = refs/heads/main
    ```

    > 刪除連結遠端儲存庫 origin 的設定。

    ```
    $ git remote remove origin
    ```
    ```
    $ git remote -v
    $ 
    ```

    > 再連回去：

    ```
    $ git remote add my-origin https://github.com/whsh99/github-demo.git
    ```

    ```
    $ git remote -v
    my-origin       https://github.com/whsh99/github-demo.git (fetch)
    my-origin       https://github.com/whsh99/github-demo.git (push)
    ```

    > 新增`test-origin.txt` 並 commit 到本機端儲存庫。

    ```
    $ echo "origin test" > test-origin.txt

    $ git add test-origin.txt
    warning: in the working copy of 'test-origin.txt', LF will be replaced by CRLF the next time Git touches it

    $ git commit -m "test my-origin"
    [main 5efd28b] test my-origin
     1 file changed, 1 insertion(+)
     create mode 100644 test-origin.txt
    ```

    > 推送到 GitHub 遠端 `my-origin` (本機端 Git 專案現在透過 `my-origin` 這個遠端名稱，連結到 GitHub 上的儲存庫)。

    ```
    $ git push my-origin main
    Enumerating objects: 4, done.
    Counting objects: 100% (4/4), done.
    Delta compression using up to 16 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 290 bytes | 290.00 KiB/s, done.
    Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
    remote: Resolving deltas: 100% (1/1), completed with 1 local object.
    To https://github.com/whsh99/github-demo.git
       a86c295..5efd28b  main -> main
    ```

    ```
    $ git log
    commit 5efd28b4a5df20205828b1e3ac48889fd8977b3f (HEAD -> main, my-origin/main)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Thu May 1 10:36:15 2025 +0800

        test my-origin

    commit a86c295e4e29eb052cfe43344c476be033bdf24b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 01:06:27 2025 +0800

        2nd commit

    commit 493da2ddb5e158e4ddd8a91cd3d430dddf03eef2
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 00:53:31 2025 +0800

        1st commit
    ```
    
7. 切換遠端預設分支到其它分支
    
    > 切換遠端預設分支到其它分支。
    
    ![切換遠端預設分支到其它分支](https://hackmd.io/_uploads/ryy1Swlxgg.png)
    
    > 更新遠端預設分支。
    
    ![更新遠端預設分支](https://hackmd.io/_uploads/S1Y4BDgxgl.png)

    > 從 GitHub clone 下來的儲存庫，預設分支是 `dev`，Git 會根據 GitHub 上儲存庫的「預設分支(default branch)」來建立「本機端分支」。

    ```
    (main)
    $ git clone https://github.com/whsh99/github-demo.git
    Cloning into 'github-demo'...
    remote: Enumerating objects: 12, done.
    remote: Counting objects: 100% (12/12), done.
    remote: Compressing objects: 100% (5/5), done.
    remote: Total 12 (delta 1), reused 12 (delta 1), pack-reused 0 (from 0)
    Receiving objects: 100% (12/12), done.
    Resolving deltas: 100% (1/1), done.

    (main)
    $ cd github-demo/

    (dev)
    $ git branch
    * dev

    (dev)
    $ git log
    commit a86c295e4e29eb052cfe43344c476be033bdf24b (HEAD -> dev, origin/dev, origin/HEAD)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 01:06:27 2025 +0800

        2nd commit

    commit 493da2ddb5e158e4ddd8a91cd3d430dddf03eef2
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 00:53:31 2025 +0800

        1st commit
    ```

    > 列出「所有本機端與遠端的分支」。

    ```
    $ git branch -a
    * dev
      remotes/origin/HEAD -> origin/dev
      remotes/origin/dev
      remotes/origin/main
    ```

8. 本機端分支 vs. 遠端分支操作

    >  列出遠端分支(Remote Branches)。

    ```
    $ git branch -r
      origin/HEAD -> origin/main
      origin/main
    ```

    > 目前的本機端分支 `main` 完全同步於遠端 `origin/main`，沒有額外分支或未推送的變更。

    ```
    $ git log
    commit 5efd28b4a5df20205828b1e3ac48889fd8977b3f (HEAD -> main, origin/main, origin/HEAD)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Thu May 1 10:36:15 2025 +0800

        test my-origin

    commit a86c295e4e29eb052cfe43344c476be033bdf24b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 01:06:27 2025 +0800

        2nd commit

    commit 493da2ddb5e158e4ddd8a91cd3d430dddf03eef2
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 00:53:31 2025 +0800

        1st commit
    ```

    > "Traditionally, tips of branches and tags (collectively known as refs) were stored one file per ref in a (sub)directory under $GIT_DIR/refs directory."

    > "While many branch tips tend to be updated often, most tags and some branch tips are never updated."

    > "When a repository has hundreds or thousands of tags, this one-file-per-ref format both wastes storage and hurts performance."

    > `.git/packed-refs` 是 Git 為了解決儲存問題並提升效能，將某些 ref 壓縮在單一檔案中方便被尋找的機制。

    ```
    $ cat .git/packed-refs
    # pack-refs with: peeled fully-peeled sorted
    5efd28b4a5df20205828b1e3ac48889fd8977b3f refs/remotes/origin/main
    ```

    > 為了確保 `.git/packed-refs` 是最新的狀態，就可以執行 `fetch`，從遠端取得最新版本的 commit 到本機，但不會自動 merge。

    ```
    $ git fetch
    ```

    > 顯示目前 Git 儲存庫與遠端的關係設定。

    ```
    $ git remote show origin
    * remote origin
      Fetch URL: https://github.com/whsh99/github-demo.git
      Push  URL: https://github.com/whsh99/github-demo.git
      HEAD branch: main
      Remote branch:
        main tracked
      Local branch configured for 'git pull':
        main merges with remote main
      Local ref configured for 'git push':
        main pushes to main (up to date)
    ```

    > 讓我們透過範例來檢視修改遠端 `file1.txt` 前後的 ref 的狀態：

    ```
    $ cat .git/packed-refs
    # pack-refs with: peeled fully-peeled sorted
    5efd28b4a5df20205828b1e3ac48889fd8977b3f refs/remotes/origin/main

    $ cat .git/refs/heads/main
    5efd28b4a5df20205828b1e3ac48889fd8977b3f
    ```

    > 修改遠端 `file1.txt` 後，執行 `git fetch`：

    ```
    $ git fetch
    remote: Enumerating objects: 5, done.
    remote: Counting objects: 100% (5/5), done.
    remote: Compressing objects: 100% (2/2), done.
    remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
    Unpacking objects: 100% (3/3), 987 bytes | 17.00 KiB/s, done.
    From https://github.com/whsh99/github-demo
       5efd28b..8809660  main       -> origin/main
    ```

    > `.git/refs/heads/main` 仍舊是 `5efd28b`，表示本機端 `main` 還沒有 merge/rebase。
    > `.git/packed-refs` 也尚未更新(這是正常的)，因為 Git 尚未重新 pack 這些 refs。

    ```
    $ cat .git/packed-refs
    # pack-refs with: peeled fully-peeled sorted
    5efd28b4a5df20205828b1e3ac48889fd8977b3f refs/remotes/origin/main

    $ cat .git/refs/heads/main
    5efd28b4a5df20205828b1e3ac48889fd8977b3f
    ```

    > `.git/refs/remotes/origin/main` 已更新為最新 commit `8809660`。

    ```
    $ cat .git/refs/remotes/origin/HEAD
    ref: refs/remotes/origin/main

    $ cat .git/refs/remotes/origin/main
    8809660df8cbf41f34771e6ad5902a3a13812e22
    ```

    > 由於 `origin/main` 已更新，也就是它已經指向另一個較新的 commmit `880966`，而本機端 `main` 分支仍停留在舊的 commit `5efd28`。

    ```
    $ git log
    commit 5efd28b4a5df20205828b1e3ac48889fd8977b3f (HEAD -> main)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Thu May 1 10:36:15 2025 +0800

        test my-origin

    commit a86c295e4e29eb052cfe43344c476be033bdf24b
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 01:06:27 2025 +0800

        2nd commit

    commit 493da2ddb5e158e4ddd8a91cd3d430dddf03eef2
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Wed Apr 30 00:53:31 2025 +0800

        1st commit
    ```
    
    > 執行 merge 來解決本機分支 `main` 落後的狀態
    
    > `git branch -vv`: 檢視本機端分枝 + 上次 commit 資訊 + 本機端和遠端分支的關係(如果本機端分支沒有和任何遠端分支建立 tracked 關係，就不會顯示)
    
    ```
    $ git branch -vv
      dev  8809660 Update file1.txt
    * main 5efd28b [origin/main: behind 1] test my-origin
    
    $ git merge origin/main
    Updating 5efd28b..8809660
    Fast-forward
     file1.txt | 1 +
     1 file changed, 1 insertion(+)
     
     $ git branch -vv
    * main 8809660 [origin/main] Update file1.txt
    ```
    
    ---
    
    > 於遠端建立 `dev` 分支後的情形：

    ```
    $ git branch -r
    origin/HEAD -> origin/main
    origin/main
    ```
    
    > 可以透過 `git remote show origin` 發現 `dev` 分支出現在其中。
    
    ```
    $ git remote show origin
    * remote origin
      Fetch URL: https://github.com/whsh99/github-demo.git
      Push  URL: https://github.com/whsh99/github-demo.git
      HEAD branch: main
      Remote branches:
        dev  new (next fetch will store in remotes/origin)
        main tracked
      Local branch configured for 'git pull':
        main merges with remote main
      Local ref configured for 'git push':
        main pushes to main (local out of date)
    ```
    
    > Fetch 後，就可以看到 `dev` 分支變為 tracked 的狀態。
    
    ```
    $ git fetch
    From https://github.com/whsh99/github-demo
     * [new branch]      dev        -> origin/dev

    $ git remote show origin
    * remote origin
      Fetch URL: https://github.com/whsh99/github-demo.git
      Push  URL: https://github.com/whsh99/github-demo.git
      HEAD branch: main
      Remote branches:
        dev  tracked
        main tracked
      Local branch configured for 'git pull':
        main merges with remote main
      Local ref configured for 'git push':
        main pushes to main (local out of date)

    $ git branch -r
      origin/HEAD -> origin/main
      origin/dev
      origin/main
    ```
    
    > 若我們將遠端分支 `dev` 刪除後，即便執行 `git fetch` 後，仍會發現 `dev` 分支還在。

    ```
    $ git branch -r
      origin/HEAD -> origin/main
      origin/dev
      origin/main
    ```
    
    > `dev` 分支變得「不新鮮」(stale)了！
    
    ```
    $ git remote show origin
    * remote origin
      Fetch URL: https://github.com/whsh99/github-demo.git
      Push  URL: https://github.com/whsh99/github-demo.git
      HEAD branch: main
      Remote branches:
        main                    tracked
        refs/remotes/origin/dev stale (use 'git remote prune' to remove)
      Local branch configured for 'git pull':
        main merges with remote main
      Local ref configured for 'git push':
        main pushes to main (local out of date)
    ```
    
    > 清除已刪除的遠端分支的 ref。
    
    ```
    $ git remote prune
    ```
    
    > 或

    ```
    $ git fetch --prune
    From https://github.com/whsh99/github-demo
     - [deleted]         (none)     -> origin/dev
    ```
    
    > 單獨在本機端建立 `dev` 分支，會發現 `dev` 分支不像 `main` 分支一樣和遠端分支有連結。

    ```
    $ git checkout -b dev
    Switched to a new branch 'dev'

    (dev)
    $ git branch -vv
    * dev  5efd28b test my-origin
      main 5efd28b [origin/main: behind 1] test my-origin
    ```
    
    * 補充：清理本地端和遠端分支。

> 檢視哪些分支已經合併到 `main`。

```
(main)
$ git branch --merged main
  checkout
* main
```

> 刪除已合併、且不是 `main` 的分支。

```
(main)
$ git branch --merged main | grep -v "main" | xargs git branch -d
Deleted branch checkout (was 9f669f3).
```

> 進一步清除已刪除的遠端分支的 ref。

```
(main)
$ git branch --merged main | grep -v "main" | xargs -r git branch -d && git remote prune origin
```

> 若想真的刪除遠端分支，可以先執行 `git fetch origin` 進行同步，再執行以下指令：

```
(main)
$ git branch --merged main | grep -v "main" | sed 's/origin\///' | xargs git push origin --delete
```
    
9. Fetch vs. Pull

    * 情境一
    
        > 1. Someone pushed a new commit to github
        > 2. Use `git fetch` to sync remote master
        > 3. `git merge origin/master`
        > * Step 2 and 3 can be done through just one command: `git pull`
        
        
        
    * 情境二
    
        > 1. Someone pushed a new commit to github
        > 2. Create a new commit on local master
        
        ```
        $ git log
        commit 82fa2f452b80105ec8420af45eaa5c7708a722f2 (HEAD -> main)
        Author: Lucas Chen <lc20240328@gmail.com>
        Date:   Thu May 8 17:24:08 2025 +0800

            5th commit

        commit 19ead9b63e7477058ac2e2876d5d7269aec5caf3 (origin/main, origin/HEAD)
        Author: Lucas Chen <lc20240328@gmail.com>
        Date:   Sun May 4 00:38:12 2025 +0800

            3rd commit

        commit 74e3c3c70c0063c37c103cd2aa9d4e5698d39479
        Author: Lucas Chen <lc20240328@gmail.com>
        Date:   Sun May 4 00:37:36 2025 +0800

            2nd commit

        commit e5f80ab92761a88e108b7a9ffecdf8474f5afb4e
        Author: Lucas Chen <lc20240328@gmail.com>
        Date:   Sun May 4 00:37:08 2025 +0800

            1st commit
        ```
        
        > 3. `git fetch`
        > 4. `git merge origin/master`
        > 5. `git push`
        > * 「3-way merge without conflict」可以使用 `pull` (`fetch` + `merge`)。
        > 若是在「with conflict」的情況下，可以先進行 `git fetch` 再使用 `diff` 檢查差異，最後再執行 `git pull`。

        ```
        $ git pull
        remote: Enumerating objects: 4, done.
        remote: Counting objects: 100% (4/4), done.
        remote: Compressing objects: 100% (2/2), done.
        remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
        Unpacking objects: 100% (3/3), 904 bytes | 7.00 KiB/s, done.
        From https://github.com/whsh99/github-demo
           19ead9b..092a0a8  main       -> origin/main
        Merge made by the 'ort' strategy.
         file4.txt | 1 +
         1 file changed, 1 insertion(+)
         create mode 100644 file4.txt
        ```
        
        ```
        $ git log
        commit 9f669f3f7fdaeafa78bb12f95934ee8556ac145b (HEAD -> main)
        Merge: 82fa2f4 092a0a8
        Author: Lucas Chen <lc20240328@gmail.com>
        Date:   Thu May 8 17:25:31 2025 +0800

            Merge branch 'main' of https://github.com/whsh99/github-demo

        commit 82fa2f452b80105ec8420af45eaa5c7708a722f2
        Author: Lucas Chen <lc20240328@gmail.com>
        Date:   Thu May 8 17:24:08 2025 +0800

            5th commit

        commit 092a0a8d8dbf59d4f1b0c68b89aee449e8dd0619 (origin/main, origin/HEAD)
        Author: Lucas Chen <whsh99@gmail.com>
        Date:   Thu May 8 17:17:58 2025 +0800

            4th commit

        commit 19ead9b63e7477058ac2e2876d5d7269aec5caf3
        Author: Lucas Chen <lc20240328@gmail.com>
        Date:   Sun May 4 00:38:12 2025 +0800

        $ git branch -vv
        * main 9f669f3 [origin/main: ahead 2] Merge branch 'main' of https://github.com/whsh99/github-demo
        
        $ git log --oneline --graph --all
        *   9f669f3 (HEAD -> main) Merge branch 'main' of https://github.com/whsh99/github-demo
        |\
        | * 092a0a8 (origin/main, origin/HEAD) 4th commit
        * | 82fa2f4 5th commit
        |/
        * 19ead9b 3rd commit
        * 74e3c3c 2nd commit
        * e5f80ab 1st commit
        ```
        
10. FETCH_HEAD

    > 這表示 Git 最近一次從遠端儲存庫(https://github.com/whsh99/github-demo)抓取(fetch)資料時，由 `main` 遠端分支上抓取到的最新 commit 物件。

    ```
    $ cat .git/FETCH_HEAD
    092a0a8d8dbf59d4f1b0c68b89aee449e8dd0619                branch 'main' of https://github.com/whsh99/github-demo
    ```
    
    > 當執行 `git fetch` 時，Git 只是把遠端的所有更新拉到本機的暫存區(`.git` 目錄)，但它不會主動建立對應的本機端分支，因為 `fetch` 的工作是更新遠端分支的參考(如 `origin/dev`，而不會影響本機端的任何分支結構。這樣設計是為了讓使用者能夠自行決定是否要追蹤或建立本地分支，以避免自動修改工作環境。

    ```
    $ git checkout dev
    branch 'dev' set up to track 'origin/dev'.
    Switched to a new branch 'dev
    ```
    
11. ORIG_HEAD

    > 當使用者執行一些比較「危險」的操作(如 merge、rebase 或 reset 等)，Git 就會把 HEAD 的狀態存放在此，讓使用者隨時可以回退到危險行為之前的狀態。

    ```
    $ cat .git/ORIG_HEAD
    a0036758e8488fa258376ca250c39ac1fbe0d3a5
    ```
    
    > 執行 `git reset` 退回：
    
    ```
    $ git reset --hard ORIG_HEAD
    HEAD is now at a003675 Create file6.txt
    ```
    
    ```
    $ git branch -vv
    * dev  a003675 [origin/dev: behind 1] Create file6.txt
      main 9f669f3 [origin/main] Merge branch 'main' of https://github.com/whsh99/gthub-demo
    ```
    
12. Git Pull
    
    > 執行 `git pull` 後，可以發現 Git 與遠端建立了連線，並使用 POST 傳送請求資料，比對本機端和遠端分支的狀態來進行更新。
    
    * 範例一
        
        ```
        (main)
        $ git fetch -v
        POST git-upload-pack (224 bytes)
        POST git-upload-pack (276 bytes)
        remote: Enumerating objects: 5, done.
        remote: Counting objects: 100% (5/5), done.
        remote: Compressing objects: 100% (2/2), done.
        remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
        Unpacking objects: 100% (3/3), 899 bytes | 6.00 KiB/s, done.
        From https://github.com/whsh99/github-demo
         = [up to date]      main       -> origin/main
           a003675..676f735  dev        -> origin/dev
        ```
        
    * 範例二
        
        ```
        (dev)
        $ git pull -v
        POST git-upload-pack (223 bytes)
        From https://github.com/whsh99/github-demo
         = [up to date]      dev        -> origin/dev
         = [up to date]      main       -> origin/main
        Updating a003675..676f735
        Fast-forward
         file6.txt | 1 +
         1 file changed, 1 insertion(+)
        ```
        
    * 範例三 - Git Pull with Conflict
        
        ```
        $ git pull -v
        ...
        Auto-merging file1.txt
        CONFLICT (content): Merge conflict in file1.txt
        Automatic merge failed; fix conflicts and then commit the result.
        ```
        
        > 檢視有哪些檔案發生衝突，並手動修改為想要保留的內容(可以混合、選擇其一或重新寫)：
        
        ```
        $ git status
        ```
        
        > 標記為已解決：
        
        ```
        $ git add file1.txt
        ```
        
        > 完成合併並「直接」提交：
        
        ```
        $ git commit -a
        ```
        
        > 推送到遠端：

        ```
        $ git push origin dev
        ```
    
13. Git Push

    ```
    (main)
    $ git checkout -b feature-1
    Switched to a new branch 'feature-1'

    (feature-1)
    $ mkdir feature-1

    (feature-1)
    $ cd feature-1/

    (feature-1)
    $ echo "feature-1" > feature-1.txt

    (feature-1)
    $ git add feature-1.txt
    warning: in the working copy of 'feature-1/feature-1.txt', LF will be replaced by CRLF the next time Git touches it

    (feature-1)
    $ git commit -m "add feature-1"
    [feature-1 b6f6b7e] add feature-1
     1 file changed, 1 insertion(+)
     create mode 100644 feature-1/feature-1.txt
    ```

    > 在本機端完成建立 `feature-1` 分支後，如果直接 `push` 會出現錯誤，因為 `feature-1` 分支還未和對應的遠端分支進行關聯。

    ```
    (feature-1)
    $ git push
    fatal: The current branch feature-1 has no upstream branch.
    To push the current branch and set the remote as upstream, use

        git push --set-upstream origin feature-1

    To have this happen automatically for branches without a tracking
    upstream, see 'push.autoSetupRemote' in 'git help config'.
    ```

    > 透過只執行 `git push origin feature-1` 會發現，本機端的 `feature-1` 分支沒有和遠端的 `origin/feature-1` 完成關聯。

    ```
    (feature-1)
    $ git push origin feature-1
    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Delta compression using up to 16 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (4/4), 330 bytes | 330.00 KiB/s, done.
    Total 4 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
    remote: Resolving deltas: 100% (1/1), completed with 1 local object.
    remote:
    remote: Create a pull request for 'feature-1' on GitHub by visiting:
    remote:      https://github.com/whsh99/github-demo/pull/new/feature-1
    remote:
    To https://github.com/whsh99/github-demo.git
     * [new branch]      feature-1 -> feature-1

    (feature-1)
    $ git branch -vv
      checkout  9f669f3 Merge branch 'main' of https://github.com/whsh99/github-demo
      dev       6416b7b [origin/dev] update file1 from local dev branch
    * feature-1 b6f6b7e add feature-1
      main      cc8dbb8 [origin/main] Update file1.txt
    ```

    > 接下來，要先將遠端的 `feature-1` 儲存庫刪除。

    ```
    (feature-1)
    $ git push origin -d feature-1
    To https://github.com/whsh99/github-demo.git
     - [deleted]         feature-1

    (feature-1)
    $ git branch -a
      checkout
      dev
    * feature-1
      main
      remotes/origin/HEAD -> origin/main
      remotes/origin/dev
      remotes/origin/main
    ```

    > 加上 `--set-uptream` 或 `-u` 並重新執行：

    ```
    (feature-1)
    $ git push -u origin feature-1
    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Delta compression using up to 16 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (4/4), 330 bytes | 330.00 KiB/s, done.
    Total 4 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
    remote: Resolving deltas: 100% (1/1), completed with 1 local object.
    remote:
    remote: Create a pull request for 'feature-1' on GitHub by visiting:
    remote:      https://github.com/whsh99/github-demo/pull/new/feature-1
    remote:
    To https://github.com/whsh99/github-demo.git
     * [new branch]      feature-1 -> feature-1
    branch 'feature-1' set up to track 'origin/feature-1'.
    ```
    
    > 成功使得本機端和遠端的 `feature-1` 分支完成關聯。

    ```
    (feature-1)
    $ git branch -vv
      checkout  9f669f3 Merge branch 'main' of https://github.com/whsh99/github-demo
      dev       6416b7b [origin/dev] update file1 from local dev branch
    * feature-1 b6f6b7e [origin/feature-1] add feature-1
      main      cc8dbb8 [origin/main] Update file1.txt
    ```
    
    > 刪除遠端分支 `feature-1`。
    
    ```
    (feature-1)
    $ git push origin -d feature-1
    To https://github.com/whsh99/github-demo.git
     - [deleted]         feature-1

    (feature-1)
    $ git branch -vv
      checkout  9f669f3 Merge branch 'main' of https://github.com/whsh99/github-demo
      dev       6416b7b [origin/dev] update file1 from local dev branch
    * feature-1 b6f6b7e [origin/feature-1: gone] add feature-1
      main      cc8dbb8 [origin/main] Update file1.txt
    ```
    
    > 確認所有分支(包含遠端)。
    
    ```
    (feature-1)
    $ git branch -a
      checkout
      dev
    * feature-1
      main
      remotes/origin/HEAD -> origin/main
      remotes/origin/dev
      remotes/origin/main
    ```
    
    > 切換到 `main` 分支，再刪除本機端分支
    
    ```
    (feature-1)
    $ git checkout main
    Switched to branch 'main'
    Your branch is up to date with 'origin/main'.

    (main)
    $ git branch -D feature-1
    Deleted branch feature-1 (was b6f6b7e).
    ```
    
14. Fork

    > 對自己沒有存取控制權限的儲存庫執行 fork。

    ![Fork](https://hackmd.io/_uploads/ryxnj5igxe.png)

    ![Create a new fork](https://hackmd.io/_uploads/H182sqsggx.png)

    ![git-test](https://hackmd.io/_uploads/SJ1pi9ilgx.png)

    > 

    ```
    $ git clone https://github.com/whsh99/git-test.git
    
    $ cd git-test/

    (master)
    $ echo "pull request" > pull_request.txt

    (master)
    $ git add pull_request.txt
    warning: in the working copy of 'pull_request.txt', LF will be replaced by CRLF the next time Git touches it

    (master)
    $ git commit -m "add pull_request"
    [master 0489f0d] add pull_request
     1 file changed, 1 insertion(+)
     create mode 100644 pull_request.txt

    (master)
    $ git push origin master
    Enumerating objects: 4, done.
    Counting objects: 100% (4/4), done.
    Delta compression using up to 16 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 290 bytes | 290.00 KiB/s, done.
    Total 3 (delta 1), reused 0 (delta 0), pack-reused 0 (from 0)
    remote: Resolving deltas: 100% (1/1), completed with 1 local object.
    To https://github.com/whsh99/git-test.git
       27a16dc..0489f0d  master -> master
    ```
    
    > 可以發現自己的遠端儲存庫出現以下訊息：This branch is 1 commit ahead of xiaopeng163/git-test:master.，因為原始的儲存庫只有7次 commit，而我們。
    
    ![This branch is 1 commit ahead](https://hackmd.io/_uploads/SymhCqiege.png)

    > 建立 pull request。
    
    ![Open pull request](https://hackmd.io/_uploads/r1bOgssggl.png)
    
    ![Create a new pull request](https://hackmd.io/_uploads/rJFazojlgx.png)
    
    > 原始的儲存庫的擁有者可以在 review 後決定是否要執行 merge。
    
    ![A new pull request](https://hackmd.io/_uploads/r1IAzjslle.png)
    
    * Sync a fork repository.
        * Configure a remote for a fork
            
            > 新增遠端儲存庫 upstream 並從原始作者的 GitHub 儲存庫網址 fork 過來。
        
            ```
            $ git remote add upstream https://github.com/ORIGINAL_OWNER/ORIGINAL_REPOSITORY.git
            ```
            
            > 列出遠端儲存庫。
        
            ```
            $ git remote –v
            ```
            
        * Syncing a fork
            
            > 取得 upstream 的最新資料。
        
            ```
            $ git fetch upstream
            ```
            
            > 合併 upstream 的變更到本機端。
            
            ```
            $ git merge upstream/master
            ```
            
            > fetch 和 merge 可以使用 pull 替代。
            
            ```
            $ git pull upstream master
            ```
            
            > 將更新推送到自己的 GitHub fork。
            
            ```
            $ git push origin master
            ```
    
            [Fork 練習測驗 1 儲存庫](https://github.com/udemy-course/git-test)

## Git SSH

> 於以下路徑 `~/.ssh/config` 新增配置檔案。

```
Host github.com
IdentityFile /Users/yungh/.ssh/github
User whsh99

Host gitlab-demo.com
IdentityFile /Users/yungh/.ssh/gitlab
User yunghsing84
```

##  Git Tag

> Git 的 Tag 是用來標記特定提交(commit)，常用於版本發布(如 v1.0.0)。

> **Summary**
> Given a version number MAJOR.MINOR.PATCH, increment the:
> 
> 1. MAJOR version when you make incompatible API changes
> 2. MINOR version when you add functionality in a backward compatible manner
> 3. PATCH version when you make backward compatible bug fixes
> 
> Additional labels for pre-release and build metadata are available as extensions to the MAJOR.MINOR.PATCH format.

[Semantic Versioning 2.0.0](https://semver.org/)

* 建立 tag
    * `git tag <tag name>`: 建立一個 Lightweight tag (如 `git tag v1.0`)
    * `git tag -a <tag name> -m <tag message>`: 建立一個擁有所有相關元資料(meta-data, like email, date, etc.)的 Annotated tag (註解標籤) (如 `git tag -a v1.0 -m "version 1.0"` )
    * `git tag -a <tag name> <commit SHA1 value>` 為較舊的 commit 建立一個 Annotated tag (如 `git tag -a v1.0 236e1ef5d755f6ea6894f8f46f36186190be7b92`)
* 表列 tags
    * `git tag`
* 刪除 tags: `git tag -d <tag name>` (如 `git tag -d v1.0`)

* 範例

    ```
    (master)
    $ git log
    commit 8a33928a5f6cf788dc3d6895d9da5fb940c7d679 (HEAD -> master, origin/master, origin/HEAD)
    Author: Lucas Chen <lc20240328@gmail.com>
    Date:   Fri May 9 23:54:43 2025 +0800

        Hi, this is Lucas

    commit 0f430c40aaa194b7c8e29462a5ae2af056159183
    Author: Lawrence-lab <59114905+Lawrence-lab@users.noreply.github.com>
    Date:   Mon Mar 24 16:20:41 2025 +0800

        Update README.md (#172)

    commit 511832c3f2ec499c9ca2a9832d2ef54014fa6319
    Author: today20050110 <39054568+today20050110@users.noreply.github.com>
    Date:   Thu Jan 16 04:24:08 2025 +0800

        Update README.md (#171)

        say hi

    commit bd479ad6465af0c99f3ad02e9392b9bf589dc96f
    Author: kenny-tan <84892451+kenny-tan@users.noreply.github.com>
    ```

    ```
    (master)
    $ git tag v1.0.0
    ```

    ```
    (master)
    $ cat .git/refs/tags/v1.0.0
    8a33928a5f6cf788dc3d6895d9da5fb940c7d679
    ```

    ```
    (master)
    $ git tag -d v1.0.0
    Deleted tag 'v1.0.0' (was 8a33928)
    ```
    
    ```
    (master)
    $ git tag -a v1.0.0 -m "version v1.0.0"

    (master)
    $ git tag
    v1.0.0
    ```
    
    ```
    (master)
    $ cat .git/refs/tags/v1.0.0
    f12085e9a117787dc13c6ccbe01d6d08d3720b7a

    (master)
    $ git cat-file -t f12085e
    tag

    (master)
    $ git cat-file -p f12085e
    object 8a33928a5f6cf788dc3d6895d9da5fb940c7d679
    type commit
    tag v1.0.0
    tagger Lucas Chen <lc20240328@gmail.com> 1746856047 +0800

    version v1.0.0
    ```
    
* 將本機端 Tag 同步至遠端

    * Push (推送) tags
        * `git push origin <tag name>`: push tag to remoteg (i.e. `git push origin v1.0`)
        * `git push --tags`: push multiple tags simultaneously
    * Sync tags from remote
        * `git fetch`
    * Delete tags:
        * `git tag -d <tag name>`: delete local git tag (i.e. `git tag -d v1.0`)
        * `git push –-delete origin <tag name>`: delete remote git tag
    * 範例 - Push tag

        ```
        (master)
        $ git tag v1.0.0

        (master)
        $ cat .git/refs/tags/v1.0.0
        8a33928a5f6cf788dc3d6895d9da5fb940c7d679

        (master)
        $ git log
        commit 8a33928a5f6cf788dc3d6895d9da5fb940c7d679 (HEAD -> master, tag: v1.0.0, origin/master, origin/HEAD)
        Author: Lucas Chen <lc20240328@gmail.com>
        Date:   Fri May 9 23:54:43 2025 +0800

            Hi, this is Lucas
            ...
        ```

        ```
        (master)
        $ git push origin v1.0.0
        Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
        To https://github.com/whsh99/git-test.git
         * [new tag]         v1.0.0 -> v1.0.0
        ```

        ```
        (master)
        $ git push origin v1.0.0
        Total 0 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
        To https://github.com/whsh99/git-test.git
         * [new tag]         v1.0.0 -> v1.0.0

        (master)
        $ git tag -a v1.0.1 -m "version v1.0.1"

        (master)
        $ git push origin v1.0.1
        Enumerating objects: 1, done.
        Counting objects: 100% (1/1), done.
        Writing objects: 100% (1/1), 166 bytes | 166.00 KiB/s, done.
        Total 1 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
        To https://github.com/whsh99/git-test.git
         * [new tag]         v1.0.1 -> v1.0.1
        ```
        
        ```
        (master)
        $ git tag -d v1.0.0
        Deleted tag 'v1.0.0' (was 8a33928)

        (master)
        $ git tag -d v1.0.1
        Deleted tag 'v1.0.1' (was a25c58c)
        ```

    * 範例 - Sync tags from remote

        ```
        (master)
        $ git push --delete origin v1.0.0
        To https://github.com/whsh99/git-test.git
         - [deleted]         v1.0.0

        (master)
        $ git push --delete origin v1.0.1
        To https://github.com/whsh99/git-test.git
         - [deleted]         v1.0.1
        ```
        
## Git hook

> Git hook 是 Git 提供的一種機制，讓使用者可以在特定的 Git 事件發生時，自動執行自訂的腳本。這些事件包括如 commit、push、merge 等。Git hook 主要是放在你專案的 `.git/hooks/` 資料夾中，並以 Shell script (或其他可執行程式)形式運作。

> 移除有 `.sample` 名稱的檔案使 hook 生效。

> 得特別注意，在預設情況下，即使在本機端進行推送，已修改過的 hook 不會被包含在其中，只會推送預設的 hook。

![git hooks](https://hackmd.io/_uploads/r1cPjOhgxg.png)

* `--no-verify`: 忽視 hook 強制進行 commit

    ```
    $ git commit -m "update test.py" --no-verify
    ```
    
## GitLab

Group vs. Organization
Project vs. Repository

* Issue

## Git 進階

### 壓縮

#### 針對 Object

> 文字檔案經過 commit 後，若內容較多重複的字元則物件大小較小，而內容為二進制字元則物件大小幾乎不變或較大。

```
$ git gc
```

```
(master)
$ du -h .git/objects
0       .git/objects/info
2.9M    .git/objects/pack <- 包含 `.idx` 和 `.pack` 副檔名的文件。
2.9M    .git/objects
```

#### 針對 Pack

> 當 Git 儲存大量的物件(如 commit、tree、blob)時，為了節省空間與提升效能，它會將這些物件壓縮打包成 `.pack` 檔案並放在 `pack/` 資料夾內。

```
(master)
$ ls -l .git/objects/pack/
total 9
-r--r--r-- 1 NT200608 1049089 1324 May 14 12:23 pack-9f47ad7e584a25023d38fd54a80a547accc16cab.idx <- 索引
-r--r--r-- 1 NT200608 1049089  733 May 14 12:23 pack-9f47ad7e584a25023d38fd54a80a547accc16cab.pack
-r--r--r-- 1 NT200608 1049089   88 May 14 12:23 pack-9f47ad7e584a25023d38fd54a80a547accc16cab.rev <- 反向索引
```

> 使用 `git verify-pack` 從 `.idx` 檔案檢視 Git 打包的內容：

```
(master)
$ git verify-pack -v .git/objects/pack/pack-9f47ad7e584a25023d38fd54a80a547accc16cab.idx
4c55a8d1a9ba41c7c48a8596ed2ae9e1b7c82332 commit 253 175 12
9f67f30670a89fd87d51351ff31ccdd45e99b028 commit 253 176 187
5e7785187e23665b017efe37cab6bff2734daf53 commit 205 141 363
e2129701f1a4d54dc44f03c93bca0a2aec7c5449 blob   6 15 504 <- 最原始的物件
6c493ff740f9380390d5c9ddef4af18697ac9375 blob   6 15 519
7c8ac2f8d82a1eb5f6aaece6629ff11015f91eb4 blob   6 15 534
cb9441797ea2b438520ce3f0623457d028698bc4 tree   111 102 549
b75f44edbd9252c32bf9faa0c1257ffb3b126c24 tree   4 14 651 1 cb9441797ea2b438520ce3f0623457d028698bc4
0b9f291245f6c596fd30bee925fe94fe0cbadd60 tree   37 48 665
non delta: 8 objects
chain length = 1: 1 object
.git/objects/pack/pack-9f47ad7e584a25023d38fd54a80a547accc16cab.pack: ok
```

### 解壓縮

> "Read a packed archive (.pack) from the standard input, expanding the objects contained within and writing them into the repository in "loose" (one object per file) format."

> "Objects that already exist in the repository will not be unpacked from the packfile. Therefore, nothing will be unpacked if you use this command on a packfile that exists within the target repository.(如果你解壓的 `.pack` 檔本來就屬於這個儲存庫(也就是 ``.git/objects/pack/`` 裡面的 `pack`)，那麼這些物件早就已經存在了，Git 會發現「都已經有了」，所以會什麼都不解壓縮。)"


```
(master)
$ ls -l .git/objects/pack/
total 9
-r--r--r-- 1 NT200608 1049089 1324 May 14 12:23 pack-9f47ad7e584a25023d38fd54a80a547accc16cab.idx
-r--r--r-- 1 NT200608 1049089  733 May 14 12:23 pack-9f47ad7e584a25023d38fd54a80a547accc16cab.pack
-r--r--r-- 1 NT200608 1049089   88 May 14 12:23 pack-9f47ad7e584a25023d38fd54a80a547accc16cab.rev
```

> 將 `.pack` 檔案先移動到 `.git/` 之下。

```
(master)
$ mv .git/objects/pack/pack-9f47ad7e584a25023d38fd54a80a547accc16cab.pack .git/
```

> 使用 `git unpack-objects` 搭配 standard input 輸入 `.pack` 檔案。

```
(master)
$ git unpack-objects < .git/pack-9f47ad7e584a25023d38fd54a80a547accc16cab.pack
Unpacking objects: 100% (9/9), 713 bytes | 12.00 KiB/s, done.
```

### 本機端和遠端 Gitlab 伺服器的 Git 壓縮

> 當本機端儲存庫欲 Push 到遠端儲存庫的時候，本機端會進行壓縮，再將 `.pack` 檔案傳輸到遠端。
> Push 後，會發現本機端和遠端的 `.pack` 物件的值不同。

### Git 垃圾 Object 清理

#### 範例一

> 先將一個原始的 `test.txt` 檔案進行 add 後，再修改檔案內容重新 add，重複執行2次。

```
$ git init
Initialized empty Git repository in D:/Git/git-test/.git/

(master)
$ echo "abc" > test.txt

(master)
$ git add test.txt
warning: in the working copy of 'test.txt', LF will be replaced by CRLF the next time Git touches it

(master)
$ ls -l .git/objects/
total 0
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 8b/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 info/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 pack/

(master)
$ echo "123" >> test.txt

(master)
$ git add test.txt
warning: in the working copy of 'test.txt', LF will be replaced by CRLF the next time Git touches it

(master)
$ ls -l .git/objects/
total 0
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:56 29/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 8b/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 info/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 pack/

(master)
$ echo "456" >> test.txt

(master)
$ git add test.txt
warning: in the working copy of 'test.txt', LF will be replaced by CRLF the next time Git touches it

(master)
$ ls -l .git/objects/
total 0
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:56 29/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:58 83/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 8b/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 info/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 pack/

(master)
$ git commit -m "1st commit"

(master)
$ ls -l .git/objects/
total 0
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:59 0d/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:56 29/ <- 垃圾物件
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:59 47/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:58 83/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 8b/ <- 垃圾物件
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 info/
drwxr-xr-x 1 NT200608 1049089 0 May 14 14:54 pack/
```


> Git 2.37+ 開始支援「cruft pack」，會把暫時不需要、但尚未被完全刪除的物件，也包進一個特殊的 `.pack` 檔案，就不需要再使用 `git-prune` 將剩餘的物件刪除。

```
$ git gc
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), done.
Total 3 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
Enumerating cruft objects: 2, done.
Traversing cruft objects: 2, done.
Counting objects: 100% (2/2), done.
Writing objects: 100% (2/2), done.
Total 2 (delta 0), reused 0 (delta 0), pack-reused 0 (from 0)
```

> 但透過 `git fsck` 可以發現兩個「懸掛(dangling)blob 物件」，也就是目前沒有被任何 commit、tag、tree 所引用的檔案內容物件，所以還是要透過 `git prune` 進行刪除(可以透過 `git prune -n` 先預覽有哪些物件會被刪除)。

```
(master)
$ git fsck
Checking object directories: 100% (256/256), done.
Checking objects: 100% (5/5), done.
dangling blob 298f44a95aa2209cba63b287183dfc214e8da626
dangling blob 8baef1b4abc478178b004d62031cf7fe6db6f903
Verifying commits in commit graph: 100% (1/1), done.
```

#### 範例二

> 先建立一個新的 `tmp` 分支，在該分支上進行一次 commit，最後再將 `tmp` 分支強制刪除。

```
(master)
$ git checkout -b tmp
fatal: a branch named 'tmp' already exists

(tmp)
$ echo "tmp" > tmp.txt

(tmp)
$ git add tmp.txt
warning: in the working copy of 'tmp.txt', LF will be replaced by CRLF the next time Git touches it

(tmp)
$ git commit -m "1st commit from tmp"

(tmp)
$ git checkout master
Switched to branch 'master'

(master)
$ git branch -D tmp
Deleted branch tmp (was 1068c68).
```

> 刪除後，會發現剩餘了一些和 `tmp` 分支關聯的垃圾物件。

```
(master)
$ ls -l .git/objects/
total 4
drwxr-xr-x 1 NT200608 1049089 0 May 14 15:22 10/
drwxr-xr-x 1 NT200608 1049089 0 May 14 15:22 7e/
drwxr-xr-x 1 NT200608 1049089 0 May 14 15:22 a9/
drwxr-xr-x 1 NT200608 1049089 0 May 14 15:04 info/
drwxr-xr-x 1 NT200608 1049089 0 May 14 15:04 pack/
```

> 執行 `git gc` 來清理。

```
$ git gc
Enumerating objects: 6, done.
Counting objects: 100% (6/6), done.
Delta compression using up to 20 threads
Compressing objects: 100% (3/3), done.
Writing objects: 100% (6/6), done.
Total 6 (delta 0), reused 3 (delta 0), pack-reused 0 (from 0)
Enumerating cruft objects: 2, done.
Traversing cruft objects: 2, done.
Counting objects: 100% (2/2), done.
Writing objects: 100% (2/2), done.
Total 2 (delta 0), reused 2 (delta 0), pack-reused 0 (from 0)
```

> 還在！

```
(master)
$ git fsck
Checking object directories: 100% (256/256), done.
Checking objects: 100% (8/8), done.
dangling blob 298f44a95aa2209cba63b287183dfc214e8da626
dangling blob 8baef1b4abc478178b004d62031cf7fe6db6f903
Verifying commits in commit graph: 100% (1/1), done.
```

> `.pack` 中也有！

```
(master)
$ git verify-pack -v .git/objects/pack/pack-27ae2143d2fa5524e9968e395a9274bfa82902ac.pack
1068c68859857b87bb181b677dcfcc74b9371001 commit 260 184 12
0d993f11dcc30d2c582a4fa5dc552a682d89c929 commit 203 140 196
838a66dcb1f86fd97048a650db5c8a48ff592bdd blob   12 21 336
47535444bd5a9377b461b4fbff2f5011f9554edb tree   36 47 357
7e26e3f6f5404402ce16d7137d710a47fcd26779 tree   71 76 404
a9a5aecf429fd8a0d81fbd5fd37006bfa498d5c1 blob   4 13 480
non delta: 6 objects
.git/objects/pack/pack-27ae2143d2fa5524e9968e395a9274bfa82902ac.pack: ok
```

> 執行最後的大絕招！

```
(master)
$ git -c gc.reflogExpire=0 -c gc.reflogExpireUnreachable=0 \
> -c gc.rerereresolved=0 -c gc.rerereunresolved=0 \
> -c gc.pruneExpure=now gc "$@"
Enumerating objects: 3, done.
Counting objects: 100% (3/3), done.
Writing objects: 100% (3/3), done.
Total 3 (delta 0), reused 3 (delta 0), pack-reused 0 (from 0)
Enumerating cruft objects: 5, done.
Traversing cruft objects: 7, done.
Counting objects: 100% (5/5), done.
Delta compression using up to 20 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (5/5), done.
Total 5 (delta 0), reused 5 (delta 0), pack-reused 0 (from 0)
```

### Git log 進階

> 將 Git 的原始碼儲存庫 clone 到本機端。

```
$ git clone https://github.com/git/git.git
Cloning into 'git'...
remote: Enumerating objects: 391226, done.
remote: Counting objects: 100% (647/647), done.
remote: Compressing objects: 100% (273/273), done.
remote: Total 391226 (delta 495), reused 374 (delta 374), pack-reused 390579 (from 3)
Receiving objects: 100% (391226/391226), 266.20 MiB | 19.72 MiB/s, done.
Resolving deltas: 100% (295216/295216), done.
Updating files: 100% (4638/4638), done.
```

> 一般顯示詳細資料的 `git log`。

```
(master)
$ git log
commit 1a8a4971cc6c179c4dd711f4a7f5d7178f4b3ab7 (HEAD -> master, origin/master, origin/HEAD)
Author: Junio C Hamano <gitster@pobox.com>
Date:   Tue May 13 14:05:18 2025 -0700

    The fourteenth batch

    Signed-off-by: Junio C Hamano <gitster@pobox.com>

...
```

> 顯示最後兩筆詳細資料。

```
(master)
$ git log -2
commit 1a8a4971cc6c179c4dd711f4a7f5d7178f4b3ab7 (HEAD -> master, origin/master, origin/HEAD)
Author: Junio C Hamano <gitster@pobox.com>
Date:   Tue May 13 14:05:18 2025 -0700

    The fourteenth batch

    Signed-off-by: Junio C Hamano <gitster@pobox.com>

commit 330a09e4a5160923f2aa4b30afab913a10a06adb
Merge: acfcd7ca93 ec727e189c
Author: Junio C Hamano <gitster@pobox.com>
Date:   Tue May 13 14:05:07 2025 -0700

    Merge branch 'kj/glob-path-with-special-char'

    "git add 'f?o'" did not add 'foo' if 'f?o', an unusual pathname,
    also existed on the working tree, which has been corrected.

    * kj/glob-path-with-special-char:
      dir.c: literal match with wildcard in pathspec should still glob
```

> `--oneline` 只顯示標題。

```
(master)
$ git log --oneline
1a8a4971cc (HEAD -> master, origin/master, origin/HEAD) The fourteenth batch
330a09e4a5 Merge branch 'kj/glob-path-with-special-char'
acfcd7ca93 Merge branch 'kh/docfixes'
1551145edb Merge branch 'js/ci-buildsystems-cleanup'
```

> `-{n}` + `--online`。

```
$ git log -10 --oneline
1a8a4971cc (HEAD -> master, origin/master, origin/HEAD) The fourteenth batch
330a09e4a5 Merge branch 'kj/glob-path-with-special-char'
acfcd7ca93 Merge branch 'kh/docfixes'
1551145edb Merge branch 'js/ci-buildsystems-cleanup'
03284715a8 Merge branch 'ps/ci-test-aggreg-fix-for-meson'
f2cc60c053 Merge branch 'en/get-tree-entry-doc'
38af977b81 The thirteenth batch
b8cc1a9acd Merge branch 'ps/meson-bin-sh'
a4ad13dd19 Merge branch 'ng/xdiff-truly-minimal'
6dbc41631d Merge branch 'ds/fix-thin-fix'
```


```
(master)
$ git log --graph
* commit 1a8a4971cc6c179c4dd711f4a7f5d7178f4b3ab7 (HEAD -> master, origin/master, origin/HEAD)
| Author: Junio C Hamano <gitster@pobox.com>
| Date:   Tue May 13 14:05:18 2025 -0700
|
|     The fourteenth batch
|
|     Signed-off-by: Junio C Hamano <gitster@pobox.com>
|
*   commit 330a09e4a5160923f2aa4b30afab913a10a06adb
|\  Merge: acfcd7ca93 ec727e189c
| | Author: Junio C Hamano <gitster@pobox.com>
| | Date:   Tue May 13 14:05:07 2025 -0700
| |
| |     Merge branch 'kj/glob-path-with-special-char'
| |
| |     "git add 'f?o'" did not add 'foo' if 'f?o', an unusual pathname,
| |     also existed on the working tree, which has been corrected.
| |
| |     * kj/glob-path-with-special-char:
| |       dir.c: literal match with wildcard in pathspec should still glob
| |
```

> `--state` 顯示檔案曾經進行了哪些修改。

```
(master)
$ git log --stat
commit 1a8a4971cc6c179c4dd711f4a7f5d7178f4b3ab7 (HEAD -> master, origin/master, origin/HEAD)
Author: Junio C Hamano <gitster@pobox.com>
Date:   Tue May 13 14:05:18 2025 -0700

    The fourteenth batch

    Signed-off-by: Junio C Hamano <gitster@pobox.com>

 Documentation/RelNotes/2.50.0.adoc | 11 +++++++++++
 1 file changed, 11 insertions(+)

commit 330a09e4a5160923f2aa4b30afab913a10a06adb
Merge: acfcd7ca93 ec727e189c
Author: Junio C Hamano <gitster@pobox.com>
Date:   Tue May 13 14:05:07 2025 -0700

    Merge branch 'kj/glob-path-with-special-char'

    "git add 'f?o'" did not add 'foo' if 'f?o', an unusual pathname,
    also existed on the working tree, which has been corrected.

    * kj/glob-path-with-special-char:
      dir.c: literal match with wildcard in pathspec should still glob
```

> 利用 `--author` 找出特定使用者的操作。

```
(master)
$ git log --author="Junio C Hamano"
$ git log --author="Junio C Hamano" --oneline
```

> 利用 `grep` 找出包含特定關鍵字的操作。

```
(master)
$ git log --grep "layer"
commit bd99d6e8db5e2c56dd24395e9711ee7ee564bf4f
Merge: 38758be7fa 8a9e27be82
Author: Junio C Hamano <gitster@pobox.com>
Date:   Mon May 12 14:22:48 2025 -0700

    Merge branch 'ps/object-store-cleanup'

    Further code clean-up in the object-store layer.

    * ps/object-store-cleanup:
      object-store: drop `repo_has_object_file()`
      treewide: convert users of `repo_has_object_file()` to `has_object()`
      object-store: allow fetching objects via `has_object()`
      object-store: move function declarations to their respective subsystems
      object-store: move and rename `odb_pack_keep()`
      object-store: drop `loose_object_path()`
      object-store: move `struct packed_git` into "packfile.h"
```

> 利用 `--merges` 找出 merge 的操做。

```
$ git log --merges
commit 330a09e4a5160923f2aa4b30afab913a10a06adb
Merge: acfcd7ca93 ec727e189c
Author: Junio C Hamano <gitster@pobox.com>
Date:   Tue May 13 14:05:07 2025 -0700

    Merge branch 'kj/glob-path-with-special-char'

    "git add 'f?o'" did not add 'foo' if 'f?o', an unusual pathname,
    also existed on the working tree, which has been corrected.

    * kj/glob-path-with-special-char:
      dir.c: literal match with wildcard in pathspec should still glob

commit acfcd7ca93e0f23893baee804e1f490c928363bd
Merge: 1551145edb 4ae2a3b418
Author: Junio C Hamano <gitster@pobox.com>
Date:   Tue May 13 14:05:07 2025 -0700

    Merge branch 'kh/docfixes'

    Docfixes.

    * kh/docfixes:
      doc: branch: fix inline-verbatim
      doc: reflog: fix `drop` subheading
```

> 使用 `git shortlog` 來統整各個作者的 commit 數量。

```
 (master)
$ git shortlog
A Large Angry SCM (15):
      Fix warning about non-void return in a void function.
      Subject: [PATCH] Add some documentation.
      Add some documentation.
      Add footnote about Thunderbird about trimming trailing WS.
      Documentation for git-daemon.
      Documentation for git-clone-dumb-http.
      Documentation for git-request-pull-script.
      Update Thunderbird specific hints.
      Docs for git-checkout-script.
      Docs for git-reset-script.
      Docs for git-show-rev-cache.
      Docs for git-build-rev-cache.
      Makefile fixups.
      Additional merge-base tests (revised)
      git-repack: re-enable parsing of -n command line option

A. Wilcox (1):
      subtree: honour USE_ASCIIDOCTOR when set

Aaron Crane (1):
      cvsimport: new -R option: generate .git/cvs-revisions mapping

Aaron Lindsay (1):
      send-email: avoid empty transfer encoding header
      
$ git shortlog -n
Junio C Hamano (26966):
      Typofix in git/show-files.
      No need to clean temp_git_file_* anymore
      ls-tree enhancements
      Add -z option to show-files
...

(master)
$ git shortlog -n -s
 26966  Junio C Hamano
  4545  Jeff King
  2361  Johannes Schindelin
  1945  Ævar Arnfjörð Bjarmason
  1824  Nguyễn Thái Ngọc Duy
  1554  Patrick Steinhardt
  1401  Shawn O. Pearce
  1290  René Scharfe
  1191  Elijah Newren
  1118  Linus Torvalds
...

 (master)
$ git shortlog -n -s -e
 26966  Junio C Hamano <gitster@pobox.com>
  4545  Jeff King <peff@peff.net>
  2361  Johannes Schindelin <Johannes.Schindelin@gmx.de>
  1945  Ævar Arnfjörð Bjarmason <avarab@gmail.com>
  1824  Nguyễn Thái Ngọc Duy <pclouds@gmail.com>
  1554  Patrick Steinhardt <ps@pks.im>
  1401  Shawn O. Pearce <spearce@spearce.org>
  1290  René Scharfe <l.s.r@web.de>
  1188  Elijah Newren <newren@gmail.com>
  1118  Linus Torvalds <torvalds@linux-foundation.org>
```

### 加快 Clone 遠端儲存庫的速度

> 計算 clone 的時間

```
$ time git clone https://github.com/git/git.git
```

> 只 clone 最新一次 commit 的部分。

```
$ time git clone https://github.com/git/git.git --depth=1
```

> 從 GitHub 上的 `origin` 抓取 tag v2.49.0 的 commit，但只抓取那個版本(不含完整歷史)，也就是一個淺層 clone 的方式。

```
$ git remote add origin https://github.com/git/git.git

$ git -c protocol.version=2 fetch origin v2.49.0 --depth=1
remote: Enumerating objects: 1, done.
remote: Counting objects: 100% (1/1), done.
remote: Total 1 (delta 0), reused 1 (delta 0), pack-reused 0 (from 0)
Unpacking objects: 100% (1/1), 782 bytes | 782.00 KiB/s, done.
From https://github.com/git/git
 * tag                     v2.49.0    -> FETCH_HEAD
```

> 把剛剛 fetch 下來的那個 tag 指向的 commit。

```
 $ git checkout FETCH_HEAD
Updating files: 100% (619/619), done.
Note: switching to 'FETCH_HEAD'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by switching back to a branch.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -c with the switch command. Example:

  git switch -c <new-branch-name>

Or undo this operation with:

  git switch -

Turn off this advice by setting config variable advice.detachedHead to false

HEAD is now at 683c54c999 Git 2.49
```

### 切換 Git 使用者

> 透過修改全域變數來達成的方便作法：

```
$ vi .git/config
```

```
[includeIf "gitdir:~/project/personal/"]
  path = ~/.gitconfig-personal

[includeIf "gitdir:~/project/work/"]
  path = ~/.gitconfig-work
```

### Git Alias

```
$ more ~/.gitconfig
[alias]
        st = status
        lg = log --oneline --graph --all
        cp = !sh -c 'git add . && git commit --amend --no-edit && git push origin $(git rev-parse --abbrev-ref HEAD) --force' -
```

### 修改最後一次 commit

> 修改最後一次 commit，會提示是否需要修改 commit message。

`git commit --amend`

> 修改最後一次 commit，但保留原先的 commit message。

`git commit --amend --no-edit`

> 假如我們修改了 gitconfig 文件，有了新的作者信息，我們希望更新最後一次提交的 author，可以通過下面的命令。

`git commit --amend --no-edit --reset-author`

### 建立 `.gitignore` 來讓 Git 忽略特定檔案

> 如果已經被 add 的檔案，就無法被後來才建立的 `.gitignore` 讓 Git 忽略(全域)，就必須使用 `git rm -f {file}` 來 untrack 檔該檔案。

```
*.out
```

[A collection of useful .gitignore templates.](https://github.com/github/gitignore)

> 也可以在目錄中(區域)的 `exclude` 檔案來定義要忽略的檔案。

```
(master)
$ cat .git/info/exclude
# git ls-files --others --exclude-from=.git/info/exclude
# Lines that start with '#' are comments.
# For a project mostly in C, the following would be a good set of
# exclude patterns (uncomment them if you want to use them):
# *.[oa]
# *~
.DS_Store
```

### 如何修改最後一次 commit

#### 針對 commit message

> 首先建立一個簡單的 python 檔案，然後將它 add 並 commit 後，發現其中的 `sum()` 錯了，所以就修正了程式。

```
(master)
$ cat hello.py
def add(a, b):
    # return sum(a - b)
    return sum(a + b)
```

```
(master)
$ git log
commit da9f87fac4b1b321d649f70b478e6f5d196b898e (HEAD -> master)
Author: Lucas Chen-陳永興 <Lucas.Chen@zyxel.com.tw>
Date:   Thu May 15 08:44:52 2025 +0800

    create add function
```

> 此時，我們就可以透過 `git commit --amend` 來將修正後的程式碼進行 commit。除此之外，如果想要修改 commit message，也可以再執行一次指令來達成。

```
(master)
$ git commit --amend -m "correct add function"
[master f124514] correct add function
 Date: Thu May 15 08:44:52 2025 +0800
 Committer: Lucas Chen-陳永興 <Lucas.Chen@zyxel.com.tw>
...
 1 file changed, 2 insertions(+)
 create mode 100644 hello.py
```

```
(master)
$ git log
commit f1245149c940045a1054064004c5aa511741fa1b (HEAD -> master)
Author: Lucas Chen-陳永興 <Lucas.Chen@zyxel.com.tw>
Date:   Thu May 15 08:44:52 2025 +0800

    correct add function
```

> 若是在程式碼已經被 push 到遠端儲存庫的情況下，就必須加上 `--force` 參數來強制執行。但要注意，如果程式碼早就被其他使用者 pull 到本機端，就不要使用這種方法以避免發生衝突。

```
(master)
$ git push origin master --force
```

#### 針對作者

> 當使用者將 `.git/config` 的作者資訊修改後，就可以透過 `git commit --amend` 加上 `--reset-author` 來修正 commit message 中的作者資訊。

```
(master)
$ cat .git/config
[core]
        repositoryformatversion = 0
        filemode = false
        bare = false
        logallrefupdates = true
        symlinks = false
        ignorecase = true
[user]
        email = test@gmail.com
        name = Jhon Doe
```

> 實際執行：

```
(master)
$ git commit --amend --reset-author -m "append print function and reset author info"
[master d3cb564] append print function and reset author info
 1 file changed, 4 insertions(+), 1 deletion(-)
```

> 成功！

```
(master)
$ git log
commit d3cb5648183387a1a9f96a2afb714e2c5a21a696 (HEAD -> master)
Author: Jhon Doe <test@gmail.com>
Date:   Thu May 15 09:17:26 2025 +0800

    append print function and reset author info

commit f1245149c940045a1054064004c5aa511741fa1b
Author: Lucas Chen-陳永興 <Lucas.Chen@zyxel.com.tw>
Date:   Thu May 15 08:44:52 2025 +0800

    correct add function
```

#### 跳過修改 commit message 或作者資訊

> `git commit --amend --no-edit`

```
(master)
$ git commit --amend --no-edit
[master c83942c] append print function and reset author info
 Date: Thu May 15 09:17:26 2025 +0800
 1 file changed, 4 insertions(+), 1 deletion(-)
```

### 如何修改最後 N 次 commit

* Git reset 的三種模式
    * `git reset` 是恢復到某次 commit 紀錄的指令，有三種常用模式：
        * mixed(預設模式): `git reset <commit-id>`
        * soft: `git reset --soft <commit-id>`
        * hard: `git reset --hard <commit-id>`
 
 > `git reset` 和 `git commit --amend` 同樣都會修改 git 的 commit 歷史。

> 前置作業中，先預先製作了3次 commit：

```
(master)
$ git log
commit a271c30d91be7443dfc52378f14f101b62285529 (HEAD -> master)
Author: Lucas Chen-陳永興 <Lucas.Chen@zyxel.com.tw>
Date:   Thu May 15 09:46:38 2025 +0800

    append test function

commit 3b0042b7d125a72b1205cc6fe261e5cbbe8c38f5
Author: Lucas Chen-陳永興 <Lucas.Chen@zyxel.com.tw>
Date:   Thu May 15 09:42:40 2025 +0800

    append hello function

commit e0531fba7cbaaba4c4d779439aa9f5886ff0f5c5
Author: Lucas Chen-陳永興 <Lucas.Chen@zyxel.com.tw>
Date:   Thu May 15 09:41:02 2025 +0800

    create add function
```

#### 範例一 - mixed 模式

> 回到首次 commit `e0531f`：

```
(master)
$ git reset e0531f
Unstaged changes after reset:
M       hello.py
```

> 執行 `git reset` 後，會發現 `hello.py` 變成 unstaged 的狀態。

```
(master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.py

no changes added to commit (use "git add" and/or "git commit -a")
```

> 所以，我們要再次進行 add 和 commit。

```
(master)
$ git add hello.py

(master)
$ git commit -m "append hello and test function 2nd time"
```

#### 範例二 - soft 模式

```
(master)
$ git reset --soft e0531f
```

> `hello.py` 已經在 staged 的狀態，所以可以直接 commit。

```
(master)
$ git status
On branch master
Changes to be committed:
  (use "git restore --staged <file>..." to unstage)
        modified:   hello.py

(master)
$ git commit -m "append hello and test function 3rd time"
```

#### 範例三 - hard 模式

> 請注意，hard 模式之下，`hello.py` 會完全恢復到最原始、也就是第一次 commit 的狀態。

```
(master)
$ git reset --hard e0531f
HEAD is now at e0531fb create add function

(master)
$ git status
On branch master
nothing to commit, working tree clean
```

#### 範例四 - mixed 模式的進階用法

> 首先，請先將 `hello()` 寫回到 `hello.py`，然後進行 mixed reset。

```
(master)
$ git reset e0531f
Unstaged changes after reset:
M       hello.py

NT200608@NT200608-PC01 MINGW64 /d/git/git-demo (master)
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   hello.py

no changes added to commit (use "git add" and/or "git commit -a")
```

> 可以發現 `hello.py` 還是有 `hello()` 的狀態。

```
(master)
$ cat hello.py
def add(a, b):
    return sum(a + b)

def hello(s)
    print(s)
```

> 此時就可以透過 `git checkout hello.py` 來讓這個檔案完全恢復到最原始的狀態。

```
(master)
$ git checkout hello.py
Updated 1 path from the index

(master)
$ cat hello.py
def add(a, b):
    return sum(a + b)
```

### `git stash`

#### 儲存變更

> 把目前工作目錄和暫存區的變更(是 tracked 狀態的)暫存起來。

```
$ git stash # 或 git stash push -m "stash test"
```

> 接下來，可以進一步把 untracked 的變更也一起納入暫存。

```
$ git stash -u # 或 --include-untracked
```

#### 檢視 stash 清單

```
$ git stash list
```

#### 還原(取出)並移除 stash

> 還原(取出)最近被暫存的變更，同時從 stash 清單中移除這筆紀錄。

```
git stash pop
```

#### 刪除 stash 項目

> 刪除指定項目：

```
$ git stash drop stash@{0}
```

> 刪除所有 stash：

```
$ git stash clear
```

### `git worktree`

> ✅情境：當使用者正在開發 `dev` 分支，突然要處理一個緊急 hotfix，可以快速建一個 worktree 不影響原本的目錄。

```
$ git worktree add ../my-feature-branch feature-branch
```

> 這會在 `../my-feature-branch` 建立一個新的資料夾，並且檢出 feature-branch(如果分支不存在會自動建立)。

> 列出所有 worktree

```
$ git worktree list
```

> 移除 worktree

```
$ git worktree remove ../hotfix-bug
```

### Git Submodule

> 讓使用者可以在一個 Git 儲存庫中引用另一個 Git 儲存庫，被稱為「子模組(submodule)」。這在需要將某些外部程式碼(如第三方函式庫、共用模組)與主專案一起管理時非常有用。

### `git cherry-pick`


> `git cherry-pick` 是 Git 中的一個指令，用來將其他分支上的單一或多個提交(commit)套用到目前分支上，而不需要進行整個合併(merge)或重放整個歷史(rebase)。

### `git-format-patch`

> 為 e-mail 發送準備 patch。
