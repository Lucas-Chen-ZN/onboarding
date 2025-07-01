# Lesson 11: Magento 2

![MVVM](https://github.com/user-attachments/assets/8108817c-589e-4fa8-9e41-697e875dd70a)
> 圖片參考來源：SwiftUI Architectures: Model-View, Redux & MVVM - ViewState MVVM
> 圖片參考連結：https://quickbirdstudios.com/blog/swiftui-architecture-redux-mvvm/

## MVVM 框架

### Model

* 負責層級：資料與商業邏輯
* 功能：
  * 定義商業邏輯
  * 存取資料庫

### View

* 負責層級：UI
* 功能：
  * 畫面呈現與資料視覺化
  * 僅負責顯示資料(不處理商業邏輯)
  * 以 `.phtml` 檔實作，`.phtml` 檔會和 Layout 的 `.xml` 檔組成 binder

### ViewModel

* 負責層級：介於 Model 和 View 之間的中介
* 功能：
  * 從 Model 層獲取資料並進行轉換
  * 將資料整理成 View 需要的格式
  * 操作 Model 來處理商業邏輯和使用者的 Request

---

> 以下內容為閱讀 [Magento 2 Tutorial: How to Build a Complete Module](https://www.toptal.com/magento/magento-2-tutorial-building-a-complete-module) 之筆記

---

## Magento 2的 Source Code 分層架構 - 以處理網頁和 static 區塊的 module-cms 為例

```
~# cd /var/www/magento2/vendor/magento/module-cms
/var/www/magento2/vendor/magento/module-cms# ll
total 116
drwxrws---  16 www-data www-data  4096 Jun 19 14:09 ./
drwxrws--- 386 www-data www-data 20480 Jun 19 14:09 ../
drwxrws---   3 www-data www-data  4096 Jun 19 14:09 Api/  # Service Interface 與 Data Interface
drwxrws---   4 www-data www-data  4096 Jun 19 14:09 Block/  # ViewModel
drwxrws---   2 www-data www-data  4096 Jun 19 14:09 Command/
drwxrws---   6 www-data www-data  4096 Jun 19 14:09 Controller/  # 處理路由
drwxrws---   3 www-data www-data  4096 Jun 19 14:09 Helper/  # 輔助性質功能函式
-rw-rw----   1 www-data www-data 10364 Jun  2 21:42 LICENSE.txt
-rw-rw----   1 www-data www-data 10376 Jun  2 21:42 LICENSE_AFL.txt
drwxrws---  11 www-data www-data  4096 Jun 19 14:09 Model/  # Model(資料 object，實作 Data Interface) + ResourceModel(ORM，實際執行 query) + Collection (用來操作 ORM，組裝出符合的 query set)
drwxrws---   2 www-data www-data  4096 Jun 19 14:09 Observer/  # 監聽事件，被事件觸發後會建立一個 Model object 來執行所需的商業邏輯
-rw-rw----   1 www-data www-data  1326 Jun  2 21:42 README.md
drwxrws---   3 www-data www-data  4096 Jun 19 14:09 Setup/  # Migration class，負責建立 table 和 data
drwxrws---   5 www-data www-data  4096 Jun 19 14:09 Test/  # Unit test
drwxrws---   3 www-data www-data  4096 Jun 19 14:09 Ui/  # UI element
drwxrws---   3 www-data www-data  4096 Jun 19 14:09 ViewModel/
-rw-rw----   1 www-data www-data   987 Jun  2 21:42 composer.json
drwxrws---   6 www-data www-data  4096 Jun 19 14:09 etc/  # 模組組態設定
drwxrws---   2 www-data www-data  4096 Jun 19 14:09 i18n/  # 翻譯用 .csv 檔
-rw-rw----   1 www-data www-data   241 Jun  2 21:42 registration.php
drwxrws---   4 www-data www-data  4096 Jun 19 14:09 view/  # 前端 .xml 和 .phtml 檔
```

![Magento 2 module](https://github.com/user-attachments/assets/9abb4011-ac12-4ec0-9e4a-7e450534a528)

### Presentation Layer

* 功能：
  * 使用者與系統互動的 UI
  * 包含：
 Block (ViewModel)
    * Controller - 處理 Request 相關流程
    * CSS、JS
    * Layout - 定義網頁區塊與結構
    * Template(負責顯示介面的 `.phtml` 檔)
    
    
  * 可呼叫 Service Layer 中的 Service Contracts。
  * 有時會與部分商業邏輯（Business Logic）重疊。

### Service Layer

* 功能：
  * 是 展示層與領域層的橋樑。
  * 提供資源存取服務（如資料操作、商業邏輯轉接）。
  * 實作 Service Contracts（PHP Interface 定義）。
  * 透過 依賴注入（di.xml） 管理服務的注入與擴充。
  * 優勢：可在不破壞系統的前提下調整商業邏輯或資源模型。

### Domain Layer

* 功能：
  * 定義與管理 商業邏輯，不含資料庫細節。
  * 包含如 Customer、Product 等 Magento 資料物件（Model）。
  * 定義資料可被執行的操作（如新增、計算、驗證等邏輯）。

### Persistence Layer

* 功能：
  * 負責資料庫的 CRUD 操作（Create、Read、Update、Delete）。
  * 透過 Resource Model 存取與修改資料。
  * 包含直接操作 SQL 的邏輯。
