---
title: チュートリアル - Azure の Linux 仮想マシンに MEAN スタックを作成する | Microsoft Docs
description: このチュートリアルでは、Azure 内の Linux VM に MongoDB、Express、AngularJS、Node.js (MEAN) スタックを作成する方法について説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 6d870e5eedf362a6c929216735c8b5e9240aaa4f
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67708483"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>チュートリアル:Azure 内の Linux 仮想マシンに MongoDB、Express、AngularJS、Node.js (MEAN) スタックを作成する

このチュートリアルでは、Azure 内の Linux 仮想マシン (VM) に MongoDB、Express、AngularJS、Node.js (MEAN) スタックを実装する方法について説明します。 MEAN スタックを作成することで、データベースに書籍を追加したり、データベースの書籍を削除したり、一覧表示したりすることが可能になります。 学習内容は次のとおりです。

> [!div class="checklist"]
> * Linux VM の作成
> * Node.js のインストール
> * MongoDB のインストールとサーバーの設定
> * Express のインストールとサーバーへのルートの設定
> * AngularJS によるルートへのアクセス
> * アプリケーションの実行

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.30 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。


## <a name="create-a-linux-vm"></a>Linux VM の作成

[az group create](https://docs.microsoft.com/cli/azure/group) コマンドでリソース グループを作成し、[az vm create](https://docs.microsoft.com/cli/azure/vm) コマンドで Linux VM を作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。

次の例では、Azure CLI を使用して *myResourceGroupMEAN* という名前のリソース グループを *eastus* に作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は、SSH キーと共に *myVM* という名前の VM も作成します。 特定のキーのセットを使用するには、--ssh-key-value オプションを使用します。

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
`publicIpAddress` を書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。

次のコマンドを使用して、VM との SSH セッションを作成します。 正しいパブリック IP アドレスを使用していることを確認してください。 上記の例では、IP アドレスは 13.72.77.9 です。

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Node.js のインストール

[Node.js](https://nodejs.org/en/) は、Chrome の V8 JavaScript エンジン上に構築された JavaScript ランタイムです。 このチュートリアルでは、Node.js を使用して、Express ルートと AngularJS コントローラーを設定します。

SSH を使用して開いた bash シェルを使用して、VM に Node.js をインストールします。

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>MongoDB のインストールとサーバーの設定
[MongoDB](https://www.mongodb.com) は、柔軟性の高い JSON のようなドキュメントにデータを格納します。 データベースのフィールドはドキュメントごとに異なる場合があり、時間の経過と共にデータ構造を変更することもできます。 このアプリケーションの例では、書籍の名前、ISBN 番号、著者、ページ数の情報が格納されている MongoDB に書籍のレコードを追加します。 

1. VM にて、SSH を使用して開いた bash シェルを使用して、MongoDB のキーを設定します。

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. このキーを使用して、パッケージ マネージャーを更新します。
  
    ```bash
    sudo apt-get update
    ```

3. MongoDB をインストールします。

    ```bash
    sudo apt-get install -y mongodb
    ```

4. サーバーを起動します。

    ```bash
    sudo service mongodb start
    ```

5. [body-parser](https://www.npmjs.com/package/body-parser-json) パッケージのインストールも必要です。このパッケージを使用して、サーバーへの要求に渡される JSON を処理することができます。

    npm パッケージ マネージャーをインストールします。

    ```bash
    sudo apt-get install npm
    ```

    body parser パッケージをインストールします。
    
    ```bash
    sudo npm install body-parser
    ```

6. *Books* という名前のフォルダーを作成し、このフォルダーに、Web サーバーの構成が含まれる *server.js* というファイルを追加します。

    ```javascript
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Express のインストールとサーバーへのルートの設定

[Express](https://expressjs.com) は、最小限かつ柔軟性の高い Node.js の Web アプリケーション フレームワークで、Web アプリケーションとモバイル アプリケーションの機能を提供します。 このチュートリアルでは、Express を使用して、MongoDB データベースとの書籍情報の送受信を行います。 [Mongoose](https://mongoosejs.com) は、アプリケーション データのモデリングを行う簡単かつスキーマ ベースのソリューションを提供します。 このチュートリアルでは、Mongoose を使用して、書籍のスキーマをデータベースに提供します。

1. Express と Mongoose をインストールします。

    ```bash
    sudo npm install express mongoose
    ```

2. *Books* フォルダーで *apps* というフォルダーを作成し、Express のルートが定義された *routes.js* というファイルを追加します。

    ```javascript
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. *apps* フォルダーで *models* というフォルダーを作成し、書籍モデルの構成が定義された *book.js* というファイルを追加します。  

    ```javascript
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>AngularJS によるルートへのアクセス

[AngularJS](https://angularjs.org) は、Web アプリケーションで動的なビューを作成するための Web フレームワークを提供します。 このチュートリアルでは、AngularJS を使用して Web ページと Express を接続し、書籍のデータベースにアクションを実行します。

1. ディレクトリを *Books* (`cd ../..`) まで戻したら、*public* というフォルダーを作成し、コントローラーの構成が定義された *script.js* というファイルを追加します。

    ```javascript
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. *public* フォルダーで、Web ページが定義された *index.html* というファイルを作成します。

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>アプリケーションの実行

1. ディレクトリを *Books* (`cd ..`) まで戻し、次のコマンドを実行してサーバーを起動します。

    ```bash
    nodejs server.js
    ```

2. Web ブラウザーを開いて、VM 用に記録したアドレスに移動します。 ここでは *http:\//13.72.77.9:3300* としています。 次のようなページが表示されます。

    ![書籍のレコード](media/tutorial-mean/meanstack-init.png)

3. テキストボックスにデータを入力して、 **[追加]** をクリックします。 例:

    ![書籍のレコードの追加](media/tutorial-mean/meanstack-add.png)

4. ページを更新すると、次のようなページが表示されます。

    ![書籍のレコードの一覧表示](media/tutorial-mean/meanstack-list.png)

5. **[削除]** をクリックして、データベースから書籍のレコードを削除することもできます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Linux VM で MEAN スタックを使用して、書籍のレコードの記録をつける Web アプリケーションを作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Linux VM の作成
> * Node.js のインストール
> * MongoDB のインストールとサーバーの設定
> * Express のインストールとサーバーへのルートの設定
> * AngularJS によるルートへのアクセス
> * アプリケーションの実行

SSL 証明書を使用して Web サーバーをセキュリティ保護する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [SSL による Web サーバーのセキュリティ保護](tutorial-secure-web-server.md)
