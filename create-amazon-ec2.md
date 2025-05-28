# Create Amazon EC2

## 1. 由主控台首頁左上角的運算中找到EC2

![服務 運算 EC2](https://github.com/user-attachments/assets/1b64d438-d38c-4862-9c1f-e349e245f560)

## 2. 啟動執行個體

![啟動執行個體](https://github.com/user-attachments/assets/b458b89a-d2f3-4416-b329-23a1321b15d7)

## 3. 進入 EC2 預設登陸頁面

![EC2 預設登陸頁面](https://github.com/user-attachments/assets/c491ea79-87e2-44bc-a7a3-91c5e3478197)

## 4. 建立安全群組

於網路和安全分類下的安全群組建立安全群組

![網路和安全 安全群組](https://github.com/user-attachments/assets/de0c0969-fddc-44d0-a28d-69d0e754b0b2)

配置基本詳細資訊、傳入規則和傳出規則

![建立安全群組](https://github.com/user-attachments/assets/ba3533c7-eb6f-4b5a-8a74-a3301459846c)

已成功建立安全群組

![安全群組已成功建立](https://github.com/user-attachments/assets/24a36e9c-88fe-4445-98c7-08f484c4313e)

## 5. 配置執行個體

輸入名稱和標籤

![名稱和標籤](https://github.com/user-attachments/assets/5f29e5ad-b8bc-44c7-9c55-454594ceeb1c)

選擇應用程式和作業系統映像：Ubuntu Server 24.04 LTS (HVM), SSD Volume Type

![應用程式和作業系統映像](https://github.com/user-attachments/assets/9149f419-82e8-49b7-a69e-3622a0ba3fb2)

選擇 t2.micro

![執行個體類型](https://github.com/user-attachments/assets/be0861da-f14f-4850-addd-4fe9dbcfae31)

建立新的金鑰對

![金鑰對](https://github.com/user-attachments/assets/85ab28b5-d150-4024-81f4-4aa229362265)

輸入金鑰對名稱、選擇 RSA 金鑰對類型並選擇搭配 PuTTY 使用的私有金鑰檔案格式，成功建立金鑰對後，請將 .ppk 檔案妥善保存

![建立金鑰對](https://github.com/user-attachments/assets/99acf6a3-2ad1-4186-94bf-85c84a823a5d)

選擇編輯來進行網路設定，其中記得選取之前建立、現有的安全群組

![網路設定](https://github.com/user-attachments/assets/055db52a-f0de-4c1e-8e67-87b5f5003b2e)

設定 10GiB 容量儲存空間

![設定儲存](https://github.com/user-attachments/assets/939e578e-218e-48cd-86db-5fdc7fb468ac)

啟動執行個體

![啟動執行個體](https://github.com/user-attachments/assets/5d3cc828-06d9-4dca-bc46-d5267c4a70df)

成功建立執行個體

![已成功啟動執行個體](https://github.com/user-attachments/assets/72289fc9-8891-44c2-9463-758611468566)

## 6. 建立快照

於 Elastic Block Store 分類下的快照建立快照

![Elastic Block Store 快照](https://github.com/user-attachments/assets/16ee5c5e-a02c-46b7-89f6-bdc9aa139cf4)

選擇執行個體快照來源和 ID、輸入快照詳細資訊並建立快照

![建立快照](https://github.com/user-attachments/assets/e5405b29-e62b-48e4-a30b-6d84b1452d98)

成功建立快照

![已成功建立快照](https://github.com/user-attachments/assets/0a84996f-1d4c-479f-9f8d-1328a51f0935)

## 7. 連線

選擇欲連線的執行個體

![連線](https://github.com/user-attachments/assets/37741c3c-5f3b-4f06-a98e-6731b695cd19)

### 7.1. EC2 Instance Connect

選擇使用 EC2 Instance Connect 連線

![連線至執行個體](https://github.com/user-attachments/assets/b0ae5f4f-a462-4f47-85ba-87126ed788ce)

成功於瀏覽器建立連線

![連線成功畫面](https://github.com/user-attachments/assets/c5c33cc6-9bd3-4a1e-bb42-8d36ebf86e9e)

### 7.2. SSH 用戶端 - PuTTY

安裝並開啟 PuTTY，透過 Connection -> SSH 進入 Auth Credentials to authenticate with 頁面，於 Private key file for authentication 選擇金鑰對

![PuTTY Connection SSH Auth Credentials to authenticate with](https://github.com/user-attachments/assets/53a06459-336d-43be-91df-31aed8208d36)

於 Session 頁面 Host Name (or IP address)輸入個體的公有 IPv4 位址、Sessions 名稱後，選擇建立連線

![Session](https://github.com/user-attachments/assets/f06b85f6-8c99-4aaa-a519-34b2b38f1385)

選擇 Accept 或 Connect Once

![PuTTY Security Alert](https://github.com/user-attachments/assets/50c13b73-186b-4fa0-84c8-578d5fd27f2d)

成功於 PuTTY 建立連線

![連線成功畫面(1)](https://github.com/user-attachments/assets/01fadd33-0b95-4717-a83b-cba2f52737ea)
