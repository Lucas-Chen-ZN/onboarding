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
  * 以 `.phtml` 檔實作，`.phtml` 檔會和 Layout (定義網頁區塊與結構)的 `.xml` 檔組成 binder

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
    * Block (ViewModel)
    * Controller
    * CSS、JS
    * Layout
    * Template
  * 可呼叫 Service Layer 中的 Service Contracts
  * 有時會與部分商業邏輯重疊

### Service Layer

* 功能：
  * Presentation Layer 和 Domain Layer 的中介
  * 提供資源存取服務(如資料操作、商業邏輯轉接)
  * 實作 Service Contracts (PHP Interface 定義)
  * 透過依賴注入(di.xml)管理服務的注入與擴充
  * 可在不破壞系統的前提下調整商業邏輯或資源模型

### Domain Layer

* 功能：
  * 定義與管理商業邏輯，不含資料庫細節。
  * 包含如 Customer、Product 等 Magento 資料物件(Model)
  * 定義資料可被執行的操作(如新增、計算、驗證等邏輯)

### Persistence Layer

* 功能：
  * 負責資料庫的 CRUD 操作
  * 透過 Resource Model 存取與修改資料
 
## 建立 Blog 模組

1. 建立 `app/code/Toptal/Blog` 目錄，存放所有 Blog 模組的 Code
2. 建立三個註冊檔
 * `composer.json` - 在未使用 Composer 的狀況下提供 Composer 讀取而不出現錯誤訊息
 * `registration.php` - 呼叫 ComponentRegistrar::register 把 'Toptal_Blog' 加入 autoloader，讓 Magento 知道要去哪裡尋找模組的 Class 和 XML 檔

```
<?php
\Magento\Framework\Component\ComponentRegistrar::register(
    \Magento\Framework\Component\ComponentRegistrar::MODULE,'Toptal_Blog',__DIR__
);
```
   
 * `etc/module.xml` - 註冊 Magento 模組名稱、Magento 安裝版本、相依的兩個核心模組：`Magento_Directory` 和 `Magento_Config`

```
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="../../../../../lib/internal/Magento/Framework/Module/etc/module.xsd">
    <module name="Toptal_Blog" setup_version="0.1.0">
        <sequence>
            <module name="Magento_Directory" />
            <module name="Magento_Config" />
        </sequence>
    </module>
</config>
``` 

3. 啟用 Total_Blog 模組

```
bin/magento module:enable Toptal_Blog
```

4. 配置資料庫

| 欄位名稱   | 資料型態         | 是否可為 NULL | 鍵值 | 預設值            |
| ---------- | ---------------- | ------------- | ---- | ----------------- |
| post_id    | int(10) unsigned | NO            | PRI  | NULL              |
| title      | text             | NO            |      | NULL              |
| content    | text             | NO            |      | NULL              |
| created_at | timestamp        | NO            |      | CURRENT_TIMESTAMP |

建立為了進行 Migration 的 InstallSchema Class (路徑：`app/code/Toptal/Blog/Setup/InstallSchema.php`)，
透過 install method 實際建立 table，
執行 `./bin/magento setup:upgrade` 就可以開始進行 Migration

* 流程
  * 首次執行 `setup:upgrade` 時，Magento 發現 `setup_module` table 裡沒有 `Toptal_Blog` table
  * 先執行 InstallSchema 來建立 table
  * 建立成功後，Magento 會把 `Toptal_Blog` 的 版本 `0.1.0` 寫進 `setup_module` (記錄每個 module 的 migration 執行狀態)，表示「已執行過 migration」

```
<?php

namespace Toptal\Blog\Setup;

use \Magento\Framework\Setup\InstallSchemaInterface;
use \Magento\Framework\Setup\ModuleContextInterface;
use \Magento\Framework\Setup\SchemaSetupInterface;
use \Magento\Framework\DB\Ddl\Table;

/**
 * Class InstallSchema
 *
 * @package Toptal\Blog\Setup
 */
class InstallSchema implements InstallSchemaInterface
{
    /**
     * Install Blog Posts table
     *
     * @param SchemaSetupInterface $setup
     * @param ModuleContextInterface $context
     */
    public function install(SchemaSetupInterface $setup, ModuleContextInterface $context)
    {
        $setup->startSetup();

        $tableName = $setup->getTable('toptal_blog_post');

        if ($setup->getConnection()->isTableExists($tableName) != true) {
            $table = $setup->getConnection()
                ->newTable($tableName)
                ->addColumn(
                    'post_id',
                    Table::TYPE_INTEGER,
                    null,
                    [
                        'identity' => true,
                        'unsigned' => true,
                        'nullable' => false,
                        'primary' => true
                    ],
                    'ID'
                )
                ->addColumn(
                    'title',
                    Table::TYPE_TEXT,
                    null,
                    ['nullable' => false],
                    'Title'
                )
                ->addColumn(
                    'content',
                    Table::TYPE_TEXT,
                    null,
                    ['nullable' => false],
                    'Content'
                )
                ->addColumn(
                    'created_at',
                    Table::TYPE_TIMESTAMP,
                    null,
                    ['nullable' => false, 'default' => Table::TIMESTAMP_INIT],
                    'Created At'
                )
                ->setComment('Toptal Blog - Posts');
            $setup->getConnection()->createTable($table);
        }

        $setup->endSetup();
    }
}
```

