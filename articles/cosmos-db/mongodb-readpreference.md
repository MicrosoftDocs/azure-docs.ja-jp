---
title: Azure Cosmos DB の MongoDB 用 API を使用して読み取り設定を使用する
description: Azure Cosmos DB の MongoDB 用 API を使用して MongoDB 読み取り設定を使用する方法について説明します
author: sivethe
ms.author: sivethe
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: how-to
ms.date: 02/26/2019
ms.custom: devx-track-js
ms.openlocfilehash: 3c78ad6605e927015d35df12cadf0347dd0337cf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96349046"
---
# <a name="how-to-globally-distribute-reads-using-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API を使用して読み取りをグローバルに配布する方法について説明します
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

この記事では、Azure Cosmos DB の MongoDB 用 API を使用して [MongoDB 読み取り設定](https://docs.mongodb.com/manual/core/read-preference/)で読み取り操作をグローバルに配布する方法について示します。

## <a name="prerequisites"></a>前提条件 
Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 
[!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

Azure Portal を使用して Cosmos アカウントをグローバル配布で設定し、接続する手順については、この[クイック スタート](tutorial-global-distribution-mongodb.md)の記事を参照してください。

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

git ターミナル ウィンドウ (git bash など) を開き、`cd` を実行して作業ディレクトリに移動します。  

次のコマンドを実行して、サンプル リポジトリを複製します。 使用するプラットフォームに基づいて、次のいずれかのサンプル リポジトリを使用します。

1. [.NET サンプル アプリケーション](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)
2. [NodeJS サンプル アプリケーション]( https://github.com/Azure-Samples/azure-cosmos-db-mongodb-node-geo-readpreference)
3. [Mongoose サンプル アプリケーション](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-mongoose-geo-readpreference)
4. [Java サンプル アプリケーション](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference)
5. [SpringBoot サンプル アプリケーション](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-spring)


```bash
git clone <sample repo url>
```

## <a name="run-the-application"></a>アプリケーションの実行

使用するプラットフォームに基づいて、必要なパッケージをインストールし、アプリケーションを起動します。 依存関係をインストールするには、サンプル アプリケーション リポジトリに含まれている README に従います。 たとえば、NodeJS サンプル アプリケーションでは、次のコマンドを使用して必要なパッケージをインストールし、アプリケーションを起動します。

```bash
cd mean
npm install
node index.js
```
アプリケーションは MongoDB ソースに接続しようとしますが、接続文字列が無効であるために失敗します。 README の手順に従って、接続文字列 `url` を更新します。 また、`readFromRegion` を自分の Cosmos アカウントの読み取りリージョンに更新します。 次の手順は、NodeJS サンプルのものです。

```
* Next, substitute the `url`, `readFromRegion` in App.Config with your Cosmos account's values. 
```

この手順を行うと、サンプル アプリケーションが実行され、次の出力が生成されます。

```
connected!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromNearestfunc query completed!
readFromRegionfunc query completed!
readDefaultfunc query completed!
readFromSecondaryfunc query completed!
```

## <a name="read-using-read-preference-mode"></a>読み取り設定モードを使用した読み取り

MongoDB プロトコルには、クライアントが使用できる以下の読み取り設定モードがあります。

1. PRIMARY
2. PRIMARY_PREFERRED
3. SECONDARY
4. SECONDARY_PREFERRED
5. NEAREST

これらの読み取り設定モードの個々の動作の詳細については、[MongoDB 読み取り設定の動作](https://docs.mongodb.com/manual/core/read-preference-mechanics/#replica-set-read-preference-behavior)に関するドキュメントを参照してください。 Cosmos DB では、プライマリは WRITE リージョンにマップされ、セカンダリは READ リージョンにマップされます。

一般的なシナリオに基づいて、次の設定を使用することをお勧めします。

1. **待ち時間の短い読み取り** が必要な場合は、**NEAREST** 読み取り設定モードを使用します。 この設定では、読み取り操作で、最も近い使用可能リージョンが利用されます。 最も近いリージョンが WRITE リージョンである場合、これらの操作はそのリージョンを対象にすることに注意してください。
2. **高可用性と読み取りの geo ディストリビューション** が必要な (待ち時間が制約でない) 場合は、**PRIMARY PREFERRED** または **SECONDARY PREFERRED** 読み取り設定モードを使用します。 この設定では、読み取り操作がそれぞれ、使用可能な WRITE または READ リージョンに転送されます。 そのリージョンを使用できない場合は、読み取り設定の動作に従って、要求は次の使用可能なリージョンに転送されます。

サンプル アプリケーションの次のスニペットは、NodeJS で NEAREST 読み取り設定を構成する方法を示しています。

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.NEAREST});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromNearestfunc query completed!");
  });
