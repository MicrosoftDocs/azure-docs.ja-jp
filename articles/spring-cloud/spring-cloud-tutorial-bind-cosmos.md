---
title: Azure Cosmos DB を Azure Spring Cloud アプリケーションにバインドする | Microsoft Docs
description: Azure Cosmos DB を Azure Spring Cloud アプリケーションにバインドする方法について説明します
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d051fd431180e9cb86f1df4642fb5e947103c007
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038670"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>チュートリアル:Azure Cosmos DB を Azure Spring Cloud アプリケーションにバインドする

Azure Spring Cloud では、Spring Boot アプリケーションを手動で構成するのではなく、選択した Azure サービスをアプリケーションに自動的にバインドすることができます。 この記事では、アプリケーションを Azure Cosmos DB にバインドする方法を示します。

前提条件:
* デプロイされた Azure Spring Cloud インスタンス。  [クイックスタート](spring-cloud-quickstart-launch-app-cli.md)に従って始めてください。
* 共同作成者の最小アクセス許可レベルを持つ Azure Cosmos DB アカウント。

## <a name="bind-azure-cosmos-db"></a>Azure Cosmos DB をバインドする

Azure Cosmos DB では、5 種類の API でバインドがサポートされています。

1. Azure Cosmos DB のデータベースを作成します。 データベースの作成については、[こちらの記事を参照してください](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)。 データベースの名前を記録します。 ここでの名前は `testdb` です。

1. API の種類に従って、Spring Cloud アプリケーションの `pom.xml` に次の依存関係のいずれかを追加します。
    
    #### <a name="api-type-core-sql"></a>API の種類: コア (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>API の種類: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>API の種類: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>API の種類: Gremlin (グラフ)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>API の種類: Azure テーブル

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. `az spring-cloud app update` を使用して現在のデプロイを更新するか、`az spring-cloud app deployment create` を使用してこの変更用に新しいデプロイを作成します。  これらのコマンドでは、新しい依存関係でアプリケーションが更新または作成されます。

1. Azure portal で Azure Spring Cloud サービスのページに移動します。 これは、前のステップで更新またはデプロイしたものと同じアプリケーションです。 **アプリケーション ダッシュボード**を探し、Cosmos DB にバインドするアプリケーションを選択します。 次に、[`Service binding`]\(サービス バインド\) を選択し、[`Create service binding`]\(サービス バインドの作成\) ボタンを選択します。 フォームに入力し、**バインドの種類** (`Azure Cosmos DB`)、API の種類、データベース名、および Azure Cosmos DB アカウントを選択します。

    > [!NOTE]
    > Cassandra を使用している場合は、データベース名にキー スペースを使用します。

1. アプリケーション ページの **[再起動]** ボタンを選択して、アプリケーションを再起動します。

1. サービスを確実に正しくバインドするには、バインド名を選択し、その詳細を確認します。 `property` フィールドは次のようになります。

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Spring Cloud アプリケーションを CosmosDB にバインドする方法について学習しました。  アプリケーションを Azure Redis Cache にバインドする方法について学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Spring Cloud アプリケーションを Azure Redis Cache にバインドする](spring-cloud-tutorial-bind-redis.md)
