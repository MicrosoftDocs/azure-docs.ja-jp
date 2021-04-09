---
title: スキルセットの概念とワークフロー
titleSuffix: Azure Cognitive Search
description: スキルセットは、Azure Cognitive Search で AI エンリッチメント パイプラインを作成する場所です。 スキルセットの作成に関する重要な概念と詳細について説明します。
manager: nitinme
author: vkurpad
ms.author: vikurpad
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/15/2020
ms.openlocfilehash: b5a893ee1923ba4b2bec53b20fb164337bd65902
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96558115"
---
# <a name="skillset-concepts-in-azure-cognitive-search"></a>Azure Cognitive Search のスキルセットの概念

この記事は、スキルセットの概念と構成について理解を深める必要がある開発者を対象としています。ここでは、AI エンリッチメント プロセスの知識を前提としています。 この概念に馴染みがない場合は、「[Azure Cognitive Search における AI エンリッチメント](cognitive-search-concept-intro.md)」から初めてください。

## <a name="introducing-skillsets"></a>スキルセットの概要

スキルセットは、インデクサーにアタッチされている Azure Cognitive Search の再利用可能なリソースであり、スキルセットではインデックス作成時にテキストや画像の内容を分析、変換、および強化するために使用される一連のスキルが指定されています。 スキルには入力と出力があり、多くの場合、1 つのスキルの出力が、チェーンまたは一連のプロセス内の別のスキルの入力になります。

スキルセットには、次の 3 つの主要なプロパティがあります。

+ `skills` は、各スキルに必要な入力に基づいてプラットフォームによって実行順序が決定される、順序付けされていないスキルのコレクションです。
+ `cognitiveServices` は、組み込みのスキルが含まれる、スキルセットの画像とテキストの処理を実行する Cognitive Services リソースのキーです。
+ `knowledgeStore`、(省略可能) エンリッチされたドキュメントがプロジェクションされる Azure ストレージ アカウントです。 エンリッチされたドキュメントは、検索インデックスでも使用されます。

スキルセットは JSON で作成します。 次の例は、この記事の概念を示すために使用される、この[ホテル レビュー スキルセット](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)の少し簡略化されたバージョンです。 

最初の 2 つのスキルを次に示します。

+ スキル #1 は、"reviews_text" フィールドの内容を入力として受け取り、その内容を出力として 5,000 文字の "pages" に分割する[テキスト分割スキル](cognitive-search-skill-textsplit.md)です。
+ スキル #2 は、"pages" を入力として受け入れ、センチメント分析の結果が含まれる "Sentiment" という名前の新しいフィールドを作成する[センチメント検出スキル](cognitive-search-skill-sentiment.md)です。


