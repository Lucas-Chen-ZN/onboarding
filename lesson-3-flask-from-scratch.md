# Lesson 3: Flask from scratch

## 參考範例

* [Flask 入门教程](https://tutorial.helloflask.com/)

## 1. 更新 OS

```
~$ history
    1  sudo apt-get update
    2  sudo apt-get upgrade
    3  sudo apt-get install build-essential
    4  reboot
```

## 2. 檢視 Ubuntu 內建的 Python

```
~$ python3 --version
Python 3.12.3
```

## 3. 為 Flask 專案建立資料夾並改變工作目錄至該資料夾

```
~$ mkdir flaskproj
~$ cd flaskproj/
~/flaskproj$
```

## 4. 安裝 Python 專用的虛擬環境模組 venv

```
~/flaskproj$ sudo apt-get install python3.12-venv
```

## 5. 建立並啟動 Python 虛擬環境

```
~/flaskproj$ python3 -m venv env
~/flaskproj$ . env/bin/activate
(env) ...:~/flaskproj$
```

## 6. 安裝 Flask

```
(env) ...:~/flaskproj$ pip install flask
```

## 7. 撰寫一個簡單的網頁

```
(env) ...:~/flaskproj$ vi app.py
(env) ...:~/flaskproj$ cat app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return 'Hello!'

if __name__ == "__main__":
    app.run(debug=True, host="0.0.0.0", port=5000)
```

## 8. 啟動網頁

```
(env) ...:~/flaskproj$ ~/flaskproj$ flask run --debug --host=0.0.0.0 --port=5000
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:5000
 * Running on http://172.31.2.34:5000
Press CTRL+C to quit
 * Restarting with stat
 * Debugger is active!
 * Debugger PIN: 135-645-353
```

## 9. 成功顯示網頁

![網頁](https://github.com/user-attachments/assets/d8f3910f-1414-4dc8-af3e-26f66539ccec)
