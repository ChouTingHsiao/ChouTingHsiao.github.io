---
title: 使用 Flask-SQLAlchemy 連接 MSSQL 進行 CRUD 
date: 2023-07-07 23:00:00
categories: Python
tags: [Python, Flask, MSSQL]
---

# 安裝套件
```bash
pip install Flask
pip install flask-sqlalchemy
pip install pymssql
```

<!--more-->

# 建立 app.py 內容如下

```python
from flask import Flask,request,jsonify
from urllib import parse
from flask_sqlalchemy import SQLAlchemy

config = {
    "SQLALCHEMY_TRACK_MODIFICATIONS": True,
    "SQLALCHEMY_DATABASE_URI": "mssql+pymssql://[使用者帳號]:%s@[服務器IP]:[端口]/[資料庫名稱]?charset=utf8"% parse.quote('[使用者密碼]'),
}

app = Flask(__name__)
app.config.from_mapping(config)
db = SQLAlchemy(app)

# 建立資料表對應類別
class AddressMap(db.Model):
    # 資料表名稱
    __tablename__ = 'AddressMap'
    # 唯一識別碼
    id = db.Column(db.Integer, primary_key=True, autoincrement=True)
    # 地址
    address = db.Column(db.String(200, collation="Chinese_Taiwan_Stroke_CI_AS"), unique=True, nullable=False)
    # 郵區
    zip = db.Column(db.String(6), unique=False, nullable=False)

    def __init__(self, address, zip):
        self.address = address
        self.zip = zip

    def serialize(self):
       return {
           'Id' : self.id,
           'Address' : self.address,
           'Zip' : self.zip
       }

    def __repr__(self):
        return '<AddressMap %r>' % self.address

@app.before_first_request
def create_table():
    db.create_all()
 
# 建立資料
@app.route('/Address/Create' , methods = ['POST'])
def Create():
    data = request.json
    address = data['Address']
    zip = data['Zip']
    addressMap = AddressMap(address=address, zip=zip)
    db.session.add(addressMap)
    db.session.commit()
    return "Create Success"

# 取得資料
@app.route('/Address/GetAll')
def AllAddress():
    all_address = AddressMap.query.all()
    addressList = list(map(lambda x: x.serialize(), all_address))
    return jsonify(result=addressList)

# 更新資料
@app.route('/Address/Update' , methods = ['POST'])
def Update():
    data = request.json
    id = data['Id']
    address = AddressMap.query.filter_by(id=id).first()
    if address:
        address.verified = True
        address.address = data['Address']
        address.zip = data['Zip']
        db.session.commit()
    return "Update Success"

# 刪除資料
@app.route('/Address/Delete' , methods = ['POST'])
def Delete():
    data = request.json
    id = data['Id']
    address = AddressMap.query.filter_by(id=id).first()
    if address:
        db.session.delete(address)
        db.session.commit()
    return "Delete Success"

if __name__ == '__main__':
    app.run()
```

<!-- export FLASK_APP=app
flask shell
from app import db, AddressMap
db.create_all() -->

# 使用Curl呼叫API

```bash
# 建立資料
curl --location 'http://127.0.0.1:5000/Address/Create' \
--header 'Content-Type: application/json' \
--data '{
    "Address" : "新竹市",
    "Zip" : "300"
}'

# 取得資料
curl --location 'http://127.0.0.1:5000/Address/GetAll'

# 更新資料
curl --location 'http://127.0.0.1:5000/Address/Update' \
--header 'Content-Type: application/json' \
--data '{
    "Id" : 1,
    "Address" : "新竹市",
    "Zip" : "999"
}'

# 刪除資料
curl --location 'http://127.0.0.1:5000/Address/Delete' \
--header 'Content-Type: application/json' \
--data '{
    "Id" : 1
}'
```

{% note warning %}
📜 參考資料
1. [Flask-SQLAlchemy](https://flask-sqlalchemy.palletsprojects.com/en/3.0.x/)
{% endnote %}
