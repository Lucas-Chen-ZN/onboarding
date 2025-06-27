# Lesson 04: Create Amazon EC2

## 什麼是 EC2？

![instance-types-ec2](https://github.com/user-attachments/assets/6f5a43d0-43f9-4c24-b42f-0c3c6644898a)
> 圖片參考來源：AWS Documentation - What is Amazon EC2?  
> 原始資料授權條款：CC BY-SA 4.0  
> 官方文件連結：[https://docs.aws.amazon.com/zh_tw/AWSEC2/latest/UserGuide/concepts.html](https://docs.aws.amazon.com/zh_tw/AWSEC2/latest/UserGuide/concepts.html)

* 彈性擴充的雲端運算服務
* 提供使用者依照需求啟動 VM、設定儲存、網路和安全性等等功能
* 具有各種定價方式，包含[隨需](https://aws.amazon.com/tw/ec2/pricing/on-demand/) (隨用隨付)、[Savings Plans](https://aws.amazon.com/tw/savingsplans/compute-pricing/) (長期合約的年繳折扣) 和 [Spot](https://aws.amazon.com/tw/ec2/spot/pricing/) (釋出未使用的服務以提供更多的折扣，但必須面對系統失效的風險) 三種選項
  * 預估需求成本：[aws pricing calculator](https://calculator.aws/#/)

## 1. 由主控台首頁左上角的運算中找到EC2

![服務 運算 EC2](https://github.com/user-attachments/assets/1b64d438-d38c-4862-9c1f-e349e245f560)

## 2. 啟動執行個體，也就是在雲端運作的 VM

![啟動執行個體](https://github.com/user-attachments/assets/b458b89a-d2f3-4416-b329-23a1321b15d7)

## 3. 進入 EC2 預設登陸頁面

![EC2 預設登陸頁面](https://github.com/user-attachments/assets/7dd1e5da-a113-475a-9fdd-715f720c9920)

## 4. 配置執行個體

輸入名稱和標籤

![名稱和標籤](https://github.com/user-attachments/assets/5f29e5ad-b8bc-44c7-9c55-454594ceeb1c)

選擇應用程式和作業系統映像：Ubuntu Server 24.04 LTS (HVM), SSD Volume Type

![應用程式和作業系統映像](https://github.com/user-attachments/assets/9149f419-82e8-49b7-a69e-3622a0ba3fb2)

選擇 t2.micro

![執行個體類型](https://github.com/user-attachments/assets/be0861da-f14f-4850-addd-4fe9dbcfae31)

建立新的金鑰對

![金鑰對](https://github.com/user-attachments/assets/85ab28b5-d150-4024-81f4-4aa229362265)

輸入金鑰對名稱、選擇 RSA 金鑰對類型並選擇搭配 PuTTY 使用的私有金鑰檔案格式，成功建立金鑰對後，請將 .ppk 檔案妥善保存

![建立金鑰對](https://github.com/user-attachments/assets/d6280f70-86be-4d0c-a25e-3e7f7af1ef02)

選擇編輯來進行網路設定，其中記得選取之前建立、現有的安全群組

![網路設定](https://github.com/user-attachments/assets/f27ab17f-8b20-44f8-b751-6d7bf6c3d380)

---

## 什麼是 VPC？

![how-it-works-VPC](https://github.com/user-attachments/assets/f0b8f5e6-67f0-4ca5-9765-e95168e64f45)
> 圖片參考來源：AWS Documentation - What is Amazon VPC?  
> 原始資料授權條款：CC BY-SA 4.0  
> 官方文件連結：[https://docs.aws.amazon.com/zh_tw/vpc/latest/userguide/what-is-amazon-vpc.html](https://docs.aws.amazon.com/zh_tw/vpc/latest/userguide/what-is-amazon-vpc.html)  

* 上圖所示的範例 VPC，它為每個 Available Zone (在同一個 AWS Region 內) 均建立了一個 Subnet，各 Subnet 內部署了 EC2 Instance，並配置了 Internet Gateway，讓 VPC 內的資源能夠連接到 Internet

---

設定 10GiB 容量儲存空間

![設定儲存](https://github.com/user-attachments/assets/939e578e-218e-48cd-86db-5fdc7fb468ac)

啟動執行個體

![啟動執行個體](https://github.com/user-attachments/assets/54f94d45-9653-478a-b42a-c7e2597ca4ae)

成功建立執行個體

![已成功啟動執行個體](https://github.com/user-attachments/assets/28fed2de-d461-470a-8bc3-527d30915ec7)

## 5. 建立安全群組

![建立安全群組](https://github.com/user-attachments/assets/35cbfcea-41e7-42e1-9482-bb13e994563d)

於網路和安全的網路介面，開啟右上角動作清單選擇變更安全群組

![變更安全群組](https://github.com/user-attachments/assets/4e875ff0-f023-4024-bbce-3dc853398a91)

新增安全群組後儲存即可成功變更安全群組

![新增安全群組](https://github.com/user-attachments/assets/244defd7-006a-4827-9316-ad807b26bebf)

---

什麼是安全群組？

![security-group-overview](https://github.com/user-attachments/assets/cd86a576-0b9d-403a-8cbe-8a88bf8ef4f1)

> 圖片參考來源：AWS Documentation - Control traffic to your AWS resources using security groups  
> 原始資料授權條款：CC BY-SA 4.0  
> 官方文件連結：[https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-security-groups.html)  

* 安全群組(security group)是 VPC 中的「虛擬防火牆」，控制與其相關資源(例如 EC2 Instance)的 inbound 與 outbound 的流量
* 建立 VPC 時會自動產生一個預設 security group，後續亦可自行新增其它 security group  
* Inbound rule：定義「Source + Port range + Protocol」；Outbound rule：定義「Destination + Port range + Protocol」
* 只有符合 rule 的流量才可通過，例如：若允許 ICMP，可從本機 ping 到 instance；若未允許 SSH，則無法透過 SSH 與 instance 連線  

---

## 6. 連線

選擇欲連線的執行個體

![連線](https://github.com/user-attachments/assets/456538c3-829b-4886-b370-174c542aed81)

### 6.1. EC2 Instance Connect

選擇使用 EC2 Instance Connect 連線

![連線至執行個體](https://github.com/user-attachments/assets/0c5580b3-eca4-4c2a-951d-4f6fdea80f3e)

成功於瀏覽器建立連線

![連線成功畫面](https://github.com/user-attachments/assets/b14f3b94-5453-4767-b155-526e8dcefffe)

### 6.2. SSH 用戶端 - PuTTY

安裝並開啟 PuTTY，透過 Connection -> SSH 進入 Auth Credentials to authenticate with 頁面，於 Private key file for authentication 選擇金鑰對

![PuTTY Connection SSH Auth Credentials to authenticate with](https://github.com/user-attachments/assets/501d6b48-223b-4339-9b6d-9b9b90b7da60)

於 Session 頁面 Host Name (or IP address)輸入個體的公有 IPv4 位址、Sessions 名稱後，選擇建立連線

![Session](https://github.com/user-attachments/assets/5704043d-3d75-4c05-9c01-2c0bafab33e5)

選擇 Accept 或 Connect Once

![PuTTY Security Alert](https://github.com/user-attachments/assets/edc46371-82fe-4183-b25d-4301630277b8)

成功於 PuTTY 建立連線

![連線成功畫面(1)](https://github.com/user-attachments/assets/90a2bc0a-490b-40c3-b304-8a2199b5b614)
