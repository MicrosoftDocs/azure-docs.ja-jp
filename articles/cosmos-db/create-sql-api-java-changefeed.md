---
title: 変更フィードを使用してエンド ツー エンドの Azure Cosmos DB Java SDK v4 アプリケーション サンプルを作成する
description: このガイドでは、変更フィードを使用してコンテナーのマテリアライズドビューを維持しながら、Azure Cosmos DB コンテナーにドキュメントを挿入する単純な Java SQL API アプリケーションについて説明します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 06/11/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 765fd3afc7fe688d3e6b0e3394e7dc8c39af69b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93096854"
---
# <a name="how-to-create-a-java-application-that-uses-azure-cosmos-db-sql-api-and-change-feed-processor"></a>Azure Cosmos DB SQL API と変更フィード プロセッサを使用する Java アプリケーションを作成する方法
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

この攻略ガイドでは、変更フィードおよび変更フィード プロセッサを使用してコンテナーのマテリアライズドビューを維持しながら、Azure Cosmos DB SQL API を使用してドキュメントを Azure Cosmos DB コンテナーに挿入する単純な Java アプリケーションについて説明します。 Java アプリケーションは、Azure Cosmos DB Java SDK v4 を使用して、Azure Cosmos DB SQL API と通信します。

> [!IMPORTANT]  
> このチュートリアルは、Azure Cosmos DB Java SDK v4 のみを対象としています。 詳細については、Azure Cosmos DB Java SDK v4 [リリース ノート](sql-api-sdk-java-v4.md)、[Maven リポジトリ](https://mvnrepository.com/artifact/com.azure/azure-cosmos)、Azure Cosmos DB Java SDK v4 [パフォーマンスに関するヒント](performance-tips-java-sdk-v4-sql.md)、Azure Cosmos DB Java SDK v4 [トラブルシューティング ガイド](troubleshoot-java-sdk-v4-sql.md)を参照してください。 v4 より前のバージョンを現在使用している場合、v4 にアップグレードするには、[Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) ガイドを参照してください。
>

## <a name="prerequisites"></a>前提条件

* Azure Cosmos DB アカウントの URI とキー

* Maven

* Java 8

## <a name="background"></a>バックグラウンド

Azure Cosmos DB の変更フィードには、ドキュメントの挿入に応答してアクションをトリガーするイベントドリブン インターフェイスが用意されています。 これには多くの用途があります。 たとえば、読み取りと書き込みの両方の負荷が高いアプリケーションでは、変更フィードは主に、ドキュメントを取り込んでいるときに、コンテナーの **マテリアライズドビュー** をリアルタイムで作成するために使用されます。 マテリアライズドビューのコンテナーは、同じデータを保持しますが、効率的な読み取りのためにパーティション分割されているため、アプリケーションの読み取りと書き込みの両方を効率的に行うことができます。

変更フィードのイベントを管理する作業は、主に、SDK に組み込まれている変更フィード プロセッサ ライブラリによって行われます。 このライブラリは、必要に応じて変更フィードのイベントを複数のワーカー間に配布するのに十分な性能を備えています。 変更フィード ライブラリにコールバックを提供するだけで利用できます。

この簡単な例では、1 つのワーカーを持つ変更フィード プロセッサ ライブラリが、マテリアライズドビューでドキュメントを作成および削除するところを示します。

## <a name="setup"></a>セットアップ

まだ行っていない場合は、アプリのサンプル リポジトリを複製します。

```bash
git clone https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example.git
```

リポジトリ ディレクトリでターミナルを開きます。 次を実行してアプリをビルドします。

```bash
mvn clean package
```

## <a name="walkthrough"></a>チュートリアル

1. 最初のチェックとして、Azure Cosmos DB アカウントを持っている必要があります。 ブラウザーで **Azure portal** を開き、Azure Cosmos DB アカウントに移動します。左側のペインで **データ エクスプローラー** に移動します。

   :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_empty.JPG" alt-text="Azure Cosmos DB アカウント":::

1. 次のコマンドを使用して、ターミナルでアプリを実行します。

    ```bash
    mvn exec:java -Dexec.mainClass="com.azure.cosmos.workedappexample.SampleGroceryStore" -DACCOUNT_HOST="your-account-uri" -DACCOUNT_KEY="your-account-key" -Dexec.cleanupDaemonThreads=false
    ```

1. 次が表示されたら、Enter キーを押します。

    ```bash
    Press enter to create the grocery store inventory system...
    ```

    次に、ブラウザーで Azure portal のデータ エクスプローラーに戻ります。 次の 3 つの空のコンテナーを持つ **GroceryStoreDatabase** というデータベースが追加されていることがわかります。 

    * **InventoryContainer** - サンプル食料品店のインベントリ レコード。項目 ```id``` (UUID) でパーティション分割されています。
    * **InventoryContainer-pktype** - インベントリ レコードのマテリアライズドビュー。項目 ```type``` に対してクエリを実行するように最適化されています。
    * **InventoryContainer-leases** - リース コンテナーは、変更フィードに常に必要です。リースは、変更フィードの読み取りでのアプリの進行状況を追跡します。

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_account_resources_lease_empty.JPG" alt-text="空のコンテナー":::

1. ターミナルで、プロンプトが表示されます。

    ```bash
    Press enter to start creating the materialized view...
    ```

    Enter キーを押します。 これで、次のコード ブロックが実行され、別のスレッドで変更フィード プロセッサが初期化されます。 

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=InitializeCFP)]

    ```"SampleHost_1"``` は、変更フィード プロセッサ ワーカーの名前です。 ```changeFeedProcessorInstance.start()``` が実際に変更フィード プロセッサを開始します。

    ブラウザーで Azure portal のデータ エクスプローラーに戻ります。 **InventoryContainer-leases** コンテナーで、 **[items]\(項目\)** をクリックして内容を表示します。 変更フィード プロセッサによってリース コンテナーが設定されたことがわかります。つまり、プロセッサが ```SampleHost_1``` ワーカーに、**InventoryContainer** の一部のパーティションに対するリースを割り当てました。

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_leases.JPG" alt-text="リース":::

