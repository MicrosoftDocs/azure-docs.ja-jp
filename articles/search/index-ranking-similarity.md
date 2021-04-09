---
title: 類似性アルゴリズムの構成
titleSuffix: Azure Cognitive Search
description: 以前の検索サービスで BM25 を有効にする方法と、インデックスのコンテンツに合わせて BM25 パラメーターを変更して調整する方法について説明します。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/12/2021
ms.openlocfilehash: 52b3523d3c092f1b9375f53038cc3b20d0ddedcc
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232836"
---
# <a name="configure-the-similarity-ranking-algorithm-in-azure-cognitive-search"></a>Azure Cognitive Search で類似性ランク付けアルゴリズムを構成する

Azure Cognitive Search では、2 つの類似性ランク付けアルゴリズムがサポートされています。

+ *クラシック類似性* アルゴリズム。2020 年 7 月 15 日まで、すべての検索サービスで使用されます。
+ *Okapi BM25* アルゴリズムの実装。7 月 15 日以降に作成されるすべての検索サービスで使用されます。

BM25 ランク付けは新しい既定値です。この方が、よりユーザーの期待に合致する検索ランキングが生成される傾向があるためです。 ドキュメント サイズなどの要素に基づいて結果をチューニングするための[パラメーター](#set-bm25-parameters)が伴います。 

2020 年 7 月 15 日以降に作成された新しいサービスでは、BM25 が自動的に使用され、これは唯一の類似性アルゴリズムです。 新しいサービスで類似性を ClassicSimilarity に設定しようとすると、サービスでそのアルゴリズムがサポートされていないため、エラー 400 が返されます。

2020 年 7 月 15 日より前に作成された古いサービスでは、クラシック類似性は既定のアルゴリズムのままになります。 以下で説明するように、古いサービスはインデックスごとに BM25 にアップグレードできます。 クラシックから BM25 に切り替えようとする場合、検索結果の順序にいくらかの違いが発生すると予想されます。

> [!NOTE]
> 選択したリージョンの標準サービスについて、現在プレビュー段階にあるセマンティック ランキングは、より関連性の高い結果を生成するための追加の手順です。 他のアルゴリズムとは異なり、既存の結果セットを反復処理するアドオン機能です。 詳細については、[セマンティック検索の概要](semantic-search-overview.md)と[セマンティック ランキング](semantic-ranking.md)に関するページを参照してください。

## <a name="enable-bm25-scoring-on-older-services"></a>以前のサービスで BM25 スコアリングを有効にする

2020 年 7 月 15 日より前に作成された検索サービスを実行している場合は、新しいインデックスで類似性プロパティを設定して BM25 を有効にできます。 プロパティは新しいインデックスでのみ公開されます。したがって、既存のインデックスで BM25 が必要な場合は、新しい類似性プロパティが "Microsoft.Azure.Search.BM25Similarity" に設定されたインデックスを[削除して再構築](search-howto-reindex.md)する必要があります。

類似性プロパティを持つインデックスが存在する場合は、BM25Similarity または ClassicSimilarity を切り替えることができます。 

次のリンクでは、Azure SDK の類似性プロパティについて説明しています。 

| クライアント ライブラリ | 類似性プロパティ |
|----------------|---------------------|
| .NET  | [SearchIndex.Similarity](/dotnet/api/azure.search.documents.indexes.models.searchindex.similarity) |
| Java | [SearchIndex.setSimilarity](/java/api/com.azure.search.documents.indexes.models.searchindex.setsimilarity) |
| JavaScript | [SearchIndex.Similarity](/javascript/api/@azure/search-documents/searchindex#similarity) |
| Python | [SearchIndex の類似性プロパティ](/python/api/azure-search-documents/azure.search.documents.indexes.models.searchindex) |

### <a name="rest-example"></a>REST の例

次の例に示すように、[REST API](/rest/api/searchservice/create-index) を使用することもできます。

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30
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

## <a name="set-bm25-parameters"></a>BM25 パラメーターの設定

BM25 類似性では、計算後の関連性スコアを制御するため、ユーザーがカスタマイズできるパラメーターが 2 つ加わります。 インデックスの作成時、またはインデックスの作成時に BM25 アルゴリズムが指定されている場合は、インデックスの更新として、BM25 パラメーターを設定できます。

| プロパティ | 種類 | 説明 |
|----------|------|-------------|
| k1 | number | 一致する各単語の出現頻度とドキュメントとクエリのペアの最終的な関連性スコアの間のスケーリング関数が制御されます。 値は通常 0.0 から 3.0 で、既定値は 1.2 です。 </br></br>値が 0.0 のときは "バイナリ モデル" となり、一致する 1 つの単語は、その単語がテキスト内に出現する回数に関係なく、一致するすべてのドキュメントに対して等しく寄与します。一方、k1 値がそれより大きいときは、ドキュメント内で同じ単語が見つかるのに合わせてスコアを継続的に増やすことができます。 </br></br>複数の単語が検索クエリに含まれるとき、場合によっては、k1 値を高くすることが重要です。 そのような場合、1 つの検索クエリ単語だけ複数回一致するドキュメントより、たくさんの異なる単語が一致するドキュメントを優先することが推奨されるかもしれません。 たとえば、"Apollo Spaceflight" という言葉が含まれるドキュメントのインデックスを問い合わせるとき、"Spaceflight" は出てこないが "Apollo" という単語が何度も出てくるギリシャ神話に関する記事のスコアを、"Apollo" と "Spaceflight" の両方がほんの数回だけ出てくる別の記事よりも下げることが推奨されるときがあります。 |
| b | number | ドキュメントの長さが関連性スコアに与える影響が制御されます。 値は 0 から 1 の間で、既定値は 0.75 です。 </br></br>値が 0.0 のとき、ドキュメントの長さはスコアに影響を与えません。一方、値が 1.0 のとき、単語の出現頻度が関連性スコアに与える影響はドキュメントの長さによって正規化されます。 </br></br>ドキュメントの長さで単語の出現頻度を正規化することは、長いドキュメントに罰則を科す場合に役立ちます。 ドキュメントが長いと (1 冊の小説全体など)、それよりずっと短いドキュメントに比べ、関係のない単語が多く含まれる可能性が高くなります。 |

### <a name="setting-k1-and-b-parameters"></a>k1 パラメーターと b パラメーターを設定する

B 値または k1 値を設定または変更するには、それらを BM25 類似性オブジェクトに追加します。 既存のインデックスでこれらの値を設定するか変更すると、インデックスが少なくとも数秒間オフラインになり、アクティブなインデックス作成やクエリ要求が失敗します。 そのため、更新要求の "allowIndexDowntime=true" パラメーターを設定する必要があります。

```http
PUT https://[search service name].search.windows.net/indexes/[index name]?api-version=2020-06-30&allowIndexDowntime=true
{
    "similarity": {
        "@odata.type": "#Microsoft.Azure.Search.BM25Similarity",
        "b" : 0.5,
        "k1" : 1.3
    }
}
```

## <a name="see-also"></a>関連項目  

+ [REST API リファレンス](/rest/api/searchservice/)
+ [スコアリング プロファイルをインデックスに追加する](index-add-scoring-profiles.md)
+ [インデックスの作成 API](/rest/api/searchservice/create-index)
+ [Azure Cognitive Search .NET SDK](/dotnet/api/overview/azure/search)