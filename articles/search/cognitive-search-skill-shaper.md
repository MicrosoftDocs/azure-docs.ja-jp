---
title: Shaper コグニティブ スキル
titleSuffix: Azure Cognitive Search
description: 非構造化データからメタデータと構造化された情報を抽出し、Azure Cognitive Search の AI エンリッチメント パイプライン内で複合型としてシェイプします。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 81eb0e60befc544a6c3bee8f04e901b6a5e472bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85560818"
---
# <a name="shaper-cognitive-skill"></a>Shaper コグニティブ スキル

**Shaper** スキルは、複数の入力を、エンリッチメント パイプラインの後の部分で参照できる [複合型](search-howto-complex-data-types.md)に統合します。 **Shaper** スキルでは、基本的に、構造を作成し、その構造のメンバーの名前を定義して、各メンバーに値を割り当てることができます。 統合されたフィールドが検索のシナリオで役立つ例として、姓と名を組み合わせて 1 つの構造体にする、市と県を組み合わせて 1 つの構造体にする、名前と誕生日を組み合わせて 1 つの構造体にする、などの操作を行って一意の ID を確立することが挙げられます。

さらに、[シナリオ 3](#nested-complex-types) で示されている **Shaper** スキルにより、オプションの "*sourceContext*" プロパティが入力に追加されます。 *source* および *sourceContext* プロパティは相互に排他的です。 入力がスキルのコンテキストにある場合は、単に *source* を使用します。 入力がスキル コンテキストとは *別の* コンテキストにある場合は、*sourceContext* を使用します。 *sourceContext* には、特定の要素がソースとして扱われる入れ子の入力を定義する必要があります。 

出力の名前は常に "output" です。 内部的には、パイプラインでは別の名前をマップできます (下の例では "analyzedText") が、**Shaper** スキル自体は、応答内で "output" を返します。 これは、エンリッチメントしたドキュメントのデバッグ中に名前付けの不一致に気付いた場合や、カスタム スキルを構築し、応答を自身で作成している場合に重要なことがあります。

> [!NOTE]
> **Shaper** スキルは Cognitive Services API にバインドされていないため、使用に対しては課金されません。 ただし、1 日あたりの毎日のエンリッチメントの数を少数に制限する **無料** リソースのオプションをオーバーライドするには、引き続き [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)する必要があります。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="scenario-1-complex-types"></a>シナリオ 1: 複合型

*analyzedText* という名前の構造を作成し、それに 2 つのメンバーがあって、それぞれが *text* と *sentiment* という名前だとします。 インデックス内では、マルチパートの検索可能フィールドは *複合型* と呼ばれ、通常は、それにマップする対応した複合構造をソース データが備えている場合に作成されます。

ただし、複合型を作成するための別の方法として、**Shaper** スキルを利用することもできます。 このスキルをスキルセットに含めることで、スキルセット処理中のメモリ内操作において、入れ子になった構造を持つデータ シェイプを出力できます。その後、それをインデックス内の複合型にマップできます。 

次の例のスキル定義では、入力としてメンバー名を提供します。 


```json
{
  "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
  "context": "/document/content/phrases/*",
  "inputs": [
    {
      "name": "text",
      "source": "/document/content/phrases/*"
    },
    {
      "name": "sentiment",
      "source": "/document/content/phrases/*/sentiment"
    }
  ],
  "outputs": [
    {
      "name": "output",
      "targetName": "analyzedText"
    }
  ]
}
```

### <a name="sample-index"></a>サンプル インデックス

スキルセットはインデクサーによって呼び出され、インデクサーはインデックスを必要とします。 インデックス内の複合フィールドの表現は、次の例のようになります。 

```json

    "name": "my-index",
    "fields": [
        {   "name": "myId", "type": "Edm.String", "key": true, "filterable": true   },
        {   "name": "analyzedText", "type": "Edm.ComplexType",
            "fields": [{
                    "name": "text",
                    "type": "Edm.String",
                    "filterable": false,
                    "sortable": false,
                    "facetable": false,
                    "searchable": true  },
          {
                    "name": "sentiment",
                    "type": "Edm.Double",
                    "searchable": true,
                    "filterable": true,
                    "sortable": true,
                    "facetable": true
                },
```

### <a name="skill-input"></a>スキルの入力

この **Shaper** スキルに使用できる入力を提供する入力 JSON ドキュメントは、次のようになります。

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "text": "this movie is awesome",
                "sentiment": 0.9
            }
        }
    ]
}
```


### <a name="skill-output"></a>スキルの出力

**Shaper** スキルにより、*text* と *sentiment* の要素が組み合わせられている *analyzedText* という名前の新しい要素が生成されます。 この出力は、インデックス スキーマに準拠します。 Azure Cognitive Search のインデックスにインポートされ、インデックスが作成されます。

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
            "analyzedText": 
              {
                "text": "this movie is awesome" ,
                "sentiment": 0.9
              }
           }
      }
    ]
}
```

