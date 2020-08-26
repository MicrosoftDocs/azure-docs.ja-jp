---
title: 類似性とスコアリングの概要
titleSuffix: Azure Cognitive Search
description: 類似性とスコアリングの概念について、さらにスコアリングの結果をカスタマイズするために開発者ができることについて説明します。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 5b3df38e8feef2a7b9bbc090e11a669164010f32
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/14/2020
ms.locfileid: "88213201"
---
# <a name="similarity-and-scoring-in-azure-cognitive-search"></a>Azure Cognitive Search での類似性とスコアリング

スコアリングとは、フル テキスト検索クエリの検索結果に返されるすべての項目の検索スコアを計算することです。 スコアは、現在の検索操作のコンテキストにおける項目の関連性を示すインジケーターです。 スコアが高いほど、項目の関連性が高くなります。 検索結果内の項目は、項目ごとに計算された検索スコアに基づいて、順位の高い順に並べられます。 

既定では、上位 50 個が応答で返されます。しかし、 **$top** パラメーターを使用すれば、返される項目数を減らしたり増やしたりすることができ (1 回の応答で 1000 個まで)、 **$skip** を使用すれば、次の結果セットを取得することができます。

検索スコアは、データとクエリの統計プロパティに基づいて計算されます。 Azure Cognitive Search では、検索語句 ([searchMode](https://docs.microsoft.com/rest/api/searchservice/search-documents#searchmodeany--all-optional) に応じて一部またはすべて) に一致するドキュメントが検索され、検索語句のインスタンスを多く含むドキュメントが優先されます。 データ インデックス全体での語句の出現頻度は低いがドキュメント内ではよく使用されている場合、検索スコアはより高くなります。 関連性を計算するこのアプローチの基礎となる手法は、*TF-IDF* (単語の出現頻度 - 逆文書頻度) と呼ばれています。

検索スコアの値は、結果セット全体で繰り返すことができます。 同じ検索スコアを持つ項目が複数ヒットした場合、同じスコアを持つ項目の順序付けは定義されていないので安定しません。 クエリを再度実行すると、特に、複数のレプリカで無料のサービスまたは課金対象サービスを使用している場合は、項目の位置が変わる場合があります。 同一スコアの項目が 2 つ存在する場合、最初に表示される項目を特定することはできません。

繰り返しスコアの間の関係を解除したい場合は、 **$orderby** 句を追加することで、まずスコアで並べ替えを行い、次に別の並べ替え可能なフィールド (`$orderby=search.score() desc,Rating desc` など) で並べ替えを行うことができます。 詳細については、[$orderby](https://docs.microsoft.com/azure/search/search-query-odata-orderby) に関するページを参照してください。

> [!NOTE]
> `@search.score = 1.00` は、スコア付けまたは順位付けが行われていない結果セットを示します。 スコアは、すべての結果にわたって均一です。 スコア付けされていない結果が生じるのは、クエリ フォームがあいまい検索、ワイルドカードまたは正規表現のクエリ、または **$filter** 式である場合です。 

## <a name="scoring-profiles"></a>スコアリング プロファイル

さまざまなフィールドの順位付け方法をカスタマイズするには、カスタム "*スコアリング プロファイル*" を定義します。 スコアリング プロファイルでは、検索結果の項目の順位付けをより細かく制御できます。 たとえば、収益の可能性に基づいて項目をブーストしたり、より新しい項目を昇格させたり、場合によっては在庫期間が長すぎる項目をブーストしたりできます。 

スコアリング プロファイルは、インデックス定義の一部であり、重み付けされたフィールド、関数、およびパラメーターで構成されます。 定義方法の詳細については、「[スコアリング プロファイル](index-add-scoring-profiles.md)」を参照してください。

<a name="scoring-statistics"></a>

## <a name="scoring-statistics-and-sticky-sessions"></a>スコアリング統計とスティッキー セッション

スケーラビリティのために、Azure Cognitive Search ではシャーディング プロセスを介して各インデックスが水平方向に分散されます。つまり、インデックスの各部が物理的に分離されます。

既定では、ドキュメントのスコアは、"*シャード内*" のデータの統計プロパティに基づいて計算されます。 このアプローチは、一般に、データの大規模なコーパスでは問題にならず、すべてのシャードの情報に基づいてスコアを計算する必要がある場合よりもパフォーマンスが向上します。 ただし、このパフォーマンスの最適化を使用すると、2 つの非常に類似したドキュメント (またはまったく同一のドキュメント) は、それぞれが異なるシャードになる場合、関連性スコアが異なる可能性があります。

すべてのシャードの統計プロパティに基づいてスコアを計算する場合、これを行うには、*scoringStatistics=global* を[クエリ パラメーター](https://docs.microsoft.com/rest/api/searchservice/search-documents)として追加します (または[クエリ要求](https://docs.microsoft.com/rest/api/searchservice/search-documents)の本文パラメーターとして *"scoringStatistics": "global"* を追加します)。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?scoringStatistics=global&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
scoringStatistics を使用すると、同じレプリカのすべてのシャードで同じ結果が得られるようになります。 ただし、レプリカはインデックスの最新の変更で常に更新されるため、それぞれ若干異なる場合があります。 一部のシナリオでは、ユーザーが "クエリ セッション" 中により一貫した結果を得られるようにすることが必要な場合があります。 このようなシナリオでは、クエリの一部として `sessionId` を指定できます。 `sessionId` は、一意のユーザー セッションを参照するために作成する一意の文字列です。

```http
GET https://[service name].search.windows.net/indexes/[index name]/docs?sessionId=[string]&api-version=2020-06-30&search=[search term]
  Content-Type: application/json
  api-key: [admin or query key]  
```
同じ `sessionId` が使用されていれば、同じレプリカをターゲットにするためにベストエフォートの試行が行われるので、ユーザーに表示される結果の一貫性が向上します。 

> [!NOTE]
> 同じ `sessionId` 値を繰り返し再利用すると、レプリカ間での要求の負荷分散が妨げられ、検索サービスのパフォーマンスに悪影響を与える可能性があります。 sessionId として使用される値は、'_' 文字で始めることはできません。

## <a name="similarity-ranking-algorithms"></a>類似性ランク付けアルゴリズム

Azure Cognitive Search では、次の 2 種類の類似性ランク付けアルゴリズムがサポートされています: *ClassicSimilarity* アルゴリズムと、*Okapi BM25* アルゴリズム (現在プレビュー段階) の公式実装。 従来の類似性アルゴリズムは既定のアルゴリズムですが、7 月 15 日からは、その日以降に作成された新しいサービスでは新しい BM25 アルゴリズムが使用されます。 これは、新しいサービスで利用できる唯一のアルゴリズムです。

今のところ、使用したい類似性ランク付けアルゴリズムを指定できます。 詳細については、[ランク付けアルゴリズム](index-ranking-similarity.md)に関するページを参照してください。

次のビデオ セグメントは、Azure Cognitive Search で使用されるランク付けアルゴリズムの説明に早送りされます。 詳しい背景情報については、ビデオ全編をご覧ください。

> [!VIDEO https://www.youtube.com/embed/Y_X6USgvB1g?version=3&start=322&end=643]

<a name="featuresMode-param"></a>

## <a name="featuresmode-parameter-preview"></a>featuresMode パラメーター (プレビュー)

[ドキュメントの検索](https://docs.microsoft.com/rest/api/searchservice/preview-api/search-documents)の要求には、フィールド レベルでの関連性に関する追加の詳細情報を提供できる新しい [featuresMode](https://docs.microsoft.com/rest/api/searchservice/preview-api/search-documents#featuresmode) パラメーターがあります。 `@searchScore` はドキュメント全体に対して計算されますが (このクエリのコンテキストにおけるこのドキュメントの関連度)、featuresMode を使用すると、`@search.features` 構造体で表現された、個々のフィールドに関する情報を取得できます。 この構造体には、クエリで使用されるすべてのフィールド (クエリ内の **searchFields** を介した特定のフィールド、またはインデックス内で**検索可能**として属性が付けられているすべてのフィールド) が含まれます。 フィールドごとに、次の値が取得されます。

+ フィールド内で見つかった一意のトークン数
+ 類似性スコア。つまり、クエリ用語に対するフィールド内容の類似度のメジャー
+ 用語の頻度。つまり、フィールド内でクエリ用語が見つかった回数

"Description" および "title" フィールドを対象とするクエリの場合、`@search.features` を含む応答は次のようになります。

```json
"value": [
 {
    "@search.score": 5.1958685,
    "@search.features": {
        "description": {
            "uniqueTokenMatches": 1.0,
            "similarityScore": 0.29541412,
            "termFrequency" : 2
        },
        "title": {
            "uniqueTokenMatches": 3.0,
            "similarityScore": 1.75451557,
            "termFrequency" : 6
        }
```

[カスタムのスコアリング ソリューション](https://github.com/Azure-Samples/search-ranking-tutorial)でこれらのデータ ポイントを使用したり、この情報を使用して検索の関連性の問題をデバッグしたりできます。


## <a name="see-also"></a>関連項目

 [スコアリング プロファイル](index-add-scoring-profiles.md) [REST API リファレンス](https://docs.microsoft.com/rest/api/searchservice/)   
 [ドキュメント API の検索](https://docs.microsoft.com/rest/api/searchservice/search-documents)   
 [Azure Cognitive Search .NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/search?view=azure-dotnet)  
