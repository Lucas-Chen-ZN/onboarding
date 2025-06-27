# Lesson 07: Deploy a basic website on aws using Amplify

## 參考範例

* [Deploy Your First Web App on AWS with AWS Amplify, Lambda, DynamoDB and API Gateway](https://dev.to/juliafmorgado/deploy-your-first-web-app-on-aws-with-aws-amplify-lambda-dynamodb-and-api-gateway-2ah7)
* [Serverless Web App Development Made Easy: A Complete Guide with AWS Amplify, DynamoDB, Lambda and API Gateway](https://kevinkiruri.medium.com/serverless-web-app-development-made-easy-a-complete-guide-with-aws-amplify-dynamodb-lambda-and-052daf5b978d)

## 1. 將前端程式碼部署到 AWS Amplify

把 `index.html` 壓縮到壓縮檔案再上傳至 Amplify

```=html
<!-- index.html -->
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Movies</title>
  <!-- 引入 Bootstrap -->
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet">

  <script>
    // 定義呼叫 API 的函式，接收電影標題(title)、導演(director)、年份(year)作為參數
    let callAPI = (title, director, year) => {
      // 建立標頭物件，配置傳送資料格式為 JSON
      let myHeaders = new Headers();
      myHeaders.append("Content-Type", "application/json");

      // 將輸入資料轉為 JSON 字串
      let raw = JSON.stringify({
        "title": title,
        "director": director,
        "year": year
      });

      // 配置 Fetch API 的參數
      let requestOptions = {
        method: 'POST',           // 使用 POST 方法
        headers: myHeaders,       // request 的標頭
        body: raw,                // 傳送的 JSON 主體
        redirect: 'follow'        // 自動處理重新導向
      };

      // 使用 Fetch API 傳送 request 到指定 API URL
      fetch("https://i9i4133m9j.execute-api.us-east-2.amazonaws.com/web-app-stage", requestOptions)  // API key
        .then(response => response.text())              // 將回應轉為文字格式
        .then(result => alert(JSON.parse(result).message)) // 解析回傳訊息並以 alert 顯示
        .catch(error => console.log('error', error));   // 錯誤處理
    }
  </script>
</head>
<body>
  <form class="container mt-4">
    <h1>Add Movie</h1>
    <div class="row g-2 align-items-center">
      <!-- 電影標題輸入欄位 -->
      <div class="col-md-3">
        <input type="text" class="form-control" id="title" placeholder="Movie Title">
      </div>
      <!-- 導演輸入欄位 -->
      <div class="col-md-3">
        <input type="text" class="form-control" id="director" placeholder="Director Name">
      </div>
      <!-- 年份輸入欄位 -->
      <div class="col-md-3">
        <input type="text" class="form-control" id="year" placeholder="Year">
      </div>
      <!-- 送出按鈕，點擊時呼叫 callAPI 並傳入三個欄位的值 -->
      <div class="col-md-3">
        <button type="button" class="btn btn-primary"
          onclick="callAPI(document.getElementById('title').value, document.getElementById('director').value, document.getElementById('year').value)">
          Submit
        </button>
      </div>
    </div>
  </form>
</body>
</html>
```

成功完成部署的 Movies 應用程式

![AWS Amplify](https://github.com/user-attachments/assets/8132b706-3da8-4eca-bb5f-25de25f43c6f)

開啟網域 URL 即可檢視前端介面

![Add Movie](https://github.com/user-attachments/assets/9f90b290-965a-4600-aa43-a1839df6c6de)

## 2. 建立 Lambda 函式

---

什麼是 AWS Lambda？

* Lambda 是一種 serverless、event-driven 的運算服務
* 只需要撰寫 Lambda 函式程式碼，Lambda 就會分配包含 CPU、記憶體、網路和其他運算資源的主機群給函式來執行

---

將 Lambda 函數的程式碼複製到程式碼來源中

```=python
import json
import boto3
import uuid
from time import gmtime, strftime

dynamodb = boto3.resource('dynamodb')
table = dynamodb.Table('Movies')
now = strftime("%a, %d %b %Y %H:%M:%S +0000", gmtime())

def lambda_handler(event, context):
    movie_id = str(uuid.uuid4())  # 自動產生唯一的 UUID
    
    # 從 event 取得資料
    title = event.get('title', '')
    director = event.get('director', '')
    year = event.get('year', '')

    # 寫入 DynamoDB
    response = table.put_item(
        Item={
            'ID': movie_id,
            'Title': title,
            'Director': director,
            'Year': year,
            'LatestGreetingTime': now
        })

    return {
        'statusCode': 200,
        'body': json.dumps(f'Hello from Lambda, {title} {director} {year}')
    }
```

可以先建立測試事件後按下 Test (Ctrl+Shift+I) 進行測試，沒問題的話再按下 Deploy (Ctrl+Shift+U) 部署函數

![Lambda 函數 my-movie-app-function 程式碼來源](https://github.com/user-attachments/assets/f5e89037-abeb-4900-b471-5d3e45d96bb8)

## 3. 使用 API Gateway 建立 REST API 用於驗證和處理 Request

---

什麼是 Amazon API Gateway？

![Product-Page-Diagram_Amazon-API-Gateway-How-Works](https://github.com/user-attachments/assets/967c821e-1563-4db7-b0eb-9f00772e052f)
> 圖片參考來源：AWS Documentation - What is Amazon API Gateway?  
> 原始資料授權條款：CC BY-SA 4.0  
> 官方文件連結：[https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html](https://docs.aws.amazon.com/apigateway/latest/developerguide/welcome.html)  

* 可以用來建立、發佈、維護、監控(AWS CloudTrail / Amazon CloudWatch)和保護(AWS WAF) REST (stateless)、HTTP (stateless) 和 WebSocket (stateful) API
* API Gateway 可作為應用程式連接後端服務(部署於 Amazon EC2 的 App、AWS Lambda 中的程式碼、各類 Web App)的「入口點」，使其能夠存取資料、執行商業邏輯或觸發功能

---


建立 POST 方法並開啟 CORS (Cross-Origin Resource Sharing)允許 Server 讓 Browser 可以與除其自身以外的任何來源(domain、protocol 或 port)的資源互動，最後執行部署 API

![API Gateway](https://github.com/user-attachments/assets/1618151c-0a69-4d2e-8033-d00459a958f2)

## 4. 建立 DynamoDB Table

建立名稱為 Movies，分區索引鑑為 ID (String) 的 table

![DynamoDB Movies](https://github.com/user-attachments/assets/3bcddfa7-2c69-4b90-8cc2-68af7aee395c)

## 5. 執行結果

於 Add Movie 輸入 Movie Title、Director Name 和 Year 後按下 Submit

![Add Movie (1)](https://github.com/user-attachments/assets/3a602cd6-234e-4a13-bd73-3136992a43ba)

可以看到 table 裡面有剛剛輸入的資料

![DynamoDB Movies (1)](https://github.com/user-attachments/assets/2c14d2f6-ceed-41f5-b4b2-80badc5f47ca)
