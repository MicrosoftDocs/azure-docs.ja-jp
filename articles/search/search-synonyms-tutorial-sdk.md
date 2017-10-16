---
title: "Azure Search でのシノニム プレビュー チュートリアル | Microsoft Docs"
description: "Azure Search のインデックスにシノニム プレビュー機能を追加します。"
services: search
manager: jhubbard
documentationcenter: 
author: HeidiSteen
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 03/31/2017
ms.author: heidist
ms.openlocfilehash: 014959ed471f796d2184f0f8ff10d15cdc8a2ec6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="synonym-preview-c-tutorial-for-azure-search"></a>Azure Search のシノニム (プレビュー) C# チュートリアル

シノニムは、意味的に入力用語と同等と見なされる用語を一致させることにより、クエリを拡張します。 たとえば、"car" を "automobile" または "vehicle" という用語が含まれているドキュメントと一致させたい場合があります。

Azure Search では、シノニムは同等の用語を関連付ける "*マッピング規則*" を通じて "*シノニム マップ*" で定義されています。 複数のシノニム マップを作成し、サービス全体の任意のインデックスで使用できるリソースとしてポストして、フィールド レベルでどのマップを使用するかを参照することができます。 クエリの際に、Azure Search はインデックスを検索するだけでなく、クエリで使用されているフィールドにシノニム マップが指定されていれば、そのマップも参照します。

> [!NOTE]
> シノニム機能は現時点でプレビュー段階であり、最新のプレビュー API および SDK バージョン (api-version=2016-09-01-Preview、SDK version 4.x-preview) だけでサポートされています。 現在、Azure Portal でのサポートはありません。 プレビュー API は SLA の対象ではなく、プレビュー機能は変更される場合があるため、実稼働アプリケーションでの使用はお勧めしません。

## <a name="prerequisites"></a>前提条件

