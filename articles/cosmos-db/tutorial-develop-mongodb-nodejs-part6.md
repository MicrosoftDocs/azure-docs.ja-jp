---
title: Azure Cosmos DB の MongoDB 用 API で Angular アプリを作成する - アプリに CRUD 関数を追加する
titleSuffix: Azure Cosmos DB
description: Angular と Node で MongoDB に使われる API をそのまま使用して、Azure Cosmos DB を対象とした MongoDB アプリを作成するチュートリアル シリーズのパート 6 です。
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18
ms.reviewer: sngun
ms.openlocfilehash: 42015ca816f2744ef28660c5396db4cfd93a76f0
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/04/2019
ms.locfileid: "54040559"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---add-crud-functions-to-the-app"></a>Azure Cosmos DB の MongoDB 用 API で Angular アプリを作成する - アプリに CRUD 関数を追加する

複数のパートから成るこのチュートリアルでは、Express と Angular を使用して Node.js で新しいアプリを作成した後、[Azure Cosmos DB の MongoDB 用 API で構成された Cosmos アカウント](mongodb-introduction.md)にそれを接続する方法を紹介します。 このチュートリアルのパート 6 では、[パート 5](tutorial-develop-mongodb-nodejs-part5.md) の内容をベースとして、次のタスクについて取り上げます。

> [!div class="checklist"]
> * ヒーロー サービスに Post、Put、Delete の各関数を作成する
> * アプリの実行