```

同様に、次のスニペットは、NodeJS で SECONDARY_PREFERRED 読み取り設定を構成する方法を示しています。

```javascript
  var query = {};
  var readcoll = client.db('regionDB').collection('regionTest', {readPreference: ReadPreference.SECONDARY_PREFERRED});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromSecondaryPreferredfunc query completed!");
  });
```

この読み取り設定はまた、接続文字列の URI オプションのパラメーターとして `readPreference` を渡すことによっても設定できます。

```javascript
const MongoClient = require('mongodb').MongoClient;
const assert = require('assert');

// Connection URL
const url = 'mongodb://localhost:27017?ssl=true&replicaSet=globaldb&readPreference=nearest';

// Database Name
const dbName = 'myproject';

// Use connect method to connect to the Server
MongoClient.connect(url, function(err, client) {
  console.log("Connected correctly to server");

  const db = client.db(dbName);

  client.close();
});
```

他のプラットフォームについては、[.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)、[Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference) など、対応するサンプル アプリケーションのリポジトリを参照してください。

## <a name="read-using-tags"></a>タグを使用した読み取り

MongoDB プロトコルでは、読み取り設定モードの他に、タグを使用して読み取り操作を指示することもできます。 Cosmos DB の MongoDB 用 API では、`region` タグは既定で `isMaster` 応答の一部として含まれています。

```json
"tags": {
         "region": "West US"
      }
```

そのため、MongoClient は `region` タグとリージョン名を使用して、読み取り操作を特定のリージョンに送ることができます。 Cosmos アカウントの場合は、Azure Portal の左側の **[設定] -> [データをグローバルにレプリケート]** にリージョン名が表示されます。 この設定は、**読み取りの分離** を実現する場合に便利です。たとえば、クライアント アプリケーションに読み取り操作を特定のリージョンだけで行うようにさせる場合などです。 この設定は、実稼働ではない、分析的な種類のシナリオに適しています。バックグラウンドで実行され、運用上のクリティカルなサービスは含まれません。

サンプル アプリケーションの次のスニペットは、NodeJS でタグを使用して読み取り設定を構成する方法を示しています。

```javascript
 var query = {};
  var readcoll = client.db('regionDB').collection('regionTest',{readPreference: new ReadPreference(ReadPreference.SECONDARY_PREFERRED, {"region": "West US"})});
  readcoll.find(query).toArray(function(err, data) {
    assert.equal(null, err);
    console.log("readFromRegionfunc query completed!");
  });
```

他のプラットフォームについては、[.NET](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-dotnet-geo-readpreference)、[Java](https://github.com/Azure-Samples/azure-cosmos-db-mongodb-java-geo-readpreference) など、対応するサンプル アプリケーションのリポジトリを参照してください。

この記事では、Azure Cosmos DB の MongoDB 用 API で読み取り設定を使用して、読み取り操作をグローバルに配布する方法について説明しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このアプリの使用を続けない場合は、以下の手順に従い、この記事で作成したすべてのリソースを Azure Portal で削除してください。

1. Azure Portal の左側のメニューで、 **[リソース グループ]** をクリックしてから、作成したリソースの名前をクリックします。 
2. リソース グループのページで **[削除]** をクリックし、削除するリソースの名前をテキスト ボックスに入力してから **[削除]** をクリックします。

## <a name="next-steps"></a>次のステップ

* [MongoDB データを Azure Cosmos DB にインポートする](../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
* [Azure Cosmos DB の MongoDB 用 API を使用してグローバル分散型データベースを設定する](tutorial-global-distribution-mongodb.md)
* [Azure Cosmos DB エミュレーターを使用したローカルでの開発](local-emulator.md)