# Lesson 06: Deploy a basic website on aws using EC2

## 什麼是 DynamoDB？

* Serverless NoSQL database 服務

![dynamodb-partition-key-1](https://github.com/user-attachments/assets/a3d889fd-1bab-4dbf-b97e-0be6799dd65a)
> 圖片參考來源：AWS Blogs - Choosing the Right DynamoDB Partition Key
> 官方文章連結：[https://aws.amazon.com/tw/blogs/database/choosing-the-right-dynamodb-partition-key/](https://aws.amazon.com/tw/blogs/database/choosing-the-right-dynamodb-partition-key/)

* DynamoDB 支援的 Primary Key 類型：Partition Key 或 Partition Key + Sort Key

![dynamodb-partition-key-2](https://github.com/user-attachments/assets/4f5eb800-f6b3-465f-af0e-a2d7db9270a7)
> 圖片參考來源：AWS Blogs - Choosing the Right DynamoDB Partition Key
> 官方文章連結：[https://aws.amazon.com/tw/blogs/database/choosing-the-right-dynamodb-partition-key/](https://aws.amazon.com/tw/blogs/database/choosing-the-right-dynamodb-partition-key/)

* DynamoDB 會先把 partition key 值進行雜湊，再根據雜湊值決定資料要配置到哪個實體分割區(partition)，也就是說每個 table 都會配置到一個或多個 partition 上
* 具有相同 partition key 的 item 通常會被放在同一個 partition 上，方便 query 時一次取得整個 collection (一組具有相同 partition key、不同 sort key 的 item)
* 若單一欄位值「重複率高」，只有少數幾個可能值(例如：`status`(active/inactive))，就可以使用組合鍵(composite attribute)，例如將 `customerid#productid#countrycode` 作為 partition key、`order_date` 作為 sort key 這種字串拼接的方式

## 1. 建立資料表

於主控台首頁的服務選單中選擇DynamoDB

![DynamoDB](https://github.com/user-attachments/assets/76777fb3-d835-457c-bb2e-6e86fd040da3)

於建立資源區塊選擇建立資料表

![建立資源-建立資料表](https://github.com/user-attachments/assets/a697a03d-468c-4b6e-9c56-c3ff0935a0e7)

輸入資料名稱和分區索引鍵

![建立資料表-資料表詳細資訊](https://github.com/user-attachments/assets/1579d11b-1cd7-40f8-bed9-f41248ff21c8)

完成後選擇建立資料表

![建立資料表](https://github.com/user-attachments/assets/a4d180c7-584a-4e85-b6ad-c1f837bcf6bd)

成功建立後的資料表

![資料表](https://github.com/user-attachments/assets/be3dcf45-6494-4cd1-8309-78a863c98469)

使用程式功能新增的電影資料可以從探索項目中檢視

![探索項目](https://github.com/user-attachments/assets/595a3af5-9b4c-45f5-b970-7a36a45ba5fc)

## 2. 根據 Flask 框架的規範，依照以下結構在 EC2 建立目錄和檔案

```
│   flaskproj
│   ├── app.py  # 定義 View Function
│   ├── controller.py
│   ├── templates/  # HTML Template
│       ├── index.html
│       └── edit.html
```

## 3. `controller.py` - 資料庫操作

### 3.1. 初始化
* 使用 `boto3` 連接 AWS DynamoDB，region 為 `us-east-2`
* Table name 為 `Movie`
* Primary key 為 `id`（字串型別）

### 3.2. CRUD 操作

| 函式名稱                          | 功能說明                          |
|:--------------------------------- |:--------------------------------- |
| `write_to_movie(title, director)` | 新增一筆電影資料，產生 UUID 為 id |
| `update_in_movie(id, data)`       | 更新電影的 `title` 和 `director`  |
| `delete_from_movie(id)`           | 刪除指定的電影                    |
| `get_movie()`                     | 取得一筆特定電影資料：            |
| `get_all_movies()`                | 撈取整個資料表內容(使用 scan)     |

```=python
# controller.py
from boto3 import resource as boto3_resource
import uuid

# Initialize the DynamoDB resource with a specific AWS region
resource = boto3_resource(
    'dynamodb',
    region_name='us-east-2'
)

# Reference the Movie table object
MovieTable = resource.Table('Movie')

# Retrieve a single movie by ID for editing
def get_movie(id):
    response = MovieTable.get_item(
        Key={
            'id': id
        },
    )
    return response.get('Item')

# Insert a new movie into the table
def write_to_movie(title, director):
    id = str(uuid.uuid4())  # Generate unique ID
    MovieTable.put_item(
        Item={
            'id': id,
            'title': title,
            'director': director
        }
    )
    return id

# Update the title and director of an existing movie
def update_in_movie(id, data: dict):
    response = MovieTable.update_item(
        Key={'id': id},
        UpdateExpression="SET title = :title, director = :director",
        ExpressionAttributeValues={
            ':title': data['title'],
            ':director': data['director']
        },
        ReturnValues="UPDATED_NEW"
    )
    return response

# Delete a movie by its ID
def delete_from_movie(id):
    response = MovieTable.delete_item(
        Key={'id': id}
    )
    return response

# Retrieve all movies from the table
def get_all_movies():
    response = MovieTable.scan()
    return response.get('Items', [])
```

## 4. `app.py` - Flask 應用程式後端

### 4.1. 路由定義與說明：

| 路由           | 方法       | 說明                                               |
|:-------------- |:---------- |:-------------------------------------------------- |
| `/`            | GET        | 顯示所有電影(`index.html`)                         |
| `/add`         | POST       | 新增一筆電影資料(`write_to_movie()`)               |
| `/edit/<id>`   | GET / POST | 編輯電影資料（顯示 `edit.html`；送出更新後的內容） |
| `/delete/<id>` | GET        | 刪除指定的電影                                     |

```=python
# app.py
from flask import Flask, request, render_template, redirect, url_for
import controller as dynamodb  # Import DynamoDB controller functions

app = Flask(__name__)

# Home route: display all movies or search by comma-separated IDs
@app.route('/', methods=['GET', 'POST'])
def index():
    movies = dynamodb.get_all_movies()
    return render_template('index.html', movies=movies)

# Route for adding a new movie
@app.route('/add', methods=['POST'])
def add_movie():
    data = request.form
    title = data['title']
    director = data['director']
    new_id = dynamodb.write_to_movie(title, director)  # Save new movie
    movies = dynamodb.get_all_movies()  # Refresh movie list
    return render_template('index.html', movies=movies)

# Route for editing an existing movie (GET to display, POST to update)
@app.route('/edit/<id>', methods=['GET', 'POST'])
def edit_movie(id):
    if request.method == 'POST':
        title = request.form['title']
        director = request.form['director']
        dynamodb.update_in_movie(id, {'title': title, 'director': director})
        return redirect(url_for('index'))  # Redirect back to home after update
    movie = dynamodb.get_movie(id)
    return render_template('edit.html', movie=movie)

# Route for deleting a movie
@app.route('/delete/<id>')
def delete_movie(id):
    dynamodb.delete_from_movie(id)
    return redirect(url_for('index'))  # Redirect back to home after deletion

# Run the Flask app
if __name__ == '__main__':
    app.run(debug=True, host="0.0.0.0", port=5000)
```

## 5. `index.html` - 主畫面模板

![add movie   movie list](https://github.com/user-attachments/assets/bb0ae7d2-ac33-4e6e-b88e-71a7ada86ab2)

### 功能說明

* 新增電影
  * 顯示欄位：`Movie Title`, `Director Name`
  * 按鈕動作：以 POST 送出資料至 `/add`

* 電影清單

  * 顯示欄位：`Movie Title`, `Director Name`
  * 按鈕動作：
    * `Edit`(連結至 `/edit/<id>`)
    * `Delete`(連結至 `/delete/<id>`)

```=html
<!-- index.html -->
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <title>Movies</title>
    <!-- Include Bootstrap CSS for styling -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body class="bg-light">
<div class="container py-5">
    <!-- Add new movie -->
    <div class="fw-bold mb-3">Add Movie</div>
    <form method="POST" action="/add" class="row g-3 mb-5">
        <div class="col-md-4">
            <!-- Input for movie title -->
            <input type="text" name="title" class="form-control" placeholder="Movie Title" required>
        </div>
        <div class="col-md-4">
            <!-- Input for director name -->
            <input type="text" name="director" class="form-control" placeholder="Director Name" required>
        </div>
        <div class="col-md-2">
            <!-- Submit button to add movie -->
            <button type="submit" class="btn btn-primary w-100">Add</button>
        </div>
    </form>

    <!-- Display movie list -->
    <div class="fw-bold mb-3">List Movie</div>
    {% if movies %}
    <table class="table table-striped">
        <thead>
        <tr>
            <th>Movie Title</th>
            <th>Director Name</th>
            <th>Options</th>
        </tr>
        </thead>
        <tbody>
        {% for movie in movies %}
        <tr>
            <td>{{ movie.title }}</td>
            <td>{{ movie.director }}</td>
            <td>
                <!-- Edit and delete buttons for each movie -->
                <div class="d-inline-flex gap-1">
                    <a href="/edit/{{ movie.id }}" class="btn btn-success btn-sm">Edit</a>
                    <a href="/delete/{{ movie.id }}" class="btn btn-danger btn-sm">Delete</a>
                </div>
            </td>
        </tr>
        {% endfor %}
        </tbody>
    </table>
    {% endif %}
</div>
</body>
</html>
```

## 6. `edit.html` - 編輯畫面模板

![edit movie](https://github.com/user-attachments/assets/3d1e7806-4ead-43ea-a12d-5d85cf6b1278)

### 功能說明

* 顯示指定電影的資料
* `Done`：儲存修改
* `Cancel`：返回首頁 `/`

```=html

<!-- edit.html -->
<!DOCTYPE html>
<html lang="zh-Hant">
<head>
    <meta charset="UTF-8">
    <title>Edit Movie</title>
    <!-- Include Bootstrap CSS for styling -->
    <link rel="stylesheet" href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0/dist/css/bootstrap.min.css">
</head>
<body class="bg-light">
<div class="container py-5">
    <h3 class="mb-4">Edit Movie</h3>
    
    <!-- Form to edit existing movie information -->
    <form method="POST" class="row g-3">
        <div class="col-6">
            <!-- Pre-filled input for movie title -->
            <input type="text" name="title" class="form-control" value="{{ movie.title }}" required>
        </div>
        <div class="col-6">
            <!-- Pre-filled input for director name -->
            <input type="text" name="director" class="form-control" value="{{ movie.director }}" required>
        </div>
        <div class="col-12">
            <!-- Submit button to save changes -->
            <button type="submit" class="btn btn-success">Done</button>
            <!-- Link to cancel and go back to home page -->
            <a href="/" class="btn btn-secondary">Cancel</a>
        </div>
    </form>
</div>
</body>
</html>
```
