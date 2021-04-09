---
title: Azure Cosmos DB の MongoDB 用 API を使用して Node.js Express アプリを作成する (パート 2)
description: Angular と Node で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成するチュートリアル シリーズのパート 2 です。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: e3af713d6e120e66a0cde5116f4556bb8caedf55
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93097854"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---create-a-nodejs-express-app"></a>Azure Cosmos DB の MongoDB 用 API で Angular アプリを作成する - Node.js Express アプリを作成する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

複数のパートから成るこのチュートリアルでは、Express と Angular を使用して Node.js で新しいアプリを作成した後、[Cosmos DB の MongoDB 用 API を使用して構成された Cosmos アカウント](mongodb-introduction.md)にそれを接続する方法を紹介します。

このチュートリアルのパート 2 では、[イントロダクション](tutorial-develop-mongodb-nodejs.md)の内容をベースとして、次のタスクについて取り上げます。

> [!div class="checklist"]
> * Angular CLI と TypeScript をインストールする
> * Angular を使って新しいプロジェクトを作成する
> * Express フレームワークを使ってアプリを作成する
> * Postman でアプリをテストする

## <a name="video-walkthrough"></a>ビデオ チュートリアル

> [!VIDEO https://www.youtube.com/embed/lIwJIYcGSUg]

## <a name="prerequisites"></a>前提条件

このチュートリアル パートに取り組む前に、[イントロダクションの動画](tutorial-develop-mongodb-nodejs.md)をご覧ください。

さらに、次の要件を満たす必要があります。 
* [Node.js](https://nodejs.org/) バージョン 8.4.0 以降。
* [Postman](https://www.getpostman.com/)
* [Visual Studio Code](https://code.visualstudio.com/) または任意のコード エディター。

> [!TIP]
> このチュートリアルでは、アプリケーションを作成する手順を段階的に説明しています。 完成したプロジェクトをダウンロードしたい場合は、GitHub の [angular-cosmosdb リポジトリ](https://github.com/Azure-Samples/angular-cosmosdb)から完全なアプリケーションを取得できます。

## <a name="install-the-angular-cli-and-typescript"></a>Angular CLI と TypeScript をインストールする

1. Windows のコマンド プロンプトまたは Mac のターミナル ウィンドウを開いて Angular CLI をインストールします。

    ```bash
    npm install -g @angular/cli
    ```

2. 次のコマンドをプロンプトに入力して TypeScript をインストールします。 

    ```bash
    npm install -g typescript
    ```

## <a name="use-the-angular-cli-to-create-a-new-project"></a>Angular CLI を使って新しいプロジェクトを作成する

1. コマンド プロンプトで、新しいプロジェクトの作成先となるフォルダーに移動し、次のコマンドを実行します。 このコマンドを実行すると、angular-cosmosdb という新しいフォルダーとプロジェクトが作成され、新しいアプリに必要な Angular コンポーネントがインストールされます。 最小限の設定を行い (--minimal)、プロジェクトで Sass (フラグ形式の scss を使った CSS ライクな構文) を使うように指定します。

    ```bash
    ng new angular-cosmosdb --minimal --style scss
    ```

2. コマンドが完了したら、src/client フォルダーに移動します。

    ```bash
    cd angular-cosmosdb
    ```

3. そのフォルダーを Visual Studio Code で開きます。

    ```bash
    code .
    ```

## <a name="build-the-app-using-the-express-framework"></a>Express フレームワークを使ってアプリを作成する

1. Visual Studio Code の **[エクスプローラー]** ウィンドウで **src** フォルダーを右クリックし、 **[新しいフォルダー]** をクリックして、新しいフォルダーに *server* という名前を付けます。

2. **[エクスプローラー]** ウィンドウで **server** フォルダーを右クリックし、 **[新しいファイル]** をクリックして、新しいファイルに *index.js* という名前を付けます。

3. コマンド プロンプトに戻り、次のコマンドを使って body-parser をインストールします。 アプリはこれを使って、API 経由で渡された JSON データを解析することができます。

    ```bash
    npm i express body-parser --save
    ```

4. Visual Studio Code で、次のコードを index.js ファイルにコピーします。 このコードによって以下が行われます。
    * Express を参照する
    * 要求の本体に格納された JSON データを読み取るための body-parser を読み込む
    * 組み込みの機能 (path) を使用する
    * コードの場所を特定しやすいようにルート変数を設定する
    * ポートを設定する
    * Express を作成する
    * サーバーの機能に使用されたミドルウェアの使い方をアプリに伝える
    * dist フォルダーに置かれるすべてのもの (静的コンテンツ) を提供する
    * アプリケーションを表示する。サーバーに存在しない GET 要求には index.html を返す (ディープ リンク)
    * app.listen でサーバーを起動する
    * アロー関数を使って、ポートが稼働状態であることをログに出力する
    
   ```node
   const express = require('express');
   const bodyParser = require('body-parser');
   const path = require('path');
   const routes = require('./routes');

   const root = './';
   const port = process.env.PORT || '3000';
   const app = express();

   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(express.static(path.join(root, 'dist/angular-cosmosdb')));
   app.use('/api', routes);
   app.get('*', (req, res) => {
     res.sendFile('dist/angular-cosmosdb/index.html', {root});
   });

   app.listen(port, () => console.log(`API running on localhost:${port}`));
   ```

5. Visual Studio Code の **[エクスプローラー]** ウィンドウで **server** フォルダーを右クリックし、 **[新しいファイル]** をクリックします。 新しいファイルに *routes.js* という名前を付けます。 

6. 次のコードを **routes.js** にコピーします。 このコードによって以下が行われます。
   * Express ルーターを参照する
   * ヒーローを取得する
   * 定義されているヒーローの JSON を返す

   ```node
   const express = require('express');
   const router = express.Router();

   router.get('/heroes', (req, res) => {
    res.send(200, [
       {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    ])
   });

   module.exports=router;
   ```

7. 変更を加えたファイルをすべて保存します。 

8. Visual Studio Code で、 **[デバッグ]** ボタン :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: をクリックし、歯車ボタン :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/gear-button.png"::: をクリックします。 Visual Studio Code で新しい launch.json ファイルが開きます。 

8. launch.json ファイルの 11 行目で `"${workspaceFolder}\\server"` を `"program": "${workspaceRoot}/src/server/index.js"` に変更して、ファイルを保存します。

9. アプリを実行するには、 **[デバッグの開始]** ボタン :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/start-debugging-button.png"::: をクリックします。

    エラーが発生することなくアプリが実行されれば成功です。

## <a name="use-postman-to-test-the-app"></a>Postman でアプリをテストする

1. 今度は Postman を開いて [GET] ボックスに「`http://localhost:3000/api/heroes`」と入力します。 

2. **[Send]\(送信\)** ボタンをクリックして、アプリから json の応答を取得します。 

    この応答から、アプリが稼働していること、またローカルで実行されていることが確認できます。 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part2/azure-cosmos-db-postman.png" alt-text="要求と応答を示す Postman の画面":::


## <a name="next-steps"></a>次のステップ

本チュートリアルのこのパートでは、次の手順を行いました。

> [!div class="checklist"]
> * Angular CLI を使って Node.js プロジェクトを作成しました。
> * Postman を使ってアプリをテストしました。

引き続き UI を作成します。次のチュートリアル パートに進んでください。

> [!div class="nextstepaction"]
> [Angular で UI を作成する](tutorial-develop-mongodb-nodejs-part3.md)
