---
title: Azure Cosmos DB 用 SQL API の Node.js チュートリアル
description: SQL API を使用して Azure Cosmos DB に接続して照会する方法を示す Node.js チュートリアル
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 06/05/2019
ms.author: dech
Customer intent: As a developer, I want to build a Node.js console application to access and manage SQL API account resources in Azure Cosmos DB, so that customers can better use the service.
ms.openlocfilehash: ba1ec821bd25e3b9f4479c3d09fdf5ab981ab0a7
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305509"
---
# <a name="tutorial-build-a-nodejs-console-app-with-the-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>チュートリアル:JavaScript SDK を使用して、Azure Cosmos DB SQL API データを管理するための Node.js コンソール アプリを構築する

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.JS](sql-api-nodejs-get-started.md)
> 

開発者であれば、NoSQL ドキュメント データを使用するアプリケーションをお持ちかもしれません。 Azure Cosmos DB の SQL API アカウントを使用して、このドキュメント データを格納し、それにアクセスできます。 このチュートリアルでは、Azure Cosmos DB リソースを作成してそれらに対するクエリを実行する Node.js コンソール アプリケーションを構築する方法について説明します。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Azure Cosmos DB アカウントを作成して接続する。
> * アプリケーションを設定する。
> * データベースを作成する。
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

## <a id="SetupNode"></a>Node.js アプリケーションを設定する

アプリケーションを構築するコードを書く前に、アプリケーションのフレームワークを構築することができます。 次の手順を実行して、フレームワーク コードを含む Node.js アプリケーションを設定します。

1. お使いのターミナルを開きます。
2. Node.js アプリケーションを保存するフォルダーまたはディレクトリを見つけます。
3. 次のコマンドを使用して、2 つの空の JavaScript ファイルを作成します。

   * Windows:
     * ```fsutil file createnew app.js 0```
     * ```fsutil file createnew config.js 0```

   * Linux/OS X:
     * ```touch app.js```
     * ```touch config.js```

4. `package.json` ファイルを作成して初期化します。 次のコマンドを使用します。
   * ```npm init -y```

5. npm で @azure/cosmos モジュールをインストールします。 次のコマンドを使用します。
   * ```npm install @azure/cosmos --save```

## <a id="Config"></a>アプリの構成を設定する

アプリができたら、Azure Cosmos DB と対話できることを確認する必要があります。 次の手順のようにいくつかの構成設定を更新することで、Azure Cosmos DB と対話するようにアプリを設定できます。

1. 普段使用しているテキスト エディターで ```config.js``` を開きます。

1. 以下のコード スニペットをコピーして貼り付け、プロパティ ```config.endpoint``` と ```config.primaryKey``` を Azure Cosmos DB エンドポイント URI と主キーに設定します。 これらの構成はどちらも [Azure ポータル](https://portal.azure.com)にあります。

   ![Azure portal からキーを取得するスクリーンショット][keys]

   ```javascript
   // ADD THIS PART TO YOUR CODE
   var config = {}

   config.endpoint = "~your Azure Cosmos DB endpoint uri here~";
   config.primaryKey = "~your primary key here~";
   ``` 

1. ```database```、```container```、および ```items``` データをコピーして ```config``` オブジェクトに貼り付けます。```config.endpoint``` プロパティと ```config.primaryKey``` プロパティの設定に続けて追加してください。 データベースに保存するデータが既にある場合は、データをここに定義する代わりに、Azure Cosmos DB のデータ移行ツールを使用できます。 config.js ファイルには、次のコードが必要です。

   [!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/config.js)]

   JavaScript SDK では、*コンテナー*と*項目*という一般的な用語を使用しています。 コンテナーは、コレクション、グラフ、またはテーブルを表します。 項目は、ドキュメント、エッジ/頂点、行など、コンテナー内の内容を表します。 
   
   ```app.js``` ファイル内で参照できるように ```config``` オブジェクトをエクスポートするために、`module.exports = config;` コードが使用されます。

