---
title: Azure Cosmos DB の Cassandra API でサポートされる Apache Cassandra の機能とコマンド
description: Azure Cosmos DB の Cassandra API でサポートされる Apache Cassandra の機能について説明します
author: kanshiG
ms.author: govindk
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 09/24/2018
ms.openlocfilehash: a6fc9f1a5c32fc9ffa1e1e6ebe525b72030fe803
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155657"
---
# <a name="apache-cassandra-features-supported-by-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB の Cassandra API でサポートされる Apache Cassandra の機能 

Azure Cosmos DB は、Microsoft のグローバルに分散されたマルチモデル データベース サービスです。 Azure Cosmos DB の Cassandra API とは、Cassandra Query Language (CQL) v4 [ワイヤ プロトコル](https://github.com/apache/cassandra/blob/trunk/doc/native_protocol_v4.spec)準拠のオープン ソースの Cassandra クライアント [ドライバー](https://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver)経由で通信できます。 

Azure Cosmos DB の Cassandra API を使用することで、Apache Cassandra API のメリットと、Azure Cosmos DB が提供するエンタープライズ機能を享受できます。 エンタープライズ機能には、[グローバル配信](distribute-data-globally.md)、[自動スケールアウトのパーティション分割](partition-data.md)、可用性と待機時間の保証、保存時の暗号化などがあります。

## <a name="cassandra-protocol"></a>Cassandra プロトコル 

Azure Cosmos DB の Cassandra API は、CQL バージョン **v4** と互換性があります。 サポートされている CQL コマンド、ツール、制限事項、および例外を、以下に示します。 これらのプロトコルを認識するクライアント ドライバーはすべて、Azure Cosmos DB の Cassandra API に接続できるはずです。

## <a name="cassandra-driver"></a>Cassandra ドライバー

Azure Cosmos DB の Cassandra API では、次のバージョンの Cassandra ドライバーがサポートされています。

* [Java 3.5+](https://github.com/datastax/java-driver)  
* [C# 3.5+](https://github.com/datastax/csharp-driver)  
* [Nodejs 3.5+](https://github.com/datastax/nodejs-driver)  
* [Python 3.15+](https://github.com/datastax/python-driver)  
* [C++ 2.9](https://github.com/datastax/cpp-driver)   
* [PHP 1.3](https://github.com/datastax/php-driver)  
* [Gocql](https://github.com/gocql/gocql)  
 
## <a name="cql-data-types"></a>CQL データ型 

Azure Cosmos DB の Cassandra API では、次の CQL データ型がサポートされています。

* ascii  
* bigint  
* BLOB  
* ブール値  
* counter  
* date  
* decimal  
* double  
* float  
* frozen  
* inet  
* int  
* list  
* set  
* smallint  
* text  
* time  
* timestamp  
* timeuuid  
* tinyint  
* tuple  
* uuid  
* varchar  
* varint  
* tuples  
* udt  
* map  

## <a name="cql-functions"></a>CQL 関数

Azure Cosmos DB の Cassandra API では、次の CQL 関数がサポートされています。

* トークン  
* 集計関数
  * min、max、avg、count
* Blob 変換関数 
  * typeAsBlob(value)  
  * blobAsType(value)
* UUID および timeuuid 関数 
  * dateOf()  
  * now()  
  * minTimeuuid()  
  * unixTimestampOf()  
  * toDate(timeuuid)  
  * toTimestamp(timeuuid)  
  * toUnixTimestamp(timeuuid)  
  * toDate(timestamp)  
  * toUnixTimestamp(timestamp)  
  * toTimestamp(date)  
  * toUnixTimestamp(date) 
  


## <a name="cassandra-query-language-limits"></a>Cassandra クエリ言語の制限

Azure Cosmos DB の Cassandra API には、テーブルに格納されるデータのサイズに制限がありません。 パーティション キーの制限を確実に適用しながら、数百テラバイトまたはペタバイトのデータを格納することができます。 同様に、すべてのエンティティまたは同等の行に列数の制限はありませんが、エンティティの合計サイズが 2 MB を超えることはできません。

## <a name="tools"></a>ツール 

Azure Cosmos DB の Cassandra API は、管理されたサービス プラットフォームです。 クラスターを管理するために、管理オーバーヘッドや、ガベージ コレクター、Java 仮想マシン (JVM)、nodetool などのユーティリティは必要ありません。 バイナリ CQLv4 互換性を利用する cqlsh などのツールをサポートしています。 

* Azure portal のデータ エクスプローラー、メトリック、ログの診断、PowerShell、および cli は、アカウントを管理するためにサポートされているその他のメカニズムです。

## <a name="cql-shell"></a>CQL シェル  

CQLSH コマンドライン ユーティリティは、Apache Cassandra 3.1.1 付属しており、次の環境変数が有効にするとすぐに機能します。

次のコマンドを実行する前に、[Baltimore ルート証明書を cacerts ストアに追加](https://docs.microsoft.com/java/azure/java-sdk-add-certificate-ca-store?view=azure-java-stable#to-add-a-root-certificate-to-the-cacerts-store)します。 

**Windows:** 

```bash
set SSL_VERSION=TLSv1_2 
SSL_CERTIFICATE=<path to Baltimore root ca cert>
set CQLSH_PORT=10350 
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```
**Unix/Linux/Mac:**

```bash
export SSL_VERSION=TLSv1_2 
export SSL_CERTFILE=<path to Baltimore root ca cert>
cqlsh <YOUR_ACCOUNT_NAME>.cassandra.cosmosdb.azure.com 10350 -u <YOUR_ACCOUNT_NAME> -p <YOUR_ACCOUNT_PASSWORD> --ssl 
```

## <a name="cql-commands"></a>CQL コマンド

Azure Cosmos DB は、Cassandra API アカウントで以下のデータベース コマンドをサポートしています。

* CREATE KEYSPACE 
* CREATE TABLE 
* ALTER TABLE 
* USE 
* INSERT 
* SELECT 
* UPDATE 
* BATCH - unlogged コマンドのみサポートされています 
* DELETE

CQLV4 互換の SDK から実行された場合のすべての CRUD 操作により、エラー、消費された要求の単位数、アクティビティ ID に関する追加情報が返されます。 コマンドの削除と更新は、プロビジョニングされたリソースの過剰使用を防ぐため、検討中のリソース ガバナンスで処理する必要があります。 
* gc_grace_seconds を指定する場合は値を 0 にする必要があることに注意してください。

```csharp
var tableInsertStatement = table.Insert(sampleEntity); 
var insertResult = await tableInsertStatement.ExecuteAsync(); 
 
foreach (string key in insertResult.Info.IncomingPayload) 
        { 
            byte[] valueInBytes = customPayload[key]; 
            double value = Encoding.UTF8.GetString(valueInBytes); 
            Console.WriteLine($“CustomPayload:  {key}: {value}”); 
        } 
```

## <a name="consistency-mapping"></a>一貫性のマッピング 

Azure Cosmos DB の Cassandra API では、読み取り操作の一貫性を選択することができます。  一貫性のマッピングについては、[こちら](https://docs.microsoft.com/azure/cosmos-db/consistency-levels-across-apis#cassandra-mapping) に詳しく説明されています。

## <a name="permission-and-role-management"></a>アクセス許可とロールの管理

Azure Cosmos DB は、プロビジョニングのためのロールベースのアクセス制御 (RBAC)、キーのローテーション、メトリックの表示、[Azure portal](https://portal.azure.com) から取得できる読み書きおよび読み取り専用のパスワードおよびキーをサポートしています。 Azure Cosmos DB では、CRUD アクティビティのためのユーザーとロールはまだサポートされていません。 

## <a name="planned-support"></a>計画されているサポート 
* create keyspace コマンドのリージョン名は現在無視されます。データのディストリビューションは、基礎となる Cosmos DB プラットフォームに実装され、アカウントのポータルまたは PowerShell を介して公開されます。 





## <a name="next-steps"></a>次の手順

- Java アプリケーションを使用した[ Cassandra API アカウント、データベースおよびテーブルの作成](create-cassandra-api-account-java.md)の開始

