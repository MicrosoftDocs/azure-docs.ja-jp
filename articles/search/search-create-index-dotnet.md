---
title: ".NET SDK を使用した Azure Search インデックスの作成 | Microsoft Docs"
description: "Azure Search .NET SDK を使用して、コードでインデックスを作成します。"
services: search
documentationcenter: 
author: brjohnstmsft
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 3a851647-fc7b-4fb6-8506-6aaa519e77cd
ms.service: search
ms.devlang: dotnet
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 01/13/2017
ms.author: brjohnst
translationtype: Human Translation
ms.sourcegitcommit: 1f06a7197cc1a6dcf7a39c91183a4317bef126bb
ms.openlocfilehash: 3a5131323f438109d94137cb4f577054ec13227f


---
# <a name="create-an-azure-search-index-using-the-net-sdk"></a>.NET SDK を使用した Azure Search インデックスの作成
> [!div class="op_single_selector"]
> * [概要](search-what-is-an-index.md)
> * [ポータル](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST ()](search-create-index-rest-api.md)
> 
> 

この記事では、[Azure Search .NET SDK](https://aka.ms/search-sdk) を使用して Azure Search の[インデックス](https://docs.microsoft.com/rest/api/searchservice/Create-Index)を作成するプロセスについて説明します。

このガイドに従ってインデックスを作成する前に、既に [Azure Search サービスを作成済み](search-create-service-portal.md)です。

この記事に記載されたすべてのサンプル コードは、C# で記述されていることにご注意ください。 [GitHub](http://aka.ms/search-dotnet-howto)に完全なソース コードがあります。

## <a name="identify-your-azure-search-services-admin-api-key"></a>Azure Search サービスの管理者 API キーの識別
Azure Search サービスのプロビジョニングが完了すると、あと少しで、.NET SDK を使用して、サービス エンドポイントに対して要求を発行できます。 まず、プロビジョニングした検索サービス用に生成された管理者 API キーの 1 つを取得する必要があります。 .NET SDK は、サービスに対する要求ごとに、この API キーを送信します。 有効なキーがあれば、要求を送信するアプリケーションとそれを処理するサービスの間で、要求ごとに信頼を確立できます。

1. サービスの API キーを探すには、[Azure Portal](https://portal.azure.com/) にサインインする必要があります。
2. Azure Search サービスのブレードに移動します。
3. "キー" アイコンをクリックします。

サービスで*管理者キー*と*クエリ キー*を使用できるようになります。

* プライマリおよびセカンダリ *管理者キー* は、サービスの管理のほか、インデックス、インデクサー、データ ソースの作成と削除など、すべての操作に対する完全な権限を付与するものです。 キーは 2 つあるため、プライマリ キーを再生成することにした場合もセカンダリ キーを使い続けることができます (その逆も可能です)。
* *クエリ キー* はインデックスとドキュメントに対する読み取り専用アクセスを付与するものであり、通常は、検索要求を発行するクライアント アプリケーションに配布されます。

インデックスを作成する目的では、プライマリ管理者キーとセカンダリ管理者キーのどちらかを使用できます。

<a name="CreateSearchServiceClient"></a>

## <a name="create-an-instance-of-the-searchserviceclient-class"></a>SearchServiceClient クラスのインスタンスの作成
Azure Search .NET SDK を使い始めるには、`SearchServiceClient` クラスのインスタンスを作成する必要があります。 このクラスにはいくつかのコンストラクターがあります。 目的のコンストラクターは、パラメーターとして検索サービスの名前と `SearchCredentials` オブジェクトを使用します。 `SearchCredentials` は API キーをラップします。

次のコードは、アプリケーションの構成ファイル (`app.config` または `web.config`) に格納されている検索サービスの名前と API キーの値を使用して新しい `SearchServiceClient` を作成します。

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

`SearchServiceClient` には `Indexes` プロパティが含まれています。 このプロパティは、Azure Search インデックスの作成、一覧表示、更新、または削除に必要なすべてのメソッドを提供します。

> [!NOTE]
> `SearchServiceClient` クラスは検索サービスへの接続を管理します。 開いている接続の数が多くなりすぎないよう、可能であれば、アプリケーションで `SearchServiceClient` の単一のインスタンスを共有する必要があります。 SearchServiceClient のメソッドはスレッド セーフなので、このような共有が可能です。
> 
> 

<a name="DefineIndex"></a>

## <a name="define-your-azure-search-index"></a>Azure Search インデックスの定義
`Indexes.Create` メソッドに対する 1 回の呼び出しでインデックスが作成されます。 このメソッドでは、Azure Search インデックスを定義する `Index` オブジェクトがパラメーターとして使用されます。 次のように、 `Index` オブジェクトを作成して初期化する必要があります。

1. `Index` オブジェクトの `Name` プロパティをインデックスの名前に設定します。
2. `Index` オブジェクトの `Fields` プロパティを `Field` オブジェクトの配列に設定します。 `Field` オブジェクトを作成する最も簡単な方法は、`FieldBuilder.BuildForType` メソッドを呼び出し、型パラメーターのモデル クラスを渡すことです。 モデル クラスには、インデックスのフィールドにマップされるプロパティがあります。 これにより、ドキュメントを検索インデックスからモデル クラスのインスタンスにバインドすることができます。

> [!NOTE]
> モデル クラスを使用する予定がない場合でも、`Field` オブジェクトを直接作成してインデックスを定義できます。 コンストラクターに対して、データ型 (または文字列フィールドのアナライザー) と共に、フィールドの名前を指定することができます。 `IsSearchable` や `IsFilterable` など、他のプロパティを設定することもできます。
>
>

各フィールドには[適切なプロパティ](https://docs.microsoft.com/rest/api/searchservice/Create-Index)を割り当てる必要があるため、インデックスを設計する際は、検索のユーザー エクスペリエンスとビジネス ニーズに留意することが重要です。 これらのプロパティでは、どのフィールドにどの検索機能 (フィルター、ファセット、全文検索の並べ替えなど) が適用されるかを制御します。 明示的に設定しないプロパティについては、明確に有効にしない限り、`Field` クラスの既定では、対応する検索機能が無効になります。

この例では、インデックスに "hotels" という名前を付け、モデル クラスを使用してフィールドを定義しました。 モデル クラスの各プロパティには、対応するインデックス フィールドの検索に関連した動作を決定する属性があります。 モデル クラスは次のように定義されています。

```csharp
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [Key]
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

    // ToString() method omitted for brevity...
}
```

各プロパティの属性は、アプリケーションでどのように使用されるかに応じて、慎重に選択しています。 たとえば、ホテルについて検索するユーザーは `description` フィールドでのキーワードの一致に関心がある可能性が高いため、`IsSearchable` 属性を `Description` プロパティに追加してそのフィールドの全文検索を有効にしています。

`Key` 属性を追加して、インデックスの `string` 型のフィールドを 1 つだけ *key* フィールドとして指定する必要があることにご注意ください (上記の例の `HotelId` を参照)。

上記のインデックス定義では、フランス語のテキストを格納することを目的としているため、`description_fr` フィールドに言語アナライザーを使用しています。 言語アナライザーの詳細については、[言語サポートのトピック](https://docs.microsoft.com/rest/api/searchservice/Language-support)と、対応する[ブログ記事](https://azure.microsoft.com/blog/language-support-in-azure-search/)を参照してください。

> [!NOTE]
> 既定では、モデル クラス内の各プロパティの名前が、インデックス内の対応するフィールドの名前として使用されます。 すべてのプロパティ名を Camel 形式のフィールド名にマップする場合は、クラスを `SerializePropertyNamesAsCamelCase` 属性でマークします。 別の名前にマップする場合は、上記の `DescriptionFr` プロパティのように `JsonProperty` 属性を使用できます。 `JsonProperty` 属性は `SerializePropertyNamesAsCamelCase` 属性よりも優先されます。
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

## <a name="create-the-index"></a>インデックスの作成
`Index` オブジェクトが初期化されたので、`SearchServiceClient` オブジェクトで `Indexes.Create` を呼び出すだけでインデックスを作成できます。

```csharp
serviceClient.Indexes.Create(definition);
```

要求が成功すると、メソッドは通常どおりに復帰します。 無効なパラメーターなど、要求に問題がある場合、メソッドは `CloudException` をスローします。

インデックスが不要になり、それを削除する場合は、`SearchServiceClient` で `Indexes.Delete` を呼び出すだけです。 たとえば、"hotels" を削除する方法を次に示します。

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> この記事のコード例では、わかりやすくするため、Azure Search .NET SDK の同期メソッドを使用します。 実際のアプリケーションでは、高い拡張性と応答性を維持するため、非同期メソッドを使用することをお勧めします。 たとえば、上記の例では、`Create` と `Delete` の代わりに、`CreateAsync` と `DeleteAsync` を使用できます。
> 
> 

## <a name="next-steps"></a>次のステップ
Azure Search インデックスを作成すると、データの検索を開始できるように [インデックスにコンテンツをアップロードする](search-what-is-data-import.md) 準備が完了します。




<!--HONumber=Feb17_HO3-->


