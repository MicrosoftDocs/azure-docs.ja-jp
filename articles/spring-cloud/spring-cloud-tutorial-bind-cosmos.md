---
title: Azure Cosmos DB を Azure Spring Cloud アプリケーションにバインドする
description: Azure Cosmos DB を Azure Spring Cloud アプリケーションにバインドする方法について説明します
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7fb1f88ab5eaf22047f5a696ec9cb021981cdd5e
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89297180"
---
# <a name="bind-an-azure-cosmos-db-database-to-your-azure-spring-cloud-application"></a>Azure Cosmos DB データベースを Azure Spring Cloud アプリケーションにバインドする

Spring Boot アプリケーションを手動で構成するのではなく、Azure Spring Cloud を使用して、選択した Azure サービスをアプリケーションに自動的にバインドすることができます。 この記事では、アプリケーションを Azure Cosmos DB データベースにバインドする方法を示します。

前提条件:

* デプロイされた Azure Spring Cloud インスタンス。 [Azure CLI を使用したデプロイに関するクイックスタート](spring-cloud-quickstart.md)に従って作業を開始してください。
* 共同作成者の最小アクセス許可レベルを持つ Azure Cosmos DB アカウント。

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB をバインドする

Azure Cosmos DB では、5 種類の API でバインドがサポートされています。 その使用方法を次の手順で説明します。

1. Azure Cosmos DB のデータベースを作成します。 詳細については、[データベースの作成](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)に関するクイックスタートを参照してください。 

1. データベースの名前を記録します。 この手順では、データベース名は **testdb** です。

1. 次の依存関係のいずれかを Azure Spring Cloud アプリケーションの pom.xml ファイルに追加します。 API の種類に適した依存関係を選択します。

    * API の種類: コア (SQL)

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
          <version>2.1.6</version>
      </dependency>
      ```

    * API の種類: MongoDB

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-mongodb</artifactId>
      </dependency>
      ```

    * API の種類: Cassandra

      ```xml
      <dependency>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-data-cassandra</artifactId>
      </dependency>
      ```

    * API の種類: Gremlin (グラフ)

      ```xml
      <dependency>
          <groupId>com.microsoft.spring.data.gremlin</groupId>
          <artifactId>spring-data-gremlin</artifactId>
          <version>2.1.7</version>
      </dependency>
      ```

    * API の種類: Azure テーブル

      ```xml
      <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>azure-storage-spring-boot-starter</artifactId>
          <version>2.0.5</version>
      </dependency>
      ```

1. `az spring-cloud app update` を使用して現在のデプロイを更新するか、`az spring-cloud app deployment create` を使用して新しいデプロイを作成します。 これらのコマンドでは、新しい依存関係でアプリケーションが更新または作成されます。

1. Azure portal で、自分のAzure Spring Cloud サービスのページに移動します。 **アプリケーション ダッシュボード**に移動し、Azure Cosmos DB にバインドするアプリケーションを選択します。 このアプリケーションは、前の手順で更新またはデプロイしたものと同じです。

1. **[サービス バインド]** を選択し、 **[サービス バインドの作成]** を選択します。 フォームに入力するには、次のように選択します。
   * **[バインドの種類]** 値に **[Azure Cosmos DB]** 。
   * API の種類。
   * データベース名。
   * Azure Cosmos DB アカウント。

    > [!NOTE]
    > Cassandra を使用している場合は、データベース名にキー スペースを使用します。

1. アプリケーション ページで **[再起動]** を選択して、アプリケーションを再起動します。

1. サービスを確実に正しくバインドするには、バインド名を選択し、その詳細を確認します。 `property` フィールドは次の例のようになります。

    ```
    azure.cosmosdb.uri=https://<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>次のステップ

この記事では、Azure Spring Cloud アプリケーションを Azure Cosmos DB データベースにバインドする方法について学習しました。 サービスをアプリケーションにバインドする方法については、[Azure Cache for Redis キャッシュへのバインド](spring-cloud-tutorial-bind-redis.md)に関するページを参照してください。
