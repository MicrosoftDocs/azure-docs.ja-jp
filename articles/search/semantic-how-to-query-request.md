---
title: セマンティック クエリを作成する
titleSuffix: Azure Cognitive Search
description: ディープ ラーニング モデルをクエリ処理にアタッチし、検索順位と関連性の一部として意図とコンテキストを推測するために、セマンティック クエリ型を設定します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/18/2021
ms.openlocfilehash: c33739124092a17acf0590f00b2f9c3c09bf894e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104654664"
---
# <a name="create-a-query-for-semantic-captions-in-cognitive-search"></a>Cognitive Search でセマンティック キャプションに対するクエリを作成する

> [!IMPORTANT]
> セマンティック検索はパブリック プレビュー段階にあり、プレビューの REST API と Azure portal を介して利用できます。 プレビュー機能は、[補足利用規約](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に基づいて、現状のまま提供されます。 これらの機能は課金対象です。 詳細については、[可用性と価格](semantic-search-overview.md#availability-and-pricing)に関するページを参照してください。

この記事では、セマンティック ランク付けを使用して、関連する用語と語句を強調したセマンティック キャプション (および必要に応じて[セマンティック回答](semantic-answers.md)) を返す検索要求を作成する方法について説明します。 "セマンティック" クエリの種類を使用して作成されたクエリでは、キャプションと回答の両方が返されます。

キャプションと回答は、検索ドキュメント内のテキストから逐語的に抽出されます。 セマンティック サブシステムでは、コンテンツのどの部分がキャプションまたは回答の特性を持つかは特定されますが、新しい文や語句は作成されません。 このため、セマンティック検索には、説明または定義を含むコンテンツが最も適しています。

## <a name="prerequisites"></a>前提条件

+ 米国中北部、米国西部、米国西部 2、米国東部 2、北ヨーロッパ、西ヨーロッパのいずれかのリージョンにある、Standard レベル (S1、S2、S3) の検索サービス。 これらのリージョンのいずれかに既存の S1 以上のサービスがある場合は、新しいサービスを作成しなくてもアクセスを要求できます。

+ セマンティック検索プレビューへのアクセス: [サインアップ](https://aka.ms/SemanticSearchPreviewSignup)

+ 英語のコンテンツを含む既存の検索インデックス

+ クエリを送信するための検索クライアント

  検索クライアントは、クエリ要求でプレビューの REST API をサポートする必要があります。 [Postman](search-get-started-rest.md)、[Visual Studio Code](search-get-started-vs-code.md)、またはプレビューの API への REST 呼び出しを行うコードを使用できます。 Azure portal で [Search エクスプローラー](search-explorer.md)を使用してセマンティック クエリを送信することもできます。

+ [クエリ要求](/rest/api/searchservice/preview-api/search-documents)には、この記事で説明するセマンティック オプションやその他のパラメーターを含める必要があります。

## <a name="whats-a-semantic-query"></a>セマンティック クエリとは

Cognitive Search では、クエリは、クエリ処理と応答の形を決定するパラメーター化された要求です。 "*セマンティック クエリ*" では、一致する結果のコンテキストと意味を評価し、関連性の高い一致を最上位に昇格させ、セマンティック回答とキャプションを返すことができるセマンティック再ランク付けモデルを呼び出すパラメーターが追加されます。

次の要求は、最小限のセマンティック クエリ (回答なし) を表しています。

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=2020-06-30-Preview      
{    
    "search": " Where was Alan Turing born?",    
    "queryType": "semantic",  
    "searchFields": "title,url,body",  
    "queryLanguage": "en-us"  
}
```

Cognitive Search のすべてのクエリと同様に、要求は、単一インデックスのドキュメント コレクションを対象とします。 さらに、セマンティック クエリによって実行される分析、スキャン、およびスコアリングのシーケンスは、非セマンティック クエリと同じです。 

違いは、関連性とスコアリングにあります。 このプレビュー リリースで定義されているように、セマンティック クエリとは、その "*結果*" がセマンティック言語モデルを使用して再ランク付けされるものであり、既定の類似性ランク付けアルゴリズムによって割り当てられるスコアではなく、セマンティック ランカーによって最も関連性があると見なされる一致を明らかにする方法がとられます。

最初の結果の上位 50 件の一致のみを意味的にランク付けすることができます。すべての結果には応答にキャプションが含まれます。 必要に応じて、要求に **`answer`** パラメーターを指定して、可能性のある回答を抽出できます。 詳細については、[セマンティック回答](semantic-answers.md)に関するページを参照してください。

## <a name="query-with-search-explorer"></a>検索エクスプローラーを使用したクエリ実行

[検索エクスプローラー](search-explorer.md)は更新され、セマンティック クエリ用のオプションが含められました。 これらのオプションは、次の手順を完了した後にポータルに表示されます。

1. [サインアップ](https://aka.ms/SemanticSearchPreviewSignup)して、お使いの検索サービスがプレビュー プログラムに参加する許可を得ます

1. 構文 `https://portal.azure.com/?feature.semanticSearch=true` を使用してポータルを開きます

クエリ オプションには、セマンティック クエリ、searchFields、およびスペル修正を有効にするスイッチが含まれています。 必要なクエリ パラメーターをクエリ文字列に貼り付けることもできます。

:::image type="content" source="./media/semantic-search-overview/search-explorer-semantic-query-options.png" alt-text="検索エクスプローラー内のクエリ オプション" border="true":::

## <a name="query-using-rest"></a>REST を使用してクエリを実行する

[ドキュメントの検索 (REST プレビュー)](/rest/api/searchservice/preview-api/search-documents) を使用して、プログラムで要求を作成します。

応答には、キャプションと強調表示が自動的に含められます。 スペル修正または回答を応答に含める場合は、省略可能なパラメーターである **`speller`** または **`answers`** を要求に追加します。

次の例では、hotels-sample-index を使用して、セマンティック回答およびキャプションを含むセマンティック クエリ要求を作成します。

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30-Preview      
{
    "search": "newer hotel near the water with a great restaurant",
    "queryType": "semantic",
    "queryLanguage": "en-us",
    "searchFields": "HotelName,Category,Description",
    "speller": "lexicon",
    "answers": "extractive|count-3",
    "highlightPreTag": "<strong>",
    "highlightPostTag": "</strong>",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

次の表は、セマンティック クエリで使用されるクエリ パラメーターを全体的に参照できるようにまとめたものです。 すべてのパラメーターの一覧については、「[Search Documents (REST プレビュー)](/rest/api/searchservice/preview-api/search-documents)」を参照してください

| パラメーター | Type | 説明 |
|-----------|-------|-------------|
| queryType | String | 有効な値は、simple、full、semantic です。 セマンティック クエリには、"semantic" の値が必要です。 |
| queryLanguage | String | セマンティック クエリに必要です。 現在、"en-us" のみが実装されています。 |
| searchFields | String | 検索可能なフィールドのコンマ区切りの一覧。 セマンティック ランク付けを行うフィールドを指定します。ここから、キャプションと回答が抽出されます。 </br></br>単純および完全なクエリの種類とは対照的に、フィールドの順番によって優先順位が決まります。 使用方法の詳細については、「[手順 2: searchFields を設定する](#searchfields)」を参照してください。 |
| スペル チェック | String | 検索エンジンに到達する前にスペルミスを修正する省略可能なパラメーターであり、セマンティック クエリに固有のものではない。 詳細については、[クエリへのスペル修正の追加](speller-how-to-add.md)に関するページを参照してください。 |
| answers |String | セマンティック回答を結果に含めるかどうかを指定する省略可能なパラメーター。 現在、"extractive" のみが実装されています。 回答は、最大 5 つを返すように構成できます。 既定値は 1 です。 この例は、回答の数が 3 であることを示しています: "extractive\|count3"。 詳細については、[セマンティック回答を返す](semantic-answers.md)に関するページを参照してください。|

### <a name="formulate-the-request"></a>要求を作成する

このセクションでは、セマンティック検索に必要なクエリ パラメーターについて、順を追って説明します。

#### <a name="step-1-set-querytype-and-querylanguage"></a>Step 1: queryType と queryLanguage を設定する

REST に次のパラメーターを追加します。 どちらのパラメーターも必須です。

```json
"queryType": "semantic",
"queryLanguage": "en-us",
```

queryLanguage は、インデックス スキーマのフィールド定義に割り当てられている[言語アナライザー](index-add-language-analyzers.md)と一致している必要があります。 queryLanguage が "en-us" の場合、言語アナライザーも英語のバリアント ("en.microsoft" または "en.lucene") である必要があります。 keyword や simple など、言語に依存しないアナライザーは、queryLanguage 値と競合しません。

クエリ要求で[スペル修正](speller-how-to-add.md)も使用している場合、設定した queryLanguage は、speller、answers、および captions に等しく適用されます。 個々のパーツに対するオーバーライドはありません。 

検索インデックスのコンテンツは複数の言語で構成できますが、通常、クエリ入力は 1 つの言語で行われます。 検索エンジンでは、queryLanguage、言語アナライザー、およびコンテンツが構成されている言語の互換性が確認されません。そのため、誤った結果が生成されないように、適切にクエリのスコープを設定してください。

<a name="searchfields"></a>

#### <a name="step-2-set-searchfields"></a>手順 2: searchFields を設定する

searchFields パラメーターは、クエリに対する "セマンティックの類似性" について評価すべき一節を識別するために使用されます。 プレビューでは、処理すべき最も重要なフィールドに関するヒントがモデルに必要なため、searchFields を空白のままにすることはお勧めしません。

searchFields の順序は重要です。 単純および完全な Lucene クエリの既存のコードで既に searchFields を使用している場合は、セマンティック クエリの種類に切り替えるときにこのパラメーターを見直してフィールドの順序を確認してください。

2 つ以上の searchFields の場合:

+ コレクションには、文字列フィールドと最上位の文字列フィールドのみを含めます。 文字列以外のフィールドまたは下位のフィールドをコレクションに含めると、エラーは発生しませんが、セマンティック ランク付けでそれらのフィールドは使用されません。

+ 最初のフィールドは、常に簡潔 (タイトルや名前など) にする必要があります。25 単語未満であると理想的です。

+ インデックスにテキスト (`www.domain.com/?id=23463&param=eis` のようにマシンにフォーカスされている形式ではなく、`www.domain.com/name-of-the-document-and-other-details` のように人間が読める形式) の URL フィールドがある場合は、これをリストの 2 番目に配置します (または、簡潔なタイトル フィールドがない場合は先頭)。

+ これらのフィールドの後に、セマンティック クエリの回答が見つかる可能性がある説明フィールドを配置します (ドキュメントの主な内容など)。

フィールドが 1 つしか指定されていない場合は、ドキュメントの主な内容など、セマンティック クエリに対する回答が見つかる可能性のある説明フィールドを使用します。 

#### <a name="step-3-remove-orderby-clauses"></a>手順 3: orderBy 句を削除する

既存の要求に orderBy 句が存在する場合はすべて削除します。 結果の順序付けにはセマンティック スコアが使用されます。明示的な並べ替えロジックを含めると、HTTP 400 エラーが返されます。

#### <a name="step-4-add-answers"></a>手順 4: 回答を追加する

回答を提供する追加の処理を含める場合は、必要に応じて "answers" を追加します。 回答 (およびキャプション) は、searchFields にリストされているフィールド内の節から抽出されます。 応答で最適な回答を得るために、十分な内容を持つフィールドを searchFields に含めてください。 詳細については、[セマンティック回答を戻す方法](semantic-answers.md)に関するページを参照してください。

#### <a name="step-5-add-other-parameters"></a>手順 5: その他のパラメーターを追加する

要求で必要なその他のパラメーターを設定します。 [speller](speller-how-to-add.md)、[select](search-query-odata-select.md)、count などのパラメーターを使用すると、要求の品質と応答の読みやすさが向上します。

必要に応じて、キャプションに適用される強調表示のスタイルをカスタマイズできます。 キャプションでは、ドキュメント内の重要な一説 (応答を要約する部分) に強調の書式設定が適用されます。 既定では、 `<em>`です。 書式設定の種類 (黄色の背景など) を指定する場合は、highlightPreTag と highlightPostTag を設定できます。

## <a name="evaluate-the-response"></a>応答を評価する

すべてのクエリと同様に、応答は、取得可能としてマークされているすべてのフィールド、または select パラメーターに指定されているフィールドのみで構成されます。 これには、元の関連性スコアが含まれます。また、要求の作成方法に応じて、カウントまたはバッチ結果が含まれる場合もあります。

セマンティック クエリでは、次の追加の要素が応答に含められます: 意味的にランク付けされた新しい関連性スコア、プレーン テキスト内の強調表示されたキャプション、および必要に応じて回答。

クライアント アプリでは、特定のフィールドの内容全体ではなく、キャプションを一致の説明として含めるように、検索ページを構成することができます。 これは、検索結果ページにおいて個々のフィールドの密度が高すぎる場合に役立ちます。

上記のクエリ例に対する応答では、最上位の選択として次の一致が返されます。 キャプションについては、プレーンテキスト バージョンと強調表示されたバージョンが自動的に返されます。 この例では回答が省略されています。この特定のクエリとコーパスに対してそれを特定できなかったためです。

```json
"@odata.count": 35,
"@search.answers": [],
"value": [
    {
        "@search.score": 1.8810667,
        "@search.rerankerScore": 1.1446577133610845,
        "@search.captions": [
            {
                "text": "Oceanside Resort. Luxury. New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
                "highlights": "<strong>Oceanside Resort.</strong> Luxury. New Luxury Hotel. Be the first to stay.<strong> Bay</strong> views from every room, location near the pier, rooftop pool, waterfront dining & more."
            }
        ],
        "HotelName": "Oceanside Resort",
        "Description": "New Luxury Hotel. Be the first to stay. Bay views from every room, location near the pier, rooftop pool, waterfront dining & more.",
        "Category": "Luxury"
    },
```

## <a name="next-steps"></a>次のステップ

セマンティック ランク付けと応答は最初の結果セットを基に構築されていることを思い出してください。 最初の結果の品質を向上させるロジックは、セマンティック検索に引き継がれます。 次の手順として、最初の結果に寄与する機能を確認します。これには、文字列のトークン化の方法に影響を与えるアナライザー、結果を調整できるスコアリング プロファイル、および既定の関連性アルゴリズムが含まれます。

+ [テキスト処理のためのアナライザー](search-analyzers.md)
+ [類似性ランク付けアルゴリズム](index-similarity-and-scoring.md)
+ [スコアリング プロファイル](index-add-scoring-profiles.md)
+ [セマンティック検索の概要](semantic-search-overview.md)
+ [セマンティック ランク付けアルゴリズム](semantic-ranking.md)
