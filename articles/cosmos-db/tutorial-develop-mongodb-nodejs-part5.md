---
title: Mongoose を使用して Angular アプリ を Azure Cosmos DB の MongoDB 用 API に接続する
description: このチュートリアルでは、Express と Angular を使用して Cosmos DB に格納されているデータを管理するための Node.js アプリケーションを構築する方法について説明します。 このパートでは、Mongoose を使用して Azure Cosmos DB に接続します。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: a3097fa539f460ef5e8ffe73598fa5d55516717e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "93097806"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---use-mongoose-to-connect-to-cosmos-db"></a>Azure Cosmos DB の MongoDB 用 API で Angular アプリを作成する - Mongoose を使用して Cosmos DB に接続する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

複数のパートから成るこのチュートリアルでは、Express と Angular を使用して Node.js アプリを作成し、[Azure Cosmos DB の MongoDB 用 API で構成された Cosmos アカウント](mongodb-introduction.md)にそれを接続する方法を紹介します。 この記事はチュートリアルのパート 5 であり、[パート 4](tutorial-develop-mongodb-nodejs-part4.md) が基になっています。

チュートリアルのこのパートでは、次のことを行います。

> [!div class="checklist"]
> * Mongoose を使用して Cosmos DB に接続します。
> * Cosmos DB の接続文字列を取得します。
> * ヒーロー モデルを作成します。
> * ヒーロー データを取得するためのヒーロー サービスを作成します。
> * アプリをローカルで実行します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="prerequisites"></a>前提条件

* このチュートリアルを始める前に、[パート 4](tutorial-develop-mongodb-nodejs-part4.md) の手順を完了しておきます。

