# Lesson 03: aws from scratch

## 前置作業

### 1. 由直屬主管給予 AWS Account 資訊：
   * Account ID (zyxel-entrance)
   * IAM (Identity and Access Management) username
   * (預設) Password
### 2. 由 IAM user sign in 登入
![Amazon Web Services Sign-In](https://github.com/user-attachments/assets/0cab011a-fec3-4c08-9322-28d1a932f61c)

### 3. 取得存取許可

當主控台首頁的應用程式出現下圖的錯誤訊息，就必須透過切換角色來解決

![存取遭拒](https://github.com/user-attachments/assets/8b691c2b-ee48-4a92-a670-03c72669a2cd)

由主控台首頁右上角進入切換角色頁面

![主控台首頁](https://github.com/user-attachments/assets/c586aa0e-4a8b-4cb9-9d2e-8c879f0fb3a6)

依序填入 Account ID、IAM role name、Display name - optional 並選擇 Display color - optional

![Switch Role](https://github.com/user-attachments/assets/f300a43d-aec9-4454-9ded-ac371dcb2258)

區域可以選擇 us-east-1 或 us-east-2 (測試環境)

## Amazon Route 53, S3, CloudFront

* S3 (Simple Storage Service)
  * 是一種物件(object)儲存服務，物件由檔案本身和描述該檔案的中介資料(meatadata)組成，每個物件都有一個 key (key name)作為唯一識別碼
  * 以物件、階層式(hierarchical)或表格(tabular)形式將資料儲存在儲存貯體(bucket)中，建立 bucket 時需要指定名稱和 Region
  * Bucket 和其中的 object 預設為 private，可以設定 bucket policy、IAM policy、ACL 和 S3 access point 來管理存取控制權限
* Cloudfront
  * 是一種 CDN 服務，為了降低存取網頁資源時遇到的延遲，可以將使用者對於網頁資源的 request 自動導向至最低延遲的邊緣(edge)端點
  * 會將使用者建立的網頁資源快取到全世界的多個邊緣端點
* Route53
  * 是一種 DNS 服務
  * 包含 Domain name registration、DNS routing 和 Health checking (向網站資源，例如 Web server 發送自動 request，檢查其可用性)等三個主要功能

### 參考範例

* [利用 AWS S3, Cloudfront, Route 53 五步驟部署前端 React App](https://malik11217.medium.com/%E5%88%A9%E7%94%A8-aws-s3-cloudfront-route-53-%E9%83%A8%E7%BD%B2-react-%E5%89%8D%E7%AB%AF%E6%87%89%E7%94%A8%E7%A8%8B%E5%BC%8F-d9475b8ea971)
* [https://aws.amazon.com/tw/blogs/security/how-to-protect-your-web-application-against-ddos-attacks-by-using-amazon-route-53-and-a-content-delivery-network/](https://aws.amazon.com/tw/blogs/security/how-to-protect-your-web-application-against-ddos-attacks-by-using-amazon-route-53-and-a-content-delivery-network/)

![Amazon Route 53, S3, CloudFront](https://github.com/user-attachments/assets/a9d56f42-0008-4c31-a927-3eb79ab84937)
> 圖片參考來源：AWS Blogs - How to Protect Your Web Application Against DDoS Attacks by Using Amazon Route 53 and an External Content Delivery Network  
> 官方文章連結：[https://aws.amazon.com/tw/blogs/security/how-to-protect-your-web-application-against-ddos-attacks-by-using-amazon-route-53-and-a-content-delivery-network/](https://aws.amazon.com/tw/blogs/security/how-to-protect-your-web-application-against-ddos-attacks-by-using-amazon-route-53-and-a-content-delivery-network/)

1. 使用者從瀏覽器向 Route 53 發送 DNS request  
2. Route 53 有為 `example.com` domain 配置了一個託管區域(hosted zone)  
3. 該託管區域將提供以下 DNS record：  
   a. 若查詢的是 root domain (apex zone，例如 `example.com`)，則會回應指向 CloudFront Distribution 的別名資源記錄集(alias resource record set，類似 CNAME record)  
   b. 若請求的是 `www` 子網域，則回應一筆指向外部託管的 CDN 的 CNAME record  
4. CloudFront 將 request 轉送至 S3  
5. S3 收到 request 後，會將來自 `example.com` 的流量以安全的方式重新導向(HTTPS Redirect)到 `www.example.com`  

## Amazon SES

![arch_overview-diagram](https://github.com/user-attachments/assets/8899f5e0-72a3-4050-8610-1d6e5bcef814)
> 圖片參考來源：AWS Documentation - How email sending works in Amazon SES  
> 原始資料授權條款：CC BY-SA 4.0  
> 官方文件連結：[https://docs.aws.amazon.com/ses/latest/dg/send-email-concepts-process.html](https://docs.aws.amazon.com/ses/latest/dg/send-email-concepts-process.html)  

* 📤 傳送 Email
  1. 寄件人透過 Client 應用程式向 SES 發出送信的 Request
  2. 若 Request 沒問題，SES 回傳成功 Response 及 message ID；若 Request 失敗(格式錯誤、寄件人地址未先驗證等)，會收到錯誤 Response 或例外。

* 📥 SES 處理 Email
  3. SES 根據 Request 參數以組合並掃描 Email(防毒)，然後使用 SMTP 傳送給收件人的 ISP

* 📮 遞送結果
  * ✅ 成功傳送：ISP 接收並送達收件人
  * ❌ 退信(Bounce)
    * 硬退信(Hard Bounce)：ISP 因為收件人地址無效或在 SES 黑名單而拒絕接收，當硬退信通知從 ISP 送回 SES，SES 會透過 Email 或 SNS (Amazon Simple Notification Service)通知寄件人
    * 軟退信(Soft Bounce)：因為暫時性問題(收件人信箱已滿、網域不存在、ISP 太忙等)。SES 會在一個時間區間不斷重試，再決定是否通知退信
    * 投訴(Complaint)：收件人將 Email 標記為垃圾郵件，ISP 就會通知 SES，SES 再通知寄件人
    * 自動回覆(Auto-response)：ISP 會將「不在辦公室」(OOTO)的通知回傳給 SES，SES 將轉發給寄件人
   
## Amazon SQS

![sqs-message-lifecycle-diagram](https://github.com/user-attachments/assets/250aec3d-c1c7-48b8-afdd-97b6a65d4f4c)
> 圖片參考來源：AWS Documentation - What is Amazon Simple Queue Service?
> 原始資料授權條款：CC BY-SA 4.0  
> 官方文件連結：[https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html](https://docs.aws.amazon.com/AWSSimpleQueueService/latest/SQSDeveloperGuide/welcome.html)

* 由分散式系統的元件(例如 EC2、Lambda等)、Queue 和 Queue 裡面的 Message 組成
* Producer(生產者)：能將 Message 傳送至 Queue 的元件；Consumer(消費者)：從 Queue 中接收 Message 的元件
* Queue 負責儲存 Message，並將這些訊息以冗餘的方式分散存放於多個 Amazon SQS Server 上，以確保可用性與可靠性
