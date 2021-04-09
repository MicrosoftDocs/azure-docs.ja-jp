---
title: あいまい検索
titleSuffix: Azure Cognitive Search
description: 「候補」検索を実装して、スペルミスや入力ミスを自動修正します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b2f2c8497d5365104a5ffc361b791450925d0c19
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101694786"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>あいまい検索によるスペルミスと入力ミスの修正

Azure Cognitive Search では、あいまい検索をサポートしています。これは、入力された文字列内の誤字やスペルミスを補正するクエリの一種です。 類似した構成の用語をスキャンして、これを実行します。 ほぼ一致をカバーするように検索機能を拡張すると、いくつかの文字の場所が違う場合に、入力ミスが自動修正されます。 

## <a name="what-is-fuzzy-search"></a>あいまい検索とは何でしょう?

類似した構成の用語と一致させる拡張検索です。 あいまい検索を指定すると、エンジンは、クエリ内のすべての用語について、同様に構成された用語のグラフを ([決定論的有限自動理論](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)に基づいて) 作成します。 たとえば、クエリに「university of washington」という 3 つの用語が含まれている場合、クエリ `search=university~ of~ washington~` 内のすべての用語に対してグラフが作成されます (あいまい検索では単語が削除されないため、「of」のグラフも作成されます)。

グラフは、各用語の最大 50 の展開 (または順列) で構成され、プロセス内の正しいバリアントと不適切なバリアントの両方をキャプチャします。 その後、エンジンは、応答で最も関連性の高い一致を返します。 

「university」のような用語の場合、グラフには「unversty, universty, university, universe, inverse」のように表示される可能性があります。 グラフ内のものと一致するドキュメントすべてが結果に含まれます。 テキストを分析して同じ語の異なる形式 (「mice」と「mouse」など) を処理する他のクエリとは対照的に、あいまいクエリの比較では、テキストに対する言語分析を行わず、額面通りに分析されます。 「Universe」や「inverse」は意味が異なりますが、構文上の不一致が小さいため、一致になります。

不一致が 2 つ以下の編集に制限されている場合、つまり、編集が挿入、削除、置換、または転置文字であれば、一致と見なされます。 差異を指定する文字列修正アルゴリズムは、[ダメラウ・レーベンシュタイン距離](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance)メトリックです。これは、1 つの単語を別の単語に変更するために必要な「操作の最小数」(挿入、削除、置換、または 2 つの隣接する文字の転置) として説明されています。 

Azure Cognitive Search:

+ あいまいクエリは完全な用語に適用されますが、AND 構築を使用して語句をサポートできます。 たとえば、"Unviersty~ of~ "Wshington~" は "University of Washington" と一致します。

+ 編集の既定の距離は 2 です。 `~0` の値は、拡張がないことを意味します (完全な用語のみが一致と見なされます)。ただし、1 つの相違点、または 1 つの編集に対して `~1` を指定することもできます。 

+ あいまいクエリでは、さらに 50 の順列まで 1 つの用語を拡張できます。 この制限は構成できませんが、編集距離を 1 に減らすと、展開の数を効果的に減らせます。

+ 応答は、関連する一致 (最大 50) を含むドキュメントで構成されます。

これらのグラフはまとめて、インデックス内のトークンに対する一致条件として送信されます。 ご想像のとおり、あいまい検索は、本質的に他のクエリ形式よりも遅くなります。 インデックスのサイズと複雑さによって、応答の待機時間のオフセットに十分な利点があるかどうかを判断できます。

> [!NOTE]
> あいまい検索は低速になる傾向があるため、n グラム インデックス作成などの代替手段を調査し、短い文字シーケンスを処理することができます (bigram と trigram トークンの場合は 2 つおよび 3 つの文字シーケンス)。 言語およびクエリ サーフェスによっては、n グラムを使用するとパフォーマンスが向上します。 トレードオフは、n グラムのインデックス作成が非常に多くのストレージを消費し、より大きなインデックスを生成することです。
>
> もう 1 つの方法として、最もひどいケースのみを処理する場合に[シノニム マップ](search-synonyms.md)を使用することもできます。 たとえば、「search」を「serach、serach、sarch」、または「retrieve」を「retreive」にマッピングします。

## <a name="indexing-for-fuzzy-search"></a>あいまい検索のインデックス作成

拡張グラフを作成するクエリ処理中、アナライザーは使用されませんが、あいまい検索のシナリオではアナライザーを無視すべきというわけではありません。 結局のところ、インデックスの作成中はアナライザーを使用して、一致が行われるトークンを作成するのです。クエリが自由形式、フィルター検索、または入力としてグラフを使用したあいまい検索のいずれかは関係ありません。 