## <a id="Connect"></a>Azure Cosmos DB アカウントに接続する

1. テキスト エディターで、空の ```app.js``` ファイルを開きます。 ```@azure/cosmos``` モジュールと新たに作成した ```config``` モジュールをインポートするために、以下のコードをコピーして貼り付けます。

   ```javascript
   // ADD THIS PART TO YOUR CODE
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');
   ```

1. 先ほど保存した ```config.endpoint``` と ```config.primaryKey``` を使用して新しい CosmosClient を作成するために、以下のコードをコピーして貼り付けます。

   ```javascript
   const config = require('./config');

   // ADD THIS PART TO YOUR CODE
   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });
   ```
   
> [!Note]
> **Cosmos DB エミュレーター**に接続する場合は、カスタム接続ポリシーを作成して SSL 検証を無効にします。
>   ```
>   const ConnectionPolicy = require('@azure/cosmos').ConnectionPolicy;
>   const connectionPolicy = new ConnectionPolicy();
>   connectionPolicy.DisableSSLVerification = true;
>
>   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey }, connectionPolicy });
>   ```

Azure Cosmos DB クライアントを初期化するためのコードは以上で完成です。続いて、Azure Cosmos DB リソースの使用方法について説明します。

## <a name="create-a-database"></a>データベースを作成する

1. データベース ID およびコンテナー ID を設定するために、以下のコードをコピーして貼り付けます。 Azure Cosmos DB クライアントは、これらの ID を通じて適切なデータベースとコンテナーを検出します。

   ```javascript
   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   // ADD THIS PART TO YOUR CODE
   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };
   ```

   データベースは、**Databases** クラスの `createIfNotExists` または create 関数を使用して作成できます。 データベースは、コンテナーに分割された項目の論理上のコンテナーです。 

2. app.js ファイルの ```databaseId``` と ```containerId``` の定義の下に、**createDatabase** メソッドと **readDatabase** メソッドをコピーして貼り付けます。 **createDatabase** 関数は、```config``` オブジェクトから指定された ID が ```FamilyDatabase``` の新しいデータベースを作成します (該当するデータベースが存在しない場合)。 **readDatabase** 関数は、データベースの定義を読み取り、データベースが存在することを確認します。

   ```javascript
   /**
    * Create the database if it does not exist
    */
   async function createDatabase() {
       const { database } = await client.databases.createIfNotExists({ id: databaseId });
       console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
      const { body: databaseDefinition } = await client.database(databaseId).read();
      console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }
   ```

3. 終了メッセージを印刷するヘルパー関数 **exit** を追加するために、**createDatabase** 関数と **readDatabase** 関数を設定したコードの下に以下のコードをコピーして貼り付けます。 

   ```javascript
   // ADD THIS PART TO YOUR CODE
   function exit(message) {
      console.log(message);
      console.log('Press any key to exit');
      process.stdin.setRawMode(true);
      process.stdin.resume();
      process.stdin.on('data', process.exit.bind(process, 0));
   };
   ```

4. **createDatabase** 関数と **readDatabase** 関数を呼び出すために、**exit** 関数を設定したコードの下に以下のコードをコピーして貼り付けます。

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error \${JSON.stringify(error)}`) });
   ```

   この時点で、```app.js``` のコードは次のようになります。

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

    /**
    * Create the database if it does not exist
    */
    async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
    console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error) }`) });
   ```

5. ターミナルで、```app.js``` ファイルを見つけ、コマンドを実行します。 

   ```bash 
   node app.js
   ```

## <a id="CreateContainer"></a>コンテナーを作成する

次に、データの保存とクエリに使用できるように、Azure Cosmos DB アカウント内にコンテナーを作成します。 

