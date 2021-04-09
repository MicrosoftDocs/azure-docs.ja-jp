---
title: シノニム C# の例
titleSuffix: Azure Cognitive Search
description: この C# の例では、Azure Cognitive Search のインデックスにシノニム機能を追加する方法を学習します。 シノニム マップとは、同等の用語の一覧です。 シノニムをサポートするフィールドでは、ユーザー指定の用語とそれに関連するすべてのシノニムを含むように、クエリが拡張されます。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/18/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 50d5d73e71b8129f061ec49b363a0ebb13d22bdf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704658"
---
# <a name="example-add-synonyms-for-azure-cognitive-search-in-c"></a>例:C# で Azure Cognitive Search にシノニムを追加する

シノニムは、意味的に入力用語と同等と見なされる用語を一致させることにより、クエリを拡張します。 たとえば、"car" を "automobile" または "vehicle" という用語が含まれているドキュメントと一致させたい場合があります。 

Azure Cognitive Search では、シノニムは同等の用語を関連付ける "*マッピング規則*" を通じて "*シノニム マップ*" で定義されています。 この例では、既にインデックスがある状態で同意語を追加して使用する最も重要な手順について取り上げます。

この例では、次のことについて説明します。

> [!div class="checklist"]
> * [SynonymMap クラス](/dotnet/api/azure.search.documents.indexes.models.synonymmap)を使用して、シノニム マップを作成する。 
> * シノニムを介してクエリ拡張をサポートする必要があるフィールドに対して [SynonymMapsName プロパティ](/dotnet/api/azure.search.documents.indexes.models.searchfield.synonymmapnames)を設定する。

シノニム対応フィールドに対するクエリは、通常と同じ方法で実行できます。 クエリ構文を追加しなくてもシノニムにアクセスできます。

複数のシノニム マップを作成し、サービス全体の任意のインデックスで使用できるリソースとしてポストして、フィールド レベルでどのマップを使用するかを参照することができます。 クエリの際に、Azure Cognitive Search はインデックスを検索するだけでなく、クエリで使用されているフィールドにシノニム マップが指定されていれば、そのマップも参照します。

> [!NOTE]
> シノニムは、プログラムで作成できますが、ポータルでは作成できません。

## <a name="prerequisites"></a>前提条件

チュートリアルには、次のような要件があります。