通常、フィールドごとにアナライザーを割り当てる場合、分析チェーンを微調整するかどうかは、あいまい検索のように特殊なクエリ形式ではなく、主なユース ケース (フィルターまたはフルテキスト検索) に基づいて決定されます。 このため、あいまい検索には特定のアナライザーの推奨事項はありません。 

ただし、テスト クエリで期待どおりの一致が生成されない場合は、インデックス作成アナライザーを変更し、[言語アナライザー](index-add-language-analyzers.md)に設定して、より良い結果が得られるかどうか確認できます。 いくつかの言語 (特に母音変更を使用する言語) では、Microsoft の自然言語プロセッサによって生成される語形変化と不規則な単語の形式を利用できます。 場合によっては、適切な言語アナライザーを使用すると、ユーザーが指定した値と互換性のある方法で用語がトークン化されているかどうか区別できます。

## <a name="how-to-use-fuzzy-search"></a>あいまい検索の使用方法

あいまいクエリは、完全な Lucene クエリ構文を使用して作成され、[Lucene クエリ パーサー](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html)を呼び出します。

1. クエリで完全な Lucene パーサーを設定します (`queryType=full`)。

1. 必要に応じて、このパラメーター (`searchFields=<field1,field2>`) を使用し、特定のフィールドに要求のスコープを指定します。 

1. 完全な用語 (`search=<string>~`) の末尾にチルダ (`~`) 演算子を追加します。

   編集距離 (`~1`) を指定する場合は、省略可能なパラメーター (既定で 0 から 2 の間の数字) を含めます。 たとえば、"blue~" または "blue~1" を指定すると、"blue"、"blues"、および "glue" が返されます。

Azure Cognitive Search では、用語と距離 (最大 2) 以外に、クエリで設定する追加パラメーターはありません。

> [!NOTE]
> クエリの処理中、あいまいクエリでは[字句解析](search-lucene-query-architecture.md#stage-2-lexical-analysis)は行われません。 クエリ入力は、クエリ ツリーに直接追加され、展開して用語のグラフを作成します。 実行される変換は、小文字の区別だけです。

## <a name="testing-fuzzy-search"></a>あいまい検索のテスト

単純なテストでは、クエリ式を反復処理するために [Search エクスプローラー](search-explorer.md)または [Postman](search-get-started-rest.md) を使用することをお勧めします。 どちらのツールも対話型なので、ひとつの用語の複数のバリエーションをすばやくステップ実行し、返された応答を評価できます。

結果があいまいな場合は、[検索語句を強調表示](search-pagination-page-layout.md#hit-highlighting)すると応答内の一致を識別しやすくなります。 

> [!Note]
> あいまい一致を識別するための検索語句の強調表示には使用制限があり、基本的なあいまい検索でのみ機能します。 インデックスにスコアリング プロファイルがある場合や、追加の構文でクエリをレイヤー化する場合は、検索語句の強調表示が失敗して一致を識別できないことがあります。 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>例 1: 正確な用語を使用したあいまい検索

検索ドキュメントの `"Description"` フィールドに次の文字列が存在するとします: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

「special」のあいまい検索から始め、検索語句の強調表示を [説明] フィールドに追加します。

```console
search=special~&highlight=Description
```

応答では、検索語句の強調表示を追加したので、書式設定が一致用語として「special」に適用されます。

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

複数の文字 (「pe」) を取り除き「special」のスペルを間違えて、要求を再び試してみてください。

```console
search=scial~&highlight=Description
```

これまでのところ、応答に変更はありません。 既定の 2 度の距離を使用して、「pe」の 2 字を「special」から削除しても、その用語での一致が正常に行われます。

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

もうひとつ要求を試し、合計 3 字を削除して検索語句をさらに変更します (「special」から「scal」に変更)。

```console
search=scal~&highlight=Description
```

同じ応答が返されますが、「special」ではなく、あいまい一致は「SQL」になります。

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

この展開例では、検索語句の強調表示があいまいな結果になる可能性があることを示しています。 どのような場合でも、同じドキュメントが返されます。 一致を確認するためにドキュメント ID を使用しましたが、「special」から「SQL」への変更を把握できなかった可能性があります。

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search のフルテキスト検索のしくみ (クエリ解析アーキテクチャ)](search-lucene-query-architecture.md)
+ [Search エクスプローラー](search-explorer.md)
+ [.NET におけるクエリの実行方法](./search-get-started-dotnet.md)
+ [REST におけるクエリの実行方法](./search-get-started-powershell.md)