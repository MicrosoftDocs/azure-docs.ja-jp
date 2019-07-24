---
title: Azure Cosmos DB の MongoDB 用 API に格納されているデータを管理するための MongoDB 拡張コマンド
description: この記事では、MongoDB 拡張コマンドを使用して、Azure Cosmos DB の MongoDB 用 API に格納されているデータを管理する方法について説明します。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: aef77f121f20d867c8ec5e764d8c9639c961713d
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876890"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB の MongoDB 用 API に格納されているデータを管理するために MongoDB 拡張コマンドを使用する 

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の MongoDB 用 API との通信は、オープン ソースで公開されている任意の MongoDB クライアント [ドライバー](https://docs.mongodb.org/ecosystem/drivers)を使って行うことができます。 Azure Cosmos DB の MongoDB 用 API では、MongoDB [ワイヤ プロトコル](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol)に従うことにより、既存のクライアント ドライバーを利用できます。

Azure Cosmos DB の MongoDB 用 API を使用することで、MongoDB アプリへの投資を保持しながら、グローバル配布、自動シャーディング、高可用性、低待機時間の保証、自動化、保存時の暗号化、バックアップなど多くの Cosmos DB の利点を活用できます。

## <a name="mongodb-protocol-support"></a>MongoDB のプロトコル サポート

既定では、Azure Cosmos DB の MongoDB 用 API はMongoDB サーバー バージョン 3.2 と互換性があります。詳細については、「[サポートされる機能と構文](mongodb-feature-support.md)」を参照してください。 現在、MongoDB バージョン 3.4 で追加された機能やクエリ演算子は、Azure Cosmos DB の MongoDB 用 API のプレビューとして使用できます。 次の拡張コマンドは、Azure Cosmos DB の MongoDB 用 API に格納されているデータに対して CRUD 操作を実行するときに、Azure Cosmos DB の固有の機能をサポートします。

* [データベースを作成する](#create-database)
* [データベースを更新する](#update-database)
* [データベースの取得](#get-database)
* [コレクションの作成](#create-collection)
* [コレクションの更新](#update-collection)
* [コレクションの取得](#get-collection)

## <a id="create-database"></a>データベースの作成

データベースの作成拡張コマンドは、新しい MongoDB データベースを作成します。 データベース名は、コマンドの実行対象のデータベース コンテキストから使用されます。 CreateDatabase コマンドの形式は次のとおりです。

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| customAction   |  文字列  |   カスタム コマンドの名前。"CreateDatabase" にする必要があります。      |
| offerThroughput | int  | データベースに設定したプロビジョニング済みスループット。 このパラメーターは省略可能です。 |

### <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、[既定の出力](#default-output)を参照してください。

### <a name="examples"></a>例

**データベースを作成する**

"test" という名前のデータベースを作成するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**スループットを指定してデータベースを作成する**

"test" という名前で、プロビジョニング済みスループットが 1000 RU のデータベースを作成するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a id="update-database"></a>データベースの更新

データベースの更新拡張コマンドは、指定したデータベースに関連付けられているプロパティを更新します。 現時点では、更新できるのは "offerThroughput" プロパティのみです。

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| customAction    |    文字列     |   カスタム コマンドの名前。 "UpdateDatabase" にする必要があります。      |
|  offerThroughput   |  int       |     データベースに設定する新しいプロビジョニング済みスループット。    |

### <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、[既定の出力](#default-output)を参照してください。

### <a name="examples"></a>例

**データベースに関連付けられているプロビジョニング済みスループットを更新する**

"test" という名前のデータべースのプロビジョニング済みスループットを 1200 RU に更新するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a id="get-database"></a>データベースの取得

データベースの取得拡張コマンドは、データベース オブジェクトを返します。 データベース名は、コマンドの実行対象のデータベース コンテキストから使用されます。

```
{
  customAction: "GetDatabase"
}
```

次の表では、コマンド内のパラメーターについて説明します。


|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  customAction   |   文字列      |   カスタム コマンドの名前。 "GetDatabase" にする必要があります|
        
### <a name="output"></a>出力

コマンドが成功すると、応答には次のフィールドを持つドキュメントが含まれます。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `ok`   |   `int`     |   応答の状態。 1 == 成功。 0 == 失敗。      |
| `database`    |    `string`        |   データベースの名前。      |
|   `provisionedThroughput`  |    `int`      |    データベースに設定されているプロビジョニング済みスループット。 これは省略可能な応答パラメーターです。     |

コマンドが失敗すると、既定のカスタム コマンド応答が返されます。 出力内のパラメーターについては、[既定の出力](#default-output)を参照してください。

### <a name="examples"></a>例

**データベースを取得する**

"test" という名前のデータベースのデータベース オブジェクトを取得するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a id="create-collection"></a>コレクションの作成

コレクションの作成拡張コマンドは、新しい MongoDB コレクションを作成します。 データベース名は、コマンドの実行対象のデータベース コンテキストから使用されます。 CreateCollection コマンドの形式は次のとおりです。

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| customAction    | 文字列 | カスタム コマンドの名前。 "CreateDatabase" にする必要があります     |
| collection      | 文字列 | コレクションの名前                                   |
| offerThroughput | int    | データベースに設定するプロビジョニング済みスループット。 これは、省略可能なパラメーターです |
| shardKey        | 文字列 | シャード コレクションを作成するシャード キー パス。 これは、省略可能なパラメーターです |

### <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、[既定の出力](#default-output)を参照してください。

### <a name="examples"></a>例

**シャード化されていないコレクションを作成する**

"testCollection" という名前で、プロビジョニング済みスループットが 1000 RU のシャード化されていないコレクションを作成するには、次のコマンドを使用します。 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**シャード コレクションを作成する**

"testCollection" という名前で、プロビジョニング済みスループットが 1000 RU のシャード コレクションを作成するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a id="update-collection"></a>コレクションの更新

コレクションの更新拡張コマンドは、指定したコレクションに関連付けられているプロパティを更新します。

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

次の表では、コマンド内のパラメーターについて説明します。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  customAction   |   文字列      |   カスタム コマンドの名前。 "UpdateCollection" にする必要があります。      |
|  collection   |   文字列      |   コレクションの名前。       |
| offerThroughput   |int|   コレクションに設定するプロビジョニング済みスループット。|

## <a name="output"></a>出力

既定のカスタム コマンド応答を返します。 出力内のパラメーターについては、[既定の出力](#default-output)を参照してください。

### <a name="examples"></a>例

**コレクションに関連付けられているプロビジョニング済みスループットを更新する**

"testCollection" という名前のコレクションのプロビジョニング済みスループットを 1200 RU に更新するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a id="get-collection"></a>コレクションの取得

コレクションの取得カスタム コマンドは、コレクション オブジェクトを返します。

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

次の表では、コマンド内のパラメーターについて説明します。


|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
| customAction    |   文字列      |   カスタム コマンドの名前。 "GetCollection" にする必要があります。      |
| collection    |    文字列     |    コレクションの名前。     |

### <a name="output"></a>出力

コマンドが成功すると、応答には次のフィールドを持つドキュメントが含まれます


|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   応答の状態。 1 == 成功。 0 == 失敗。      |
| `database`    |    `string`     |   データベースの名前。      |
| `collection`    |    `string`     |    コレクションの名前。     |
|  `shardKeyDefinition`   |   `document`      |  シャード キーとして使用されるインデックス仕様ドキュメント。 これは省略可能な応答パラメーターです。       |
|  `provisionedThroughput`   |   `int`      |    コレクションに設定するプロビジョニング済みスループット。 これは省略可能な応答パラメーターです。     |

コマンドが失敗すると、既定のカスタム コマンド応答が返されます。 出力内のパラメーターについては、「[既定の出力](#default-output)」を参照してください。

### <a name="examples"></a>例

**コレクションを取得する**

"TestCollection" という名前のコレクションのコレクション オブジェクトを取得するには、次のコマンドを使用します。

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a id="default-output"></a>カスタム コマンドの既定の出力

指定しない場合、カスタムの応答には次のフィールドを持つドキュメントが含まれます。

|**フィールド**|**Type** |**説明** |
|---------|---------|---------|
|  `ok`   |    `int`     |   応答の状態。 1 == 成功。 0 == 失敗。      |
| `code`    |   `int`      |   コマンドが失敗した場合 (ok == 0) のみ返されます。 MongoDB のエラー コードが含まれます。 これは省略可能な応答パラメーターです。      |
|  `errMsg`   |  `string`      |    コマンドが失敗した場合 (ok == 0) のみ返されます。 わかりやすいエラー メッセージが含まれます。 これは省略可能な応答パラメーターです。      |

## <a name="next-steps"></a>次の手順

次は、以下の Azure Cosmos DB の概念について学習することができます。 

* [Azure Cosmos DB のインデックス作成](../cosmos-db/index-policy.md)
* [Time to Live を使用して Azure Cosmos DB のデータの有効期限が自動的に切れるようにする](../cosmos-db/time-to-live.md)