* [Visual Studio](https://www.visualstudio.com/downloads/)
* [Azure Cognitive Search サービス](search-create-service-portal.md)
* [Azure.Search.Documents パッケージ](https://www.nuget.org/packages/Azure.Search.Documents/)

.NET クライアント ライブラリに慣れていない場合は、[.NET での Azure Cognitive Search の使用方法](search-howto-dotnet-sdk.md)に関するページを参照してください 。

## <a name="sample-code"></a>サンプル コード

この例で使用したサンプル アプリケーションの完全なソース コードは [GitHub](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) にあります。

## <a name="overview"></a>概要

前と後のクエリは、シノニムの値を示すために使用されます。 この例では、サンプル アプリケーションによってクエリが実行され、2 つのドキュメントが設定されたサンプルの "hotels" インデックスに対して結果が返されます。 まず、アプリケーションから、インデックス内に出現しない語句を使用した検索クエリが実行されます。 次に、コードによってシノニム機能が有効になり、同じクエリが再発行されます。今回は、シノニム マップの一致に基づいて結果が返されます。 

次のコードは、全体的な流れを示しています。

```csharp
static void Main(string[] args)
{
   SearchIndexClient indexClient = CreateSearchIndexClient();

   Console.WriteLine("Cleaning up resources...\n");
   CleanupResources(indexClient);

   Console.WriteLine("Creating index...\n");
   CreateHotelsIndex(indexClient);

   SearchClient searchClient = indexClient.GetSearchClient("hotels");

   Console.WriteLine("Uploading documents...\n");
   UploadDocuments(searchClient);

   SearchClient searchClientForQueries = CreateSearchClientForQueries();

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Adding synonyms...\n");
   UploadSynonyms(indexClient);

   Console.WriteLine("Enabling synonyms in the test index...\n");
   EnableSynonymsInHotelsIndexSafely(indexClient);
   Thread.Sleep(10000); // Wait for the changes to propagate

   RunQueriesWithNonExistentTermsInIndex(searchClientForQueries);

   Console.WriteLine("Complete.  Press any key to end application...\n");

   Console.ReadKey();
}
```

## <a name="before-queries"></a>"前" のクエリ

`RunQueriesWithNonExistentTermsInIndex` では、"five star"、"internet"、および "economy AND hotel" で検索クエリを発行します。

"five star" などの語句クエリは引用符で囲む必要があります。また、クライアントによってはエスケープ文字が必要になる場合もあります。

```csharp
Console.WriteLine("Search the entire index for the phrase \"five star\":\n");
results = searchClient.Search<Hotel>("\"five star\"", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the term 'internet':\n");
results = searchClient.Search<Hotel>("internet", searchOptions);
WriteDocuments(results);

Console.WriteLine("Search the entire index for the terms 'economy' AND 'hotel':\n");
results = searchClient.Search<Hotel>("economy AND hotel", searchOptions);
WriteDocuments(results);
```

これらの用語は 2 つのインデックス付きドキュメントのどちらにも含まれていないため、最初の `RunQueriesWithNonExistentTermsInIndex` からは **no document matched**(ドキュメントがマッチしませんでした) という出力が返されます。

## <a name="enable-synonyms"></a>シノニムの有効化

"前" のクエリが実行されると、サンプル コードによってシノニムが有効になります。 シノニムの有効化は、2 段階のプロセスです。 最初に、シノニム規則を定義してアップロードします。 次に、それらを使用するようにフィールドを構成します。 プロセスの概要は、`UploadSynonyms` および `EnableSynonymsInHotelsIndex` で示されています。

1. シノニム マップを検索サービスに追加します。 `UploadSynonyms` で、シノニム マップ 'desc-synonymmap' に 4 つの規則を定義し、サービスにアップロードします。

   ```csharp
   private static void UploadSynonyms(SearchIndexClient indexClient)
   {
      var synonymMap = new SynonymMap("desc-synonymmap", "hotel, motel\ninternet,wifi\nfive star=>luxury\neconomy,inexpensive=>budget");

      indexClient.CreateOrUpdateSynonymMap(synonymMap);
   }
   ```

1. インデックス定義でシノニム マップを使用するように、検索可能フィールドを構成します。 `AddSynonymMapsToFields` の 2 つのフィールド `category` および `tags` で、`SynonymMapNames` プロパティを新たにアップロードしたシノニム マップの名前に設定して、シノニムを有効にします。

   ```csharp
   private static SearchIndex AddSynonymMapsToFields(SearchIndex index)
   {
      index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
      index.Fields.First(f => f.Name == "tags").SynonymMapNames.Add("desc-synonymmap");
      return index;
   }
   ```

   シノニム マップを追加するときに、インデックスの再作成は必要ありません。 シノニム マップをサービスに追加し、任意のインデックス内の既存のフィールド定義を、新しいシノニム マップを使用するように修正できます。 新しい属性を追加しても、インデックスの可用性に影響はありません。 フィールドでシノニムを無効にする場合も同様です。 単に `SynonymMapNames` プロパティに空のリストを設定するだけです。

   ```csharp
   index.Fields.First(f => f.Name == "category").SynonymMapNames.Add("desc-synonymmap");
   ```

## <a name="after-queries"></a>"後" のクエリ

シノニム マップがアップロードされ、インデックスがシノニム マップを使用するように更新された後、2 回目の `RunQueriesWithNonExistentTermsInIndex` 呼び出しは次の出力を返します。

```dos
Search the entire index for the phrase "five star":

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the term 'internet':

Name: Fancy Stay        Category: Luxury        Tags: [pool, view, wifi, concierge]

Search the entire index for the terms 'economy' AND 'hotel':

Name: Roach Motel       Category: Budget        Tags: [motel, budget]
```

最初のクエリは、規則 `five star=>luxury` からドキュメントを見つけます。 2 番目のクエリは `internet,wifi` を使用して検索を拡張し、3 番目のクエリは `hotel, motel` と `economy,inexpensive=>budget` の両方を使用してそれらと一致するドキュメントを見つけます。

シノニムを追加することで、検索エクスペリエンスがまったく違うものになります。 この例で元のクエリは、インデックス内のドキュメントが関連性を持っているにもかかわらず、意味のある結果を返すことができませんでした。 シノニムを有効にすると、インデックス内の基になるデータは変更せずに、よく使用される用語が含まれるようにインデックスを拡張できます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

例の後で最も速くクリーンアップする方法は、Azure Cognitive Search サービスが含まれているリソース グループを削除することです。 リソース グループを削除することで、そのすべての内容を完全に削除することができます。 ポータルでは、リソース グループ名は Azure Cognitive Search サービスの [概要] ページに表示されます。

## <a name="next-steps"></a>次のステップ

この例では、マッピング規則を作成、ポストしてからクエリでシノニム マップを呼び出す C# コードのシノニム機能を示します。 さらに詳しい情報については、[.NET SDK](/dotnet/api/overview/azure/search.documents-readme) と [REST API](/rest/api/searchservice/) のリファレンス ドキュメントをご覧ください。

> [!div class="nextstepaction"]
> [Azure Cognitive Search でシノニムを使用する方法](search-synonyms.md)