> [!WARNING]
> コンテナーを作成すると、料金に影響があります。 かかる料金については、[料金ページ](https://azure.microsoft.com/pricing/details/cosmos-db/)を参照してください。

コンテナーは、**Containers** クラスの `createIfNotExists` または create 関数を使用して作成できます。  コンテナーは、項目 (SQL API の場合は JSON ドキュメント) と、それに関連する JavaScript アプリケーション ロジックで構成されます。

1. app.js ファイルの **readDatabase** 関数の下に、**createContainer** 関数と **readContainer** 関数をコピーして貼り付けます。 **createContainer** 関数は、```config``` オブジェクトから指定された ```containerId``` を持つ新しいコンテナーを作成します (該当するコンテナーが存在しない場合)。 **readContainer** 関数は、コンテナー定義を読み取り、コンテナーが存在することを確認します。

   ```javascript
   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }
   ```

1. **createContainer** 関数と **readContainer** 関数を実行するために、**readDatabase** の呼び出しの下に以下のコードをコピーして貼り付けます。

   ```javascript
   createDatabase()
     .then(() => readDatabase())

     // ADD THIS PART TO YOUR CODE
     .then(() => createContainer())
     .then(() => readContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

   この時点で、```app.js``` のコードは次のようになります。

   ```javascript
   const CosmosClient = require('@azure/cosmos').CosmosClient;

   const config = require('./config');

   const endpoint = config.endpoint;
   const masterKey = config.primaryKey;

   const client = new CosmosClient({ endpoint: endpoint, auth: { masterKey: masterKey } });

   const HttpStatusCodes = { NOTFOUND: 404 };

   const databaseId = config.database.id;
   const containerId = config.container.id;
   const partitionKey = { kind: "Hash", paths: ["/Country"] };

   /**
   * Create the database if it does not exist
   */
   async function createDatabase() {
     const { database } = await client.databases.createIfNotExists({ id: databaseId });
     console.log(`Created database:\n${database.id}\n`);
   }

   /**
   * Read the database definition
   */
   async function readDatabase() {
     const { body: databaseDefinition } = await client.database(databaseId).read();
     console.log(`Reading database:\n${databaseDefinition.id}\n`);
   }

   /**
   * Create the container if it does not exist
   */

   async function createContainer() {

    const { container } = await client.database(databaseId).containers.createIfNotExists({ id: containerId, partitionKey }, { offerThroughput: 400 });
    console.log(`Created container:\n${config.container.id}\n`);
   }

   /**
    * Read the container definition
   */
   async function readContainer() {
      const { body: containerDefinition } = await client.database(databaseId).container(containerId).read();
    console.log(`Reading container:\n${containerDefinition.id}\n`);
   }

   /**
   * Exit the app with a prompt
   * @param {message} message - The message to display
   */
   function exit(message) {
     console.log(message);
     console.log('Press any key to exit');
     process.stdin.setRawMode(true);
     process.stdin.resume();
     process.stdin.on('data', process.exit.bind(process, 0));
   }

   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. ターミナルで、```app.js``` ファイルを見つけ、コマンドを実行します。 

   ```bash 
   node app.js
   ```

## <a id="CreateItem"></a>項目を作成する

項目は、**Items** クラスの create 関数を使用して作成できます。 SQL API を使用している場合、項目はドキュメントとして投影されます。これは、ユーザー定義の (任意の) JSON コンテンツです。 これで、Azure Cosmos DB に項目を挿入できます。

1. **readContainer** 関数の下に、**createFamilyItem** 関数をコピーして貼り付けます。 **createFamilyItem** 関数は、```config``` オブジェクトに保存された JSON データを含む項目を作成します。 項目を作成する前に、同じ ID を持つ項目が存在していないことを確認します。

   ```javascript
   /**
   * Create family item
   */
   async function createFamilyItem(itemBody) {
      const { item } = await client.database(databaseId).container(containerId).items.upsert(itemBody);
      console.log(`Created family item with id:\n${itemBody.id}\n`);
   };
   ```

1. **createFamilyItem** 関数を実行するために、**readContainer** の呼び出しの下に以下のコードをコピーして貼り付けます。

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. ターミナルで、```app.js``` ファイルを見つけ、コマンドを実行します。 

   ```bash 
   node app.js
   ```


## <a id="Query"></a>Azure Cosmos DB リソースにクエリを実行する

Azure Cosmos DB では、各コンテナーに格納された JSON ドキュメントに対する豊富なクエリがサポートされています。 次のサンプル コードは、コンテナー内のドキュメントに対して実行できるクエリを示しています。

1. app.js ファイルの **createFamilyItem** 関数の下に、**queryContainer** 関数をコピーして貼り付けます。 以下のとおり、Azure Cosmos DB は SQL に似たクエリをサポートしています。

   ```javascript
   /**
   * Query the container using SQL
    */
   async function queryContainer() {
     console.log(`Querying container:\n${config.container.id}`);

     // query to return all children in a family
     const querySpec = {
        query: "SELECT VALUE r.children FROM root r WHERE r.lastName = @lastName",
        parameters: [
            {
                name: "@lastName",
                value: "Andersen"
            }
        ]
    };

    const { result: results } = await client.database(databaseId).container(containerId).items.query(querySpec, {enableCrossPartitionQuery:true}).toArray();
    for (var queryResult of results) {
        let resultString = JSON.stringify(queryResult);
        console.log(`\tQuery returned ${resultString}\n`);
    }
   };
   ```

1. **createFamilyItem** の呼び出しの下に、**queryContainer** 関数を実行するためのコードをコピーして貼り付けます。

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))

     // ADD THIS PART TO YOUR CODE
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. ターミナルで、```app.js``` ファイルを見つけ、コマンドを実行します。

   ```bash 
   node app.js
   ```


