---
title: Azure Search .NET SDK バージョン 9 へのアップグレード
titleSuffix: Azure Cognitive Search
description: 以前のバージョンから Azure Search .NET SDK バージョン 9 にコードを移行します。 新機能と必要なコード変更について説明します。
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: 7726877efcb72caf5bb4c92e2e1c65e5df68f270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89002710"
---
# <a name="upgrade-to-azure-search-net-sdk-version-9"></a>Azure Search .NET SDK バージョン 9 へのアップグレード

バージョン 7.0-preview 以前の [Azure Search .NET SDK](/dotnet/api/overview/azure/search) を使用している場合、この記事を参考にして、バージョン 9 を使用するようにアプリケーションをアップグレードできます。

> [!NOTE]
> バージョン 8.0-preview を使用してまだ一般提供されていない機能を評価する場合、この記事の手順に従って、以前のバージョンから 8.0-preview にアップグレードすることもできます。

例を含む SDK の一般的なチュートリアルについては、「 [.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md)」を参照してください。

Azure Search .NET SDK のバージョン 9 には、以前のバージョンからの変更が多く含まれています。 これらの一部は大きな変更ですが、コードに対する変更はごくわずかです。 新しいバージョンの SDK を使用するようにコードを変更する方法については、「 [アップグレードの手順](#UpgradeSteps) 」を参照してください。

> [!NOTE]
> 4\.0-preview 以前のバージョンを使用している場合は、まずバージョン 5 にアップグレードしてから、バージョン 9 にアップグレードする必要があります。 手順については、「[Azure Search .NET SDK バージョン 5 へのアップグレード](search-dotnet-sdk-migration-version-5.md)」をご覧ください。
>
> Azure Search サービスのインスタンスは、最新のバージョンを含む複数の REST API バージョンをサポートします。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するようにコードを移行することをお勧めします。 REST API を使用している場合は、api-version パラメーターを使用して、すべての要求に API バージョンを指定する必要があります。 .NET SDK を使用している場合は、使用している SDK のバージョンによって REST API の対応するバージョンが決まります。 サービスが新しいバージョンの API をサポートするようにアップグレードされた場合でも、使用中の古い SDK のコードを変更なしで引き続き実行できます。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-9"></a>バージョン 9 の新機能
Azure Search .NET SDK のバージョン 9 では、Azure Search REST API の 2019-05-06 バージョンを対象としており、次の機能を備えています。

* [AI エンリッチメント](cognitive-search-concept-intro.md)では、画像や BLOB などの構造化されていないデータ ソースからテキストを抽出し、コンテンツを充実させて Azure Search インデックスで検索しやすくします。
* [複合型](search-howto-complex-data-types.md)のサポートにより、Azure Search インデックス内の入れ子になったほとんどすべての JSON 構造をモデル化できます。
* [オートコンプリート](search-autocomplete-tutorial.md)には、自動検索候補の動作を実装するための **サジェスト** API の代替手段が用意されています。 オートコンプリートは、ユーザーが現在入力している単語または語句を "完成" させます。
* [JsonLines 解析モード](search-howto-index-json-blobs.md)。Azure BLOB インデックスの一部で、JSON エンティティごとに 1 つの検索ドキュメントが新規行として作成されます。

### <a name="new-preview-features-in-version-80-preview"></a>バージョン 8.0-preview での新しいプレビュー機能
Azure Search .NET SDK のバージョン 8.0-preview は、API バージョン 2017-11-11-Preview を対象とします。 このバージョンには、バージョン 9 と同じ機能が含まれるほか、以下も含まれます。

* [顧客が管理する暗号化キー](search-security-manage-encryption-keys.md)。これは、サービス側の暗号化の保存用の新しいプレビュー機能です。 Microsoft が管理する組み込みの暗号化の保存だけではなく、自分だけが鍵を所有している暗号化のレイヤーを追加で適用できます。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順
最初に、NuGet パッケージ マネージャー コンソールを使用して、または Visual Studio でプロジェクト参照を右クリックして [NuGet パッケージの管理...] を選択することで、 `Microsoft.Azure.Search` の NuGet 参照を更新します。

NuGet が新しいパッケージとその依存関係をダウンロードした後、プロジェクトをリビルドします。 コードの構成方法に応じて、正常にリビルドされます。 リビルドされれば、準備は完了です。

ビルドが失敗した場合、各ビルド エラーを修正する必要があります。 可能性のある各ビルド エラーを解決する方法については、「[バージョン 9 における重大な変更](#ListOfChanges)」をご覧ください。

古いメソッドまたはプロパティに関連するビルド警告が表示される場合があります。 それらの警告には、非推奨の機能の代わりに何を使用するかについての指示が含まれます。 たとえば、アプリケーションで `DataSourceType.DocumentDb` プロパティ使用している場合は、"このメンバーは非推奨です。 CosmosDb を使用してください" という警告が表示されます。

ビルドのエラーまたは警告をすべて修正した後、必要に応じて新しい機能を利用するようにアプリケーションを変更できます。 SDK の新機能の詳細については、「[バージョン 9 の新機能](#WhatsNew)」をご覧ください。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-9"></a>バージョン 9 における重大な変更

バージョン 9 には、アプリケーションのリビルドだけでなく、コードの変更が必要な場合がある重大な変更がいくつか含まれています。

> [!NOTE]
> 以下の変更の一覧はすべてを網羅しているわけではありません。 変更によっては、ビルド エラーが発生しない可能性がありますが、以前のバージョンの Azure Search .NET SDK アセンブリに依存するアセンブリとのバイナリの互換性が失われるために技術的に重大になります。 こうした変更は、以下には含まれません。 バージョン 9 にアップグレードするときに、バイナリ互換性の問題を回避するために、アプリケーションを再構築してください。

### <a name="immutable-properties"></a>不変のプロパティ

いくつかのモデル クラスのパブリック プロパティが変更できなくなりました。 テスト用にこれらのクラスのカスタム インスタンスを作成する必要がある場合は、新しいパラメーター化されたコンストラクターを使用できます。

  - `AutocompleteItem`
  - `DocumentSearchResult`
  - `DocumentSuggestResult`
  - `FacetResult`
  - `SearchResult`
  - `SuggestResult`

### <a name="changes-to-field"></a>フィールドに対する変更

`Field` クラスが変更され、、複合フィールドも表せるようになりました。

次の `bool` プロパティで null が許容されるようになりました。

  - `IsFilterable`
  - `IsFacetable`
  - `IsSearchable`
  - `IsSortable`
  - `IsRetrievable`
  - `IsKey`

その理由は、これらのプロパティは複合フィールドの場合、`null` である必要があるためです。 これらのプロパティを読み取るコードがある場合は、これが `null` を処理するように準備される必要があります。 `Field` のその他のすべてのプロパティでは null 値が許容されており、引き続き null 値を許容する必要があること、またこれらの一部は複合フィールドの場合 (具体的には以下の場合)、`null` になることにも注意してください。

  - `Analyzer`
  - `SearchAnalyzer`
  - `IndexAnalyzer`
  - `SynonymMaps`

`Field` のパラメーターなしのコンストラクターは `internal` になりました。 今後、すべての `Field` に、構築時に明示的な名前とデータ型が必要になります。

### <a name="simplified-batch-and-results-types"></a>バッチおよび結果の種類の簡略化

バージョン 7.0-preview 以前では、ドキュメントのグループをカプセル化するさまざまなクラスが並列クラス階層に構成されていました。

  -  `DocumentSearchResult` と `DocumentSearchResult<T>` は `DocumentSearchResultBase` を継承
  -  `DocumentSuggestResult` と `DocumentSuggestResult<T>` は `DocumentSuggestResultBase` を継承
  -  `IndexAction` と `IndexAction<T>` は `IndexActionBase` を継承
  -  `IndexBatch` と `IndexBatch<T>` は `IndexBatchBase` を継承
  -  `SearchResult` と `SearchResult<T>` は `SearchResultBase` を継承
  -  `SuggestResult` と `SuggestResult<T>` は `SuggestResultBase` を継承

ジェネリック型パラメーターがない派生型は、"動的に型指定された" シナリオで使用され、型 `Document` の使用が想定されます。

バージョン 8.0-preview 以降では、基底クラスと非ジェネリック形式の派生クラスはすべて削除されています。 動的に型指定されたシナリオでは、`IndexBatch<Document>`、`DocumentSearchResult<Document>` などを使用できます。
 
### <a name="removed-extensibleenum"></a>削除された ExtensibleEnum

`ExtensibleEnum` 基底クラスは削除されました。 ここから派生したすべてのクラスは、`AnalyzerName`、`DataType`、および `DataSourceType` などの構造体になります。 その `Create` メソッドも削除されています。 これらの型は文字列から暗黙的に変換可能なため、`Create` に対する呼び出しを削除するだけで済みます。 これによりコンパイラ エラーが発生した場合は、キャストにより変換演算子を明示的に呼び出すことができます。 たとえば、次のようなコードを変更できます。

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", AnalyzerName.Create("my_email_analyzer")) { IsSearchable = true }
    },
    ...
}
```

これを、次のように変更します。

```csharp
var index = new Index()
{
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("message", (AnalyzerName)"my_email_analyzer") { IsSearchable = true }
    },
    ...
}
```

これらの型の省略可能な値を保持するプロパティは null 許容型として明示的に型指定されているため、引き続き省略可能です。

### <a name="removed-facetresults-and-hithighlights"></a>削除された FacetResults と HitHighlights

`FacetResults` および `HitHighlights` クラスが削除されました。 ファセットの結果は `IDictionary<string, IList<FacetResult>>` として型指定され、`IDictionary<string, IList<string>>` として強調表示されます。 この変更によるビルド エラーを解決する簡単な方法として、削除された型を使用する各ファイルの上部に `using` エイリアスを追加します。 次に例を示します。

```csharp
using FacetResults = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<Models.FacetResult>>;
using HitHighlights = System.Collections.Generic.IDictionary<string, System.Collections.Generic.IList<string>>;
```

### <a name="change-to-synonymmap"></a>SynonymMap に対する変更 

`SynonymMap` コンストラクターで、`SynonymMapFormat` に対する `enum` パラメーターはなくなりました。 この列挙型の値は 1 つだけであり、そのため冗長でした。 この結果としてビルド エラーが発生する場合は、単純に `SynonymMapFormat` パラメーターの参照を削除してください。

### <a name="miscellaneous-model-class-changes"></a>その他のモデル クラスの変更

`AutocompleteParameters` の `AutocompleteMode` プロパティが null 許容ではなくなりました。 このプロパティを `null` に割り当てるコードがある場合、これを単に削除することで、プロパティは既定値に自動的に初期化されます。

`IndexAction` コンストラクターに対するパラメーターの順序が変わり、このコンストラクターは自動生成されるようになりました。 コンストラクターを使用する代わりに、ファクトリ メソッド `IndexAction.Upload`、`IndexAction.Merge` などを使用することをお勧めします。

### <a name="removed-preview-features"></a>削除されたプレビュー機能

バージョン 8.0-preview からバージョン 9 にアップグレードする場合、ユーザーが管理するキーによる暗号化は、。この機能はまだプレビュー段階であるため、削除されていることに注意してください。 具体的には、`Index` および `SynonymMap` の `EncryptionKey` プロパティが削除されています。

アプリケーションにこの機能に対するハードの依存関係が存在する場合、Azure Search .NET SDK のバージョン 9 にアップグレードすることはできません。 バージョン 8.0-preview を引き続き使用できますが、 **実稼働アプリケーションでのプレビュー版 SDK の使用は推奨されない** ことに注意してください。 プレビュー機能は評価のみを目的としており、変更される場合があります。

> [!NOTE]
> SDK のバージョン 8.0-preview を使用して暗号化されたインデックスまたはシノニム マップを作成した場合、引き続きこれらを使用したり、SDK のバージョン 9 を使用したその定義を変更したりできます。その際、その暗号化の状態に悪影響はありません。 SDK のバージョン 9 では `encryptionKey` プロパティは REST API に送信されず、結果として、REST API によってリソースの暗号化の状態が変更されることはありません。 

### <a name="behavioral-change-in-data-retrieval"></a>データ取得の動作変更

型 `Document` のインスタンスを返す "動的に型指定された" `Search`、`Suggest`、または `Get` API を使用する場合、空の JSON 配列が `string[]` ではなく `object[]` に逆シリアル化されるようになったことに注意してください。

## <a name="conclusion"></a>まとめ
Azure Search .NET SDK の使い方について詳しくは、[.NET の方法](search-howto-dotnet-sdk.md)に関する記事をご覧ください。

SDK についてのご意見をお待ちしております。 問題が発生した場合は、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) でご質問ください。 バグを発見した場合は、 [Azure .NET SDK の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/issues)で問題を報告できます。 問題のタイトルの前に、必ず "[Azure Search]" を付けてください。

Azure Search をお使いいただきありがとうございます。