若之後需要更新 schema，就需要建立 `app/code/Toptal/Blog/Setup/UpgradeData.php`

* 流程
    * 把 `module.xml` 中的 `setup_version` 改成 `0.1.1`
    * 執行 `./bin/magento setup:upgrade`，Magento 比對發現舊版本 0.1.0 < 新版本 0.1.1，必須升級
    * 升級完成後，把 `setup_module` 的版本欄更新為 0.1.1。

```
class UpgradeData implements UpgradeDataInterface
{
    public function upgrade(ModuleDataSetupInterface $setup, ModuleContextInterface $context)
    {
        $setup->startSetup();

        // 僅當舊版本 < 0.1.1 時才執行
        if (version_compare($context->getVersion(), '0.1.1', '<')) {
            $setup->getConnection()->insertMultiple(
                $setup->getTable('toptal_blog_post'),
                [
                    ['title'=>'Post 1', 'content'=>'First post content'],
                    ['title'=>'Post 2', 'content'=>'Second post content']
                ]
            );
        }

        $setup->endSetup();
    }
}
```

5. 定義 Resource Model - Post，也就是定義 Model 連接的 Table 和 Primary key 是什麼(路徑：`app/code/Toptal/Blog/Model/ResourceModel/Post.php`)

```
<?php

namespace Toptal\Blog\Model\ResourceModel;

use \Magento\Framework\Model\ResourceModel\Db\AbstractDb;

class Post extends AbstractDb
{
    /**
     * Post Abstract Resource Constructor
     * @return void
     */
    protected function _construct()
    {
        $this->_init('toptal_blog_post', 'post_id');
    }
}
```

6. 定義另一個 Resource Model - Collection，用來查詢資料庫中的多篇貼文

```
<?php
namespace Toptal\Blog\Model\ResourceModel\Post;

use \Magento\Framework\Model\ResourceModel\Db\Collection\AbstractCollection;

class Collection extends AbstractCollection
{
    /**
     * Remittance File Collection Constructor
     * @return void
     */
    protected function _construct()
    {
        $this->_init('Toptal\Blog\Model\Post', 'Toptal\Blog\Model\ResourceModel\Post');
    }
}
```


7. 建立 Data Interface (路徑：`app/code/Toptal/Blog/Api/Data/PostInterface.php`)

```
<?php

namespace Toptal\Blog\Api\Data;

interface PostInterface
{
    /**#@+
     * Constants for keys of data array. Identical to the name of the getter in snake case
     */
    const POST_ID               = 'post_id';
    const TITLE                 = 'title';
    const CONTENT               = 'content';
    const CREATED_AT            = 'created_at';
    /**#@-*/


    /**
     * Get Title
     *
     * @return string|null
     */
    public function getTitle();

    /**
     * Get Content
     *
     * @return string|null
     */
    public function getContent();

    /**
     * Get Created At
     *
     * @return string|null
     */
    public function getCreatedAt();

    /**
     * Get ID
     *
     * @return int|null
     */
    public function getId();

    /**
     * Set Title
     *
     * @param string $title
     * @return $this
     */
    public function setTitle($title);

    /**
     * Set Content
     *
     * @param string $content
     * @return $this
     */
    public function setContent($content);

    /**
     * Set Crated At
     *
     * @param int $createdAt
     * @return $this
     */
    public function setCreatedAt($createdAt);

    /**
     * Set ID
     *
     * @param int $id
     * @return $this
     */
    public function setId($id);
}
```

8. 建立 View

在前端中定義路由(路徑：`app/code/Toptal/Blog/etc/frontend/routes.xml`)

```
<?xml version="1.0"?>
<config xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="urn:magento:framework:App/etc/routes.xsd">
    <router id="standard">
        <route id="blog" frontName="blog">
            <module name="Toptal_Blog"/>
        </route>
    </router>
</config>
```

9. 在 Index 頁面列出文章清單

```
<?php

namespace Toptal\Blog\Controller\Index;

use \Magento\Framework\App\Action\Action;
use \Magento\Framework\View\Result\PageFactory;
use \Magento\Framework\View\Result\Page;
use \Magento\Framework\App\Action\Context;
use \Magento\Framework\Exception\LocalizedException;

class Index extends Action
{

    /**
     * @var PageFactory
     */
    protected $resultPageFactory;

    /**
     * @param Context $context
     * @param PageFactory $resultPageFactory
     *
     * @codeCoverageIgnore
     * @SuppressWarnings(PHPMD.ExcessiveParameterList)
     */
    public function __construct(
        Context $context,
        PageFactory $resultPageFactory
    ) {
        parent::__construct(
            $context
        );
        $this->resultPageFactory = $resultPageFactory;
    }

    /**
     * Prints the blog from informed order id
     * @return Page
     * @throws LocalizedException
     */
    public function execute()
    {
        $resultPage = $this->resultPageFactory->create();
        return $resultPage;
    }
}
```

10. 定義前端的 Layout 結構(路徑：)

```
<?xml version="1.0"?>
<page xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" layout="2columns-left" xsi:noNamespaceSchemaLocation="urn:magento:framework:View/Layout/etc/page_configuration.xsd">
    <body>
        <referenceContainer name="content">
            <block class="Toptal\Blog\Block\Posts"
                   name="posts.list"
                   template="Toptal_Blog::post/list.phtml" />
        </referenceContainer>
    </body>
</page>
```
11. 
