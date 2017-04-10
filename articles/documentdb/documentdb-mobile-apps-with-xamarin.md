---
title: "Xamarin と Azure DocumentDB を使用したモバイル アプリケーションの構築 | Microsoft Docs"
description: "Azure DocumentDB を使用して Xamarin iOS、Android、またはフォーム アプリケーションを作成するチュートリアル。 DocumentDB は、モバイル アプリ向けの高速な世界規模のクラウド データベースです。"
services: documentdb
documentationcenter: .net
author: arramac
manager: monicar
editor: 
ms.assetid: ff97881a-b41a-499d-b7ab-4f394df0e153
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/20/2017
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: 3e058505f7c17d19d2cebca053badb3505a00f13
ms.lasthandoff: 03/28/2017


---
# <a name="build-mobile-applications-with-xamarin-and-azure-documentdb"></a>Xamarin と Azure DocumentDB を使用したモバイル アプリケーションの構築
ほとんどのモバイル アプリでは、クラウドにデータを格納する必要があります。Azure DocumentDB は、モバイル アプリ向けのクラウド データベースです。 モバイル開発者が必要なすべてのものを備えています。 必要に応じて拡大縮小できる、完全に管理されたサービスとしての NoSQL データベースです。 ユーザーが世界中どこにいても、アプリケーションにデータを透過的に提供できます。 [Azure DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md) を使用することで、中間層なしで、Xamarin モバイル アプリが DocumentDB と直接やり取りすることができます。

この記事では、Xamarin と DocumentDB を使用したモバイル アプリの構築についてのチュートリアルを提供します。 ユーザーとアクセス許可を管理する方法を含め、このチュートリアルの完全なソース コードについては、[GitHub の Xamarin と DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) に関するページを参照してください。

## <a name="documentdb-capabilities-for-mobile-apps"></a>モバイル アプリ向けの DocumentDB の機能
DocumentDB は、モバイル アプリ開発者向けの次の主要な機能を備えています。

![モバイル アプリ向けの DocumentDB の機能](media/documentdb-mobile-apps-with-xamarin/documentdb-for-mobile.png)

