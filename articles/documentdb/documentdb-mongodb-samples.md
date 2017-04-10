---
title: "MongoDB API を使用して DocumentDB アプリを構築する | Microsoft Docs"
description: "DocumentDB: MongoDB 用 API を使用してオンライン データベースを作成する NoSQL チュートリアル。"
keywords: "mongodb の例"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: fb38bc53-3561-487d-9e03-20f232319a87
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 72b2d9142479f9ba0380c5bd2dd82734e370dee7
ms.openlocfilehash: 867ec5d0b27e790f3b00c94a4e5d14e4b2b17f73
ms.lasthandoff: 03/08/2017


---
# <a name="build-a-documentdb-api-for-mongodb-app-using-nodejs"></a>Node.js を使用して DocumentDB: MongoDB 用 API を構築する
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [MongoDB 用 Node.js](documentdb-mongodb-samples.md)
> * [Node.JS](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
>

次の例は、Node.js を使用して DocumentDB: MongoDB コンソール アプリ用 API を構築する方法を示しています。

この例を使用するための前提条件は、以下のとおりです。

* DocumentDB: MongoDB 用 API アカウントを[作成](documentdb-create-mongodb-account.md)していること
* MongoDB [接続文字列](documentdb-connect-mongodb-account.md)情報を取得していること

## <a name="create-the-app"></a>アプリケーションの作成

1. *app.js* ファイルを作成し、次のコードをコピーして貼り付けます。

    ```nodejs
    var MongoClient = require('mongodb').MongoClient;
    var assert = require('assert');
    var ObjectId = require('mongodb').ObjectID;
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';

    var insertDocument = function(db, callback) {
    db.collection('families').insertOne( {
            "id": "AndersenFamily",
            "lastName": "Andersen",
            "parents": [
                { "firstName": "Thomas" },
                { "firstName": "Mary Kay" }
            ],
            "children": [
                { "firstName": "John", "gender": "male", "grade": 7 }
            ],
            "pets": [
                { "givenName": "Fluffy" }
            ],
            "address": { "country": "USA", "state": "WA", "city": "Seattle" }
        }, function(err, result) {
        assert.equal(err, null);
        console.log("Inserted a document into the families collection.");
        callback();
    });
    };
    
    var findFamilies = function(db, callback) {
    var cursor =db.collection('families').find( );
    cursor.each(function(err, doc) {
        assert.equal(err, null);
        if (doc != null) {
            console.dir(doc);
        } else {
            callback();
        }
    });
    };
    
    var updateFamilies = function(db, callback) {
    db.collection('families').updateOne(
        { "lastName" : "Andersen" },
        {
            $set: { "pets": [
                { "givenName": "Fluffy" },
                { "givenName": "Rocky"}
            ] },
            $currentDate: { "lastModified": true }
        }, function(err, results) {
        console.log(results);
        callback();
    });
    };
    
    var removeFamilies = function(db, callback) {
    db.collection('families').deleteMany(
        { "lastName": "Andersen" },
        function(err, results) {
            console.log(results);
            callback();
        }
    );
    };
    
    MongoClient.connect(url, function(err, db) {
    assert.equal(null, err);
    insertDocument(db, function() {
        findFamilies(db, function() {
        updateFamilies(db, function() {
            removeFamilies(db, function() {
                db.close();
            });
        });
        });
    });
    });
    ```

2. アカウント設定に従って、*app.js* ファイル内の次の変数を変更します (接続文字列を確認する方法は[こちら](documentdb-connect-mongodb-account.md)を参照)。
   
    ```nodejs
    var url = 'mongodb://<endpoint>:<password>@<endpoint>.documents.azure.com:10250/?ssl=true';
    ```
     
3. お好きなターミナルを開き、**npm install mongodb --save** を実行してから、**node app.js** でアプリを実行します。

## <a name="next-steps"></a>次のステップ
* DocumentDB: MongoDB 用 API アカウントで [MongoChef を使用](documentdb-mongodb-mongochef.md)する方法を説明します。

