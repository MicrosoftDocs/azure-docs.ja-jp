---
title: Node.js Mongoose アプリケーションを Azure Cosmos DB に接続する
description: Mongoose フレームワークを使用して Azure Cosmos DB のデータを格納および管理する方法について説明します。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18, devx-track-js
ms.openlocfilehash: 8958699ae279d2613f8dbadca802ee2137407e75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94442414"
---
# <a name="connect-a-nodejs-mongoose-application-to-azure-cosmos-db"></a>Node.js Mongoose アプリケーションを Azure Cosmos DB に接続する
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

このチュートリアルでは、Cosmos DB にデータを格納するときに [Mongoose フレームワーク](https://mongoosejs.com/)を使用する方法を示します。 このチュートリアルでは、Azure Cosmos DB の MongoDB 用 API を使用します。 知らない場合に備えて説明すると、Mongoose は、Node.js での MongoDB 用のオブジェクト モデル化フレームワークです。アプリケーション データをモデル化するための単純なスキーマベース ソリューションが提供されます。

Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 ドキュメント、キー/値、およびグラフ データベースをすばやく作成したり、クエリを実行したりでき、そのすべてで、Cosmos DB の中核にあるグローバル配信および水平スケール機能が活用されます。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

[Node.js](https://nodejs.org/) バージョン v0.10.29 以降

## <a name="create-a-cosmos-account"></a>Cosmos アカウントを作成する

Cosmos アカウントを作成しましょう。 使用するアカウントが既にある場合は、「Node.js アプリケーションをセットアップする」に進んでかまいません。 Azure Cosmos DB Emulator を使用する場合は、[Azure Cosmos DB Emulator](local-emulator.md) に関する記事に記載されている手順に従ってエミュレーターをセットアップし、「Node.js アプリケーションをセットアップする」に進んでください。

[!INCLUDE [cosmos-db-create-dbaccount-mongodb](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

### <a name="create-a-database"></a>データベースを作成する 
このアプリケーションでは、Azure Cosmos DB でコレクションを作成する 2 つの方法について説明します。 
- **各オブジェクト モデルを個別のコレクションに格納する**:[専用のスループットを使用してデータベースを作成する](set-throughput.md#set-throughput-on-a-database)ことをお勧めします。 この容量モデルを使用すると、コスト効率が向上します。

    :::image type="content" source="./media/mongodb-mongoose/db-level-throughput.png" alt-text="Node.js のチュートリアル - Mongoose Node モジュールで使用するために、データ エクスプローラーで Azure Cosmos DB アカウントのデータベースを作成する方法を示す Azure portal のスクリーンショット":::

- **すべてのオブジェクト モデルを 1 つの Cosmos DB コレクションに格納する**:すべてのモデルを 1 つのコレクションに格納する場合は、[スループットのプロビジョニング] オプションを選択せずに、新しいデータベースを作成できます。 この容量モデルを使用すると、すべてのオブジェクト モデルに対して独自のスループット容量を持つ各コレクションが作成されます。

データベースを作成したら、以下の `COSMOSDB_DBNAME` 環境変数で名前を使用します。

## <a name="set-up-your-nodejs-application"></a>Node.js アプリケーションをセットアップする

>[!Note]
> アプリケーションをセットアップするのではなくサンプル コードを確認するだけの場合は、このチュートリアルで使用される[サンプル](https://github.com/Azure-Samples/Mongoose_CosmosDB)を複製して、Node.js Mongoose アプリケーションを Azure Cosmos DB 上に構築します。

1. 選択したフォルダーに Node.js アプリケーションを作成するには、ノードのコマンド プロンプトで次のコマンドを実行します。

    ```npm init```

    質問に回答すると、プロジェクトが準備完了になります。

2. 新しいファイルをフォルダーに追加し、名前を ```index.js``` にします。
3. 次の ```npm install``` オプションのいずれかを使用して、必要なパッケージをインストールします。
   * Mongoose: ```npm install mongoose@5 --save```

     > [!Note]
     > 以下の Mongoose の接続の例は Mongoose 5+ に基づいており、これはその前のバージョンから変更されています。
    
   * Dotenv (.env ファイルからシークレットを読み込む場合): ```npm install dotenv --save```

     >[!Note]
     > ```--save``` フラグによって、package.json ファイルに依存関係が追加されます。

4. index.js ファイルに依存関係をインポートします。

    ```JavaScript
   var mongoose = require('mongoose');
   var env = require('dotenv').config();   //Use the .env file to load the variables
    ```

5. Cosmos DB の接続文字列と Cosmos DB の名前を ```.env``` ファイルに追加します。 プレースホルダー {cosmos-account-name} および {dbname} を独自の Cosmos アカウント名およびデータベース名に置き換えます。このとき、中かっこ記号は入力しません。

    ```JavaScript
   # You can get the following connection details from the Azure portal. You can find the details on the Connection string pane of your Azure Cosmos account.

   COSMOSDB_USER = "<Azure Cosmos account's user name, usually the database account name>"
   COSMOSDB_PASSWORD = "<Azure Cosmos account password, this is one of the keys specified in your account>"
   COSMOSDB_DBNAME = "<Azure Cosmos database name>"
   COSMOSDB_HOST= "<Azure Cosmos Host name>"
   COSMOSDB_PORT=10255
    ```

6. index.js の最後に次のコードを追加することによって、Mongoose フレームワークを使用して Cosmos DB に接続します。
    ```JavaScript
   mongoose.connect("mongodb://"+process.env.COSMOSDB_HOST+":"+process.env.COSMOSDB_PORT+"/"+process.env.COSMOSDB_DBNAME+"?ssl=true&replicaSet=globaldb", {
      auth: {
        user: process.env.COSMOSDB_USER,
        password: process.env.COSMOSDB_PASSWORD
      },
    useNewUrlParser: true,
    useUnifiedTopology: true,
    retryWrites: false
    })
    .then(() => console.log('Connection to CosmosDB successful'))
    .catch((err) => console.error(err));
    ```
    >[!Note]
    > ここで、環境変数は、'dotenv' npm パッケージを使用して process.env.{variableName} によって読み込まれます。

    Azure Cosmos DB に接続したら、Mongoose でのオブジェクト モデルのセットアップを開始できます。

## <a name="best-practices-for-using-mongoose-with-cosmos-db"></a>Cosmos DB で Mongoose を使用するためのベスト プラクティス

ユーザーが作成するすべてのモデルに対して、Mongoose は新しいコレクションを作成します。 これは、以前に説明した[データベース レベルのスループット オプション](set-throughput.md#set-throughput-on-a-database)を使用して解決することをお勧めします。 1 つのコレクションを使用するには、Mongoose [Discriminators](https://mongoosejs.com/docs/discriminators.html) を使用する必要があります。 ディスクリミネーターはスキーマ継承メカニズムです。 これにより、基礎となる同一の MongoDB コレクション上で、スキーマが重複する複数のモデルを持つことが可能になります。

同一コレクション内にさまざまなデータ モデルを格納でき、クエリの際にはフィルター句を使用して必要なデータのみをプルダウンできます。 各モデルについて説明します。

### <a name="one-collection-per-object-model"></a>オブジェクト モデルごとに 1 つのコレクション

このセクションでは、Azure Cosmos DB の MongoDB 用 API でこれを実現する方法について説明します。 コストと容量を制御できるため、この方法をお勧めします。 その結果、データベースの要求ユニットの数は、オブジェクト モデルの数に左右されなくなります。 これは Mongoose の既定の動作モデルなので、慣れているかもしれません。

1. 再び、```index.js``` を開きます。

1. 'Family' のスキーマ定義を作成します。

    ```JavaScript
    const Family = mongoose.model('Family', new mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
            gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }));
    ```

1. 'Family' のオブジェクトを作成します。

    ```JavaScript
    const family = new Family({
        lastName: "Volum",
        parents: [
            { firstName: "Thomas" },
            { firstName: "Mary Kay" }
        ],
        children: [
            { firstName: "Ryan", gender: "male", grade: 8 },
            { firstName: "Patrick", gender: "male", grade: 7 }
        ],
        pets: [
            { givenName: "Buddy" }
        ],
        address: { country: "USA", state: "WA", city: "Seattle" }
    });
    ```

1. 最後に、そのオブジェクトを Cosmos DB に保存しましょう。 これによりコレクションが自動的に作成されます。

    ```JavaScript
    family.save((err, saveFamily) => {
        console.log(JSON.stringify(saveFamily));
    });
    ```

1. 次に、別のスキーマとオブジェクトを作成します。 ここでは、家族連れ向けの行楽地 (Vacation Destinations) に対応するものを作成します。
   1. 前回と同じように、まずスキーマを作成します。
      ```JavaScript
      const VacationDestinations = mongoose.model('VacationDestinations', new mongoose.Schema({
       name: String,
       country: String
      }));
      ```

   1. サンプル オブジェクトを作成して保存します (このスキーマには複数のオブジェクトを追加できます)。
      ```JavaScript
      const vacaySpot = new VacationDestinations({
       name: "Honolulu",
       country: "USA"
      });

      vacaySpot.save((err, saveVacay) => {
       console.log(JSON.stringify(saveVacay));
      });
      ```

1. ここで、Azure Portal に移動すると、Cosmos DB に作成された 2 つのコレクションに気が付きます。

   :::image type="content" source="./media/mongodb-mongoose/mongo-mutliple-collections.png" alt-text="Node.js チュートリアル - Azure Cosmos DB アカウントを示し、複数のコレクション名が強調表示されている Azure portal のスクリーンショット - Node データベース":::

1. 最後に、Cosmos DB からデータを読み取りましょう。 既定の Mongoose 処理モデルを使用しているため、読み取り方法は Mongoose の他の読み取りと同じです。

    ```JavaScript
    Family.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family: " + JSON.stringify(fam)));
    });
    ```

### <a name="using-mongoose-discriminators-to-store-data-in-a-single-collection"></a>Mongoose ディスクリミネーターを使用して 1 つのコレクションにデータを格納する

この方法では、各コレクションのコストを最適化しやすくするために [Mongoose ディスクリミネーター](https://mongoosejs.com/docs/discriminators.html)を使用します。 ディスクリミネーターによって、識別するための "キー" を定義することができ、このキーを使用して、さまざまなオブジェクト モデルでの格納、識別、フィルター処理が可能になります。

ここでは、ベース オブジェクト モデルを作成し、識別キーを定義し、ベース モデルに拡張子として 'Family' と 'VacationDestinations' を追加します。

1. 基本構成を設定し、ディスクリミネーター キーを定義します。

    ```JavaScript
    const baseConfig = {
        discriminatorKey: "_type", //If you've got a lot of different data types, you could also consider setting up a secondary index here.
        collection: "alldata"   //Name of the Common Collection
    };
    ```

1. 次は、共通オブジェクト モデルを定義します。

    ```JavaScript
    const commonModel = mongoose.model('Common', new mongoose.Schema({}, baseConfig));
    ```

1. ここで 'Family' モデルを定義します。 ```mongoose.model```の代わりに ```commonModel.discriminator``` を使用していることに注意してください。 さらに、Mongoose スキーマに基本構成を追加します。 次のように、discriminatorKey は ```FamilyType``` になります。

    ```JavaScript
    const Family_common = commonModel.discriminator('FamilyType', new     mongoose.Schema({
        lastName: String,
        parents: [{
            familyName: String,
            firstName: String,
            gender: String
        }],
        children: [{
            familyName: String,
            firstName: String,
           gender: String,
            grade: Number
        }],
        pets:[{
            givenName: String
        }],
        address: {
            country: String,
            state: String,
            city: String
        }
    }, baseConfig));
    ```

1. 同様に、'VacationDestinations' に対してもう 1 つのスキーマを追加します。 ここでは、DiscriminatorKey は ```VacationDestinationsType``` です。

    ```JavaScript
    const Vacation_common = commonModel.discriminator('VacationDestinationsType', new mongoose.Schema({
        name: String,
        country: String
    }, baseConfig));
    ```

1. 最後に、モデルのオブジェクトを作成して保存します。
   1. 'Family' モデルにオブジェクトを追加します。
      ```JavaScript
      const family_common = new Family_common({
       lastName: "Volum",
       parents: [
           { firstName: "Thomas" },
           { firstName: "Mary Kay" }
       ],
       children: [
           { firstName: "Ryan", gender: "male", grade: 8 },
           { firstName: "Patrick", gender: "male", grade: 7 }
       ],
       pets: [
           { givenName: "Buddy" }
       ],
       address: { country: "USA", state: "WA", city: "Seattle" }
      });

      family_common.save((err, saveFamily) => {
       console.log("Saved: " + JSON.stringify(saveFamily));
      });
      ```

   1. 次に、'VacationDestinations' モデルにオブジェクトを追加し、保存します。
      ```JavaScript
      const vacay_common = new Vacation_common({
       name: "Honolulu",
       country: "USA"
      });

      vacay_common.save((err, saveVacay) => {
       console.log("Saved: " + JSON.stringify(saveVacay));
      });
      ```

1. ここで、Azure Portal に戻ると、```alldata``` というコレクションが 1 つだけがあり、'Family' と 'VacationDestinations' 両方のデータが含まれていることがわかります。

   :::image type="content" source="./media/mongodb-mongoose/mongo-collections-alldata.png" alt-text="Node.js チュートリアル - Azure Cosmos DB アカウントを示し、コレクション名が強調表示されている Azure portal のスクリーンショット - Node データベース":::

1. また、各オブジェクトには ```__type``` と呼ばれる別の属性があることも確認できます。これが 2 つの異なるオブジェクト モデルを区別するために使用されます。

1. 最後に、Azure Cosmos DB に格納されているデータを読み取ります。 モデルに基づいたデータのフィルター処理は Mongoose によって行われます。 そのため、データを読み取るときにそれ以外の処理を行う必要はありません。 モデル (この場合は ```Family_common```)のみを指定すると、Mongoose が 'DiscriminatorKey' に基づいたフィルター処理を行います。

    ```JavaScript
    Family_common.find({ 'children.gender' : "male"}, function(err, foundFamily){
        foundFamily.forEach(fam => console.log("Found Family (using discriminator): " + JSON.stringify(fam)));
    });
    ```

説明からわかるように、Mongoose ディスクリミネーターは容易に使用できます。 そのため、Mongoose フレームワークを使用するアプリがある場合は、このチュートリアルを使用すると、それほど多くの変更を行わなくても Azure Cosmos の MongoDB 用 API を使用してアプリケーションを実行できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

- Azure Cosmos DB の MongoDB 用 API と共に [Studio 3T を使用する](mongodb-mongochef.md)方法を学習します。
- Azure Cosmos DB の MongoDB 用 API と共に [Robo 3T を使用する](mongodb-robomongo.md)方法を学びます。
- Azure Cosmos DB の MongoDB 用 API を使用した MongoDB の[サンプル](mongodb-samples.md)を調査します。

[dbleveltp]: ./media/mongodb-mongoose/db-level-throughput.png
