---
title: .NET SDK バージョン10 にアップグレードする
titleSuffix: Azure Cognitive Search
description: 以前のバージョンから Azure Cognitive Search .NET SDK バージョン 10 にコードを移行します。 新機能と必要なコード変更について説明します。
manager: nitinme
author: arv100kri
ms.author: arjagann
ms.service: cognitive-search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: devx-track-csharp
ms.openlocfilehash: bfe24ff38446fa0d0ccea96799e6f42b561713bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89002812"
---
# <a name="upgrade-to-azure-cognitive-search-net-sdk-version-10"></a>Azure Cognitive Search .NET SDK バージョン 10 へのアップグレード

バージョン 9.0 以前の [.NET SDK](/dotnet/api/overview/azure/search) を使用している場合、アプリケーションをアップグレードしてバージョン 10 を使用するには、この記事が役に立ちます。

バージョン10では Azure Search の名前が Azure Cognitive Search に変更されますが、名前空間とパッケージ名は変更されません。 以前のバージョンの SDK (9.0 以前) では、変更前の名前が引き続き使用されます。 例を含む SDK の使用方法の詳細については、「[.NET アプリケーションから Azure Cognitive Search を使用する方法](search-howto-dotnet-sdk.md)」を参照してください。

バージョン 10 では、いくつかの機能とバグ修正が追加され、REST API バージョン `2019-05-06` と同じ機能レベルになっています。 変更によって既存のコードが機能しなくなる場合は、[問題を解決するために必要な手順](#UpgradeSteps)を説明します。

> [!NOTE]
> 8\.0-preview 以前のバージョンを使用している場合は、まずバージョン 9 にアップグレードしてから、バージョン 10 にアップグレードする必要があります。 手順については、[Azure Search .NET SDK バージョン 9 へのアップグレード](search-dotnet-sdk-migration-version-9.md)に関するページを参照してください。
>
> 検索サービスのインスタンスは、最新のバージョンを含む複数の REST API バージョンをサポートします。 バージョンが最新ではなくなった場合でも、そのバージョンを引き続き使用できますが、最新バージョンを使用するようにコードを移行することをお勧めします。 REST API を使用している場合は、api-version パラメーターを使用して、すべての要求に API バージョンを指定する必要があります。 .NET SDK を使用している場合は、使用している SDK のバージョンによって REST API の対応するバージョンが決まります。 サービスが新しいバージョンの API をサポートするようにアップグレードされた場合でも、使用中の古い SDK のコードを変更なしで引き続き実行できます。

<a name="WhatsNew"></a>

## <a name="whats-new-in-version-10"></a>バージョン 10 の新機能
Azure Cognitive Search .NET SDK のバージョン 10 は、これらの更新が含まれている REST API `2019-05-06` を対象としています。

* 2 つの新しいスキルの導入 - [条件付きスキル](cognitive-search-skill-conditional.md)と[テキスト翻訳スキル](cognitive-search-skill-text-translation.md)。
* 入れ子になったコンテキストからの統合に対応するために、[Shaper スキル](cognitive-search-skill-shaper.md)の入力が再構築されました。 詳細については、こちらの [JSON 定義の例](./cognitive-search-skill-shaper.md#scenario-3-input-consolidation-from-nested-contexts)を参照してください。
* 2 つの新しい[フィールド マッピング関数](search-indexer-field-mappings.md)の追加:
    - [urlEncode](./search-indexer-field-mappings.md#urlencode-function)
    - [urlDecode](./search-indexer-field-mappings.md#urldecode-function)
* 場合によっては、[インデクサー実行の状態](/rest/api/searchservice/get-indexer-status)に表示されるエラーや警告に、デバッグに役立つ追加情報が含まれていることがあります。 `IndexerExecutionResult` は、この動作を反映するように更新されました。
* [スキルセット](cognitive-search-defining-skillset.md)内で定義された個々のスキルは、`name` プロパティを指定することで、必要に応じて識別できます。
* `ServiceLimits` は[複合型](./search-howto-complex-data-types.md)の制限を示し、`IndexerExecutionInfo` は関連するインデクサーの制限またはクォータを示します。

<a name="UpgradeSteps"></a>

## <a name="steps-to-upgrade"></a>アップグレードの手順

1. NuGet パッケージ マネージャー コンソールを使用して、または Visual Studio でプロジェクト参照を右クリックして [NuGet パッケージの管理] を選択することで、`Microsoft.Azure.Search` の NuGet 参照を更新します。

2. NuGet が新しいパッケージとその依存関係をダウンロードした後、プロジェクトをリビルドします。 

3. ビルドが失敗した場合、各ビルド エラーを修正する必要があります。 可能性のある各ビルド エラーを解決する方法については、「[バージョン 10 における破壊的変更](#ListOfChanges)」を参照してください。

4. ビルドのエラーまたは警告をすべて修正した後、必要に応じて新しい機能を利用するようにアプリケーションを変更できます。 SDK の新機能の詳細については、「[バージョン 10 の新機能](#WhatsNew)」をご覧ください。

<a name="ListOfChanges"></a>

## <a name="breaking-changes-in-version-10"></a>バージョン 10 における破壊的変更

バージョン 10 には、アプリケーションのリビルドだけでなく、コードの変更が必要な可能性のある破壊的変更がいくつか含まれています。

> [!NOTE]
> 以下の変更の一覧はすべてを網羅しているわけではありません。 変更によっては、ビルド エラーが発生しない可能性がありますが、以前のバージョンの Azure Cognitive Search .NET SDK アセンブリに依存するアセンブリとのバイナリの互換性が失われるために技術的に重大になります。 推奨事項と共に、このカテゴリに分類される重要な変更も一覧表示されます。 バージョン 10 にアップグレードするときに、バイナリ互換性の問題を回避するために、アプリケーションを再構築してください。

### <a name="custom-web-api-skill-definition"></a>カスタム Web API スキルの定義

バージョン 9 以前では、[カスタム Web API スキル](cognitive-search-custom-skill-web-api.md)の定義が正しく指定されていませんでした。 

`WebApiSkill` のモデルでは、辞書を "_含む_" オブジェクト プロパティとして `HttpHeaders` を指定していました。 この方法で `WebApiSkill` を含むスキルセットを作成すると、REST API で要求の形式が正しくないと見なされるため、例外が発生します。 この問題は、REST API からの有効な要求と見なされる **最上位の辞書プロパティ** として `WebApiSkill` モデル自体に `HttpHeaders` を作成することで修正されました。

たとえば、事前に `WebApiSkill` を次のようにインスタンス化しようとした場合などです。

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new WebApiHttpHeaders()
    {
        Headers = new Dictionary<string, string>()
        {
            ["header"] = "value"
        }
    }
};

```

REST API からの検証エラーを回避するために、次のように変更します。

```csharp

var webApiSkill = new WebApiSkill(
            inputs, 
            outputs,
            uri: "https://contoso.example.org")
{
    HttpHeaders = new Dictionary<string, string>()
    {
        ["header"] = "value"
    }
};

```

## <a name="shaper-skill-allows-nested-context-consolidation"></a>入れ子になったコンテキストの統合を可能にする Shaper スキル

Shaper スキルにより、入れ子になったコンテキストからの入力を統合できるようになりました。 この変更を有効にするために、`Source` プロパティだけ、または `SourceContext` と `Inputs` プロパティの両方を指定してインスタンス化できるように `InputFieldMappingEntry` を変更しました。

ほとんどの場合、コードを変更する必要はありませんが、これら 2 つの組み合わせのうち、可能なのは 1 つのみであることに注意してください。 そのため、次のようになります。

- `Source` のみが初期化されている状態で `InputFieldMappingEntry` を作成するのは有効です。
- `SourceContext` と `Inputs` のみが初期化されている状態で `InputFieldMappingEntry` を作成するのは有効です。
- これら 3 つのプロパティを使用したその他の組み合わせはすべて無効です。

この新機能の使用を開始する場合は、その変更を展開する前に、すべてのクライアントがバージョン 10 を使用するように更新されていることをまず確認してください。 そうしないと、Shaper スキルに対するクライアント (古いバージョンの SDK を使用) の更新が原因で検証エラーが発生する可能性があります。

> [!NOTE]
> 基になる `InputFieldMappingEntry` モデルが、入れ子になったコンテキストからの統合が可能になるように変更されている場合でも、その使用は Shaper スキルの定義内でのみ有効です。 この機能を他のスキルで使用すると、コンパイル時は有効でも、実行時に検証エラーが発生します。

## <a name="skills-can-be-identified-by-a-name"></a>名前によるスキルの識別

スキルセット内の各スキルに新しいプロパティ `Name` が追加されました。これはコード内で初期化することができ、スキルを識別するのに役立ちます。 これは省略可能です。指定されていない場合は (明示的なコード変更が行われていない場合はこれが既定です)、スキルセット内のスキルの 1 から始まるインデックスを使用し、先頭に "#" 文字が付いた、既定の名前が割り当てられます。 例として、次のスキルセット定義 (簡略化のため、ほとんどの初期化をスキップしています) をご覧ください。

```csharp
var skillset = new Skillset()
{
    Skills = new List<Skill>()
    {
        new SentimentSkill(),
        new WebApiSkill(),
        new ShaperSkill(),
        ...
    }
}
```

`SentimentSkill` には `#1`、`WebApiSkill` には `#2`、`ShaperSkill` には `#3` というように名前が割り当てられています。

カスタム名でスキルを識別する場合は、最初に、お使いのクライアントの全インスタンスで SDK をバージョン 10 に更新してください。 そうしないと、古いバージョンの SDK を使用しているクライアントがスキルの `Name` プロパティを `null` にしてしまい、既定の名前付けスキームでクライアントのフォールバックが発生する可能性があります。

## <a name="details-about-errors-and-warnings"></a>エラーと警告の詳細

インデクサーの実行中に発生したエラーと警告の詳細を (それぞれ) カプセル化する `ItemError` および `ItemWarning` モデルは、インデクサーのデバッグに役立つよう、3 つの新しいプロパティを含むように変更されました。 それらのプロパティは以下のとおりです。

- `Name`:エラーが発生したソースの名前。 たとえば、添付されたスキルセット内の特定のスキルを参照できます。
- `Details`:エラーまたは警告に関する追加の詳細情報。
- `DocumentationLink`:特定のエラーまたは警告に関するトラブルシューティング ガイドへのリンク。

> [!NOTE]
> これらの便利な詳細情報が可能な限り含まれるよう、エラーと警告の構成に着手しました。 これらの詳細がすべてのエラーと警告で必ず表示されるように作業を進めています。しかし、この作業は進行中であるため、これらの追加情報が設定されていない場合もあります。

## <a name="next-steps"></a>次のステップ

- Shaper スキルに対する変更によって影響を受ける可能性が最も高いのは、新規または既存のコードです。 次の手順として、入力構造を示すこの例を見直してください。[Shaper スキルの JSON 定義の例](cognitive-search-skill-shaper.md)
- [AI エンリッチメントの概要](cognitive-search-concept-intro.md)に関するページを参照してください。
- SDK についてのご意見をお待ちしております。 問題が発生した場合は、[Stack Overflow](https://stackoverflow.com/questions/tagged/azure-search) でご質問ください。 バグを発見した場合は、 [Azure .NET SDK の GitHub リポジトリ](https://github.com/Azure/azure-sdk-for-net/issues)で問題を報告できます。 ご自身の問題のタイトルの前には、必ず "[Azure Cognitive Search]" を付けてください。