1. ターミナルでもう一度 Enter キーを押します。 これにより、**InventoryContainer** への 10 個のドキュメントの挿入がトリガーされます。 各ドキュメントの挿入は JSON として変更フィードに表示されます。次のコールバック コードは、JSON ドキュメントをマテリアライズドビューにミラーリングすることによって、これらのイベントを処理します。

    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=CFPCallback)]

1. コードの実行には 5 から 10 秒かかります。 次に、Azure portal のデータ エクスプローラーに戻り、 **[InventoryContainer] > [items]\(項目\)** に移動します。 項目がインベントリ コンテナーに挿入されていることがわかります。パーティション キー (```id```) に注意してください。

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_items.JPG" alt-text="フィード コンテナー":::

1. 次に、データ エクスプローラーで、 **[InventoryContainer-pktype] > [items]\(項目\)** に移動します。 これはマテリアライズドビューです。このコンテナー内の項目は、変更フィードによってプログラムで挿入されたため、**InventoryContainer** をミラーリングしています。 パーティション キー (```type```) に注意してください。 したがって、このマテリアライズドビューは、```type``` をフィルター処理するクエリ用に最適化されています。これは、```id``` でパーティション分割されている **InventoryContainer** では効率が悪くなります。

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview2.JPG" alt-text="スクリーンショットには、項目が選択された Azure Cosmos DB アカウントのデータ エクスプローラー ページが示されています。":::

1. 1 つの ```upsertItem()``` 呼び出しだけを使用して、**InventoryContainer** と **InventoryContainer-pktype** の両方からドキュメントを削除します。 まず、Azure portal のデータ エクスプローラーを見てみましょう。 ```/type == "plums"``` のドキュメント (下で赤で囲まれている) を削除します。

    :::image type="content" source="media/create-sql-api-java-changefeed/cosmos_materializedview-emph-todelete.JPG" alt-text="スクリーンショットには、特定の項目 I D が選択された Azure Cosmos DB アカウントのデータ エクスプローラー ページが示されています。":::

    もう一度 Enter キーを押して、コード例の ```deleteDocument()``` 関数を呼び出します。 以下に示すこの関数は、ドキュメントの新しいバージョンを ```/ttl == 5``` で upsert します。これにより、ドキュメントの Time-To-Live (TTL) は 5 秒に設定されます。 
    
    ### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-connection-policy-async"></a>Java SDK V4 (Maven com.azure::azure-cosmos) 非同期 API

    [!code-java[](~/azure-cosmos-java-sql-app-example/src/main/java/com/azure/cosmos/workedappexample/SampleGroceryStore.java?name=DeleteWithTTL)]

    変更フィード ```feedPollDelay``` は 100 ミリ秒に設定されています。そのため、変更フィードは、この更新にほぼ瞬時に応答し、上に示した ```updateInventoryTypeMaterializedView()``` を呼び出します。 最後の関数呼び出しは、TTL 5 秒の新しいドキュメントを **InventoryContainer-pktype** に upsert します。

    その結果、約 5 秒後にドキュメントの有効期限が切れて、両方のコンテナーから削除されます。

    変更フィードがイベントを発行するのは、項目の挿入または更新に対してのみで、項目の削除は対象にならないため、この手順が必要になります。

1. もう一度 Enter キーを押して、プログラムを終了し、そのリソースをクリーンアップします。
