---
title: "Azure を対象とした MongoDB、Angular、Node のチュートリアル - パート 5 | Microsoft Docs"
description: "Angular と Node で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成するチュートリアル シリーズのパート 5 です。"
services: cosmos-db
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: 
ms.service: cosmos-db
ms.workload: 
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: hero-article
ms.date: 09/05/2017
ms.author: mimig
ms.translationtype: HT
ms.sourcegitcommit: 4eb426b14ec72aaa79268840f23a39b15fee8982
ms.openlocfilehash: e752e18f6d579633c0cf553224ae7617b774ad0f
ms.contentlocale: ja-jp
ms.lasthandoff: 09/06/2017

---
# <a name="create-a-mongodb-app-with-angular-and-azure-cosmos-db---part-5-use-mongoose-to-connect-to-azure-cosmos-db"></a>Angular と Azure Cosmos DB を使って MongoDB アプリを作成する - パート 5: Mongoose を使って Azure Cosmos DB に接続する

複数のパートから成るこのチュートリアルでは、Node.js で Express、Angular、Azure Cosmos DB データベースを使って記述された新しい [MongoDB API](mongodb-introduction.md) アプリの作成方法を紹介します。

このチュートリアルのパート 5 では、[パート 4](tutorial-develop-mongodb-nodejs-part4.md) の内容をベースとして、次のタスクについて取り上げます。

> [!div class="checklist"]
> * Mongoose を使って Azure Cosmos DB に接続する
> * Azure Cosmos DB から接続文字列情報を取得する
> * ヒーロー モデルを作成する
> * ヒーロー データを取得するためのヒーロー サービスを作成する
> * アプリをローカルで実行する

## <a name="video-walkthrough"></a>ビデオ チュートリアル

