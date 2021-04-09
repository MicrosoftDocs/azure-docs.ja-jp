---
title: スキルセットで入力と出力を参照する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Searchの AI エンリッチメント パイプラインでの注釈の構文と、スキルセットの入力と出力で注釈を参照する方法について説明します。
manager: nitinme
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03431d861ca6d469b894e45c36fe2a3d7904c3a2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88935536"
---
# <a name="how-to-reference-annotations-in-an-azure-cognitive-search-skillset"></a>Azure Cognitive Searchスキルセットで注釈を参照する方法

この記事では、さまざまなシナリオを説明する例を使用して、スキル定義で注釈を参照する方法を学習します。 ドキュメントの内容がスキルのセットを通過するにつれて、注釈が豊富になっていきます。 注釈は、ダウンストリームをさらに豊富にするための入力として使用することも、インデックスの出力フィールドにマッピングすることもできます。 
 
この記事の例は、[Azure Blob インデクサー](search-howto-indexing-azure-blob-storage.md)によってドキュメント クラッキング フェーズの一部として自動的に生成される *content* フィールドに基づいています。 BLOB コンテナーのドキュメントを参照するときは、`"/document/content"` などの書式を使用します。ここで、*content* フィールドは *document* の一部です。 

## <a name="background-concepts"></a>バックグラウンドの概念

構文を調べる前に、この記事の後の方で説明する例をよく理解できるように、いくつかの重要な概念を再確認しておきましょう。

| 期間 | 説明 |
|------|-------------|
| エンリッチされたドキュメント | エンリッチされたドキュメントは、ドキュメントに関連するすべての注釈を保持するためにパイプラインによって作成されて使用される内部構造です。 エンリッチされたドキュメントのことは、注釈のツリーとして考えてください。 一般に、前の注釈から作成された注釈は、その子になります。<p/>エンリッチされたドキュメントは、スキルセットの実行中にのみ存在します。 コンテンツが検索インデックスにマップされると、エンリッチされたドキュメントは必要なくなります。 エンリッチされたドキュメントを直接操作することはありませんが、スキルセットの作成時にドキュメントのメンタル モデルがあると便利です。 |
| エンリッチメント コンテキスト | どの要素がエンリッチされるかという観点での、エンリッチメントが行われるコンテキストです。 既定では、エンリッチメント コンテキストは `"/document"` レベルであり、個々のドキュメントがスコープになります。 スキルを実行すると、そのスキルの出力は、[定義されたコンテキストのプロパティ](#example-2)になります。|

<a name="example-1"></a>
## <a name="example-1-simple-annotation-reference"></a>例 1: 単純な注釈参照

Azure BLOB ストレージに、エンティティ認識を使用して抽出する人名への参照を含む、さまざまなファイルがあるとします。 以下のスキル定義では、`"/document/content"` はドキュメント全体のテキスト表現であり、"people" は人物として識別されるエンティティのフル ネームの抽出です。

既定のコンテキストは `"/document"` であるため、人の一覧は `"/document/people"` として参照できるようになりました。 この特定のケースでは、`"/document/people"` は注釈であり、インデックス内のフィールドにマップすることも、同じスキルセット内の別のスキルで使用することもできます。

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
    "categories": [ "Person"],
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "persons",
        "targetName": "people"
      }
    ]
  }
```

<a name="example-2"></a>

## <a name="example-2-reference-an-array-within-a-document"></a>例 2: ドキュメント内の配列の参照

この例は、前の例を基に構築されており、同じドキュメントに対して複数回エンリッチメント ステップを呼び出す方法を示しています。 前の例では、1 つのドキュメントから 10 人の名前を持つ文字列の配列が生成されたものとします。 妥当な次のステップは、フル ネームから姓を抽出する 2 回目のエンリッチメントでしょう。 10 個の名前があるため、このステップをこのドキュメントで 10 回、各人に対して 1 回ずつ呼び出します。 

適切な反復回数の呼び出しを行うには、コンテキストを `"/document/people/*"` に設定します。ここで、アスタリスク (`"*"`) は、エンリッチされたドキュメントのすべてのノードを `"/document/people"` の子孫として表します。 このスキルはスキル配列で一度だけ定義されますが、すべてのメンバーが処理されるまで、ドキュメント内の各メンバーに対して呼び出されます。

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the last name from a full name",
    "uri": "http://names.azurewebsites.net/api/GetLastName",
    "context" : "/document/people/*",
    "defaultLanguageCode": "en",
    "inputs": [
      {
        "name": "fullname",
        "source": "/document/people/*"
      }
    ],
    "outputs": [
      {
        "name": "lastname",
        "targetName": "last"
      }
    ]
  }
```

注釈が文字列の配列またはコレクションである場合、配列全体ではなく特定のメンバーをターゲットにしたい場合もあります。 上の例では、コンテキストによって表されている各ノードの下に、`"last"` という名前の注釈を生成しています。 この一連の注釈を参照する場合は、`"/document/people/*/last"` という構文を使用することができます。 特定の注釈を参照する場合は、明示的なインデックス `"/document/people/1/last`" を使用すると、ドキュメント内で特定された最初の人の姓を参照することができます。 この構文では、配列が "0 でインデックスされた" ことに注意してください。

<a name="example-3"></a>

## <a name="example-3-reference-members-within-an-array"></a>例 3: 配列内のメンバーの参照

場合によっては、特定の種類のすべての注釈をグループ化して、特定のスキルに渡す必要があります。 例 2 で抽出されたすべての姓の中から最も一般的な姓を特定する、架空のカスタム スキルを考えてみましょう。 カスタム スキルに姓だけを提供するには、コンテキストを `"/document"`、入力を `"/document/people/*/lastname"` と指定します。

`"/document/people/*/lastname"` のカーディナリティがドキュメントのカーディナリティよりも大きいことに注意してください。 このドキュメントには 1 つのドキュメント ノードしか存在しませんが、10 個の姓ノードが存在しています。 その場合、システムはドキュメント内のすべての要素を含む `"/document/people/*/lastname"` の配列を自動的に作成します。

```json
  {
    "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
    "description": "Fictitious skill that gets the most common string from an array of strings",
    "uri": "http://names.azurewebsites.net/api/MostCommonString",
    "context" : "/document",
    "inputs": [
      {
        "name": "strings",
        "source": "/document/people/*/lastname"
      }
    ],
    "outputs": [
      {
        "name": "mostcommon",
        "targetName": "common-lastname"
      }
    ]
  }
```



## <a name="see-also"></a>参照
+ [エンリッチメント パイプラインにカスタム スキルを統合する方法](cognitive-search-custom-skill-interface.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [スキルセットを作成する (REST)](/rest/api/searchservice/create-skillset)
+ [エンリッチされたフィールドをインデックスにマップする方法](cognitive-search-output-field-mapping.md)