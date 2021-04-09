---
title: CouchBase から Azure Cosmos DB SQL API に移行する
description: CouchBase から Azure Cosmos DB SQL API への移行に関するステップバイステップのガイダンス
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: a15c6b5919f428b28daab86fea9c3b6473d19162
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97606200"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>CouchBase から Azure Cosmos DB SQL API に移行する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB は、グローバルに分散された、スケーラブルなフル マネージド データベースです。 短い待機時間でのデータへのアクセスが保証されます。 Azure Cosmos DB の詳細については、[概要](introduction.md)に関する記事をご覧ください。 この記事では、Couchbase に接続されている Java アプリケーションを Azure Cosmos DB の SQL API アカウントに移行する手順について説明します。

## <a name="differences-in-nomenclature"></a>分類における違い

次に示すのは、Couchbase と比較したときに Azure Cosmos DB で動作が異なる主な機能です。

| Couchbase | Azure Cosmos DB |
|--|--|
| Couchbase サーバー | Account |
| バケット | データベース |
| バケット | コンテナー/コレクション |
| JSON ドキュメント | 項目/ドキュメント |

## <a name="key-differences"></a>主な相違点

* Azure Cosmos DB には、ドキュメント内に "ID" フィールドがありますが、Couchbase にはバケットの一部として ID があります。 "ID" フィールドは、パーティション全体で一意です。

* Azure Cosmos DB は、パーティション分割またはシャーディングの手法を使用してスケーリングします。 つまり、データは複数のシャード/パーティションに分割されます。 これらのパーティション/シャードは、指定されたパーティション キー プロパティに基づいて作成されます。 パーティション キーを選択して、読み取りおよび書き込み操作を最適化することも、読み取り/書き込みを最適化することもできます。 詳細については、[パーティション分割](./partitioning-overview.md)に関する記事をご覧ください。

* Azure Cosmos DB では、コレクション名が既に存在しているため、最上位の階層でコレクションを示す必要はありません。 この機能により、JSON 構造が非常に単純になります。 Couchbase と Azure Cosmos DB 間のデータ モデルの違いを示す例を次に示します。

   **Couchbase**: Document ID = "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: 次に示すドキュメント内の "ID" を参照してください

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Java SDK のサポート

Azure Cosmos DB には、さまざまな Java フレームワークをサポートする次の SDK があります。

* Async SDK
* Spring Boot SDK

次のセクションでは、これらの各 SDK をどのような場合に使用するかについて説明します。 3 種類のワークロードがある例について考えてみます。

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>ドキュメント リポジトリしての Couchbase、および Spring Data ベースのカスタム クエリ

移行するワークロードが Spring Boot ベースの SDK に基づいている場合は、次の手順を使用できます。

1. POM.xml ファイルに親を追加します。

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. POM.xml ファイルにプロパティを追加します。

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. POM.xml ファイルに依存関係を追加します。

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. リソースの下にアプリケーション プロパティを追加し、次を指定します。 URL、キー、およびデータベース名のパラメーターを必ず置き換えてください。

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. モデル内のコレクションの名前を定義します。 さらに注釈を指定することもできます。 たとえば、次のようにして、ID、パーティション キーを明示的に示します。

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

CRUD 操作のコード スニペットを次に示します。

### <a name="insert-and-update-operations"></a>挿入と更新の操作

ここで、 *_repo* はレポジトリのオブジェクトで、*doc* は POJO クラスのオブジェクトです。 `.save` を使用して挿入または upsert できます (指定された ID を持つドキュメントが見つかった場合)。 次のコード スニペットは、doc オブジェクトを挿入または更新する方法を示しています。

```_repo.save(doc);```

### <a name="delete-operation"></a>削除操作。

次のコード スニペットについて考えてみます。ここで、doc オブジェクトには、オブジェクトの検索と削除に必須の ID とパーティション キーがあります。

```_repo.delete(doc);```

### <a name="read-operation"></a>読み取り操作。

