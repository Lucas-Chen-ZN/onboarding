# aws from scratch

## 前置作業

### 1. 由直屬主管給予 AWS Account 資訊：
   * Account ID (zyxel-entrance)
   * IAM username
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