* スキーマなしのデータに対するリッチ クエリ。 DocumentDB では、データをスキーマなしの JSON ドキュメントとして異種コレクションに格納します。 スキーマやインデックスについて心配する必要なく、[高速なリッチ クエリ](documentdb-sql-query.md)を提供します。
* 高速スループット。 DocumentDB を使用してドキュメントを読み書きするのには、数ミリ秒しかかかりません。 開発者は必要なスループットを指定でき、DocumentDB では 99.99% の SLA でそのスループットが保証されます。
* 無限のスケール。 DocumentDB のコレクションは、[アプリの規模の拡大に合わせて増加](documentdb-partition-data.md)します。 小さいデータ サイズと 1 秒あたり数百件の要求のスループットから開始できます。 コレクションは、ペタバイトのデータと 1 秒あたり数億件の要求という任意の大きさのスループットに拡張できます。
* グローバルに分散。 モバイル アプリ ユーザーは外に出かけ、世界中を飛び回ることもよくあります。 DocumentDB は、[グローバル分散データベース](documentdb-distribute-data-globally.md)です。 ユーザーがデータにアクセスするには、地図をクリックします。
* 組み込みの豊富な承認機能。 DocumentDB では、複雑なカスタムの承認コードを作成せずに、[ユーザーごとのデータ](https://aka.ms/documentdb-xamarin-todouser)やマルチ ユーザー共有データのような一般的なパターンを簡単に実装できます。
* 地理空間クエリ。 現在、多くのモバイル アプリが地理的な場所に応じたエクスペリエンスを提供しています。 DocumentDB では、[地理空間型](documentdb-geospatial.md)を高いレベルでサポートすることにより、そうしたエクスペリエンスの作成を簡単に実現できます。
* バイナリの添付ファイル。 アプリ データには、バイナリ BLOB が含まれていることがよくあります。 添付ファイルがネイティブでサポートされているため、DocumentDB をアプリ データのワンストップ ショップとして簡単に使用できます。

## <a name="documentdb-and-xamarin-tutorial"></a>DocumentDB と Xamarin のチュートリアル
次のチュートリアルでは、Xamarin と DocumentDB を使用してモバイル アプリケーションを構築する方法について説明します。 このチュートリアルの完全なソース コードについては、[GitHub の Xamarin と DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) に関するページを参照してください。

### <a name="get-started"></a>作業開始
DocumentDB の利用を開始するのは簡単です。 Azure Portal に移動して、新しい DocumentDB アカウントを作成します。 **[クイック スタート]** タブをクリックします。 DocumentDB アカウントに既に接続されている Xamarin Forms の To-Do List サンプルをダウンロードします。 

![モバイル アプリ向けの DocumentDB の [クイック スタート]](media/documentdb-mobile-apps-with-xamarin/documentdb-quickstart.png)

または、既存の Xamarin アプリがある場合は、[DocumentDB NuGet パッケージ](documentdb-sdk-dotnet-core.md)を追加できます。 DocumentDB では、Xamarin.IOS、Xamarin.Android、および Xamarin Forms の共有ライブラリをサポートしています。

### <a name="work-with-data"></a>データの処理
DocumentDB では、データ レコードをスキーマなしの JSON ドキュメントとして異種コレクションに格納します。 同じコレクションに異なる構造のドキュメントを格納できます。

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
多くの入門サンプルと同様に、ダウンロードした DocumentDB のサンプルでは、アプリのコード内にハードコードされたマスター キーを使用してサービスに対して認証を行います。 この既定の方法は、ローカル エミュレーター以外でアプリを実行する場合にはお勧めしません。 承認されていないユーザーがマスター キーを入手すると、DocumentDB アカウントの全データのセキュリティが侵害される可能性があります。 それよりも、サインインしたユーザーのレコードのみにアプリからアクセスできるようにする必要があります。 DocumentDB では、コレクション、パーティション キーでグループ化された一連のドキュメント、または特定のドキュメントに対する読み取りアクセスまたは読み取り/書き込みアクセス許可をアプリケーションに付与できます。 

To-Do List アプリをマルチ ユーザーの To-Do List アプリに変更するには、以下の手順に従います。 

  1. Facebook、Active Directory などのプロバイダーを使用して、ログインをアプリに追加します。

  2. パーティション キーとして **/userId** を使用して DocumentDB UserItems コレクションを作成します。 コレクションのパーティション キーを指定すると、高速なクエリを引き続き提供しながら、アプリのユーザー数の増加に応じて DocumentDB を無限に拡張できます。

  3. DocumentDB リソース トークン ブローカーを追加します。 これは、ユーザーを認証し、サインインしているユーザーに対してユーザーのパーティション内のドキュメントにのみアクセスできる有効期間の短いトークンを発行する簡単な Web API です。 この例では、App Service でリソース トークン ブローカーをホストします。

  4. Facebook を使用してリソース トークン ブローカーに対して認証するようにアプリを変更し、サインインした Facebook ユーザーのリソース トークンを要求します。 これで、UserItems コレクション内のデータにアクセスできます。  

このパターンの完全なコード サンプルについては、[GitHub のリソース トークン ブローカー](http://aka.ms/documentdb-xamarin-todouser)に関するページを参照してください。 次の図には、このソリューションを示しています。

![DocumentDB ユーザーおよびアクセス許可のブローカー](media/documentdb-mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

ここで、2 人のユーザーが同じ To-Do List にアクセスできるようにする場合は、リソース トークン ブローカーでアクセス トークンにアクセス許可を追加できます。

### <a name="scale-on-demand"></a>オンデマンドでの拡張
DocumentDB は、管理されたサービスとしてのデータベースです。 ユーザー ベースが拡大しても、VM のプロビジョニングやコア数の増加について心配する必要はありません。 必要なのは、アプリに必要な 1 秒あたりの操作数 (スループット) を DocumentDB に指示することだけです。 **[スケール]** タブで、1 秒あたりの要求ユニット (RU) と呼ばれるスループットの尺度を使用して、スループットを指定できます。 たとえば、1 KB のドキュメントの読み取り操作には 1 RU が必要です。 また、**スループット** メトリックにアラートを追加して、トラフィックの増加を監視し、アラートの発生に応じてスループットをプログラムで変更することもできます。

![オンデマンドでの DocumentDB のスループットの拡張](media/documentdb-mobile-apps-with-xamarin/documentdb-scale.png)

### <a name="go-planet-scale"></a>世界規模化
アプリが普及すると、ユーザーが世界中に広がる可能性があります。 または、不測の事態に備える必要があるかもしれません。 Azure Portal に移動して、DocumentDB アカウントを開きます。 地図上でクリックすると、世界中の任意の数のリージョンにデータを継続的にレプリケートできます。 この機能により、ユーザーがどこにいてもデータを利用できるようになります。 不測の事態に備えるためにフェールオーバー ポリシーを追加することもできます。

![地理的リージョンをまたいだ DocumentDB の拡張](media/documentdb-mobile-apps-with-xamarin/documentdb-replicate-globally.png)

おめでとうございます。 ソリューションが完成し、モバイル アプリで Xamarin と DocumentDB を利用できるようになりました。 同様の手順に従って、DocumentDB JavaScript SDK を使用した Cordova アプリや、DocumentDB REST API を使用したネイティブ iOS/Android アプリを構築することができます。

## <a name="next-steps"></a>次のステップ
* [GitHub の Xamarin と DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) のソース コードを確認する。
* [DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md) をダウンロードする。
* [.NET アプリケーション](documentdb-dotnet-samples.md)向けの他のコード サンプルを探す。
* [DocumentDB のリッチ クエリ機能](documentdb-sql-query.md)について確認する。
* [DocumentDB の地理空間サポート](documentdb-geospatial.md)について確認する。




