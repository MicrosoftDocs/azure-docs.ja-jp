---
title: "Azure App Service での Node.js API アプリ | Microsoft Docs"
description: "Azure App Service で Node.js RESTful API を作成して API アプリにデプロイする方法について説明します。"
services: app-service\api
documentationcenter: node
author: bradygaster
manager: erikre
editor: 
ms.assetid: a820e400-06af-4852-8627-12b3db4a8e70
ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/13/2017
ms.author: rachelap
ms.custom: mvc
ms.openlocfilehash: f42917486e873782483f372eb6e747ab3080f820
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="build-a-nodejs-restful-api-and-deploy-it-to-an-api-app-in-azure"></a>Node.js RESTful API をビルドして Azure で API アプリにデプロイする

このクイックスタートでは、Node.js [Express](http://expressjs.com/) による REST API を [Swagger](http://swagger.io/) 定義を使って作成し、それを Azure にデプロイする方法を紹介します。 コマンドライン ツールを使用してアプリを作成し、[Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) でリソースを構成してから、Git を使ってアプリをデプロイします。  完了すると、Azure.で実行される動作サンプルの REST API を入手できます。

## <a name="prerequisites"></a>前提条件

* [Git](https://git-scm.com/)
* [Node.js および NPM](https://nodejs.org/)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このトピックでは、Azure CLI バージョン 2.0 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="prepare-your-environment"></a>環境を準備する

1. ターミナル ウィンドウで、次のコマンドを実行して、ローカル コンピューターにサンプルを複製します。

    ```bash
    git clone https://github.com/Azure-Samples/app-service-api-node-contact-list
    ```

2. サンプル コードが含まれているディレクトリに移動します。

    ```bash
    cd app-service-api-node-contact-list
    ```

3. ローカル コンピューターに [Swaggerize](https://www.npmjs.com/package/swaggerize-express) をインストールします。 Swaggerize は、Swagger 定義から REST API の Node.js コードを生成するツールです。

    ```bash
    npm install -g yo
    npm install -g generator-swaggerize
    ```

## <a name="generate-nodejs-code"></a>Node.js コードを生成する 

チュートリアルのこのセクションは、API 開発ワークフローをモデルにしています。ここでは最初に Swagger メタデータを作成し、それを使用して API のサーバー コードをスキャフォールディング (自動生成) します。 

ディレクトリを *start* フォルダーに変更して、`yo swaggerize` を実行します。 Swaggerize は、*api.json* 内の Swagger 定義から、API の Node.js プロジェクトを作成します。

```bash
cd start
yo swaggerize --apiPath api.json --framework express
```

Swaggerize からプロジェクト名を求められたら、*ContactList* を使用します。
   
   ```bash
   Swaggerize Generator
   Tell us a bit about your application
   ? What would you like to call this project: ContactList
   ? Your name: Francis Totten
   ? Your github user name: fabfrank
   ? Your email: frank@fabrikam.net
   ```
   
## <a name="customize-the-project-code"></a>プロジェクト コードをカスタマイズする

1. *lib* フォルダーを `yo swaggerize` によって作成された *ContactList*フォルダーにコピーして、ディレクトリを *ContactList* に変更します。

    ```bash
    cp -r lib/ ContactList/
    cd ContactList
    ```

2. `jsonpath` および `swaggerize-ui` NPM モジュールをインストールします。 

    ```bash
    npm install --save jsonpath swaggerize-ui
    ```

3. *handlers/contacts.js* のコードを次のコードに置き換えます。 
    ```javascript
    'use strict';

    var repository = require('../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.all())
        }
    };
    ```
    このコードでは、*lib/contactRepository.js* から提供された *lib/contacts.json* に保存されている JSON データを使用しています。 新しい *contacts.js* コードは、リポジトリ内のすべての連絡先を 1 つの JSON ペイロードとして返します。 

4. **handlers/contacts/{id}.js** ファイルのコードを次のコードに置き換えます。

    ```javascript
    'use strict';

    var repository = require('../../lib/contactRepository');

    module.exports = {
        get: function contacts_get(req, res) {
            res.json(repository.get(req.params['id']));
        }    
    };
    ```

    このコードでは、指定された ID の連絡先のみを返すパス変数を使用できるようにします。

5. **server.js** のコードを次のコードに置き換えます。

    ```javascript
    'use strict';

    var port = process.env.PORT || 8000; 

    var http = require('http');
    var express = require('express');
    var bodyParser = require('body-parser');
    var swaggerize = require('swaggerize-express');
    var swaggerUi = require('swaggerize-ui'); 
    var path = require('path');
    var fs = require("fs");
    
    fs.existsSync = fs.existsSync || require('path').existsSync;

    var app = express();

    var server = http.createServer(app);

    app.use(bodyParser.json());

    app.use(swaggerize({
        api: path.resolve('./config/swagger.json'),
        handlers: path.resolve('./handlers'),
        docspath: '/swagger' 
    }));

    // change four
    app.use('/docs', swaggerUi({
        docs: '/swagger'  
    }));

    server.listen(port, function () { 
    });
    ```   

    このコードには、Azure App Service と共に動作して、目的の API の対話型 Web インターフェイスを公開できるように、小規模の変更を加えています。

### <a name="test-the-api-locally"></a>API をローカルでテストする

1. Node.js アプリを起動する
    ```bash
    npm start
    ```
    
2. http://localhost:8000/contacts を参照して、連絡先一覧全体に対する JSON を表示します。
   
   ```json
    {
        "id": 1,
        "name": "Barney Poland",
        "email": "barney@contoso.com"
    },
    {
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    },
    {
        "id": 3,
        "name": "Lora Riggs",
        "email": "lora@contoso.com"
    }
   ```

3. http://localhost:8000/contacts/2 を参照して、2 つのうち 1 つの `id` の連絡先を表示します。
   
    ```json
    { 
        "id": 2,
        "name": "Lacy Barrera",
        "email": "lacy@contoso.com"
    }
    ```

4. http://localhost:8000/docs で、Swagger Web インターフェイスを使用して API をテストします。
   
    ![Swagger Web インターフェイス](media/app-service-web-tutorial-rest-api/swagger-ui.png)

## <a id="createapiapp"></a> API アプリを作成する

このセクションでは、Azure CLI 2.0 を使用してリソースを作成し、Azure App Service で API をホストします。 

1.  [az login](/cli/azure/#login) コマンドで Azure サブスクリプションにログインし、画面上の指示に従います。

    ```azurecli-interactive
    az login
    ```

2. 複数の Azure サブスクリプションがある場合は、既定のサブスクリプションを適宜変更します。

    ````azurecli-interactive
    az account set --subscription <name or id>
    ````

3. [!INCLUDE [Create resource group](../../includes/app-service-api-create-resource-group.md)] 

4. [!INCLUDE [Create app service plan](../../includes/app-service-api-create-app-service-plan.md)]

5. [!INCLUDE [Create API app](../../includes/app-service-api-create-api-app.md)] 


## <a name="deploy-the-api-with-git"></a>Git を使って API をデプロイする

ローカル Git リポジトリから Azure App Service へコミットをプッシュすることで、コードを API アプリにデプロイします。

1. [!INCLUDE [Configure your deployment credentials](../../includes/configure-deployment-user-no-h.md)] 

2. *ContactList* ディレクトリにある新しいリポジトリを初期化します。 

    ```bash
    git init .
    ```

3. チュートリアルの前述の手順で npm によって作成された *node_modules*ディレクトリを、Git から除外します。 現在のディレクトリに新しい `.gitignore` ファイルを作成し、ファイル内の任意の位置で、新しい行に次のようなテキストを挿入します。

    ```
    node_modules/
    ```
    `node_modules` フォルダーが、`git status` では無視されていることを確認します。

4. レポジトリへの変更をコミットします。
    ```bash
    git add .
    git commit -m "initial version"
    ```

5. [!INCLUDE [Push to Azure](../../includes/app-service-api-git-push-to-azure.md)]  
 
## <a name="test-the-api--in-azure"></a>Azure で API をテストする

1. ブラウザーで http://app_name.azurewebsites.net/contacts を開きます。 チュートリアルの前述の手順で、ローカルで要求を行ったときに返された同じ JSON が表示されます。

   ```json
   {
       "id": 1,
       "name": "Barney Poland",
       "email": "barney@contoso.com"
   },
   {
       "id": 2,
       "name": "Lacy Barrera",
       "email": "lacy@contoso.com"
   },
   {
       "id": 3,
       "name": "Lora Riggs",
       "email": "lora@contoso.com"
   }
   ```

2. ブラウザーで、`http://app_name.azurewebsites.net/docs` エンドポイントに移動して、Azure で実行している Swagger UI を試行します。

    ![Swagger Ii](media/app-service-web-tutorial-rest-api/swagger-azure-ui.png)

    これで、Azure Git リポジトリにコミットをプッシュして、サンプル API への更新を Azure に簡単にデプロイできるようになりました。

## <a name="clean-up"></a>クリーンアップ

このクイック スタートで作成したリソースをクリーンアップするには、次の Azure CLI コマンドを実行します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-step"></a>次のステップ 
> [!div class="nextstepaction"]
> [既存のカスタム DNS 名を Azure Web Apps にマップする](app-service-web-tutorial-custom-domain.md)

