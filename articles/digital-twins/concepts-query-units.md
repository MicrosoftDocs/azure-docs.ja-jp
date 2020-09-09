---
title: Azure Digital Twins のクエリ ユニット
titleSuffix: Azure Digital Twins
description: Azure Digital Twins のクエリ ユニットの請求概念について
author: baanders
ms.author: baanders
ms.date: 8/14/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 5334a1196ac8044c56e615cf8894b44646b48fb4
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88615095"
---
# <a name="query-units-in-azure-digital-twins"></a>Azure Digital Twins のクエリ ユニット 

Azure Digital Twins の**クエリ ユニット (QU)** は、[クエリ API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query) を使用して [Azure Digital Twins クエリ](how-to-query-graph.md)を実行するために使われるオンデマンド計算の単位です。 

Azure Digital Twins でサポートされているクエリ操作を実行するために必要なシステム リソース (CPU、IOPS、メモリなど) を抽象化し、代わりにクエリ ユニットで使用状況を追跡できるようにします。

クエリを実行するために消費される QU の数は、クエリの複雑さの影響を受けます。 

この記事では、クエリ ユニットの概要と、クエリ ユニットの消費を追跡する方法について説明します。

## <a name="find-the-query-unit-consumption-in-azure-digital-twins"></a>Azure Digital Twins でのクエリ ユニット消費の確認 

Azure Digital Twins の[クエリ API](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query) を使用してクエリを実行するとき、応答ヘッダーを検証して、クエリで消費した QU の数を追跡できます。 Azure Digital Twins から返された応答内の "query-charge" を探します。 

Azure Digital Twins の [SDK](how-to-use-apis-sdks.md) を使用すると、ページング可能な応答から query-charge ヘッダーを抽出できます。 このセクションでは、デジタル ツインをクエリする方法と、ページング可能な応答を反復処理して query-charge ヘッダーを抽出する方法を示します。 

次のコード スニペットは、クエリ API を呼び出すと発生するクエリ料金を抽出する方法を示しています。 まず応答ページを反復処理して query-charge ヘッダーにアクセスした後、各ページ内のデジタル ツイン結果を反復処理します。 
 
```csharp
AsyncPageable<string> asyncPageableResponseWithCharge = client.QueryAsync("SELECT * FROM digitaltwins"); 
int pageNum = 0; 

// The "await" keyword here is required, as a call is made when fetching a new page. 

await foreach (Page<string> page in asyncPageableResponseWithCharge.AsPages()) 
{ 
    Console.WriteLine($"Page {++pageNum} results:"); 

    // Extract the query-charge header from the page 

    if (QueryChargeHelper.TryGetQueryCharge(page, out float queryCharge)) 
    { 
        Console.WriteLine($"Query charge was: {queryCharge}"); 
    } 

    // Iterate over the twin instances. 

    // The "await" keyword is not required here, as the paged response is local. 

    foreach (string response in page.Values) 
    { 
        BasicDigitalTwin twin = JsonSerializer.Deserialize<BasicDigitalTwin>(response); 
        Console.WriteLine($"Found digital twin '{twin.Id}'"); 
    } 
} 
```

## <a name="next-steps"></a>次のステップ

Azure Digital Twins のクエリの詳細については、次を参照してください。
* "[*概念: クエリ言語*](concepts-query-language.md)
* [*方法: ツイン グラフにクエリを実行する*](how-to-query-graph.md)
* [クエリ API リファレンス ドキュメント](https://docs.microsoft.com/rest/api/digital-twins/dataplane/query/querytwins)

Azure Digital Twins のクエリ関連の制限については、「[*リファレンス: パブリック プレビューのサービスの制限*](reference-service-limits.md)」を参照してください。