---
title: キー フレーズ抽出コグニティブ スキル
titleSuffix: Azure Cognitive Search
description: 非構造化テキストを評価し、各レコードに対し、Azure Cognitive Search の AI エンリッチメント パイプラインのキー フレーズのリストを返します。
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 8aafb08ff0ccc9391071f796450e69f87de279ba
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102547834"
---
#   <a name="key-phrase-extraction-cognitive-skill"></a>キー フレーズ抽出コグニティブ スキル

**キー フレーズ抽出** スキルは、非構造化テキストを評価し、各レコードに対してキー フレーズのリストを返します。 このスキルでは、Cognitive Services の [Text Analytics](../cognitive-services/text-analytics/overview.md) によって提供される機械学習モデルが使用されます。

この機能は、レコード内の要点をすばやく特定する必要がある場合に便利です。 たとえば、「食べ物はおいしくて、すばらしいスタッフがいた」というテキストを入力すると、 "食べ物" と "すばらしいスタッフ" がサービスによって返されます。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure Cognitive Search のドキュメント解析段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Cognitive Search の価格](https://azure.microsoft.com/pricing/details/search/)に関するページで説明されています。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.KeyPhraseExtractionSkill 

## <a name="data-limits"></a>データ制限
レコードのサイズは、[`String.Length`](/dotnet/api/system.string.length) で測定して 50,000 文字以下にする必要があります。 データをキー フレーズ エクストラクターに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| 入力 | 説明 |
|---------------------|-------------|
| `defaultLanguageCode` | (省略可能) 言語を明示的に指定しないドキュメントに適用する言語コード。  既定の言語コードが指定されていない場合、既定の言語コードとして英語 (en) が使用されます。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照してください。 |
| `maxKeyPhraseCount`   | (省略可能) 生成するキー フレーズの最大数。 |
| `modelVersion`   | (省略可能) Text Analytics サービスを呼び出すときに使用するモデルのバージョン。 指定しない場合、既定では利用可能な最新のものになります。 絶対に必要な場合以外は、この値を指定しないことをお勧めします。 詳細については、「[Text Analytics API でのモデルのバージョン管理](../cognitive-services/text-analytics/concepts/model-versioning.md)」を参照してください。 |

## <a name="skill-inputs"></a>スキルの入力

| 入力  | 説明 |
|--------------------|-------------|
| `text` | 分析されるテキスト。|
| `languageCode`    |  レコードの言語を示す文字列。 このパラメーターが指定されていない場合、既定の言語コードがレコードを分析するために使用されます。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/text-analytics/language-support.md)を参照|

## <a name="skill-outputs"></a>スキルの出力

| 出力     | 説明 |
|--------------------|-------------|
| `keyPhrases` | 入力テキストから抽出されたキー フレーズの一覧。 キー フレーズは、重要度順に返されます。 |


##  <a name="sample-definition"></a>定義例

次のフィールドを持つ SQL レコードを検討してみます。

```json
{
    "content": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. They accumulate ice from snowfall and lose it through melting. As global temperatures have risen, many of the world’s glaciers have already started to shrink and retreat. Continued warming could see many iconic landscapes – from the Canadian Rockies to the Mount Everest region of the Himalayas – lose almost all their glaciers by the end of the century.",
    "language": "en"
}
```

スキル定義は次のようになります。

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      },
      {
        "name": "languageCode",
        "source": "/document/language" 
      }
    ],
    "outputs": [
      {
        "name": "keyPhrases",
        "targetName": "myKeyPhrases"
      }
    ]
  }
```

##  <a name="sample-output"></a>サンプル出力

上記の例では、スキルの出力は "document/myKeyPhrases" と呼ばれる強化されたツリーの新しいノードに書き込まれます。これは、指定した `targetName` が "document/myKeyPhrases" であるためです。 `targetName` が指定されていない場合は、"document/keyPhrases" になります。

#### <a name="documentmykeyphrases"></a>document/myKeyPhrases 
```json
            [
              "world’s glaciers", 
              "huge rivers of ice", 
              "Canadian Rockies", 
              "iconic landscapes",
              "Mount Everest region",
              "Continued warming"
            ]
```

他のスキルへの入力として、または[出力フィールドのマッピング](cognitive-search-output-field-mapping.md)のソースとして、"document/myKeyPhrases" を使用できます。

## <a name="warnings"></a>警告
サポートされていない言語コードを指定すると、警告が生成され、キー フレーズは抽出されません。
テキストが空の場合、警告が生成されます。
テキストが 50,000 文字を超えると、最初の 50,000 文字のみが分析され、警告が発行されます。

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
+ [出力フィールドのマッピングを定義する方法](cognitive-search-output-field-mapping.md)