## <a id="ReplaceItem"></a>項目を置換する
Azure Cosmos DB は、項目の内容の置換をサポートします。

1. app.js ファイルの **queryContainer** 関数の下に、**replaceFamilyItem** 関数をコピーして貼り付けます。 子のプロパティ "grade" が以前の 5 から 6 に変更されていることに注意してください。

   ```javascript
   // ADD THIS PART TO YOUR CODE
   /**
   * Replace the item by ID.
   */
   async function replaceFamilyItem(itemBody) {
      console.log(`Replacing item:\n${itemBody.id}\n`);
      // Change property 'grade'
      itemBody.children[0].grade = 6;
     const { item } = await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).replace(itemBody);
   };
   ```

1. **replaceFamilyItem** 関数を実行するために、**queryContainer** の呼び出しの下に以下のコードをコピーして貼り付けます。 さらに、もう一度 **queryContainer** を呼び出して項目が正常に変更されたことを確認するためのコードを追加します。

   ```javascript
   createDatabase()
     .then(() => readDatabase())
     .then(() => createContainer())
     .then(() => readContainer())
     .then(() => createFamilyItem(config.items.Andersen))
     .then(() => createFamilyItem(config.items.Wakefield))
     .then(() => queryContainer())

     // ADD THIS PART TO YOUR CODE
     .then(() => replaceFamilyItem(config.items.Andersen))
     .then(() => queryContainer())
     // ENDS HERE

     .then(() => { exit(`Completed successfully`); })
     .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. ターミナルで、```app.js``` ファイルを見つけ、コマンドを実行します。

   ```bash 
   node app.js
   ```


## <a id="DeleteItem"></a>項目を削除する

Azure Cosmos DB は、JSON 項目の削除をサポートします。

1. **replaceFamilyItem** 関数の下に、**deleteFamilyItem** 関数をコピーして貼り付けます。

   ```javascript
   /**
   * Delete the item by ID.
   */
   async function deleteFamilyItem(itemBody) {
     await client.database(databaseId).container(containerId).item(itemBody.id, itemBody.Country).delete(itemBody);
      console.log(`Deleted item:\n${itemBody.id}\n`);
   };
   ```

1. **deleteFamilyItem** 関数を実行するために、2 つ目の **queryContainer** の呼び出しの下に以下のコードをコピーして貼り付けます。

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer
      ())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())

    // ADD THIS PART TO YOUR CODE
      .then(() => deleteFamilyItem(config.items.Andersen))
    // ENDS HERE

    .then(() => { exit(`Completed successfully`); })
    .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

1. ターミナルで、```app.js``` ファイルを見つけ、コマンドを実行します。 

   ```bash 
   node app.js
   ```


## <a id="DeleteDatabase"></a>データベースを削除する

作成したデータベースを削除すると、データベースとすべての子リソース (コンテナー、項目など) が削除されます。

1. データベースとそのすべての子リソースを削除するために、**deleteFamilyItem** 関数の下に **cleanup** 関数をコピーして貼り付けます。

   ```javascript
   /**
   * Cleanup the database and container on completion
   */
   async function cleanup() {
     await client.database(databaseId).delete();
   }
   ```

1. **cleanup** 関数を実行するために、**deleteFamilyItem** の呼び出しの下に以下のコードをコピーして貼り付けます。

   ```javascript
   createDatabase()
      .then(() => readDatabase())
      .then(() => createContainer())
      .then(() => readContainer())
      .then(() => createFamilyItem(config.items.Andersen))
      .then(() => createFamilyItem(config.items.Wakefield))
      .then(() => queryContainer())
      .then(() => replaceFamilyItem(config.items.Andersen))
      .then(() => queryContainer())
      .then(() => deleteFamilyItem(config.items.Andersen))

      // ADD THIS PART TO YOUR CODE
      .then(() => cleanup())
      // ENDS HERE

      .then(() => { exit(`Completed successfully`); })
      .catch((error) => { exit(`Completed with error ${JSON.stringify(error)}`) });
   ```

## <a id="Run"></a>Node.js アプリケーションを実行する

全体的なコードは次のようになります。

[!code-javascript[nodejs-get-started](~/cosmosdb-nodejs-get-started/app.js)]

ターミナルで、```app.js``` ファイルを見つけ、コマンドを実行します。 

```bash 
node app.js
```

開始したアプリケーションの出力が表示されます。 出力は、次のテキスト例のようなものになるはずです。

   ```
    Created database:
    FamilyDatabase

    Reading database:
    FamilyDatabase

    Created container:
    FamilyContainer

    Reading container:
    FamilyContainer

    Created family item with id:
    Anderson.1

    Created family item with id:
    Wakefield.7

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}]

    Replacing item:
    Anderson.1

    Querying container:
    FamilyContainer
            Query returned [{"firstName":"Henriette Thaulow","gender":"female","grade":6,"pets":[{"givenName":"Fluffy"}]}]

    Deleted item:
    Anderson.1

    Completed successfully
    Press any key to exit
   ```

## <a id="GetSolution"></a>完全な Node.js チュートリアル ソリューションを入手する 

このチュートリアルの手順を実行する時間がない場合や、コードをダウンロードするだけの場合は、[GitHub](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started ) から入手できます。 

この記事のすべてのコードを含む使用の開始ソリューションを実行するには、以下が必要です。 

* [Azure Cosmos DB アカウント][create-account]。 
* GitHub で入手可能な[使用の開始](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-getting-started)ソリューション。 

npm でプロジェクトの依存関係をインストールします。 次のコマンドを使用します。 

* ```npm install``` 

次に、```config.js``` ファイルで、config.endpoint と config.primaryKey の値を更新します。このとき、「[手順 3:アプリの構成を設定する](#Config)」の説明に従います。  

次に、ターミナルで ```app.js``` ファイルを見つけ、次のコマンドを実行します。  

```bash  
node app.js 
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

これらのリソースが必要なくなったら、リソース グループ、Azure Cosmos DB アカウント、およびすべての関連リソースを削除できます。 そうするには、Azure Cosmos DB アカウントのために使用したリソース グループを選択し、 **[削除]** を選択した後、削除するリソース グループの名前を確認します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Cosmos DB アカウントの監視](monitor-accounts.md)

[create-account]: create-sql-api-dotnet.md#create-account
[keys]: media/sql-api-nodejs-get-started/node-js-tutorial-keys.png