## <a name="scenario-2-input-consolidation"></a>シナリオ 2: 入力の統合

別の例で、パイプライン処理の異なる段階において、書籍のタイトルを抽出し、その書籍のさまざまなページで章のタイトルを抽出したとします。 これで、これらのさまざまな入力で構成される 1 つの構造を作成できるようになりました。

このシナリオの **Shaper** スキルの定義は、次の例のようになります。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "source": "/document/content/pages/*/chapterTitles/*/title"
        }
    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>スキルの出力
この場合、**Shaper** により、すべての章のタイトルがフラット化され、1 つの配列を作成しています。 

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                        "Start young",
                        "Laugh often",
                        "Eat, sleep and exercise"
                    ]
                }
            }
        }
    ]
}
```

<a name="nested-complex-types"></a>

## <a name="scenario-3-input-consolidation-from-nested-contexts"></a>シナリオ 3: 入れ子になったコンテキストからの入力統合

本の、タイトル、章、コンテンツがあり、そのコンテンツ上でエンティティ認識とキー フレーズを実行できることを想定してください。その場合、さまざまなスキルからの結果を統合して、章の名前、エンティティ、およびキーフレーズを備えた 1 つのシェイプにまとめる必要があります。

このシナリオの **Shaper** スキルの定義は、次の例のようになります。

```json
{
    "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
    "context": "/document",
    "inputs": [
        {
            "name": "title",
            "source": "/document/content/title"
        },
        {
            "name": "chapterTitles",
            "sourceContext": "/document/content/pages/*/chapterTitles/*",
            "inputs": [
              {
                  "name": "title",
                  "source": "/document/content/pages/*/chapterTitles/*/title"
              },
              {
                  "name": "number",
                  "source": "/document/content/pages/*/chapterTitles/*/number"
              }
            ]
        }

    ],
    "outputs": [
        {
            "name": "output",
            "targetName": "titlesAndChapters"
        }
    ]
}
```

### <a name="skill-output"></a>スキルの出力
この場合、**Shaper** では複合型を作成します。 この構造体はメモリ内に存在します。 [ナレッジ ストア](knowledge-store-concept-intro.md)に保存する場合は、ストレージの特性が定義されているスキルセット内にプロジェクションを作成する必要があります。

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "titlesAndChapters": {
                    "title": "How to be happy",
                    "chapterTitles": [
                      { "title": "Start young", "number": 1},
                      { "title": "Laugh often", "number": 2},
                      { "title": "Eat, sleep and exercise", "number: 3}
                    ]
                }
            }
        }
    ]
}
```

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [複合型を使用する方法](search-howto-complex-data-types.md)
+ [ナレッジ ストア](knowledge-store-concept-intro.md)
+ [REST でナレッジ ストアを作成する](knowledge-store-create-rest.md)