ドキュメントは、パーティション キーを指定してもしなくても読み取ることができます。 パーティション キーを指定しない場合は、クロスパーティション クエリとして扱われます。 次のコード サンプルについて考えてみます。最初のものは、ID とパーティション キーのフィールドを使用して操作を実行します。 2 番目の例では、通常のフィールドを使用し、パーティション キー フィールドは指定しません。

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

これで、Azure Cosmos DB でアプリケーションを使用できるようになりました。 このドキュメントで説明している例の完全なコード サンプルは、[CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/SpringCosmos) GitHub リポジトリにあります。

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>ドキュメント リポジトリとしての Couchbase、および N1QL クエリの使用

N1QL クエリは、Couchbase でクエリを定義する方法です。

|N1QL クエリ | Azure CosmosDB クエリ|
|-------------------|-------------------|
|SELECT META(`TravelDocument`).id AS id, `TravelDocument`.* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xx.xxx.xxx.xxxx " and country = 'India’ and ANY m in Visas SATISFIES m.type == 'Multi-Entry' and m.Country IN ['India', Bhutan’] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0 | SELECT c.id,c FROM c JOIN m in  c.country=’India’ WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

N1QL クエリに次の変更点があることがわかります。

* META キーワードを使用したり、第 1 レベルのドキュメントを参照したりする必要はありません。 代わりに、コンテナーへの独自の参照を作成できます。 この例では、それを "c" としています (どのようなものでもかまいません)。 この参照は、すべての第 1 レベルのフィールドのプレフィックスとして使用されます。 たとえば、c.id、c.country など。

* "ANY" の代わりに、サブドキュメントで結合を行い、"m" などの専用のエイリアスを使用してそれを参照できるようになりました。 サブドキュメントのエイリアスを作成したら、エイリアスを使用する必要があります。 たとえば、m.Country など。

* Azure Cosmos DB クエリでは OFFSET のシーケンスが異なっており、最初に OFFSET、次に LIMIT を指定する必要があります。 最大数のカスタム定義クエリを使用している場合は、Spring Data SDK を使用しないことをお勧めします。これは、クエリを Azure Cosmos DB に渡す際にクライアント側で不要なオーバーヘッドが発生する可能性があるためです。 代わりに、この場合にはるかに効率的に使用できる、直接 Async Java SDK が用意されています。

### <a name="read-operation"></a>読み取り操作

次の手順に従って、Async Java SDK を使用します。

1. 次の依存関係を POM.xml ファイルに構成します。

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 次の例に示すように `ConnectionBuilder` メソッドを使用して、Azure Cosmos DB の接続オブジェクトを作成します。 次のコードが 1 回だけ実行されるように、この宣言を Bean に挿入してください。

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. クエリを実行するには、次のコード スニペットを実行する必要があります。

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

これで、上記のメソッドを使用して、複数のクエリを渡し、問題なく実行することができます。 複数のクエリに分割できる 1 つの大きなクエリを実行する必要がある場合は、前のものではなく、次のコード スニペットを試してみてください。

```java
for(SqlQuerySpec query:queries)
{
   objFlux= container.queryItems(query, fo);
   objFlux .publishOn(Schedulers.elastic())
         .subscribe(feedResponse->
            {
               if(feedResponse.results().size()>0)
               {
                  _docs.addAll(feedResponse.results());
               }
            
            },
            Throwable::printStackTrace,latch::countDown);
   lstFlux.add(objFlux);
}
                  
      Flux.merge(lstFlux);
      latch.await();
}
```

前のコードでは、クエリを並列で実行し、分散実行を増やして最適化することができます。 さらに、挿入と更新の操作も実行できます。

### <a name="insert-operation"></a>挿入操作

ドキュメントを挿入するには、次のコードを実行します。

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

続いて、次のように Mono をサブスクライブします。

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.statusCode()!=successStatus)
              {
                 throw new RuntimeException(resourceResponse.toString());
              }
           },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert 操作

