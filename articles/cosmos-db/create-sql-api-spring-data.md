---
title: クイックスタート - Spring Data Azure Cosmos DB v3 を使用して、Azure Cosmos DB を使用してドキュメント データベースを作成する
description: このクイックスタートでは、Azure Cosmos DB SQL API への接続とクエリに使用できる Spring Data Azure Cosmos DB v3 コード サンプルについて紹介します。
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: anfeldma
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: f31eb0fa6dbb881f7a09b21b9dd4842fdfd291f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93090292"
---
# <a name="quickstart-build-a-spring-data-azure-cosmos-db-v3-app-to-manage-azure-cosmos-db-sql-api-data"></a>クイックスタート: Spring Data Azure Cosmos DB v3 アプリを構築して Azure Cosmos DB SQL API データを管理する
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET V3](create-sql-api-dotnet.md)
> * [.NET V4](create-sql-api-dotnet-V4.md)
> * [Java SDK v4](create-sql-api-java.md)
> * [Spring Data v3](create-sql-api-spring-data.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)

このクイックスタートでは、Azure portal から Azure Cosmos DB の SQL API アカウントを作成して管理し、また GitHub からクローンされた Spring Data Azure Cosmos DB v3 アプリを使用します。 まず、Azure portal を使用して Azure Cosmos DB SQL API アカウントを作成し、次に Spring Data Azure Cosmos DB v3 コネクタを使用して Spring Boot アプリを作成し、次に Spring Boot アプリケーションを使用して Cosmos DB アカウントにリソースを追加します。 Azure Cosmos DB は、マルチモデル データベース サービスです。グローバルな分散と水平方向のスケーリング機能を備えたドキュメント データベースやテーブル データベース、キーと値のデータベース、グラフ データベースをすばやく作成し、クエリを実行することができます。

