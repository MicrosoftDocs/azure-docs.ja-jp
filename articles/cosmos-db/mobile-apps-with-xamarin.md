---
title: チュートリアル:Xamarin と Azure Cosmos DB を使用したモバイル アプリケーションの構築
description: チュートリアル:Azure Cosmos DB を使用して Xamarin iOS、Android、またはフォーム アプリケーションを作成するチュートリアル。 Azure Cosmos DB は、モバイル アプリ向けの高速な世界規模のクラウド データベースです。
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: d1164c14b406fb66638e6a5333fe38180272aa14
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359338"
---
# <a name="tutorial-build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>チュートリアル:Xamarin と Azure Cosmos DB を使用したモバイル アプリケーションの構築
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

ほとんどのモバイル アプリでは、クラウドにデータを格納する必要があります。Azure Cosmos DB は、モバイル アプリ向けのクラウド データベースです。 モバイル開発者が必要なすべてのものを備えています。 必要に応じて拡大縮小できる、フル マネージドのサービスとしてのデータベースです。 ユーザーが世界中どこにいても、アプリケーションにデータを透過的に提供できます。 [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) を使用すると、中間層なしで、Xamarin モバイル アプリが Azure Cosmos DB と直接やり取りすることができます。

この記事では、Xamarin と Azure Cosmos DB を使用したモバイル アプリの構築についてのチュートリアルを提供します。 ユーザーとアクセス許可を管理する方法を含め、このチュートリアルの完全なソース コードについては、[GitHub の Xamarin と Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin) に関するページを参照してください。

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>モバイル アプリ向けの Azure Cosmos DB の機能
Azure Cosmos DB は、モバイル アプリ開発者向けの次の主要な機能を備えています。

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-for-mobile.png" alt-text="モバイル アプリ向けの Azure Cosmos DB の機能":::

* スキーマなしのデータに対するリッチ クエリ。 Azure Cosmos DB では、データをスキーマなしの JSON ドキュメントとして異種コレクションに格納します。 スキーマやインデックスについて心配する必要なく、[高速なリッチ クエリ](./sql-query-getting-started.md)を提供します。
* 高速スループット。 Azure Cosmos DB では、ドキュメントを読み書きするのにわずか数ミリ秒しかかかりません。 開発者は必要なスループットを指定でき、Azure Cosmos DB はすべての単一リージョンのアカウントに緩やかな整合性で 99.999% の可用性 SLA を提供し、全ての複数リージョンのデータベース アカウントに 99.99% の 読み取り可用性を提供します。
* 無限のスケール。 お使いの Azure Cosmos のコンテナーは、[お使いのアプリの規模の拡大に合わせて拡大](partitioning-overview.md)します。 小さいデータ サイズと 1 秒あたり数百件の要求のスループットから開始できます。 コレクションまたはデータベースは、ペタバイトのデータと 1 秒あたり数億件の要求という任意の大きさのスループットに拡張できます。
* グローバルに分散。 モバイル アプリ ユーザーは外に出かけ、世界中を飛び回ることもよくあります。 Azure Cosmos DB は、[グローバル分散データベース](distribute-data-globally.md)です。 ユーザーがデータにアクセスするには、地図をクリックします。
* 組み込みの豊富な承認機能。 Azure Cosmos DB では、複雑なカスタムの承認コードを作成せずに、[ユーザーごとのデータ](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems)やマルチ ユーザー共有データのような一般的なパターンを簡単に実装できます。
* 地理空間クエリ。 現在、多くのモバイル アプリが地理的な場所に応じたエクスペリエンスを提供しています。 [地理空間型](./sql-query-geospatial-intro.md)のファーストクラスのサポートにより、Azure Cosmos DB は、これらのエクスペリエンスの作成を簡単に実現可能なものにしています。
* バイナリの添付ファイル。 アプリ データには、バイナリ BLOB が含まれていることがよくあります。 添付ファイルがネイティブでサポートされているため、Azure Cosmos DB をアプリ データのワンストップ ショップとして簡単に使用できます。

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Azure Cosmos DB と Xamarin のチュートリアル
次のチュートリアルでは、Xamarin と Azure Cosmos DB を使用してモバイル アプリケーションを構築する方法について説明します。 このチュートリアルの完全なソース コードについては、[GitHub の Xamarin と Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin) に関するページを参照してください。

### <a name="get-started"></a>はじめに
Azure Cosmos DB の利用を開始するのは簡単です。 Azure Portal に移動して、新しい Azure Cosmos DB アカウントを作成します。 **[クイック スタート]** タブをクリックします。Azure Cosmos DB アカウントにあらかじめ接続されている Xamarin Forms のタスク一覧のサンプルをダウンロードします。 

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-quickstart.png" alt-text="モバイル アプリ向けの Azure Cosmos DB の [クイック スタート]":::

または、既存の Xamarin アプリがある場合は、[Azure Cosmos DB NuGet パッケージ](sql-api-sdk-dotnet-core.md)を追加できます。 Azure Cosmos DB では、Xamarin.iOS、Xamarin.Android、および Xamarin Forms の共有ライブラリをサポートしています。