Upsert 操作では、更新する必要のあるドキュメントを指定する必要があります。 完全なドキュメントを取得するには、読み取り操作という見出しで言及されているスニペットを使用し、必要なフィールドを変更します。 次のコード スニペットは、ドキュメントを upsert します。

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
次に、Mono をサブスクライブします。 挿入操作の Mono サブスクリプション スニペットを参照してください。

### <a name="delete-operation"></a>削除操作

次のスニペットは、削除操作を実行します。

```java
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

次に、Mono をサブスクライブします。挿入操作の Mono サブスクリプション スニペットを参照してください。 完全なコード サンプルは、[CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncInSpring) GitHub リポジトリにあります。

## <a name="couchbase-as-a-keyvalue-pair"></a>キー/値ペアとしての Couchbase

これは、クエリではなく参照を実行できる単純な種類のワークロードです。 キー/値のペアには、次の手順を使用します。

1. "/ID" を主キーとして使用することを検討してください。これにより、特定のパーティションで参照操作を直接実行できるようになります。 コレクションを作成し、パーティション キーとして "/ID" を指定します。

1. インデックス作成を完全にオフにします。 参照操作を実行するため、インデックス作成のオーバーヘッドを負担する必要はありません。 インデックス作成をオフにするには、Azure portal にサインインし、Azure Cosmos DB アカウントに移動します。 **[データ エクスプローラー]** を開き、 **[データベース]** 、 **[コンテナー]** の順に選択します。 **[スケールと設定]** タブを開き、 **[インデックス作成ポリシー]** を選択します。 現在のインデックス作成ポリシーは次のようになります。
    
   ```json
   {
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   上記のインデックス作成ポリシーを次のポリシーに置き換えます。

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. 次のコード スニペットを使用して、接続オブジェクトを作成します。 接続オブジェクト (@Bean に配置するか、静的にします)。

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

これで、次のように CRUD 操作を実行できるようになりました。

### <a name="read-operation"></a>読み取り操作

項目を読み取るには、次のスニペットを使用します。

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
      
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.item()!=null)
           {
              doc= resourceResponse.properties().toObject(UserModel.class);
           }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>挿入操作

項目を挿入するには、次のコードを実行します。

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

続いて、次のように Mono をサブスクライブします。

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
      .subscribe(resourceResponse->
      {
         if(resourceResponse.statusCode()!=successStatus)
            {
               throw new RuntimeException(resourceResponse.toString());
            }
         },
      Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert 操作

項目の値を更新するには、次のコード スニペットを参照してください。

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
次に、Mono をサブスクライブします。挿入操作の Mono サブスクリプション スニペットを参照してください。

### <a name="delete-operation"></a>削除操作

削除操作を実行するには、次のスニペットを使用します。

```java
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

次に、Mono をサブスクライブします。挿入操作の Mono サブスクリプション スニペットを参照してください。 完全なコード サンプルは、[CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncKeyValue) GitHub リポジトリにあります。

## <a name="data-migration"></a>データ移行

データを移行するには、2 つの方法があります。

* **Azure Data Factory を使用する:** これは、最も推奨されるデータ移行方法です。 ソースを Couchbase として、シンクを Azure Cosmos DB SQL API として構成します。詳細な手順については、Azure [Cosmos DB Data Factory コネクタ](../data-factory/connector-azure-cosmos-db.md)に関する記事をご覧ください。

* **Azure Cosmos DB データ インポート ツールを使用する:** データ量が少ない VM を使用して移行する場合、このオプションをお勧めします。 詳細な手順については、[データ インポーター](./import-data.md)に関する記事をご覧ください。

## <a name="next-steps"></a>次の手順

* パフォーマンス テストを実行するには、「[Azure Cosmos DB のパフォーマンスとスケールのテスト](./performance-testing.md)」という記事を参照してください。
* コードを最適化するには、[Azure Cosmos DB のパフォーマンスに関するヒント](./performance-tips-async-java.md)についての記事をご覧ください。
* Java Async V3 SDK、[SDK リファレンス](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub リポジトリを参照してください。
