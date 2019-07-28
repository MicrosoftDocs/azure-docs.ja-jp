---
title: シノニム C# の例 - Azure Search
description: この C# の例では、Azure Search のインデックスにシノニム機能を追加する方法を学習します。 シノニム マップとは、同等の用語の一覧です。 シノニムをサポートするフィールドでは、ユーザー指定の用語とそれに関連するすべてのシノニムを含むように、クエリが拡張されます。
manager: cgronlun
author: heidisteen
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: 5b81e4b9a8773cc8e4cc76582ccf2df88565d3d8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "65025168"
---
# <a name="example-add-synonyms-for-azure-search-in-c"></a>例:C# で Azure Search にシノニムを追加する

シノニムは、意味的に入力用語と同等と見なされる用語を一致させることにより、クエリを拡張します。 たとえば、"car" を "automobile" または "vehicle" という用語が含まれているドキュメントと一致させたい場合があります。 

Azure Search では、シノニムは同等の用語を関連付ける "*マッピング規則*" を通じて "*シノニム マップ*" で定義されています。 この例では、既にインデックスがある状態で同意語を追加して使用する最も重要な手順について取り上げます。 学習内容は次のとおりです。

> [!div class="checklist"]
> * [SynonymMap](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.synonymmap?view=azure-dotnet) クラスを使用して、シノニム マップを作成する。 
> * シノニムを介してクエリ拡張をサポートする必要があるフィールドに対して [SynonymMaps](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.field.synonymmaps?view=azure-dotnet) プロパティを設定する。

シノニム対応フィールドに対するクエリは、通常と同じ方法で実行できます。 クエリ構文を追加しなくてもシノニムにアクセスできます。

複数のシノニム マップを作成し、サービス全体の任意のインデックスで使用できるリソースとしてポストして、フィールド レベルでどのマップを使用するかを参照することができます。 クエリの際に、Azure Search はインデックスを検索するだけでなく、クエリで使用されているフィールドにシノニム マップが指定されていれば、そのマップも参照します。

> [!NOTE]
> シノニムは、プログラムで作成できますが、ポータルでは作成できません。 Azure portal でのシノニムのサポートが役に立つ場合は、[UserVoice](https://feedback.azure.com/forums/263029-azure-search) でフィードバックをお送りください

## <a name="prerequisites"></a>前提条件

チュートリアルには、次のような要件があります。

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Search サービス](search-create-service-portal.md)
* [Microsoft.Azure.Search .NET ライブラリ](https://aka.ms/search-sdk)
* [.NET アプリケーションから Azure Search を使用する方法](https://docs.microsoft.com/azure/search/search-howto-dotnet-sdk)

## <a name="overview"></a>概要

前と後のクエリで、シノニムの値を示します。 この例では、サンプル インデックスに対してクエリを実行して結果を返すサンプル アプリケーションを使用します。 サンプル アプリケーションは "hotels" という名前の小さいインデックスを作成し、2 つのドキュメントを設定します。 アプリケーションはインデックス内に出現しない語句を使用して検索クエリを実行し、シノニム機能を有効にしてから同じ検索を再度発行します。 次のコードは、全体的な流れを示しています。

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

シノニムを追加することで、検索エクスペリエンスがまったく違うものになります。 この例で元のクエリは、インデックス内のドキュメントが関連性を持っているにもかかわらず、意味のある結果を返すことができませんでした。 シノニムを有効にすると、インデックス内の基になるデータは変更せずに、よく使用される用語が含まれるようにインデックスを拡張できます。

## <a name="sample-application-source-code"></a>サンプル アプリケーションのソース コード
このチュートリアルで使用したサンプル アプリケーションの完全なソース コードが [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) にあります。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

例の後で最も速くクリーンアップする方法は、Azure Search サービスが含まれているリソース グループを削除することです。 リソース グループを削除することで、そのすべての内容を完全に削除することができます。 ポータルでは、リソース グループ名は Azure Search サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次の手順

この例では、マッピング規則を作成、ポストしてからクエリでシノニム マップを呼び出す C# コードのシノニム機能を示します。 さらに詳しい情報については、[.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search) と [REST API](https://docs.microsoft.com/rest/api/searchservice/) のリファレンス ドキュメントをご覧ください。

> [!div class="nextstepaction"]
> [Azure Search で同意語を使用する方法](search-synonyms.md)
