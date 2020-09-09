---
title: ランク付けの類似性アルゴリズム
titleSuffix: Azure Cognitive Search
description: 類似性アルゴリズムを設定し、ランク付けの新しい類似性アルゴリズムを試す方法
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/13/2020
ms.openlocfilehash: ee788125a5710e5a8b9861c28c4af8e6d83d5d80
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88924539"
---
# <a name="ranking-algorithm-in-azure-cognitive-search"></a>Azure Cognitive Search のランク付けアルゴリズム

> [!IMPORTANT]
> 2020 年 7 月 15 日より、新しく作成された検索サービスでは BM25 ランク付け関数が自動的に使用されます。この関数はほとんどの場合、現行の既定のランク付けよりもユーザーの予測に沿った検索ランク付けを与えることが証明されています。 ランク付けで優れている以外に、BM25 では、ドキュメントのサイズなどの要素に基づいて結果を調整する構成オプションを使用できます。  
>
> 今回の変更では、おそらく、検索結果の順序にわずかな変化が見られるでしょう。 この変更の影響をテストする必要がある場合は、api-version 2019-05-06-Preview および 2020-06-30 で BM25 アルゴリズムを使用できます。  

この記事では、Preview API を使用して作成およびクエリされた新しいインデックスに対して、既存の検索サービスで新しい BM25 ランク付けアルゴリズムを使用する方法について説明します。

Azure Cognitive Search では、Okapi BM25 アルゴリズムの公式 Lucene 実装、*BM25Similarity* の採用過程にあります。これは、以前に使用されていた *ClassicSimilarity* 実装に取って代わります。 以前の ClassicSimilarity アルゴリズムと同じく、BM25Similarity は TF-IDF タイプの取得関数です。この関数では、単語の出現頻度 (TF) と逆文書頻度 (IDF) が変数として使用され、ドキュメントとクエリの組みごとに関連スコアが計算されます。ドキュメントとクエリの組みはその後、ランク付けに使用されます。 

概念的には以前の ClassicSimilarity アルゴリズムと同じですが、BM25 の根底は確率的情報取得にあり、それを基に改良されています。 BM25 には高度なカスタマイズ オプションもあります。たとえば、ユーザーは、一致した単語の出現頻度で関連性スコアが変動するしくみを決定できます。

## <a name="how-to-test-bm25-today"></a>今すぐ BM25 をテストする方法

新しいインデックスを作成するとき、**similarity** プロパティを設定してアルゴリズムを指定できます。 次に示すように `api-version=2019-05-06-Preview` を使用するか、`api-version=2020-06-30` を使用することができます。

```
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2019-05-06-Preview
```

```json  
{
    "name": "indexName",
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": true
        },
        {
            "name": "name",
            "type": "Edm.String",
            "searchable": true,
            "analyzer": "en.lucene"
        },
        ...
    ],
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity"
    }
}
```

**similarity** プロパティは、両方のアルゴリズムを既存のサービスでのみ使用できる、この中間期間中に便利です。 

| プロパティ | 説明 |
|----------|-------------|
| similarity | 省略可能。 有効な値には、 *"#Microsoft.Azure.Search.ClassicSimilarity"* や *"#Microsoft.Azure.Search.BM25Similarity"* などがあります。 <br/> 2020 年 7 月 15 日より前に作成された検索サービスでは、`api-version=2019-05-06-Preview` 以降が必要です。 |

2020 年 7 月 15 日以降に作成された新しいサービスでは、BM25 が自動的に使用され、これは唯一の類似性アルゴリズムです。 新しいサービスで **similarity** を `ClassicSimilarity` に設定しようとすると、新しいサービスではそのアルゴリズムがサポートされていないため、エラー 400 が返されます。

2020 年 7 月 15 日より前に作成された既存のサービスでは、ClassicSimilarity は既定のアルゴリズムのままです。 **similarity** プロパティを省略するか、null に設定した場合、インデックスでは Classic アルゴリズムが使用されます。 新しいアルゴリズムを使用する場合は、前述のように **similarity** を設定する必要があります。

## <a name="bm25-similarity-parameters"></a>BM25 類似性パラメーター

BM25 類似性では、計算後の関連性スコアを制御するため、ユーザーがカスタマイズできるパラメーターが 2 つ加わります。

### <a name="k1"></a>k1

*k1* パラメーターによって、一致する各単語の出現頻度とドキュメントとクエリのペアの最終的な関連性スコアの間のスケーリング関数が制御されます。

値が 0 のときは "バイナリ モデル" となり、一致する 1 つの単語は、その単語がテキスト内に出現する回数に関係なく、一致するすべてのドキュメントに対して等しく寄与します。一方、k1 値がそれより大きいときは、ドキュメント内で同じ単語が見つかるのに合わせてスコアを継続的に増やすことができます。 Azure Cognitive Search の既定では、k1 パラメーターの値に 1.2 が使用されます。 複数の単語が検索クエリに含まれるとき、場合によっては、k1 値を高くすることが重要です。 そのような場合、1 つの検索クエリ単語だけ複数回一致するドキュメントより、たくさんの異なる単語が一致するドキュメントを優先することが推奨されるかもしれません。 たとえば、"Apollo Spaceflight" という言葉が含まれるドキュメントのインデックスを問い合わせるとき、"Spaceflight" は出てこないが "Apollo" という単語が何度も出てくるギリシャ神話に関する記事のスコアを、"Apollo" と "Spaceflight" の両方がほんの数回だけ出てくる別の記事よりも下げることが推奨されるときがあります。 
 
### <a name="b"></a>b

*b* パラメーターでは、ドキュメントの長さが関連性スコアに与える影響が制御されます。

値が 0.0 のとき、ドキュメントの長さはスコアに影響を与えません。一方、値が 1.0 のとき、単語の出現頻度が関連性スコアに与える影響はドキュメントの長さによって正規化されます。 Azure Cognitive Search で b パラメーターに使用される既定値は 0.75 です。 ドキュメントの長さで単語の出現頻度を正規化することは、長いドキュメントに罰則を科す場合に役立ちます。 ドキュメントが長いと (1 冊の小説全体など)、それよりずっと短いドキュメントに比べ、関係のない単語が多く含まれる可能性が高くなります。

### <a name="setting-k1-and-b-parameters"></a>k1 パラメーターと b パラメーターを設定する

b または k1 値をカスタマイズするには、BM25 の使用時、類似性オブジェクトにプロパティとして値を追加します。

```json
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
```

類似性アルゴリズムは、インデックスの作成時にのみ設定できます。 つまり、既存のインデックスに使用されている類似性アルゴリズムは変更できません。 *"b"* パラメーターと *"k1"* パラメーターは、BM25 を使用する既存のインデックス定義を更新するときに変更できます。 既存のインデックスでこれらの値を変更すると、インデックスが少なくとも数秒間オフラインになり、インデックス作成やクエリ要求が失敗します。 そのため、更新要求のクエリ文字列に "allowIndexDowntime=true" パラメーターを設定する必要があります。

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=[api-version]&allowIndexDowntime=true
```

## <a name="see-also"></a>関連項目  

+ [REST API リファレンス](/rest/api/searchservice/)   
+ [スコアリング プロファイルをインデックスに追加する](index-add-scoring-profiles.md)    
+ [インデックスの作成 API](/rest/api/searchservice/create-index)   
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search?view=azure-dotnet)