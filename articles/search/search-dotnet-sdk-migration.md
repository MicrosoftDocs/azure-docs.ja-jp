---
title: Azure Search .NET SDK バージョン 3 へのアップグレード - Azure Search
description: 以前のバージョンから Azure Search .NET SDK バージョン 3 にコードを移行します。 新機能と必要なコード変更について説明します。
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 01/15/2018
ms.author: brjohnst
ms.custom: seodec2018
ms.openlocfilehash: 4acf609ca1f81e69babfa1a319b43e20e84a8395
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/12/2018
ms.locfileid: "53317255"
---
# <a name="upgrading-to-the-azure-search-net-sdk-version-3"></a>Azure Search .NET SDK バージョン 3 へのアップグレード
バージョン 2.0-preview 以前の [Azure Search .NET SDK](https://aka.ms/search-sdk) を使用している場合、この記事を参考にして、バージョン 3 を使用するようにアプリケーションをアップグレードできます。

例を含む SDK の一般的なチュートリアルについては、「 [.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md)」を参照してください。

Azure Search .NET SDK のバージョン 3 には、以前のバージョンからの変更がいくつか含まれています。 ほとんどは小さなものなので、コードの変更に必要な作業は最小限で済みます。 新しいバージョンの SDK を使用するようにコードを変更する方法については、「 [アップグレードの手順](#UpgradeSteps) 」を参照してください。

> [!NOTE]
> 1.0.2-preview 以前のバージョンを使用している場合は、まずバージョン 1.1 にアップグレードしてから、バージョン 3 にアップグレードする必要があります。 手順については、「[Azure Search .NET SDK バージョン 1.1 へのアップグレード](search-dotnet-sdk-migration-version-1.md)」をご覧ください。
>
> Azure Search サービスのインスタンスは、最新のバージョンを含む複数の REST API バージョンをサポートします。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するようにコードを移行することをお勧めします。 REST API を使用している場合は、api-version パラメーターを使用して、すべての要求に API バージョンを指定する必要があります。 .NET SDK を使用している場合は、使用している SDK のバージョンによって REST API の対応するバージョンが決まります。 サービスが新しいバージョンの API をサポートするようにアップグレードされた場合でも、使用中の古い SDK のコードを変更なしで引き続き実行できます。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-3"></a>バージョン 3 の新機能
Azure Search .NET SDK のバージョン 3 は、Azure Search REST API の最新の一般公開バージョン (2016-09-01) を対象としています。 これにより、次のような Azure Search の多数の新機能を .NET アプリケーションから使用することが可能になります。

* [カスタム アナライザー](https://aka.ms/customanalyzers)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md) と [Azure Table Storage](search-howto-indexing-azure-tables.md) のインデクサーのサポート
* [フィールド マッピング](search-indexer-field-mappings.md)
* インデックスの定義、インデクサー、およびデータ ソースの安全な同時更新を有効にする Etag のサポート
* モデル クラスを装飾し、新しい `FieldBuilder` クラスを使用することによる、インデックス フィールド定義の宣言による作成のサポート
* .NET Core と .NET Portable Profile 111 のサポート

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順
最初に、NuGet パッケージ マネージャー コンソールを使用して、または Visual Studio でプロジェクト参照を右クリックして [NuGet パッケージの管理...] を選択することで、 `Microsoft.Azure.Search` の NuGet 参照を更新します。

NuGet が新しいパッケージとその依存関係をダウンロードした後、プロジェクトをリビルドします。 コードの構成方法に応じて、正常にリビルドされます。 リビルドされれば、準備は完了です。

ビルドが失敗した場合は、次のようにビルド エラーが表示されます。

    Program.cs(31,45,31,86): error CS0266: Cannot implicitly convert type 'Microsoft.Azure.Search.ISearchIndexClient' to 'Microsoft.Azure.Search.SearchIndexClient'. An explicit conversion exists (are you missing a cast?)

次の手順として、このビルド エラーを修正します。 エラーの原因と修正方法については、「[バージョン 3 における重大な変更](#ListOfChanges)」をご覧ください。

古いメソッドまたはプロパティに関連するビルド警告が表示される場合があります。 それらの警告には、非推奨の機能の代わりに何を使用するかについての指示が含まれます。 たとえば、アプリケーションで `IndexingParameters.Base64EncodeKeys` プロパティ使用している場合は、「`"This property is obsolete. Please create a field mapping using 'FieldMapping.Base64Encode' instead."`」という警告が表示されます。

すべてのビルド エラーを修正した後、必要に応じて新しい機能を利用するようにアプリケーションを変更できます。 SDK の新機能の詳細については、「[バージョン 3 の新機能](#WhatsNew)」をご覧ください。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-3"></a>バージョン 3 における重大な変更
バージョン 3 には、アプリケーションのリビルドだけでなく、コードの変更が必要な場合がある重大な変更が少し含まれています。

### <a name="indexesgetclient-return-type"></a>Indexes.GetClient の戻り型
`Indexes.GetClient` メソッドには新しい戻り型があります。 以前は `SearchIndexClient` が返されていましたが、これはバージョン 2.0-preview で `ISearchIndexClient` に変更され、この変更がバージョン 3 にも引き継がれています。 これは、単体テストで `ISearchIndexClient` の模擬実装を返すことによって `GetClient` メソッドの模擬テストを実行したいユーザーをサポートするための変更です。

#### <a name="example"></a>例
次のようなコードがあるものとします。

```csharp
SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

この場合、次のように変更することでビルド エラーを解決できます。

```csharp
ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");
```

### <a name="analyzername-datatype-and-others-are-no-longer-implicitly-convertible-to-strings"></a>文字列に暗黙的に変換できなくなった AnalyzerName、DataType など
Azure Search .NET SDK には、`ExtensibleEnum` から派生する型が多数あります。 これまで、これらの型はすべて `string` 型に暗黙的に変換できました。 しかし、これらのクラスの `Object.Equals` 実装でバグが見つかり、バグを修正するためにこの暗黙的な変換を無効にする必要がありました。 `string` への明示的な変換は引き続き可能です。

#### <a name="example"></a>例
次のようなコードがあるものとします。

```csharp
var customTokenizerName = TokenizerName.Create("my_tokenizer"); 
var customTokenFilterName = TokenFilterName.Create("my_tokenfilter"); 
var customCharFilterName = CharFilterName.Create("my_charfilter"); 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        customTokenizerName,  
        new[] { customTokenFilterName },  
        new[] { customCharFilterName }), 
}; 
```

この場合、次のように変更することでビルド エラーを解決できます。

```csharp
const string CustomTokenizerName = "my_tokenizer"; 
const string CustomTokenFilterName = "my_tokenfilter"; 
const string CustomCharFilterName = "my_charfilter"; 
 
var index = new Index();
index.Analyzers = new Analyzer[] 
{ 
    new CustomAnalyzer( 
        "my_analyzer",  
        CustomTokenizerName,  
        new TokenFilterName[] { CustomTokenFilterName },  
        new CharFilterName[] { CustomCharFilterName })
}; 
```

### <a name="removed-obsolete-members"></a>古い形式のメンバーの削除

バージョン 2.0-preview で古い形式としてマークされており、その後バージョン 3 で削除されたメソッドまたはプロパティに関連するビルド エラーが発生する場合があります。 このようなエラーが発生した場合、その解決方法を次に示します。

- コンストラクター `ScoringParameter(string name, string value)` を使用していた場合は、代わりに `ScoringParameter(string name, IEnumerable<string> values)` を使用します。
- `ScoringParameter.Value` プロパティを使用していた場合は、代わりに `ScoringParameter.Values` プロパティまたは `ToString` メソッドを使用します。
- `SearchRequestOptions.RequestId` プロパティを使用していた場合は、代わりに `ClientRequestId` プロパティを使用します。

### <a name="removed-preview-features"></a>削除されたプレビュー機能

バージョン 2.0-preview からバージョン 3 にアップグレードする場合、BLOB インデクサーでの JSON および CSV 解析のサポートが削除されたことに注意してください。これらの機能はプレビュー段階であるためです。 具体的には、`IndexingParametersExtensions` クラスの次のメソッドが削除されました。

- `ParseJson`
- `ParseJsonArrays`
- `ParseDelimitedTextFiles`

アプリケーションにこれらの機能に対するハードの依存関係が存在する場合、Azure Search .NET SDK のバージョン 3 にアップグレードすることはできません。 バージョン 2.0-preview を引き続き使用できますが、 **実稼働アプリケーションでのプレビュー版 SDK の使用は推奨されない**ことに注意してください。 プレビュー機能は評価のみを目的としており、変更される場合があります。

## <a name="conclusion"></a>まとめ
Azure Search .NET SDK の使い方について詳しくは、[.NET の方法](search-howto-dotnet-sdk.md)に関する記事をご覧ください。

SDK についてのご意見をお待ちしております。 問題が発生した場合は、 [Azure Search の MSDN フォーラム](https://social.msdn.microsoft.com/Forums/azure/home?forum=azuresearch)でご質問ください。 バグを発見した場合は、 [Azure .NET SDK の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/issues)で問題を報告できます。 問題のタイトルの前に、必ず "[Azure Search]" を付けてください。

Azure Search をお使いいただきありがとうございます。