> [!IMPORTANT]  
> これらのリリース ノートは、Spring Data Azure Cosmos DB のバージョン 3 に関するものです。 [バージョン 2 のリリース ノートについてはこちら](sql-api-sdk-java-spring-v2.md)を参照してください。 
>
> Spring Data Azure Cosmos DB では、SQL API のみがサポートされています。
>
> 他の Azure Cosmos DB API での Spring Data については、次の記事を参照してください。
> * [Azure Cosmos DB での Apache Cassandra 用 Spring Data](/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data MongoDB](/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Azure Cosmos DB での Spring Data Gremlin](/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="prerequisites"></a>前提条件

- アクティブなサブスクリプションが含まれる Azure アカウント。 [無料で作成できます](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)。 または、Azure サブスクリプションなしで、[Azure Cosmos DB を無料で試す](https://azure.microsoft.com/try/cosmosdb/)こともできます。 または、`https://localhost:8081` の URI でキー `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==` を使用して [Azure Cosmos DB Emulator](https://aka.ms/cosmosdb-emulator) を使用することもできます。
- [Java Development Kit (JDK) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk)。 JDK のインストール先フォルダーを指すように `JAVA_HOME` 環境変数を設定してください。
- [Maven バイナリ アーカイブ](https://maven.apache.org/download.cgi)。 Ubuntu で `apt-get install maven` を実行して Maven をインストールします。
- [Git](https://www.git-scm.com/downloads). Ubuntu で `sudo apt-get install git` を実行して Git をインストールします。

## <a name="introductory-notes"></a>概要

"*Cosmos DB アカウントの構造:* " API またはプログラミング言語に関係なく、Cosmos DB "*アカウント*" には 0個以上の "*データベース*" が含まれます。"*データベース*" (DB) には 0 個以上の "*コンテナー*" が含まれます。また、次の図に示すように、"*コンテナー*" には 0 個以上の項目が含まれます。

:::image type="content" source="./media/account-databases-containers-items/cosmos-entities.png" alt-text="Azure Cosmos アカウントのエンティティ" border="false":::

データベース、コンテナー、項目の詳細については、[こちら](account-databases-containers-items.md)を参照してください。 "*プロビジョニング済みスループット*" や "*パーティション キー*" など、いくつかの重要なプロパティが、コンテナーのレベルで定義されています。 

プロビジョニング済みスループットは要求ユニット (*RU*) 単位で測定されます。RU には通貨価格が設定されており、アカウント運用コストの大きな決定要因となります。 プロビジョニング済みスループットはコンテナー単位またはデータベース単位で選択できますが、通常はコンテナーレベルのスループット仕様が推奨されます。 スループットのプロビジョニングの詳細については、[こちら](set-throughput.md)を参照してください。

Cosmos DB コンテナーに項目を挿入すると、要求を処理するためのストレージとコンピューティングが追加されて、データベースが水平方向に拡張されます。 ストレージとコンピューティングの容量は "*パーティション*" と呼ばれる個別の単位で追加されます。ドキュメント内の 1 つのフィールドを選択し、これを各ドキュメントをパーティションにマップするパーティション キーにする必要があります。 パーティションを管理する方法として、各パーティションに、パーティション キー値の範囲を超えて、ほぼ等しいスライスを割り当てます。そのため、比較的ランダムまたは均等に分散されたパーティション キーを選択することをお勧めします。 そうしないと、一部のパーティションに非常に多くの要求が集中し ("*ホット パーティション*")、他のパーティションは少数の要求しか受け取らない ("*コールド パーティション*") 状況が発生しますが、これは回避する必要があります。 パーティション分割の詳細については、[こちら](partitioning-overview.md)を参照してください。

## <a name="create-a-database-account"></a>データベース アカウントの作成

ドキュメント データベースを作成するには、Azure Cosmos DB を含んだ SQL API アカウントを事前に作成しておく必要があります。

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="add-a-container"></a>コンテナーの追加

[!INCLUDE [cosmos-db-create-collection](../../includes/cosmos-db-create-collection.md)]

<a id="add-sample-data"></a>
## <a name="add-sample-data"></a>サンプル データの追加

[!INCLUDE [cosmos-db-create-sql-api-add-sample-data](../../includes/cosmos-db-create-sql-api-add-sample-data.md)]

## <a name="query-your-data"></a>データのクエリ

[!INCLUDE [cosmos-db-create-sql-api-query-data](../../includes/cosmos-db-create-sql-api-query-data.md)]

## <a name="clone-the-sample-application"></a>サンプル アプリケーションの複製

次は、コードを使った作業に移りましょう。 GitHub から SQL API アプリの複製を作成し、接続文字列を設定して実行します。 プログラムでデータを処理することが非常に簡単であることがわかります。 

次のコマンドを実行して、サンプル レポジトリを複製します。 このコマンドは、コンピューター上にサンプル アプリのコピーを作成します。

```bash
git clone https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started.git
```

## <a name="review-the-code"></a>コードの確認

この手順は省略可能です。 コード内のデータベース リソースの作成方法に関心がある場合は、次のスニペットを確認できます。 それ以外の場合は、「[アプリの実行](#run-the-app)」に進んでください。 

### <a name="application-configuration-file"></a>アプリケーション構成ファイル

ここでは、Spring Boot と Spring Data を使用してユーザー エクスペリエンスを強化するか方法について説明します。Cosmos クライアントを確立して Cosmos リソースに接続するプロセスは、コードではなく構成になりました。 アプリケーションの起動時に、Spring Boot により **application.properties** の設定を使用してこのひな形がすべて処理されます。

```xml
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

Azure Cosmos DB アカウント、データベース、およびコンテナーを作成したら、構成ファイルの空欄を埋めるだけで、Spring Boot および Spring Data によって次の処理が自動的に実行されます。(1) URI とキーを使用して基となる Java SDK `CosmosClient` インスタンスを作成し、(2) データベースとコンテナーに接続します。 これで準備は完了です。**リソース管理コードはもう必要ありません。**

### <a name="java-source"></a>Java ソース

Spring Data の付加価値は、データストアに対して操作するための単純でクリーンな標準化されたプラットフォームに依存しないインターフェイスにもあります。 上記のリンク先の Spring Data GitHub サンプルに基づいて、Spring Data Azure Cosmos DB を使用して Azure Cosmos DB ドキュメントを操作するための CRUD とクエリのサンプルを以下に示します。

* `save` メソッドを使用した項目の作成と更新。

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]
   
* リポジトリで定義された派生クエリ メソッドを使用したポイント読み取り。 `findByIdAndLastName` では `UserRepository` のポイント読み取りを実行します。 メソッド名に示されたフィールドにより、Spring Data により `id` および `lastName` フィールドに定義されているポイント読み取りが実行されます。

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Read)]

* `deleteAll` を使用した項目の削除:

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

* リポジトリ メソッド名に基づく派生したクエリ。 Spring Data には、`UserRepository` `findByFirstName` メソッドが `firstName` フィールドの Java SDK SQL クエリとして実装されています (このクエリをポイント読み取りとして実装することはできませんでした)。

    [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

## <a name="run-the-app"></a>アプリを実行する

ここで Azure portal に戻って接続文字列情報を取得し、エンドポイント情報を使用してアプリを起動します。 これでアプリが、ホストされているデータベースと通信できます。

1. Git ターミナル ウィンドウで `cd` を使用して、サンプル コード フォルダーに移動します。

    ```bash
    cd azure-spring-data-cosmos-java-sql-api-getting-started/azure-spring-data-cosmos-java-getting-started/
    ```

2. git ターミナル ウィンドウで、次のコマンドを使用して、必要な Spring Data Azure Cosmos DB パッケージをインストールします。

    ```bash
    mvn clean package
    ```

3. git ターミナル ウィンドウで、次のコマンドを使用して Spring Data Azure Cosmos DB アプリケーションを起動します。

    ```bash
    mvn spring-boot:run
    ```
    
4. アプリによって **application.properties** が読み込まれ、Azure Cosmos DB アカウントのリソースに接続されます。
5. アプリによって上記のポイント CRUD 操作が実行されます。
6. アプリによって派生クエリが実行されます。
7. アプリでリソースは削除されません。 料金が発生しないようにするには、ポータルに戻ってアカウントから[リソースをクリーンアップ](#clean-up-resources)します。

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal での SLA の確認

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Azure Cosmos DB SQL API アカウントを作成し、データ エクスプローラーを使ってドキュメント データベースやコンテナーを作成する方法について説明しました。また、同じことをプログラムから行う Spring Data アプリを実行する方法についても説明しました。 これで、Azure Cosmos DB アカウントに追加のデータをインポートできるようになりました。 

> [!div class="nextstepaction"]
> [Azure Cosmos DB へのデータのインポート](import-data.md)