> [!VIDEO https://www.youtube.com/embed/sI5hw6KPPXI]


## <a name="prerequisites"></a>前提条件

本チュートリアルのこのパートに取り組む前に、[パート 4](tutorial-develop-mongodb-nodejs-part4.md) の手順を済ませておいてください。

> [!TIP]
> このチュートリアルでは、アプリケーションを作成する手順を段階的に説明しています。 完成したプロジェクトをダウンロードしたい場合は、GitHub の [angular-cosmosdb リポジトリ](https://github.com/Azure-Samples/angular-cosmosdb)から完全なアプリケーションを取得できます。

## <a name="use-mongoose-to-connect-to-azure-cosmos-db"></a>Mongoose を使って Azure Cosmos DB に接続する

1. MongoDB との対話に一般的に使用される API である mongoose npm モジュールをインストールします。

    ```bash
    npm i mongoose --save
    ```

2. **server** フォルダーに、**mongo.js** という新しいファイルを作成しましょう。 Azure Cosmos DB データベースに使用されるすべての接続情報はこのファイルに追加します。

3. 次のコードを **mongo.js** にコピーします。 このコードによって以下が行われます。
    * Mongoose を要求する。
    * Mongo の Promise を上書きして ES6/ES2015 以上に組み込まれている基本的な Promise を使用する。
    * 環境の種類 (ステージング、運用、開発) に応じて特定の動作を設定できる env ファイルを要求する。 このファイルは、この後すぐ作成します。
    * MongoDB 接続文字列をインクルードする。接続文字列は env ファイルで設定します。
    * Mongoose を呼び出す connect 関数を作成する。

    ```javascript
    const mongoose = require('mongoose');
    /**
     * Set to Node.js native promises
     * Per http://mongoosejs.com/docs/promises.html
     */
    mongoose.Promise = global.Promise;

    const env = require('./env/environment');

    // eslint-disable-next-line max-len
    const mongoUri = `mongodb://${env.dbName}:${env.key}@${env.dbName}.documents.azure.com:${env.cosmosPort}/?ssl=true`; //&replicaSet=globaldb`;

    function connect() {
     mongoose.set('debug', true);
     return mongoose.connect(mongoUri, { useMongoClient: true });
    }

    module.exports = {
      connect,
      mongoose
    };
    ```
    
4. [エクスプローラー] ウィンドウで、**environment** というフォルダーを **server** 下に作成します。さらに、その **environment** フォルダーに、**environment.js** という新しいファイルを作成します。

5. mongo.js ファイルを見ると、`dbName`、`key`、`cosmosPort` をインクルードする必要があると考えられるので、次のコードを **environment.js** にコピーします。

    ```javascript
    const cosmosPort = 1234; // replace with your port
    const dbName = 'your-cosmos-db-name-goes-here';
    const key = 'your-key-goes-here';

    module.exports = {
      dbName,
      key,
      cosmosPort
    };
    ```

## <a name="get-the-connection-string-information"></a>接続文字列情報を取得する

1. **environment.js** で、`cosmosPort` の値を 10255 に変更します  (Cosmos DB のポートは Azure Portal で確認できます)。

    ```javascript
    const cosmosPort = 10255;
    ```

2. **environment.js** で、`dbName` の値を、[手順 4.](tutorial-develop-mongodb-nodejs-part4.md) で作成した Azure Cosmos DB アカウントの名前に変更します。 

3. ターミナル ウィンドウで次の CLI コマンドを使って Azure Cosmos DB アカウントのプライマリ キーを取得します。 

    ```azure-cli-interactive
    az cosmosdb list-keys --name <cosmosdb-name> -g myResourceGroup
    ```    
    
    * `<cosmosdb-name>` は、[手順 4.](tutorial-develop-mongodb-nodejs-part4.md) で作成した Azure Cosmos DB アカウントの名前です。

4. プライマリ キーを `key` 値として environment.js ファイルにコピーします。

    これでアプリから Azure Cosmos DB に接続するために必要な情報はすべて揃いました。 この情報は、ポータルから取得することもできます。 詳細については、「[MongoDB 接続文字列を取得してカスタマイズする](connect-mongodb-account.md#GetCustomConnection)」を参照してください。 ポータルの "ユーザー名" は、environments.js の dbName に相当します。 

## <a name="create-a-hero-model"></a>ヒーロー モデルを作成する

1.  [エクスプローラー] ウィンドウで、**server** フォルダーに **hero.model.js** というファイルを作成します。

2. 次のコードを **hero.model.js** にコピーします。 このコードによって以下が行われます。
   * Mongoose を要求する。
   * 識別子 (id)、名前 (name)、台詞 (saying) を含んだ新しいスキーマを作成する。
   * スキーマを使ってモデルを作成する。
   * モデルをエクスポートする。 
   * コレクションに Heros ではなく Heroes という名前を付ける (Mongoose の複数形の名前付け規則に従った場合、コレクションの既定の名前が Heros になります)。

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

1.  [エクスプローラー] ウィンドウで、**server** フォルダーに **hero.service.js** というファイルを作成します。

2. 次のコードを **hero.service.js** にコピーします。 このコードによって以下が行われます。
   * 作成したモデルを取得する
   * データベースに接続する
   * hero.find メソッドを使った docquery 変数を作成して、すべてのヒーローを返すクエリを定義する。
   * Promise で docquery.exec でクエリを実行し、応答の状態が 200 であるすべてのヒーローのリストを取得する。 
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

## <a name="add-the-hero-service-to-routesjs"></a>ヒーロー サービスを routes.js に追加する

1. Visual Studio Code で **routes.js** を開き、サンプル ヒーロー データを送信する `res.send` 関数をコメントアウトして、`heroService.getHeroes` 関数を呼び出す行を追加します。

    ```javascript
    router.get('/heroes', (req, res) => {
      heroService.getHeroes(req, res);
    //  res.send(200, [
    //      {"id": 10, "name": "Starlord", "saying": "oh yeah"}
    //  ])
    });
    ```

2. **routes.js** でヒーロー サービスを要求します。

    ```javascript
    const heroService = require('./hero.service'); 
    ```

3. **hero.service.js** の getHeroes 関数を、次のように `req` パラメーターと `res` パラメーターを受け取るように変更します。

    ```javascript
    function getHeroes(req, res) {
    ```

    ここで、呼び出しチェーンを振り返ってみましょう。 最初に実行されるのは `index.js` です。ここで、ノード サーバーが設定されます。必要なルートは、ここで設定されて定義されることに注目してください。 その後、routes.js ファイルはヒーロー サービスと対話し、必要な関数 (getHeroes など) を取得して要求と応答を渡すよう命令します。 そこで hero.service.js はモデルを取得して Mongo に接続し、getHeroes が呼び出されると、それを実行して、応答コード 200 を返します。 その後、チェーンを逆順でたどっていきます。 

## <a name="run-the-app"></a>アプリの実行

1. もう一度アプリを実行してみましょう。 Visual Studio Code で変更をすべて保存してください。左側にある **[デバッグ]** ボタン (![Visual Studio Code のデバッグ アイコン](./media/tutorial-develop-mongodb-nodejs-part5/debug-button.png)) をクリックし、**[デバッグ開始]** ボタン (![Visual Studio Code のデバッグ アイコン](./media/tutorial-develop-mongodb-nodejs-part5/start-debugging-button.png)) をクリックします。

3. 次に、ブラウザーでデベロッパー ツールと [Network] タブを開き、http://localhost:3000 にアクセスすると、アプリケーションが表示されます。

    ![Azure Portal の新しい Azure Cosmos DB アカウント](./media/tutorial-develop-mongodb-nodejs-part5/azure-cosmos-db-heroes-app.png)

   今のところアプリにはヒーローが保存されていませんが、チュートリアルの次のステップに進んで、Put、Push、Delete の各機能を追加すれば、Azure Cosmos DB データベースへの Mongoose 接続を使って UI から、ヒーローの追加、更新、削除を行えるようになります。 

## <a name="next-steps"></a>次のステップ

本チュートリアルのこのパートでは、次の手順を行いました。

> [!div class="checklist"]
> * Mongoose API を使ってヒーロー アプリを Azure Cosmos DB に接続しました。 
> * ヒーローの取得機能をアプリに追加しました。

引き続き Post、Put、Delete の各関数をアプリに追加します。次のチュートリアル パートに進んでください。

> [!div class="nextstepaction"]
> [Post、Put、Delete の各関数をアプリに追加する](tutorial-develop-mongodb-nodejs-part6.md)

