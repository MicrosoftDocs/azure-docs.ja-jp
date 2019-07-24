---
title: Shaper コグニティブ検索スキル - Azure Search
description: 非構造化データからメタデータと構造化された情報を抽出し、Azure Search エンリッチメント パイプライン内で複合型としてシェイプします。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/22/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: c55783e9b209a1280a21edca34b75e72481f4cb6
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/25/2019
ms.locfileid: "56806999"
---
#   <a name="shaper-cognitive-skill"></a>Shaper コグニティブ スキル

**Shaper** スキルは、複数の入力を、エンリッチメント パイプラインの後の部分で参照できる複合型に統合します。 **Shaper** スキルでは、基本的に、構造を作成し、その構造のメンバーの名前を定義して、各メンバーに値を割り当てることができます。 統合されたフィールドが検索のシナリオで役立つ例として、姓と名を組み合わせて 1 つの構造体にする、市と県を組み合わせて 1 つの構造体にする、名前と誕生日を組み合わせて 1 つの構造体にする、などの操作を行って一意の ID を確立することが挙げられます。

既定では、この方法でサポートされるオブジェクトは深さが 1 レベルのものです。 より複雑なオブジェクトについては、いくつかの **Shaper** ステップを連鎖させることができます。

応答内では、出力名は常に "output" です。 内部的には、パイプラインで、"output" に異なる名前をマップできます (下の例では "analyzedText") が、**Shaper** スキル自体は、応答で "output" を返します。 これは、エンリッチメントしたドキュメントのデバッグ中に名前付けの不一致に気付いた場合や、カスタム スキルを構築し、応答を自身で作成している場合に重要なことがあります。

> [!NOTE]
> このスキルは Cognitive Services API にバインドされていないため、この使用に対しては課金されません。 ただし、1 日あたりの毎日のエンリッチメントの数を少数に制限する**無料**リソースのオプションをオーバーライドするには、引き続き [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)する必要があります。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Util.ShaperSkill

## <a name="sample-1-complex-types"></a>サンプル 1: 複合型

*analyzedText* という名前の構造を作成し、それに 2 つのメンバーがあって、それぞれが *text* と *sentiment* という名前だとします。 Azure Search では、マルチパートの検索可能フィールドは*複合型*と呼ばれ、標準状態ではまだサポートされていません。 このプレビューでは、**Shaper** スキルを使用し、インデックス内に複合型のフィールドを生成できます。 

次の例では、入力として、メンバー名を提供します。 出力構造 (Azure Search での複合型フィールド) は *targetName* によって指定されています。 


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

### <a name="sample-input"></a>サンプル入力
この **Shaper** スキルで使用できる入力を提供する JSON ドキュメントは、次のようになります。

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


### <a name="sample-output"></a>サンプル出力
**Shaper** スキルにより、*text* と *sentiment* の要素が組み合わせられている *analyzedText* という名前の新しい要素が生成されます。 

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

## <a name="sample-2-input-consolidation"></a>サンプル 2: 入力の統合

別の例で、パイプライン処理の異なる段階において、書籍のタイトルを抽出し、その書籍のさまざまなページで章のタイトルを抽出したとします。 これで、これらのさまざまな入力で構成される 1 つの構造を作成できるようになりました。

このシナリオの Shaper スキルの定義は、次の例のようになります。

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
            "source": "/document/content/pages/*/chapterTitles/*"
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

### <a name="sample-output"></a>サンプル出力
この場合、Shaper により、すべての章のタイトルがフラット化され、1 つの配列が作成されています。 

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

## <a name="see-also"></a>関連項目

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)

