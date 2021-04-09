---
title: Azure Cosmos DB 用 SQL API の Node.js チュートリアル
description: SQL API を使用して Azure Cosmos DB に接続して照会する方法を示す Node.js チュートリアル
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: dech
ms.custom: devx-track-js
ms.openlocfilehash: 1115d7bb0d6857aa39f246743df54a6cf3fd3676
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93098661"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>チュートリアル:JavaScript SDK を使用して、Azure Cosmos DB SQL API データを管理するための Node.js コンソール アプリを構築する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](./create-sql-api-java.md)
> * [Async Java](./create-sql-api-java.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

お客様は開発者として、NoSQL ドキュメント データが使用されるアプリケーションをお持ちかもしれません。 Azure Cosmos DB の SQL API アカウントを使用して、このドキュメント データを格納し、それにアクセスできます。 このチュートリアルでは、Azure Cosmos DB リソースを作成してそれらに対するクエリを実行する Node.js コンソール アプリケーションを構築する方法について説明します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Azure Cosmos DB アカウントを作成して接続する。
> * アプリケーションを設定する。
> * データベースを作成します。
> * コンテナーを作成します。
> * コンテナーに項目を追加する。
> * 項目、コンテナー、およびデータベースに対する基本的な操作を実行する。

## <a name="prerequisites"></a>前提条件 

以下のリソースがそろっていることを確認してください。

* アクティブな Azure アカウントアカウントがない場合、Azure 試用版にサインアップして、最大 10 件の無料 Mobile Apps を入手できます。 これがない場合は、 [Azure の無料試用版](https://azure.microsoft.com/pricing/free-trial/)にサインアップできます。 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js](https://nodejs.org/) v6.0.0 以降。

## <a name="create-azure-cosmos-db-account"></a>Azure Cosmos DB アカウントを作成する

それでは、Azure Cosmos DB アカウントを作成してみましょう。 使用するアカウントが既にある場合は、「[Node.js アプリケーションをセットアップする](#SetupNode)」に進んでかまいません。 Azure Cosmos DB Emulator を使用する場合は、[Azure Cosmos DB Emulator](local-emulator.md) に関する記事に記載されている手順に従ってエミュレーターをセットアップし、「[Node.js アプリケーションをセットアップする](#SetupNode)」に進んでください。 

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="set-up-your-nodejs-application"></a><a id="SetupNode"></a>Node.js アプリケーションを設定する

アプリケーションを構築するコードを書く前に、アプリケーションのフレームワークを構築することができます。 次の手順を実行して、フレームワーク コードを含む Node.js アプリケーションを設定します。

1. お使いのターミナルを開きます。
2. Node.js アプリケーションを保存するフォルダーまたはディレクトリを見つけます。
3. 次のコマンドを使用して、空の JavaScript ファイルを作成します。

   * Windows:
     * `fsutil file createnew app.js 0`
     * `fsutil file createnew config.js 0`
     * `md data`
     * `fsutil file createnew data\databaseContext.js 0`

   * Linux/OS X:
     * `touch app.js`
     * `touch config.js`
     * `mkdir data`
     * `touch data/databaseContext.js`

4. `package.json` ファイルを作成して初期化します。 次のコマンドを使用します。
   * ```npm init -y```

5. npm で @azure/cosmos モジュールをインストールします。 次のコマンドを使用します。
   * ```npm install @azure/cosmos --save```

## <a name="set-your-apps-configurations"></a><a id="Config"></a>アプリの構成を設定する

アプリができたら、Azure Cosmos DB と対話できることを確認する必要があります。 次の手順のようにいくつかの構成設定を更新することで、Azure Cosmos DB と対話するようにアプリを設定できます。

1. 使い慣れたテキスト エディターで *config.js* ファイルを開きます。

1. 次のコード スニペットをコピーして *config.js* ファイルに貼り付け、プロパティ `endpoint` と `key` をそれぞれ Azure Cosmos DB エンドポイント URI と主キーに設定します。 データベース名とコンテナー名は、それぞれ **Tasks** と **Items** に設定されます。 このアプリケーションに使用するパーティション キーは **/category** です。

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/config.js":::

   エンドポイントとキーの詳細については、[Azure portal](https://portal.azure.com) の **[キー]** ペインを参照してください。

   :::image type="content" source="media/sql-api-nodejs-get-started/node-js-tutorial-keys.png" alt-text="Azure portal からキーを取得するスクリーンショット":::

JavaScript SDK では、"*コンテナー*" と "*項目*" という一般的な用語を使用しています。 コンテナーは、コレクション、グラフ、またはテーブルを表します。 項目は、ドキュメント、エッジ/頂点、行など、コンテナー内の内容を表します。 前のコード スニペットでは、`module.exports = config;` コードを使用して config オブジェクトをエクスポートし、*app.js* ファイル内で参照できるようにしています。

## <a name="create-a-database-and-a-container"></a>データベースとコンテナーを作成する

1. 使い慣れたテキスト エディターで *databaseContext.js* ファイルを開きます。

1. 次のコードをコピーし、*databaseContext.js* ファイルに貼り付けます。 このコードでは、Azure Cosmos アカウントに "Tasks" データベースと "Items" コンテナーがまだ存在しない場合に、これらを作成する関数を定義しています。

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/data/databaseContext.js" id="createDatabaseAndContainer":::

   データベースは、コンテナーに分割された項目の論理上のコンテナーです。 データベースは、**Databases** クラスの `createIfNotExists` または create 関数を使用して作成します。 コンテナーは、項目 (SQL API の場合は JSON ドキュメント) で構成されます。 コンテナーは、**Containers** クラスの `createIfNotExists` または create 関数を使用して作成します。 コンテナーを作成したら、データを格納してクエリを実行できます。

   > [!WARNING]
   > コンテナーを作成すると、料金に影響があります。 かかる料金については、[料金ページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。

## <a name="import-the-configuration"></a>構成をインポートする

1. 使い慣れたテキスト エディターで *app.js* ファイルを開きます。

1. 次のコードをコピーして貼り付け、前の手順で定義した `@azure/cosmos` モジュール、構成、および databaseContext をインポートします。 

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="ImportConfiguration":::

## <a name="connect-to-the-azure-cosmos-account"></a>Azure Cosmos アカウントに接続する

次のコードをコピーして *app.js* ファイルに貼り付け、前に保存したエンドポイントとキーを使用して、新しい CosmosClient オブジェクトを作成します。

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateClientObjectDatabaseContainer":::

> [!Note]
> **Cosmos DB エミュレーター** に接続する場合は、ノード プロセスの TLS 検証を無効にします。
>   ```javascript
>   process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
>   const client = new CosmosClient({ endpoint, key });
>   ```

Azure Cosmos DB クライアントを初期化するためのコードは以上で完成です。続いて、Azure Cosmos DB リソースの使用方法について説明します。

## <a name="query-items"></a><a id="QueryItem"></a>クエリ項目

Azure Cosmos DB では、各コンテナーに格納された JSON 項目に対する豊富なクエリがサポートされています。 次のサンプル コードは、コンテナー内の項目に対して実行できるクエリを示しています。`Items` クラスの query 関数を使用して、項目に対してクエリを実行できます。 次のコードを *app.js* ファイルに追加し、Azure Cosmos アカウントから項目に対してクエリを実行します。

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="QueryItems":::

## <a name="create-an-item"></a><a id="CreateItem"></a>項目を作成する

項目は、`Items` クラスの create 関数を使用して作成できます。 SQL API を使用している場合、項目はドキュメントとして投影されます。これは、ユーザー定義の (任意の) JSON コンテンツです。 このチュートリアルでは、Tasks データベース内に新しい項目を作成します。

1. app.js ファイルで、次のように項目定義を定義します。

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DefineNewItem":::

1. 次のコードを追加して、前に定義した項目を作成します。

   :::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="CreateItem":::

## <a name="update-an-item"></a><a id="ReplaceItem"></a>項目を更新する

Azure Cosmos DB は、項目の内容の置換をサポートしています。 次のコードをコピーして、*app.js* ファイルに貼り付けます。 このコードは、コンテナーから項目を取得し、*isComplete* フィールドを true に更新します。

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="UpdateItem":::

## <a name="delete-an-item"></a><a id="DeleteItem"></a>項目を削除する

Azure Cosmos DB は、JSON 項目の削除をサポートします。 次のコードは、ID で項目を取得し、削除する方法を示しています。 次のコードをコピーして、*app.js* ファイルに貼り付けます。

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js" id="DeleteItem":::

## <a name="run-your-nodejs-application"></a><a id="Run"></a>Node.js アプリケーションを実行する

全体的なコードは次のようになります。

:::code language="javascript" source="~/cosmosdb-nodejs-get-started/app.js":::

ターミナルで、```app.js``` ファイルを見つけ、コマンドを実行します。

```bash 
node app.js
```

開始したアプリケーションの出力が表示されます。 出力は、次のテキスト例のようなものになるはずです。

```
Created database:
Tasks

Created container:
Items

Querying container: Items
1 - Pick up apples and strawberries.

Created new item: 3 - Complete Cosmos DB Node.js Quickstart ⚡

Updated item: 3 - Complete Cosmos DB Node.js Quickstart ⚡
Updated isComplete to true

Deleted item with id: 3
```

## <a name="get-the-complete-nodejs-tutorial-solution"></a><a id="GetSolution"></a>完全な Node.js チュートリアル ソリューションを入手する

このチュートリアルの手順を実行する時間がない場合や、コードをダウンロードするだけの場合は、[GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ) から入手できます。

この記事のすべてのコードを含む使用の開始ソリューションを実行するには、以下が必要です。

* [Azure Cosmos DB アカウント][create-account]。
* GitHub で入手可能な[使用の開始](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started)ソリューション。

npm でプロジェクトの依存関係をインストールします。 次のコマンドを使用します。

* ```npm install``` 

次に、```config.js``` ファイルで、config.endpoint と config.key の値を更新します。このとき、「[手順 3:アプリの構成を設定する](#Config)」の説明に従います。  

次に、ターミナルで ```app.js``` ファイルを見つけ、次のコマンドを実行します。  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これらのリソースが必要なくなったら、リソース グループ、Azure Cosmos DB アカウント、およびすべての関連リソースを削除できます。 そうするには、Azure Cosmos DB アカウントのためにお客様が使用したリソース グループを選択し、 **[削除]** を選択した後、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Cosmos DB アカウントの監視](./monitor-cosmos-db.md)

[create-account]: create-sql-api-dotnet.md#create-account