### <a name="work-with-data"></a>データの処理
Azure Cosmos DB では、データ レコードをスキーマなしの JSON ドキュメントとして異種コレクションに格納します。 同じコレクションに異なる構造のドキュメントを格納できます。

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

Xamarin プロジェクトでは、スキーマなしのデータに対して統合言語クエリを使用することができます。

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>Add users
多くの入門サンプルと同様に、ダウンロードした Azure Cosmos DB サンプルでは、アプリのコード内にハードコードされた主キーを使用してサービスに対して認証を行います。 この既定の方法は、ローカル エミュレーター以外でアプリを実行する場合にはお勧めしません。 承認されていないユーザーが主キーを入手すると、Azure Cosmos DB アカウントの全データのセキュリティが侵害されるおそれがあります。 それよりも、サインインしたユーザーのレコードのみにアプリからアクセスできるようにする必要があります。 Azure Cosmos DB では、コレクション、パーティション キーでグループ化されたドキュメントのセット、または特定のドキュメントに対する読み取りアクセスまたは読み取り/書き込みアクセス許可をアプリケーションに付与できます。 

To-Do List アプリをマルチ ユーザーの To-Do List アプリに変更するには、以下の手順に従います。 

  1. Facebook、Active Directory などのプロバイダーを使用して、ログインをアプリに追加します。

  2. パーティション キーとして **/userId** を使用して Azure Cosmos DB UserItems コレクションを作成します。 コレクションのパーティション キーを指定すると、アプリのユーザー数の増加に応じて Azure Cosmos DB を無限に拡張でき、同時に高速なクエリも継続して利用できます。

  3. Azure Cosmos DB リソース トークン ブローカーを追加します。 これは、ユーザーを認証し、サインインしているユーザーに対してユーザーのパーティション内のドキュメントにのみアクセスできる有効期間の短いトークンを発行する簡単な Web API です。 この例では、App Service でリソース トークン ブローカーをホストします。

  4. Facebook を使用してリソース トークン ブローカーに対して認証するようにアプリを変更し、サインインした Facebook ユーザーのリソース トークンを要求します。 これで、UserItems コレクション内のデータにアクセスできます。  

このパターンの完全なコード サンプルについては、[GitHub のリソース トークン ブローカー](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin/UserItems)に関するページを参照してください。 次の図には、このソリューションを示しています。

:::image type="content" source="media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png" alt-text="Azure Cosmos DB ユーザーおよびアクセス許可のブローカー" border="false":::

2 人のユーザーが同じ To-Do List にアクセスできるようにする場合は、リソース トークン ブローカーでアクセス トークンにアクセス許可を追加できます。

### <a name="scale-on-demand"></a>オンデマンドでの拡張
Azure Cosmos DB は、管理された DBaaS (database as a service) です。 ユーザー ベースが拡大しても、VM のプロビジョニングやコア数の増加について心配する必要はありません。 必要なのは、アプリに必要な 1 秒あたりの操作数 (スループット) を Azure Cosmos DB に指示することだけです。 **[スケール]** タブで、1 秒あたりの要求ユニット (RU) と呼ばれるスループットの尺度を使用して、スループットを指定できます。 たとえば、1 KB のドキュメントの読み取り操作には 1 RU が必要です。 また、**スループット** メトリックにアラートを追加して、トラフィックの増加を監視し、アラートの発生に応じてスループットをプログラムで変更することもできます。

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png" alt-text="オンデマンドでの Azure Cosmos DB のスループットの拡張":::

### <a name="go-planet-scale"></a>世界規模化
アプリが普及すると、ユーザーが世界中に広がる可能性があります。 または、不測の事態に備える必要があるかもしれません。 Azure Portal に移動して、Azure Cosmos DB アカウントを開きます。 地図上でクリックすると、世界中の任意の数のリージョンにデータを継続的にレプリケートできます。 この機能により、ユーザーがどこにいてもデータを利用できるようになります。 不測の事態に備えるためにフェールオーバー ポリシーを追加することもできます。

:::image type="content" source="media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png" alt-text="地理的リージョンをまたいだ Azure Cosmos DB の拡張" border="false":::

おめでとうございます。 ソリューションが完成し、モバイル アプリで Xamarin と Azure Cosmos DB を利用できるようになりました。 同様の手順に従って、Azure Cosmos DB JavaScript SDK を使用した Cordova アプリや、Azure Cosmos DB REST API を使用したネイティブ iOS/Android アプリを構築することができます。

## <a name="next-steps"></a>次のステップ
* [GitHub の Xamarin と Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples/xamarin) のソース コードを確認する。
* [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md) をダウンロードする。
* [.NET アプリケーション](sql-api-dotnet-samples.md)向けの他のコード サンプルを探す。
* [Azure Cosmos DB のリッチ クエリ機能](./sql-query-getting-started.md)について確認する。
* [Azure Cosmos DB の地理空間サポート](./sql-query-geospatial-intro.md)について確認する。