```json
{
    "skills": [
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "name": "#1",
            "description": null,
            "context": "/document/reviews_text",
            "defaultLanguageCode": "en",
            "textSplitMode": "pages",
            "maximumPageLength": 5000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "name": "#2",
            "description": null,
            "context": "/document/reviews_text/pages/*",
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/reviews_text/pages/*",
                }
            ],
            "outputs": [
                {
                    "name": "score",
                    "targetName": "Sentiment"
                }
            ]
        },
  "cognitiveServices": null,
  "knowledgeStore": {  }
}
```
> [!NOTE]
> [条件付きスキル](cognitive-search-skill-conditional.md)を使用して式を作成することにより、ループや分岐が含まれる複雑なスキルセットを構築できます。 構文では、いくつかの修正が加えられた [JSON ポインター](https://tools.ietf.org/html/rfc6901) パス表記を使用して、強化ツリー内のノードを識別します。 `"/"` ではツリー内の下位レベルが走査され、`"*"` はコンテキストでの for-each 演算子として機能します。 この記事の多くの例は、この構文を示しています。 

### <a name="enrichment-tree"></a>強化ツリー

[エンリッチメント パイプラインの手順](cognitive-search-concept-intro.md#enrichment-steps)の進行中、コンテンツ処理はテキストと画像がソースから抽出される *ドキュメント解析* フェーズに従います。 画像コンテンツは画像処理を指定するスキルにルーティングされ、テキスト コンテンツはテキスト処理のためキューに登録されます。 大量のテキストが含まれているソース ドキュメントの場合は、インデクサーで *解析モード* を設定して、より最適な処理を行えるようテキストをより小さなチャンクに分割することができます。 

![パイプラインにおけるナレッジ ストアの図](./media/knowledge-store-concept-intro/knowledge-store-concept-intro.svg "パイプラインにおけるナレッジ ストアの図")

強化パイプラインに入ったドキュメントは、コンテンツおよび関連付けられている強化のツリーとして表されます。 このツリーは、ドキュメント解析の出力としてインスタンス化されます。  強化ツリー形式により、強化パイプラインではメタデータをプリミティブ データ型にもアタッチできます。これは有効な JSON オブジェクトではありませんが、有効な JSON 形式に射影できます。 次の表では、強化パイプラインに入ったドキュメントの状態を示します。

|データ ソース/解析モード|Default|JSON、JSON 行、CSV|
|---|---|---|
|Blob Storage|/document/content<br>/document/normalized_images/*<br>…|/document/{key1}<br>/document/{key2}<br>…|
|SQL|/document/{column1}<br>/document/{column2}<br>…|該当なし |
|Cosmos DB|/document/{key1}<br>/document/{key2}<br>…|該当なし|

 実行されたスキルによって、強化ツリーに新しいノードが追加されます。 これらの新しいノードは、ダウンストリームのスキル、ナレッジ ストアへの射影、またはインデックス フィールドへのマッピングに対する入力として使用できます。 強化は変更できません。作成されたノードを編集することはできません。 スキルセットや強化ツリーの複雑さが増しても、強化ツリー内のすべてのノードをインデックスやナレッジ ストアにする必要はありません。 

強化のサブセットのみを選択して、インデックスまたはナレッジ ストアに保存することができます。

### <a name="context"></a>Context

各スキルにはコンテキストが必要です。 コンテキストによって次のことが決まります。

+ 選択されたノードに基づいて、スキルが実行される回数。 型コレクションのコンテキスト値の場合、末尾に `/*` を追加すると、コレクション内のインスタンスごとにスキルが 1 回呼び出されます。 

+ スキルの出力が追加される強化ツリー内の場所。 出力は、常にコンテキスト ノードの子としてツリーに追加されます。 

+ 入力の形状。 複数レベルのコレクションの場合、コンテキストを親コレクションに設定すると、スキル入力の形状に影響します。 たとえば、国または地域のリストが含まれる強化ツリーがあり、それぞれが郵便番号のリストを含む州のリストでエンリッチメント処理されている場合、次のようになります。

|Context|入力|入力の形状|スキルの呼び出し|
|-------|-----|--------------|----------------|
|`/document/countries/*` |`/document/countries/*/states/*/zipcodes/*` |国または地域のすべての郵便番号のリスト |国または地域ごとに 1 回 |
|`/document/countries/*/states/*` |`/document/countries/ */states/* /zipcodes/*`` |州内の郵便番号のリスト | 国または地域と州の組み合わせごとに 1 回|

## <a name="generate-enriched-data"></a>強化データを生成する 

[ホテル レビュー スキルセット](https://github.com/Azure-Samples/azure-search-sample-data/blob/master/hotelreviews/HotelReviews_skillset.json)を参照ポイントとして使用し、以下を見ていきます。

+ 各スキルの実行によって強化ツリーがどのように発展するか
+ スキルの実行回数の決定に対してコンテキストと入力がどのように働くか
+ コンテキストに基づく入力の形状

エンリッチメント プロセス内の "ドキュメント" は、hotel_reviews.csv のソース ファイル内の 1 つの行 (ホテルのレビュー) を表します。

### <a name="skill-1-split-skill"></a>スキル #1: 分割スキル

ソース コンテンツが大量のテキストで構成されている場合は、言語、センチメント、キーフレーズ検出の精度を高めるために、より小さいコンポーネントに分割すると便利です。 使用できるグレインは、ページと文の 2 つです。 ページは約 5,000 文字で構成されます。

通常、テキスト分割スキルは、スキルセットにおいて最初に選ばれます。

```json
      "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
      "name": "#1",
      "description": null,
      "context": "/document/reviews_text",
      "defaultLanguageCode": "en",
      "textSplitMode": "pages",
      "maximumPageLength": 5000,
      "inputs": [
        {
          "name": "text",
          "source": "/document/reviews_text"
        }
      ],
      "outputs": [
        {
          "name": "textItems",
          "targetName": "pages"
        }
```

`"/document/reviews_text"` のスキル コンテキストでは、この分割スキルは `reviews_text` に対して 1 回実行されます。 スキルの出力はリストであり、`reviews_text` が 5000 文字のセグメントに分割されています。 分割スキルからの出力は `pages` という名前で、これは強化ツリーに追加されます。 `targetName` 機能を使用することで、強化ツリーに追加される前に、スキル出力の名前を変更できます。

これで、強化ツリーのスキルのコンテキストの下に、新しいノードが配置されました。 このノードは、任意のスキル、プロジェクション、または出力フィールド マッピングで使用できます。 概念的には、ツリーは次のようになります。

![ドキュメント解析後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-doc-cracking.png "ドキュメント解析後、スキル実行前の強化ツリー")

すべての強化のルート ノードは `"/document"` です。 BLOB インデクサーを使用すると、`"/document"` ノードに子ノード `"/document/normalized_images"` と `"/document/content"` ができます。 この例のように、CSV データを使用すると、列名は `"/document"` の下のノードにマップされます。 

スキルによってノードに追加されたいずれかの強化にアクセスするには、強化の完全なパスが必要です。 たとえば、```pages``` ノードのテキストを別のスキルへの入力として使用する場合は、```"/document/reviews_text/pages/*"``` として指定する必要があります。
 
 ![スキル #1 の後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-skill1.png "スキル #1 実行後の強化ツリー")

### <a name="skill-2-language-detection"></a>スキル #2: 言語検出

ホテル レビュー ドキュメントには、複数の言語で表される顧客フィードバックが含まれています。 言語検出スキルによって、どの言語が使用されているか判断されます。 結果は、キー フレーズ抽出とセンチメント検出に渡され、センチメントと語句を検出する際に言語が考慮されます。

言語検出スキルは、スキルセットで定義されている 3 番目のスキルですが (スキル #3)、次に実行されるスキルです。 入力要求によってブロックされないため、前のスキルと並列に実行されます。 先行する分割スキルと同様に、言語検出スキルもドキュメントごとに 1 回呼び出されます。 強化ツリーには言語用の新しいノードが追加されています。

 ![スキル #2 の後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-skill2.png "スキル #2 実行後の強化ツリー")
 
 ### <a name="skill-3-key-phrases-skill"></a>スキル #3: キー フレーズ スキル 

コンテキストとして `/document/reviews_text/pages/*` が指定されたキー フレーズ スキルは、`pages` コレクションの各項目に対して 1 回ずつ呼び出されます。 スキルからの出力は、関連付けられている page 要素の下のノードになります。 

 スキルセットに含まれる残りのスキルを表示し、各スキルの実行によって強化のツリーがどのように成長し続けるかを視覚化できるようになるはずです。 マージ スキルや Shaper スキルなどの一部のスキルでも新しいノードが作成されますが、既存のノードのデータのみが使用され、新しい強化は作成されません。

![すべてのスキルの後の強化ツリー](media/cognitive-search-working-with-skillsets/enrichment-tree-final.png "すべてのスキルの後の強化ツリー")

上のツリーのコネクタの色は、強化が異なるスキルで作成されたことを示しています。ノードは個別に指定する必要があり、親ノードを選択したときに返されるオブジェクトの一部にはなりません。

## <a name="save-enrichments"></a>エンリッチメントの保存

Azure Cognitive Search では、インデクサーによって作成された出力が保存されます。 出力の 1 つは、常に[検索可能なインデックス](search-what-is-an-index.md)になります。 インデックスの指定は必須であり、スキルセットをアタッチすると、インデックスによって取り込まれたデータにはエンリッチメントの内容が含まれます。 通常、キー フレーズやセンチメント スコアなどの特定のスキルの出力は、その目的のために作成されたフィールドのインデックスに取り込まれます。

必要に応じて、インデクサーから他のツールやプロセスで使用するために[ナレッジ ストア](knowledge-store-concept-intro.md)に出力を送信することもできます。 ナレッジ ストアは、スキルセットの一部として定義されます。 この定義により、エンリッチされたドキュメントがテーブルまたはオブジェクト (ファイルまたは BLOB) として投影されるかどうかが決まります。 表形式のプロジェクションは、Power BI などのツールでの対話型分析に適しています。一方、ファイルと BLOB は通常、データ サイエンスや同様のプロセスで使用されます。 このセクションでは、スキルセットの合成によって、プロジェクションするテーブルまたはオブジェクトがどのように整形されるかについて説明します。

### <a name="projections"></a>プロジェクション

ナレッジ ストアを対象とするコンテンツについては、コンテンツをどのように構造化するかを検討する必要があります。 *プロジェクション* とは、強化ツリーからノードを選択し、ナレッジ ストアにそのノードの物理的な表現を作成するプロセスです。 プロジェクションとは、テーブルのプロジェクションまたはオブジェクトのプロジェクションとして出力できるドキュメント (コンテンツと強化) のカスタム形状です。 プロジェクションの使用について詳しくは、[プロジェクションの操作](knowledge-store-projection-overview.md)に関する記事をご覧ください。

![フィールド マッピング オプション](./media/cognitive-search-working-with-skillsets/field-mapping-options.png "エンリッチメント パイプラインのフィールド マッピング オプション")

### <a name="sourcecontext"></a>SourceContext

`sourceContext` 要素は、スキルの入力とプロジェクションでのみ使用されます。 それは、複数レベルの入れ子になったオブジェクトを構築するために使用されます。 場合によっては、新しいオブジェクトを作成して、それをスキルまたはプロジェクトへの入力としてナレッジストアに渡す必要があります。 エンリッチメント ノードは、エンリッチメント ツリー内の有効な JSON オブジェクトではない可能性があり、ツリー内のノードを参照すると、作成時のノードの状態のみが返されるため、エンリッチメントをスキル入力またはプロジェクションとして使用するには、適切な形式の JSON オブジェクトを作成する必要があります。 `sourceContext` を使用すると、階層型の匿名型オブジェクトを構築でき、コンテキストを使用する場合にのみ、複数のスキルが必要になります。 

次の例では、`sourceContext` の使用方法を示しています。 エンリッチメントを生成したスキル出力を調べて、それがプリミティブ型ではなく有効な JSON オブジェクトであるかを確認します。

### <a name="slicing-projections"></a>プロジェクションのスライス

テーブル プロジェクション グループを定義するときに、強化ツリー内の 1 つのノードを複数の関連テーブルにスライスできます。 既存のテーブル プロジェクションの子であるソース パスを含むテーブルを追加した場合、結果の子ノードは既存のテーブル プロジェクションの子にはならず、代わりに新しい関連テーブルに射影されます。 このスライス技法を使用すると、すべてのテーブル プロジェクションのソースとして使用できる 1 つのノードを Shaper スキルで定義できます。 

### <a name="shaping-projections"></a>プロジェクションの整形

プロジェクションを定義するには 2 つの方法があります。

+ Text Shaper スキルを使用して、プロジェクションするすべてのエンリッチメントのルート ノードである新しいノードを作成します。 その後、プロジェクションでは、Shaper スキルの出力のみを参照します。

+ プロジェクション定義自体の中にあるプロジェクションでインライン整形を使用します。

Shaper による方法は、インライン整形より手間がかかりますが、強化ツリーのすべての変更がスキル内に含まれ、出力が再利用できるオブジェクトになることが保証されます。 一方でインライン整形では、必要な形状を作成できますが、匿名オブジェクトであり、定義されているプロジェクションでのみ使用できます。 これらの方法は、組み合わせて使用することも、個別に使用することもできます。 ポータル ワークフローで自動的に作成されるスキルセットには、両方が含まれています。 テーブル プロジェクションに対しては Shaper スキルが使用されていますが、キー フレーズ テーブルの射影にはインライン整形も使用されます。

この例を拡張するには、インライン整形を削除し、Shaper スキルを使用してキー フレーズ用の新しいノードを作成することができます。 以下のセクションでは、3 つのテーブル `hotelReviewsDocument`、`hotelReviewsPages`、`hotelReviewsKeyPhrases` に射影される形状を作成する 2 つのオプションについて説明します。

#### <a name="shaper-skill-and-projection"></a>Shaper スキルとプロジェクション

> [!Note]
> 簡潔にするため、この例ではドキュメント テーブルの一部の列が削除されています。
>
```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "name": "#5",
    "description": null,
    "context": "/document",
    "inputs": [        
        {
            "name": "reviews_text",
            "source": "/document/reviews_text",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "reviews_title",
            "source": "/document/reviews_title",
            "sourceContext": null,
            "inputs": []
        },
        {
            "name": "AzureSearch_DocumentKey",
            "source": "/document/AzureSearch_DocumentKey",
            "sourceContext": null,
            "inputs": []
        },  
        {
            "name": "pages",
            "source": null,
            "sourceContext": "/document/reviews_text/pages/*",
            "inputs": [
                {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "LanguageCode",
                    "source": "/document/Language",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "Page",
                    "source": "/document/reviews_text/pages/*",
                    "sourceContext": null,
                    "inputs": []
                },
                {
                    "name": "keyphrase",
                    "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                    "inputs": [
                        {
                            "source": "/document/reviews_text/pages/*/Keyphrases/*",
                            "name": "Keyphrases"
                        }
                    ]
                }
            ]
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "tableprojection"
        }
    ]
}
```

上の `outputs` セクションで定義されている `tableprojection` ノードでは、スライス機能を使用して、`tableprojection` ノードの部分を別のテーブルに射影できます。

> [!Note]
> これは、ナレッジ ストア構成内のプロジェクションのスニペットにすぎません。
>
```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsDocument",
                "generatedKeyName": "Documentid",
                "source": "/document/tableprojection"
            },
            {
                "tableName": "hotelReviewsPages",
                "generatedKeyName": "Pagesid",
                "source": "/document/tableprojection/pages/*"
            },
            {
                "tableName": "hotelReviewsKeyPhrases",
                "generatedKeyName": "KeyPhrasesid",
                "source": "/document/tableprojection/pages/*/keyphrase/*"
            }
        ]
    }
]
```

#### <a name="inline-shaping-projections"></a>プロジェクションのインライン整形

インライン整形方式では、プロジェクションに必要なすべての形状は必要になった時点で作成されるため、Shaper スキルは必要ありません。 前の例と同じデータを射影するためのインライン プロジェクション オプションは次のようになります。

```json
"projections": [
    {
        "tables": [
            {
                "tableName": "hotelReviewsInlineDocument",
                "generatedKeyName": "Documentid",
                "sourceContext": "/document",     
                "inputs": [
                    {
                        "name": "reviews_text",
                        "source": "/document/reviews_text"
                    },
                    {
                        "name": "reviews_title",
                        "source": "/document/reviews_title"
                    },
                    {
                        "name": "AzureSearch_DocumentKey",
                        "source": "/document/AzureSearch_DocumentKey"
                    }                             
                ]
            },
            {
                "tableName": "hotelReviewsInlinePages",
                "generatedKeyName": "Pagesid",
                "sourceContext": "/document/reviews_text/pages/*",
                "inputs": [
                        {
                    "name": "SentimentScore",
                    "source": "/document/reviews_text/pages/*/Sentiment"
                    },
                    {
                        "name": "LanguageCode",
                        "source": "/document/Language"
                    },
                    {
                        "name": "Page",
                        "source": "/document/reviews_text/pages/*"
                    }
                ]
            },
            {
                "tableName": "hotelReviewsInlineKeyPhrases",
                "generatedKeyName": "KeyPhraseId",
                "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                "inputs": [
                    {
                        "name": "Keyphrases",
                        "source": "/document/reviews_text/pages/*/Keyphrases/*"
                    }
                ]
            }
        ]
    }
]
```
  
両方の方法から観察されることの 1 つは、`"sourceContext"` を使用して `"Keyphrases"` の値が射影される方法です。 文字列のコレクションが含まれる `"Keyphrases"` ノードは、それ自体がページ テキストの子になります。 ただし、プロジェクションには JSON オブジェクトが必要であり、ページはプリミティブ (文字列) であるため、`"sourceContext"` を使用して、キー フレーズを名前付きプロパティを持つオブジェクトにラップします。 この手法を使用すると、プリミティブであっても個別に射影できます。

## <a name="next-steps"></a>次のステップ

次のステップでは、コグニティブ スキルを使用して最初のスキルセットを作成します。

> [!div class="nextstepaction"]
> [最初のスキルセットを作成する](cognitive-search-defining-skillset.md)