* このチュートリアルでは、Azure CLI をローカルで実行する必要があります。 また、Azure CLI バージョン 2.0 以降がインストールされている必要があります。 バージョンを確認するには、`az --version` を実行します。 Azure CLI をインストールまたはアップグレードする必要がある場合は、[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

* このチュートリアルでは、アプリケーションを作成する手順を段階的に説明します。 完成したプロジェクトをダウンロードしたい場合は、GitHub の [angular-cosmosdb リポジトリ](https://github.com/Azure-Samples/angular-cosmosdb)から完全なアプリケーションを取得できます。

## <a name="use-mongoose-to-connect"></a>Mongoose を使用して接続する

Mongoose は、MongoDB および Node.js のためのオブジェクト データ モデリング (ODM) ライブラリです。 Mongoose を使用して Azure Cosmos DB アカウントに接続できます。 Mongoose をインストールして Azure Cosmos DB に接続するには、以下の手順のようにします。

1. MongoDB との対話に使用される API である mongoose npm モジュールをインストールします。

    ```bash
    npm i mongoose --save
    ```

1. **server** フォルダーに、**mongo.js** という名前のファイルを作成します。 Azure Cosmos DB アカウントの接続の詳細を、このファイルに追加します。

1. 次のコードを **mongo.js** ファイルにコピーします。 このコードでは以下の機能が提供されます。

   * Mongoose を要求する。
   * Mongo の Promise をオーバーライドして ES6/ES2015 以降のバージョンに組み込まれている基本的な Promise を使用する。
   * 環境の種類 (ステージング、運用、開発) に応じて特定の動作を設定できる env ファイルを要求する。 そのファイルは、次のセクションで作成します。
   * MongoDB の接続文字列をインクルードする。その接続文字列は、env ファイルで設定されています。
   * Mongoose を呼び出す connect 関数を作成する。

     ```javascript
     const mongoose = require('mongoose');
     /**
     * Set to Node.js native promises
     * Per https://mongoosejs.com/docs/promises.html
     */
     mongoose.Promise = global.Promise;

     const env = require('./env/environment');

     // eslint-disable-next-line max-len
     const mongoUri = `mongodb://${env.accountName}:${env.key}@${env.accountName}.documents.azure.com:${env.port}/${env.databaseName}?ssl=true`;

     function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
     }

     module.exports = {
     connect,
     mongoose
     };
     ```
    
1. [エクスプローラー] ウィンドウで、**server** の下に **environment** という名前のフォルダーを作成します。 **environment** フォルダーに、**environment.js** という名前のファイルを作成します。

1. mongo.js ファイルから、`dbName`、`key`、`cosmosPort` パラメーターの値を含める必要があります。 次のコードを **environment.js** ファイルにコピーします。

    ```javascript
    // TODO: replace if yours are different
    module.exports = {
      accountName: 'your-cosmosdb-account-name-goes-here',
      databaseName: 'admin', 
      key: 'your-key-goes-here',
      port: 10255
    };
    ```

## <a name="get-the-connection-string"></a>接続文字列を取得する

アプリケーションを Azure Cosmos DB に接続するには、アプリケーションの構成設定を更新する必要があります。 以下の手順で、設定を更新します。 

1. Azure portal で、Azure Cosmos DB アカウントのポート番号、名前、プライマリ キーの値を取得します。

1. **environment.js** ファイルで、`port` の値を 10255 に変更します。 

    ```javascript
    const port = 10255;
    ```

1. **environment.js** ファイルで、`accountName` の値を、チュートリアルの [パート 4](tutorial-develop-mongodb-nodejs-part4.md) で作成した Azure Cosmos DB アカウントの名前に変更します。 

1. ターミナル ウィンドウで次の CLI コマンドを使って Azure Cosmos DB アカウントのプライマリ キーを取得します。 

    ```azurecli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    \<cosmosdb-name> は、チュートリアルの[パート 4](tutorial-develop-mongodb-nodejs-part4.md) で作成した Azure Cosmos DB アカウントの名前です。

1. プライマリ キーを **environment.js** ファイルの `key` の値にコピーします。

これで、Azure Cosmos DB に接続するために必要なすべての情報がアプリケーションに設定されます。 

## <a name="create-a-hero-model"></a>ヒーロー モデルを作成する

次に、モデル ファイルを定義することで、Azure Cosmos DB に格納するデータのスキーマを定義する必要があります。 次の手順を使用して、データのスキーマを定義する "_ヒーロー モデル_" を作成します。

1. [エクスプローラー] ウィンドウで、**server** フォルダーに **hero.model.js** という名前のファイルを作成します。

1. 次のコードを **hero.model.js** ファイルにコピーします。 このコードでは以下の機能が提供されます。

   * Mongoose を要求する。
   * 識別子 (id)、名前 (name)、台詞 (saying) を含んだ新しいスキーマを作成する。
   * スキーマを使用してモデルを作成する。
   * モデルをエクスポートする。 
   * コレクションに **Heroes** という名前を付ける (Mongoose の複数形の名前付け規則に従ったコレクションの既定の名前である **Heros** ではありません)。

   ```javascript
   const mongoose = require('mongoose');

   const Schema = mongoose.Schema;

   const heroSchema = new Schema(
     {
       id: { type: Number, required: true, unique: true },
       name: String,
       saying: String
     },
     {
       collection: 'Heroes'
     }
   );

   const Hero = mongoose.model('Hero', heroSchema);

   module.exports = Hero;
   ```

## <a name="create-a-hero-service"></a>ヒーロー サービスを作成する

ヒーロー モデルを作成した後は、データを読み取り、一覧表示、作成、削除、更新の操作を実行するサービスを定義する必要があります。 次の手順を使用して、Azure Cosmos DB のデータのクエリを実行する "_ヒーロー サービス_" を作成します。

1. [エクスプローラー] ウィンドウで、**server** フォルダーに **hero.service.js** という名前のファイルを作成します。

1. 次のコードを **hero.service.js** ファイルにコピーします。 このコードでは以下の機能が提供されます。

   * 作成したモデルを取得する。
   * データベースに接続する。
   * `hero.find` メソッドを使用してすべてのヒーローを返すクエリを定義する `docquery` 変数を作成する。
   * Promise を使用して `docquery.exec` 関数でクエリを実行し、応答の状態が 200 であるすべてのヒーローのリストを取得する。 
   * 状態が 500 の場合は、エラー メッセージを返す。
   * モジュールを使用しているため、ヒーローを取得する。 

   ```javascript
   const Hero = require('./hero.model');

   require('./mongo').connect();

   function getHeroes() {
     const docquery = Hero.find({});
     docquery
       .exec()
       .then(heroes => {
         res.status(200).json(heroes);
       })
       .catch(error => {
         res.status(500).send(error);
         return;
       });
   }

   module.exports = {
     getHeroes
   };
   ```

## <a name="configure-routes"></a>ルートを構成する

次に、取得、作成、読み取り、削除の各要求に対する URL を処理するためのルートを設定する必要があります。 ルーティング メソッドでは、コールバック関数を指定します ("_ハンドラー関数_" とも呼ばれます)。 これらの関数は、指定されたエンドポイントと HTTP メソッドに対する要求をアプリケーションが受け取ると呼び出されます。 次の手順を使用して、ヒーロー サービスを追加し、ルートを定義します。

1. Visual Studio Code で **routes.js** ファイルを開き、サンプル ヒーロー データを送信する `res.send` 関数をコメントにします。 代わりに、`heroService.getHeroes` 関数を呼び出す行を追加します。

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

1. **routes.js** ファイルで、`require` を使用してヒーロー サービスを要求します。

    ```javascript
    const heroService = require('./hero.service'); 
    ```

1. **hero.service.js** ファイルで、`getHeroes` 関数を次のように更新し、`req` パラメーターと `res` パラメーターを受け取るようにします。

    ```javascript
    function getHeroes(req, res) {
    ```

ここで、前のコードを振り返ってみましょう。 最初の index.js ファイルでは、ノード サーバーをセットアップしています。 ルートを設定して定義していることに注意してください。 次に、routes.js ファイルでは、ヒーロー サービスと対話し、関数 (**getHeroes** など) を取得して要求と応答を渡すよう命令します。 hero.service.js ファイルでは、モデルを取得し、Mongo に接続します。 その後、呼び出すと **getHeroes** が実行されて、200 の応答が返されます。 

## <a name="run-the-app"></a>アプリを実行する

次に、以下の手順を使用してアプリを実行します。

1. Visual Studio Code ですべての変更を保存します。 左側で、 **[デバッグ]** ボタン (:::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png":::) を選択し、 **[デバッグの開始]** ボタン (:::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png":::) を選択します。

1. ここでブラウザーに切り替えます。 **開発者ツール** を開き、 **[ネットワーク]** タブを開きます。`http://localhost:3000` に移動すると、このアプリケーションが表示されます。

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png" alt-text="Azure portal の新しい Azure Cosmos DB アカウント":::

アプリにはヒーローがまだ格納されていません。 このチュートリアルの次のパートでは、プット、プッシュ、削除の機能を追加します。 そうすれば、Azure Cosmos データベースへの Mongoose 接続を使用して、UI からヒーローを追加、更新、削除することができます。 

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソースが必要なくなったら、リソース グループ、Azure Cosmos DB アカウント、およびすべての関連リソースを削除できます。 リソース グループを削除するには次のようにします。

 1. Azure Cosmos DB アカウントを作成したリソース グループに移動します。
 1. **[リソース グループの削除]** を選択します。
 1. 削除するリソース グループの名前を確認し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

続いてチュートリアルのパート 6 に進み、Post、Put、Delete の各関数をアプリに追加します。

> [!div class="nextstepaction"]
> [パート 6:Post、Put、Delete の各関数をアプリに追加する](tutorial-develop-mongodb-nodejs-part6.md)