チュートリアルには、次のような要件があります。

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Search サービス](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET ライブラリのプレビュー バージョン](https://aka.ms/search-sdk-preview)
* [.NET アプリケーションから Azure Search を使用する方法](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>概要

前と後のクエリで、シノニムの値を示します。 このチュートリアルでは、サンプル インデックスに対してクエリを実行して結果を返すサンプル アプリケーションを使用します。 サンプル アプリケーションは "hotels" という名前の小さいインデックスを作成し、2 つのドキュメントを設定します。 アプリケーションはインデックス内に出現しない語句を使用して検索クエリを実行し、シノニム機能を有効にしてから同じ検索を再度発行します。 次のコードは、全体的な流れを示しています。

```csharp
  static void Main(string[] args)
  {
      SearchServiceClient serviceClient = CreateSearchServiceClient();

      Console.WriteLine("{0}", "Cleaning up resources...\n");
      CleanupResources(serviceClient);

      Console.WriteLine("{0}", "Creating index...\n");
      CreateHotelsIndex(serviceClient);

      ISearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

      Console.WriteLine("{0}", "Uploading documents...\n");
      UploadDocuments(indexClient);

      ISearchIndexClient indexClientForQueries = CreateSearchIndexClient();

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Adding synonyms...\n");
      UploadSynonyms(serviceClient);
      EnableSynonymsInHotelsIndex(serviceClient);
      Thread.Sleep(10000); // Wait for the changes to propagate

      RunQueriesWithNonExistentTermsInIndex(indexClientForQueries);

      Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");

      Console.ReadKey();
  }
```
サンプル インデックスの作成と設定の手順は、「[.NET アプリケーションから Azure Search を使用する方法](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)」で説明されています。

## <a name="before-queries"></a>"前" のクエリ

`RunQueriesWithNonExistentTermsInIndex` では、"five star"、"internet"、および "economy AND hotel" で検索クエリを発行します。
```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = indexClient.Documents.Search<Hotel>("\"five star\"", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = indexClient.Documents.Search<Hotel>("internet", parameters);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = indexClient.Documents.Search<Hotel>("economy AND hotel", parameters);
WriteDocuments(results);
```
これらの用語は 2 つのインデックス付きドキュメントのどちらにも含まれていないため、最初の `RunQueriesWithNonExistentTermsInIndex` からは次の出力が返されます。
~~~
Search the entire index for the phrase "five star":

no document matched

Search the entire index for the term 'internet':

no document matched

Search the entire index for the terms 'economy' AND 'hotel':

no document matched
~~~

## <a name="enable-synonyms"></a>シノニムの有効化

シノニムの有効化は、2 段階のプロセスです。 まず、シノニム規則を定義してアップロードします。次に、それらを使用するフィールドを構成します。 プロセスの概要は、`UploadSynonyms` および `EnableSynonymsInHotelsIndex` で示されています。

1. シノニム マップを検索サービスに追加します。 `UploadSynonyms` で、シノニム マップ 'desc-synonymmap' に 4 つの規則を定義し、サービスにアップロードします。
```csharp
    var synonymMap = new SynonymMap()
    {
        Name = "desc-synonymmap",
        Format = "solr",
        Synonyms = "hotel, motel\n
                    internet,wifi\n
                    five star=>luxury\n
                    economy,inexpensive=>budget"
    };

    serviceClient.SynonymMaps.CreateOrUpdate(synonymMap);
```
シノニム マップは、オープン ソース標準の `solr` の形式に準拠する必要があります。 この形式については、「[Synonyms in Azure Search (Azure Search でのシノニム)](search-synonyms.md)」の「`Apache Solr synonym format` (Apache Solr のシノニムの形式)」のセクションで説明されています。

2. インデックス定義でシノニム マップを使用するように、検索可能フィールドを構成します。 `EnableSynonymsInHotelsIndex` の 2 つのフィールド `category` および `tags` で、`synonymMaps` プロパティを新たにアップロードしたシノニム マップの名前に設定して、シノニムを有効にします。
```csharp
  Index index = serviceClient.Indexes.Get("hotels");
  index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
  index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };

  serviceClient.Indexes.CreateOrUpdate(index);
```
シノニム マップを追加するときに、インデックスの再作成は必要ありません。 シノニム マップをサービスに追加し、任意のインデックス内の既存のフィールド定義を、新しいシノニム マップを使用するように修正できます。 新しい属性を追加しても、インデックスの可用性に影響はありません。 フィールドでシノニムを無効にする場合も同様です。 単に `synonymMaps` プロパティに空のリストを設定するだけです。
```csharp
  index.Fields.First(f => f.Name == "category").SynonymMaps = new List<string>();
```

## <a name="after-queries"></a>"後" のクエリ

シノニム マップがアップロードされ、インデックスがシノニム マップを使用するように更新された後、2 回目の `RunQueriesWithNonExistentTermsInIndex` 呼び出しは次の出力を返します。

~~~
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
~~~
最初のクエリは、規則 `five star=>luxury` からドキュメントを見つけます。 2 番目のクエリは `internet,wifi` を使用して検索を拡張し、3 番目のクエリは `hotel, motel` と `economy,inexpensive=>budget` の両方を使用してそれらと一致するドキュメントを見つけます。

シノニムを追加することで、検索エクスペリエンスがまったく違うものになります。 このチュートリアルで元のクエリは、インデックス内のドキュメントが関連性を持っているにもかかわらず、意味のある結果を返すことができませんでした。 シノニムを有効にすると、インデックス内の基になるデータは変更せずに、よく使用される用語が含まれるようにインデックスを拡張できます。

## <a name="sample-application-source-code"></a>サンプル アプリケーションのソース コード
このチュートリアルで使用したサンプル アプリケーションの完全なソース コードが [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) にあります。

## <a name="next-steps"></a>次のステップ

* [Azure Search でのシノニムの使用方法](search-synonyms.md)を調べる
* [シノニムの REST API ドキュメント](https://aka.ms/rgm6rq)を調べる
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) と [REST API](https://docs.microsoft.com/rest/api/searchservice/) のリファレンスを参照してください。
