# Lesson 2: aws from scratch

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

![Amazon Route 53, S3, CloudFront](https://github.com/user-attachments/assets/a9d56f42-0008-4c31-a927-3eb79ab84937)
> 圖片參考來源：AWS Blogs - How to Protect Your Web Application Against DDoS Attacks by Using Amazon Route 53 and an External Content Delivery Network  
> 官方文件連結：[https://aws.amazon.com/tw/blogs/security/how-to-protect-your-web-application-against-ddos-attacks-by-using-amazon-route-53-and-a-content-delivery-network/](https://aws.amazon.com/tw/blogs/security/how-to-protect-your-web-application-against-ddos-attacks-by-using-amazon-route-53-and-a-content-delivery-network/)

### 
