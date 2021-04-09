---
title: Azure Search .NET SDK バージョン 5 へのアップグレード
titleSuffix: Azure Cognitive Search
description: 以前のバージョンから Azure Search .NET SDK バージョン 5 にコードを移行します。 新機能と必要なコード変更について説明します。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7f0e994cd41fc55824cf347a51ccfed25589a310
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91539539"
---
# <a name="upgrade-to-azure-search-net-sdk-version-5"></a>Azure Search .NET SDK バージョン 5 へのアップグレード

バージョン 4.0-preview 以前の [.NET SDK](/dotnet/api/overview/azure/search) を使用している場合、この記事を参考にして、バージョン 5 を使用するようにアプリケーションをアップグレードできます。

例を含む SDK の一般的なチュートリアルについては、「 [.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md)」を参照してください。

Azure Search .NET SDK のバージョン 5 には、以前のバージョンからの変更がいくつか含まれています。 ほとんどは小さなものなので、コードの変更に必要な作業は最小限で済みます。 新しいバージョンの SDK を使用するようにコードを変更する方法については、「 [アップグレードの手順](#UpgradeSteps) 」を参照してください。

> [!NOTE]
> 2\.0-preview 以前のバージョンを使用している場合は、まずバージョン 3 にアップグレードしてから、バージョン 5 にアップグレードする必要があります。 手順については、「[Azure Search .NET SDK バージョン 3 へのアップグレード](search-dotnet-sdk-migration.md)」をご覧ください。
>
> Azure Search サービスのインスタンスは、最新のバージョンを含む複数の REST API バージョンをサポートします。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するようにコードを移行することをお勧めします。 REST API を使用している場合は、api-version パラメーターを使用して、すべての要求に API バージョンを指定する必要があります。 .NET SDK を使用している場合は、使用している SDK のバージョンによって REST API の対応するバージョンが決まります。 サービスが新しいバージョンの API をサポートするようにアップグレードされた場合でも、使用中の古い SDK のコードを変更なしで引き続き実行できます。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-5"></a>バージョン 5 の新機能
Azure Search .NET SDK のバージョン 5 は、Azure Search REST API の最新の一般公開バージョン (2017-11-11) を対象としています。 これにより、次のような Azure Search の新機能を .NET アプリケーションから使用することが可能になります。

* [シノニム](search-synonyms.md)。
* インデクサーの実行履歴内の警告に、プログラムからアクセスできるようになりました (詳細については、[.NET リファレンス](/dotnet/api/microsoft.azure.search.models.indexerexecutionresult)で、`IndexerExecutionResult` の `Warning` プロパティをご覧ください)。
* .NET Core 2 のサポート。
* 新しいパッケージ構造により、SDK の必要な一部のみを使用できるようになっています (詳細については、「[バージョン 5 における重大な変更](#ListOfChanges)」をご覧ください)。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順
最初に、NuGet パッケージ マネージャー コンソールを使用して、または Visual Studio でプロジェクト参照を右クリックして [NuGet パッケージの管理...] を選択することで、 `Microsoft.Azure.Search` の NuGet 参照を更新します。

NuGet が新しいパッケージとその依存関係をダウンロードした後、プロジェクトをリビルドします。 コードの構成方法に応じて、正常にリビルドされます。 リビルドされれば、準備は完了です。

ビルドが失敗した場合は、次のようにビルド エラーが表示されます。

```output
The name 'SuggesterSearchMode' does not exist in the current context
```

次の手順として、このビルド エラーを修正します。 エラーの原因と修正方法については、「[バージョン 5 における重大な変更](#ListOfChanges)」をご覧ください。

Azure Search .NET SDK のパッケージにおける変更のため、バージョン 5 を使用するためには、アプリケーションをリビルドする必要があることに注意してください。 これらの変更については、「[バージョン 5 における重大な変更](#ListOfChanges)」で詳しく説明します。

古いメソッドまたはプロパティに関連するビルド警告が表示される場合があります。 それらの警告には、非推奨の機能の代わりに何を使用するかについての指示が含まれます。 たとえば、アプリケーションが `IndexingParametersExtensions.DoNotFailOnUnsupportedContentType` メソッドを使用している場合、「この動作は既定で有効にされるようになったため、このメソッドを呼び出す必要はなくなりました」といった警告が表示されます。

ビルドのエラーまたは警告をすべて修正した後、必要に応じて新しい機能を利用するようにアプリケーションを変更できます。 SDK の新機能の詳細については、「[バージョン 5 の新機能](#WhatsNew)」をご覧ください。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-5"></a>バージョン 5 における重大な変更

### <a name="new-package-structure"></a>新しいパッケージの構造

バージョン 5 における最も重大な変更は、`Microsoft.Azure.Search` アセンブリとその内容が 4 つの別個のアセンブリに分割されたことです。これらは 4 つの別個の NuGet パッケージとして配布されるようになりました。

 - `Microsoft.Azure.Search`:依存関係がある Azure Search の他のパッケージをすべて含むメタパッケージです。 以前のバージョンの SDK からアップグレードする場合は、単純にこのパッケージをアップグレードしてリビルドすれば、新しいバージョンの使用を開始するのに十分です。
 - `Microsoft.Azure.Search.Data`:Azure Search を使用して .NET アプリケーションを開発していて、インデックス内のドキュメントのクエリまたは更新のみを行う必要がある場合は、このパッケージを使用します。 インデックス、シノニム マップ､またはサービス レベルのその他のリソースの作成や更新も行う必要がある場合は､代わりに `Microsoft.Azure.Search` パッケージを使用します｡
 - `Microsoft.Azure.Search.Service`:.NET で、Azure Search インデックス、シノニム マップ、インデクサー、データ ソース、またはサービスレベルのその他のリソースを管理するための自動化を開発する場合は、このパッケージを使用します。 インデックス内のドキュメントのクエリまたは更新のみを行う場合は､代わりに `Microsoft.Azure.Search.Data` パッケージを使用します｡ Azure Search のすべての機能が必要な場合は､代わりに `Microsoft.Azure.Search` パッケージを使用します｡
 - `Microsoft.Azure.Search.Common`:Azure Search .NET ライブラリに必要な共通の型です。 このパッケージは、アプリケーションで直接使用する必要はないはずで、依存関係としての使用のみが想定されています｡
 
この変更が技術的に重要なのは、多くの型がアセンブリ間で移動されたためです。 バージョン 5 の SDK にアップグレードするためにアプリケーションのリビルドが必要なのは、このためです。

バージョン 5 には、その他にも、アプリケーションのリビルドだけでなく、コードの変更が必要な場合がある重大な変更が少し含まれています。

### <a name="change-to-suggesters"></a>サジェスターに対する変更点 

`Suggester` コンストラクターで、`SuggesterSearchMode` に対する `enum` パラメーターはなくなりました。 この列挙型の値は 1 つだけであり、そのため冗長でした。 この結果としてビルド エラーが発生する場合は、単純に `SuggesterSearchMode` パラメーターの参照を削除してください。

### <a name="removed-obsolete-members"></a>古い形式のメンバーの削除

以前のバージョンで古い形式としてマークされており、その後バージョン 5 で削除されたメソッドまたはプロパティに関連するビルド エラーが発生する場合があります。 このようなエラーが発生した場合、その解決方法を次に示します。

- `IndexingParametersExtensions.IndexStorageMetadataOnly` メソッドを使用していた場合は、代わりに `SetBlobExtractionMode(BlobExtractionMode.StorageMetadata)` を使用します。
- `IndexingParametersExtensions.SkipContent` メソッドを使用していた場合は、代わりに `SetBlobExtractionMode(BlobExtractionMode.AllMetadata)` を使用します。

### <a name="removed-preview-features"></a>削除されたプレビュー機能

バージョン 4.0-preview からバージョン 5 にアップグレードする場合、BLOB インデクサーでの JSON 配列および CSV 解析のサポートが削除されたことに注意してください。これらの機能はプレビュー段階であるためです。 具体的には、`IndexingParametersExtensions` クラスの次のメソッドが削除されました。

- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

アプリケーションにこれらの機能に対するハードの依存関係が存在する場合、Azure Search .NET SDK のバージョン 5 にアップグレードすることはできません。 バージョン 4.0-preview を引き続き使用できますが、 **実稼働アプリケーションでのプレビュー版 SDK の使用は推奨されない** ことに注意してください。 プレビュー機能は評価のみを目的としており、変更される場合があります。

## <a name="conclusion"></a>まとめ
Azure Search .NET SDK の使い方について詳しくは、[.NET の方法](search-howto-dotnet-sdk.md)に関する記事をご覧ください。

SDK についてのご意見をお待ちしております。 問題が発生した場合は、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) でご質問ください。 バグを発見した場合は、 [Azure .NET SDK の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/issues)で問題を報告できます。 問題のタイトルの前に、必ず "[Azure Search]" を付けてください。

Azure Search をお使いいただきありがとうございます。