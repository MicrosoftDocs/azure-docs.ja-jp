---
title: クイック スタート:C# コンソール アプリケーション内でインデックスを作成する - Azure Search
description: Azure Search .NET SDK を使用して C# で全文検索可能なインデックスを作成する方法について説明します。
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/08/2019
ms.openlocfilehash: 83842893e0ffc6bb954832cd65b6312b59bbcaa3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59269046"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>クイック スタート:1 - C# で Azure Search インデックスを作成する
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [ポータル](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
> * [Postman](search-fiddler.md)
>*

この記事では、C# と [.NET SDK](https://aka.ms/search-sdk) を使用して [Azure Search のインデックス](search-what-is-an-index.md)を作成するプロセスについて説明します。 これは、インデックスの作成、読み込み、照会に関する 3 部構成の演習の最初のレッスンです。 インデックスの作成は、次のタスクを実行することによって実現されます。

> [!div class="checklist"]
> * 検索サービスに接続するための [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) オブジェクトを作成する。
> * `Indexes.Create` にパラメーターとして渡す [`Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) オブジェクトを作成する。
> * `SearchServiceClient` で `Indexes.Create` メソッドを呼び出して、`Index` をサービスに送信する。

## <a name="prerequisites"></a>前提条件

このクイック スタートでは、次のサービス、ツール、およびデータを使用します。 

[Azure Search サービスを作成](search-create-service-portal.md)するか、現在のサブスクリプションから[既存のサービスを見つけます](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 このクイック スタート用には、無料のサービスを使用できます。

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/) (任意のエディション)。 サンプル コードと手順については、無料の Community エディションでテストされています。

Azure サンプル GitHub リポジトリにある [DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) には、サンプル ソリューション (C# で記述された .NET Core コンソール アプリケーション) が置かれています。 そのソリューションをダウンロードして展開します。 既定では、ソリューションは読み取り専用です。 ソリューションを右クリックし、読み取り専用属性をオフにして、ファイルを変更できるようにします。 このソリューションにはデータが含まれています。

## <a name="get-a-key-and-url"></a>キーと URL を入手する

サービスの呼び出しには、要求ごとに URL エンドポイントとアクセス キーが必要です。 両方を使用して検索サービスが作成されるので、Azure Search をサブスクリプションに追加した場合は、次の手順に従って必要な情報を入手してください。

1. [Azure portal にサインイン](https://portal.azure.com/)し、ご使用の検索サービスの **[概要]** ページで、URL を入手します。 たとえば、エンドポイントは `https://mydemo.search.windows.net` のようになります。

2. **[設定]** > **[キー]** で、サービスに対する完全な権限の管理者キーを取得します。 管理キーをロールオーバーする必要がある場合に備えて、2 つの交換可能な管理キーがビジネス継続性のために提供されています。 オブジェクトの追加、変更、および削除の要求には、主キーまたはセカンダリ キーのどちらかを使用できます。

![HTTP エンドポイントとアクセス キーを取得する](media/search-fiddler/get-url-key.png "HTTP エンドポイントとアクセス キーを取得する")

すべての要求では、サービスに送信されるすべての要求に API キーが必要です。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

## <a name="1---configure-and-build"></a>1 - 構成とビルド

1. Visual Studio で **DotNetHowTo.sln** ファイルを開きます。

1. appsettings.json 内で、既定のコンテンツを次の例に置き換えた後、サービスのサービス名と管理者 API キーを指定します。 


   ```json
   {
       "SearchServiceName": "Put your search service name here (not the full URL)",
       "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
    }
   ```

  サービス名には名前だけが必要です。 たとえば、URL が https://mydemo.search.windows.net の場合は、JSON ファイルに `mydemo` を追加します。

1. F5 キーを押して、ソリューションをビルドし、コンソール アプリを実行します。 この演習の残りの手順とそれに続く手順では、このコードがどのように動作するかを説明します。 

代わりに、SDK の動作の詳細については、「[.NET アプリケーションから Azure Search を使用する方法](search-howto-dotnet-sdk.md)」を参照してください。 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 - クライアントを作成する

Azure Search .NET SDK を使い始めるには、`SearchServiceClient` クラスのインスタンスを作成します。 このクラスにはいくつかのコンストラクターがあります。 目的のコンストラクターは、パラメーターとして検索サービスの名前と `SearchCredentials` オブジェクトを使用します。 `SearchCredentials` は API キーをラップします。

Program.cs ファイルには、次のコードが含まれています。 このコードでは、アプリケーションの構成ファイル (appsettings.json) に格納されている検索サービスの名前と API キーの値を使用して、新しい `SearchServiceClient` を作成します。

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

`SearchServiceClient` には `Indexes` プロパティが含まれています。 このプロパティは、Azure Search インデックスの作成、一覧表示、更新、または削除に必要なすべてのメソッドを提供します。

> [!NOTE]
> `SearchServiceClient` クラスは検索サービスへの接続を管理します。 開いている接続の数が多くなりすぎないよう、可能であれば、アプリケーションで `SearchServiceClient` の単一のインスタンスを共有する必要があります。 SearchServiceClient のメソッドはスレッド セーフなので、このような共有が可能です。
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 - インデックスを作成する
`Indexes.Create` メソッドに対する 1 回の呼び出しでインデックスが作成されます。 このメソッドは、Azure Search インデックスを定義する `Index` オブジェクトをパラメーターとして受け取ります。 次のように、`Index` オブジェクトを作成して初期化します。

1. `Index` オブジェクトの `Name` プロパティをインデックスの名前に設定します。

2. `Index` オブジェクトの `Fields` プロパティを `Field` オブジェクトの配列に設定します。 `Field` オブジェクトを作成する最も簡単な方法は、`FieldBuilder.BuildForType` メソッドを呼び出し、型パラメーターのモデル クラスを渡すことです。 モデル クラスには、インデックスのフィールドにマップされるプロパティがあります。 これにより、ドキュメントを検索インデックスからモデル クラスのインスタンスにバインドすることができます。

> [!NOTE]
> モデル クラスを使用する予定がない場合でも、`Field` オブジェクトを直接作成してインデックスを定義できます。 コンストラクターに対して、データ型 (または文字列フィールドのアナライザー) と共に、フィールドの名前を指定することができます。 `IsSearchable` や `IsFilterable` など、他のプロパティを設定することもできます。
>
>

インデックスを設計する際は、検索のユーザー エクスペリエンスとビジネス ニーズに留意することが重要です。 どのフィールドにどの検索機能 (フィルター、ファセット、並べ替えなど) が適用されるかを制御する[属性](https://docs.microsoft.com/rest/api/searchservice/Create-Index)を各フィールドに割り当てる必要があります。 明示的に設定しないプロパティについては、明確に有効にしない限り、`Field` クラスの既定では、対応する検索機能が無効になります。

この例では、インデックス名は "hotels" で、フィールドはモデル クラスを使用して定義されています。 モデル クラスの各プロパティには、対応するインデックス フィールドの検索に関連した動作を決定する属性があります。 モデル クラスは次のように定義されています。

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

各プロパティの属性は、アプリケーションでどのように使用されるかに応じて、慎重に選択しています。 たとえば、ホテルについて検索するユーザーは `description` フィールドでのキーワードの一致に関心がある可能性が高いため、`IsSearchable` 属性を `Description` プロパティに追加してそのフィールドの全文検索を有効にしています。

`Key` 属性を追加して、インデックスの `string` 型のフィールドを 1 つだけ *key* フィールドとして指定する必要があることにご注意ください (上記の例の `HotelId` を参照)。

上記のインデックス定義では、フランス語のテキストを格納することを目的としているため、`description_fr` フィールドに言語アナライザーを使用しています。 詳細については、「[Azure Search のインデックスに言語アナライザーを追加する](index-add-language-analyzers.md)」を参照してください。

> [!NOTE]
> 既定では、モデル クラス内の各プロパティの名前がインデックス内のフィールド名に対応します。 すべてのプロパティ名を Camel 形式のフィールド名にマップする場合は、クラスを `SerializePropertyNamesAsCamelCase` 属性でマークします。 別の名前にマップする場合は、上記の `DescriptionFr` プロパティのように `JsonProperty` 属性を使用できます。 `JsonProperty` 属性は `SerializePropertyNamesAsCamelCase` 属性よりも優先されます。
> 
> 

モデル クラスの定義が完了したので、インデックス定義を非常に簡単に作成できます。

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 - Indexes.Create を呼び出す
`Index` オブジェクトが初期化されたので、`SearchServiceClient` オブジェクトで `Indexes.Create` を呼び出してインデックスを作成します。

```csharp
serviceClient.Indexes.Create(definition);
```

要求が成功すると、メソッドは通常どおりに復帰します。 無効なパラメーターなど、要求に問題がある場合、メソッドは `CloudException` をスローします。

インデックスが不要になり、それを削除する場合は、`SearchServiceClient` で `Indexes.Delete` を呼び出します。 例: 

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> この記事のコード例では、わかりやすくするため、Azure Search .NET SDK の同期メソッドを使用します。 実際のアプリケーションでは、高い拡張性と応答性を維持するため、非同期メソッドを使用することをお勧めします。 たとえば、上記の例では、`Create` と `Delete` の代わりに、`CreateAsync` と `DeleteAsync` を使用できます。
> 
> 

## <a name="next-steps"></a>次の手順
このクイック スタートでは、フィールドのデータ型と動作を定義するスキーマに基づいて、空の Azure Search インデックスを作成しました。 このインデックスは、名前と属性フィールドで構成される "最小限" のインデックスです。 より現実的なインデックスには、[スコアリング プロファイル](index-add-scoring-profiles.md)、先行入力をサポートする [suggester](index-add-suggesters.md)、[シノニム](search-synonyms.md)、あるいは[カスタム アナライザー](index-add-custom-analyzers.md)などのその他の要素が含まれます。 基本的なワークフローを理解してから、これらの機能を再度参照することをお勧めします。

このシリーズの次のクイック スタートでは、検索可能なコンテンツを含むインデックスを読み込む方法について説明します。

> [!div class="nextstepaction"]
> [C# を使用して Azure Search インデックスにデータを読み込む](search-import-data-dotnet.md)