> [!VIDEO https://www.youtube.com/embed/Y5mdAlFGZjc]

## <a name="prerequisites"></a>前提条件

本チュートリアルのこのパートに取り組む前に、[パート 5](tutorial-develop-mongodb-nodejs-part5.md) の手順を済ませておいてください。

> [!TIP]
> このチュートリアルでは、アプリケーションを作成する手順を段階的に説明しています。 完成したプロジェクトをダウンロードしたい場合は、GitHub の [angular-cosmosdb リポジトリ](https://github.com/Azure-Samples/angular-cosmosdb)から完全なアプリケーションを取得できます。

## <a name="add-a-post-function-to-the-hero-service"></a>Post 関数をヒーロー サービスに追加する

1. Visual Studio Code の **[エディターの分割]** ボタン (![Visual Studio の [エディターの分割] ボタン](./media/tutorial-develop-mongodb-nodejs-part6/split-editor-button.png)) を押して、**routes.js** と **hero.service.js** を左右に並べて表示します。

    **hero.service.js** の 5 行目にある `getHeroes` 関数が、routes.js の 7 行目で呼び出されていることがわかります。  これと同じペアリングを Post、Put、Delete の各関数についても作成する必要があります。 

    ![Visual Studio Code で routes.js と hero.service.js を表示したところ](./media/tutorial-develop-mongodb-nodejs-part6/routes-heroservicejs.png)
    
    それでは、ヒーロー サービスのコーディングを始めましょう。 

2. **hero.service.js** の `getHeroes` 関数の後 (`module.exports` の前) に次のコードをコピーします。 このコードによって以下が行われます。  
   * ヒーロー モデルを使って新しいヒーローをポストする。
   * 応答をチェックしてエラーが発生しているかどうかを調べ、状態値 500 を返す。

   ```javascript
   function postHero(req, res) {
     const originalHero = { uid: req.body.uid, name: req.body.name, saying: req.body.saying };
     const hero = new Hero(originalHero);
     hero.save(error => {
       if (checkServerError(res, error)) return;
       res.status(201).json(hero);
       console.log('Hero created successfully!');
     });
   }

   function checkServerError(res, error) {
     if (error) {
       res.status(500).send(error);
       return error;
     }
   }
   ```

3. **hero.service.js** の `module.exports` に新しい `postHero` 関数を追加します。 

    ```javascript
    module.exports = {
      getHeroes,
      postHero
    };
    ```

4. **routes.js** の `get` ルーターの後に、`post` 関数のルーターを追加します。 このルーターは一度に 1 つのヒーローをポストします。 このような構造をルーター ファイルに持たせることで、利用可能なすべての API エンドポイントを明確化しつつ、実際の処理を **hero.service.js** ファイルに委ねることができます。

    ```javascript
    router.post('/hero', (req, res) => {
      heroService.postHero(req, res);
    });
    ```

5. アプリを実行して正常に動作するかどうかを確かめます。 Visual Studio Code で変更内容をすべて保存してください。左側にある **[デバッグ]** ボタン (![Visual Studio Code のデバッグ アイコン](./media/tutorial-develop-mongodb-nodejs-part6/debug-button.png)) を選択し、**[デバッグ開始]** ボタン (![Visual Studio Code のデバッグ開始アイコン](./media/tutorial-develop-mongodb-nodejs-part6/start-debugging-button.png)) を選択します。

6. インターネット ブラウザーに戻り、デベロッパー ツールの [Network] タブを開きます。ほとんどのマシンでは、F12 キーを押すと、デベロッパー ツールが表示されます。 [http://localhost:3000](http://localhost:3000) にアクセスして、ネットワーク上で実行される呼び出しを観察します。

    ![Chrome の [Network] タブでネットワーク アクティビティを表示](./media/tutorial-develop-mongodb-nodejs-part6/add-new-hero.png)

7. **[Add New Hero]** ボタンを選択して新しいヒーローを追加します。 ID に「999」を、名前に「Fred」を、台詞に「Hello」と入力して、**[Save]** を選択します。 [Network] タブを見ると、新しいヒーローの POST 要求が送信されたことを確認できます。 

    ![Chrome の [Network] タブで Get 関数と Post 関数のネットワーク アクティビティを表示](./media/tutorial-develop-mongodb-nodejs-part6/post-new-hero.png)

    今度は、Put 関数と Delete 関数をアプリに追加しましょう。

## <a name="add-the-put-and-delete-functions"></a>Put 関数と Delete 関数を追加する

1. **routes.js** で、post ルーターの後に `put` ルーターと `delete` ルーターを追加します。

    ```javascript
    router.put('/hero/:uid', (req, res) => {
      heroService.putHero(req, res);
    });

    router.delete('/hero/:uid', (req, res) => {
      heroService.deleteHero(req, res);
    });
    ```

2. **hero.service.js** の `checkServerError` 関数の後に次のコードをコピーします。 このコードによって以下が行われます。
   * `put` 関数と `delete` 関数を作成する
   * ヒーローが見つかったかどうかのチェックを実行する
   * エラー処理を実行する 

   ```javascript
   function putHero(req, res) {
     const originalHero = {
       uid: parseInt(req.params.uid, 10),
       name: req.body.name,
       saying: req.body.saying
     };
     Hero.findOne({ uid: originalHero.uid }, (error, hero) => {
       if (checkServerError(res, error)) return;
       if (!checkFound(res, hero)) return;

      hero.name = originalHero.name;
       hero.saying = originalHero.saying;
       hero.save(error => {
         if (checkServerError(res, error)) return;
         res.status(200).json(hero);
         console.log('Hero updated successfully!');
       });
     });
   }

   function deleteHero(req, res) {
     const uid = parseInt(req.params.uid, 10);
     Hero.findOneAndRemove({ uid: uid })
       .then(hero => {
         if (!checkFound(res, hero)) return;
         res.status(200).json(hero);
         console.log('Hero deleted successfully!');
       })
       .catch(error => {
         if (checkServerError(res, error)) return;
       });
   }

   function checkFound(res, hero) {
     if (!hero) {
       res.status(404).send('Hero not found.');
       return;
     }
     return hero;
   }
   ```

3. **hero.service.js** で、新しいモジュールをエクスポートします。

   ```javascript
    module.exports = {
      getHeroes,
      postHero,
      putHero,
      deleteHero
    };
    ```

4. コードを更新したので、Visual Studio Code の **[再起動]** ボタン (![Visual Studio Code の [再起動] ボタン](./media/tutorial-develop-mongodb-nodejs-part6/restart-debugger-button.png)) を選択します。

5. インターネット ブラウザーでページを最新の情報に更新し、**[Add New Hero]** ボタンを選択します。 新しいヒーローを追加します。ID に「9」を、名前に「Starlord」を、台詞に「Hi」を入力してください。 **[Save]** ボタンを選択して新しいヒーローを保存します。

6. 次に **[Starlord]** ヒーローを選択し、その台詞を "Hi" から "Bye" に変更して、**[Save]** ボタンを選択します。 

    [Network] タブで ID を選択すると、ペイロードが表示されます。 ペイロードを見ると、台詞が "Bye" に設定されていることが確認できます。

    ![Heroes アプリと [Network] タブでペイロードを表示](./media/tutorial-develop-mongodb-nodejs-part6/put-hero-function.png) 

    いずれかのヒーローを UI で削除して、削除操作にかかった時間を確認することもできます。 "Fred" というヒーローの [Delete] ボタンを選択して試してみましょう。

    ![Heroes アプリと [Network] タブで関数の所要時間を表示](./media/tutorial-develop-mongodb-nodejs-part6/times.png) 

    ページを最新の情報に更新すると、ヒーローを取得するのに要した時間が [Network] タブに表示されます。 これらはごく短時間ではありますが、どの国にデータが置かれているかや、ユーザーの近隣エリアで geo レプリケーションを実行できるかどうかによって、大きく変動します。 geo レプリケーションの詳細については、近日中に次のチュートリアルで取り上げる予定です。

## <a name="next-steps"></a>次の手順

本チュートリアルのこのパートでは、次の手順を行いました。

> [!div class="checklist"]
> * Post、Put、Delete の各関数をアプリに追加しました。 

このチュートリアル シリーズには、今後も新しい動画が追加される予定ですので、定期的にチェックしてください。

