---
title: クイックスタート - Node.js を使用して Azure Cosmos DB の SQL API アカウントからクエリを実行する
description: Azure Cosmos DB の SQL API アカウントに接続してデータを照会するアプリを Node.js で作成する方法について説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 09/22/2020
ms.author: anfeldma
ms.custom: devx-track-js
ms.openlocfilehash: b4ed27e1515e898a71fb503bb0f260c608ef9f6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090207"
---
# <a name="quickstart-use-nodejs-to-connect-and-query-data-from-azure-cosmos-db-sql-api-account"></a>クイック スタート:Node.js を使用して Azure Cosmos DB の SQL API アカウントに接続してデータを照会する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> - [.NET V3](create-sql-api-dotnet.md)
> - [.NET V4](create-sql-api-dotnet-V4.md)
> - [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> - [Node.js](create-sql-api-nodejs.md)
> - [Python](create-sql-api-python.md)
> - [Xamarin](create-sql-api-xamarin-dotnet.md)

このクイックスタートでは、Azure portal から Azure Cosmos DB のSQL API アカウントを作成して管理し、また GitHub から複製された Node.js アプリを使用します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能により、ドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

## <a name="walkthrough-video"></a>チュートリアル ビデオ

この記事の内容の完全なチュートリアルについては、こちらのビデオをご覧ください。

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Quickstart-Use-Nodejs-to-connect-and-query-data-from-Azure-Cosmos-DB-SQL-API-account/player]

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。 または、`https://localhost:8081` の URI でキー `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` を使用して [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) を使用することもできます。
- [Node.js 6.0.0+](https://nodejs.org/)。
- [Git](https://www.git-scm.com/downloads).

## <a name="create-an-azure-cosmos-account"></a>Azure Cosmos アカウントを作成する

このクイックスタートの用途であれば、[[Azure Cosmos DB を無料で試す]](https://azure.microsoft.com/try/cosmosdb/) オプションを使用して Azure Cosmos アカウントを作成することができます。

1. 「[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)」ページに移動します。

1. **SQL** API アカウントを選択し、 **[作成]** を選択します。 Microsoft アカウントを使用してサインインしてください。

1. サインインに成功すれば、Azure Cosmos アカウントの準備は完了です。 **[Open in the Azure portal]\(Azure portal で開く\)** を選択して、新しく作成したアカウントを開きます。

[Azure Cosmos DB を無料で試す] オプションには、Azure サブスクリプションは不要です。30 日の期間限定で Azure Cosmos アカウントが提供されます。 もっと長い期間 Azure Cosmos アカウントを使用する場合は、ご利用の Azure サブスクリプション内から[アカウントを作成](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account)する必要があります。

## <a name="add-a-container"></a>コンテナーの追加

Azure portal でデータ エクスプローラー ツールを使用してデータベースとコンテナーを作成できるようになりました。

1. **[データ エクスプローラー]**  >  **[新しいコンテナー]** の順に選択します。

   **[コンテナーの追加]** 領域が右端に表示されます。表示するには、右へスクロールする必要がある場合があります。

   :::image type="content" source="./media/create-sql-api-nodejs/azure-cosmosdb-data-explorer.png" alt-text="Azure portal の [データ エクスプローラー] の [コンテナーの追加] ウィンドウ":::

2. **[コンテナーの追加]** ページで、新しいコンテナーの設定を入力します。

   | 設定           | 推奨値 | 説明                                                                                                                                                                                                                                                                                                                                                                           |
   | ----------------- | --------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
   | **データベース ID**   | タスク           | 新しいデータベースの名前として _タスク_ を入力します。 データベース名は 1 文字以上 255 文字以内にする必要があります。`/, \\, #, ?` は使えず、末尾にスペースを入れることもできません。 **[Provision database throughput]\(データベース スループットをプロビジョニングする\)** オプションをオンにすると、データベースにプロビジョニングされたスループットをデータベース内のすべてのコンテナーにわたって共有できます。 このオプションは、コストの削減にも役立ちます。 |
   | **スループット**    | 400             | スループットを 400 要求ユニット/秒 (RU/秒) のままにします。 待ち時間を短縮する場合、後でスループットをスケールアップできます。                                                                                                                                                                                                                                                    |
   | **コンテナー ID**  | アイテム           | 新しいコンテナーの名前として「_項目_」と入力します。 コンテナー ID には、データベース名と同じ文字要件があります。                                                                                                                                                                                                                                                               |
   | **パーティション キー** | /category       | この記事で説明するサンプルでは、 _/category_ をパーティション キーとして使用します。                                                                                                                                                                                                                                                                                                           |

   上記の設定に加え、必要に応じて、このコンテナー用に **一意なキー** を追加できます。 この例では、このフィールドを空のままにしましょう。 一意なキーを使用すると、開発者はデータベースにデータ整合性のレイヤーを追加できます。 コンテナーの作成中に一意キー ポリシーを作成すると、パーティション キーごとに 1 つ以上の値の一意性が保証されます。 詳細については、記事「[Azure Cosmos DB における一意なキー](unique-keys.md)」を参照してください。

   **[OK]** を選択します。 新しいデータベースとコンテナーがデータ エクスプローラーに表示されます。

## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>データのクエリ

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

ここで、GitHub から Node.js アプリの複製を作成し、接続文字列を設定して実行します。

1. 次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

   ```bash
   git clone https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started.git
   ```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内の Azure Cosmos データベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 関心がない場合は、「[接続文字列の更新](#update-your-connection-string)」に進んでください。

以前のバージョンの SQL JavaScript SDK に慣れている方は、"_コレクション_" や "_ドキュメント_" といった用語をよく目にしたかと思います。 Azure Cosmos DB は [複数の API モデル](introduction.md)をサポートしているため、[バージョン 2.0 以上の JavaScript SDK](https://www.npmjs.com/package/@azure/cosmos) では、コレクション、グラフ、テーブルを表す用語として "_コンテナー_" が、またコンテナーの内容を表す用語として "_項目_" が一般的に使用されます。

Cosmos DB JavaScript SDK は "@azure/cosmos" と呼ばれ、npm からインストールできます。

```bash
npm install @azure/cosmos
```

次のスニペットはすべて _app.js_ ファイルからのものです。

- `CosmosClient` は `@azure/cosmos` npm パッケージからインポートされます。

  ```javascript
  const CosmosClient = require("@azure/cosmos").CosmosClient;
  ```

- 新しい `CosmosClient` オブジェクトが初期化されます。

  ```javascript
  const client = new CosmosClient({ endpoint, key });
  ```

- "タスク" データベースを選択します。

  ```javascript
  const database = client.database(databaseId);
  ```

- "アイテム" コンテナーまたはコレクションを選択します。

  ```javascript
  const container = database.container(containerId);
  ```

- "アイテム" コンテナー内のすべての項目を選択します。

  ```javascript
  // query to return all items
  const querySpec = {
    query: "SELECT * from c"
  };

  const { resources: items } = await container.items
    .query(querySpec)
    .fetchAll();
  ```

- 新しい項目を作成します

  ```javascript
  const { resource: createdItem } = await container.items.create(newItem);
  ```

- 項目を更新します

  ```javascript
  const { id, category } = createdItem;

  createdItem.isComplete = true;
  const { resource: updatedItem } = await container
    .item(id, category)
    .replace(createdItem);
  ```

- 項目を削除する

  ```javascript
  const { resource: result } = await container.item(id, category).delete();
  ```

> [!NOTE]
> "更新" メソッドと "削除" メソッドの両方で、`container.item()` を呼び出してデータベースから項目を選択する必要があります。 渡される 2 つのパラメーターは、項目の ID と項目のパーティション キーです。 この場合、パーティション キーは "category" フィールドの値です。

## <a name="update-your-connection-string"></a>接続文字列を更新する

次に、Azure portal に戻って、Azure Cosmos アカウントの接続文字列情報を取得します。 アプリからデータベースに接続できるよう、接続文字列をアプリにコピーします。

1. [Azure portal](https://portal.azure.com/) の Azure Cosmos DB アカウントで、左のナビゲーションの **[キー]** を選択してから **[読み取り/書き込みキー]** を選択します。 次の手順では、画面の右側にあるコピー ボタンを使用して、URI と主キーを _app.js_ ファイルにコピーします。

   :::image type="content" source="./media/create-sql-api-dotnet/keys.png" alt-text="Azure portal の [キー] ブレードでアクセス キーを表示およびコピーする":::

2. _config.js_ ファイルを開きます。

3. ポータルから (コピー ボタンを使用して) URI 値をコピーし、_config.js_ の endpoint キーの値に設定します。

   `endpoint: "<Your Azure Cosmos account URI>"`

4. 次に、ポータルから [プライマリ キー] 値をコピーし、_config.js_ 内の `config.key` の値に設定します。 これで、Azure Cosmos DB と通信するために必要なすべての情報でアプリを更新しました。

   `key: "<Your Azure Cosmos account key>"`

## <a name="run-the-app"></a>アプリを実行する

1. ターミナルで `npm install` を実行して、"@azure/cosmos" npm パッケージをインストールします。

2. ターミナルで `node app.js` を実行し、node アプリケーションを起動します。

3. 先ほどこのクイックスタートで作成した 2 つの項目が列挙されます。新しい項目が作成されます。 その項目の "isComplete" フラグが "true" に更新された後、最終的にその項目は削除されます。

このサンプル アプリケーションの実験を続けるか、Data Explorer に戻ってデータに変更を加えたりデータを操作したりすることができます。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure Cosmos DB アカウントを作成し、データ エクスプローラーを使用してコンテナーを作成し、Node.js アプリを実行する方法を説明しました。 これで、Azure Cosmos DB アカウントに追加のデータをインポートできるようになりました。

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](import-data.md)
