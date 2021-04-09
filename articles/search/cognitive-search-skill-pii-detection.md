---
title: PII 検出コグニティブ スキル (プレビュー)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search のエンリッチメント パイプラインのテキストから個人情報を抽出してマスクします。 このスキルは現在、パブリック プレビューの段階です。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: acacf617d3f1d9ab891d08b32fc2dfb14deb64a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91540525"
---
# <a name="pii-detection-cognitive-skill"></a>PII 検出コグニティブ スキル

> [!IMPORTANT] 
> このスキルは現在、パブリック プレビューの段階です。 プレビュー段階の機能はサービス レベル アグリーメントなしで提供しています。運用環境のワークロードに使用することはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 現時点では、ポータルと .NET SDK によるサポートはありません。

**PII 検出** スキルは、入力テキストから個人情報を抽出し、それをマスクするオプションを提供します。 このスキルでは、Cognitive Services の [Text Analytics](../cognitive-services/text-analytics/overview.md) によって提供される機械学習モデルが使用されます。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure Cognitive Search のドキュメント解析段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Cognitive Search の価格](https://azure.microsoft.com/pricing/details/search/)に関するページで説明されています。

## <a name="odatatype"></a>@odata.type

Microsoft.Skills.Text.PIIDetectionSkill

## <a name="data-limits"></a>データ制限

レコードのサイズは、[`String.Length`](/dotnet/api/system.string.length) で測定して 50,000 文字以下にする必要があります。 データをスキルに送信する前にチャンクに分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターでは大文字と小文字が区別され、すべて任意です。

| パラメーター名     | 説明 |
|--------------------|-------------|
| `defaultLanguageCode` |    入力テキストの言語コード。 現時点では、`en` のみがサポートされています。 |
| `minimumPrecision` | 0\.0 から 1.0 の値。 (`piiEntities` 出力の) 信頼度スコアが `minimumPrecision` の設定値よりも小さい場合は、エンティティは返されず、マスクもされません。 既定では、0.0 です。 |
| `maskingMode` | 入力テキスト内で検出された個人情報をマスクするためのさまざまな方法を提供するパラメーター。 次のオプションがサポートされています。 <ul><li>`none` (既定値): マスクは行われず、`maskedText` の出力は返されません。 </li><li> `redact`:検出されたエンティティを入力テキストから削除し、削除された値は置き換えません。 この場合、`piiEntities` の出力のオフセットは、マスクされたテキストではなく元のテキストに関連します。 </li><li> `replace`:検出されたエンティティを `maskingCharacter` パラメーターで指定された文字に置き換えます。 文字は検出されたエンティティの長さに繰り返されます。これにより、オフセットが入力テキストと出力 `maskedText` の両方に正しく対応するようになります。</li></ul> |
| `maskingCharacter` | `maskingMode` パラメーターが `replace` に設定されている場合に、テキストをマスクするために使用される文字。 `*` (規定値)、`#`、`X` のオプションがサポートされています。 このパラメーターは、`maskingMode` が `replace` に設定されていない場合にのみ `null` できます。 |

## <a name="skill-inputs"></a>スキルの入力

| 入力名      | 説明                   |
|---------------|-------------------------------|
| `languageCode`    | 省略可能。 既定値は `en` です。  |
| `text`          | 分析するテキスト。          |

## <a name="skill-outputs"></a>スキルの出力

| 出力名      | 説明                   |
|---------------|-------------------------------|
| `piiEntities` | 次のフィールドが含まれる複合型の配列。 <ul><li>テキスト (抽出された実際の PII)</li> <li>type</li><li>subType</li><li>スコア (値が高いほど、実際のエンティティに近づく可能性が高くなります)</li><li>オフセット (入力テキスト内)</li><li>length</li></ul> </br> [使用できる型と subTypes については、こちらを参照してください。](../cognitive-services/text-analytics/named-entity-types.md?tabs=personal) |
| `maskedText` | `maskingMode` が `none` 以外の値に設定されている場合、この出力は、選択した `maskingMode` で説明されている入力テキストに対して実行されるマスクの結果の文字列になります。  `maskingMode` が `none` に設定されている場合、この出力は表示されません。 |

## <a name="sample-definition"></a>定義例

```json
  {
    "@odata.type": "#Microsoft.Skills.Text.PIIDetectionSkill",
    "defaultLanguageCode": "en",
    "minimumPrecision": 0.5,
    "maskingMode": "replace",
    "maskingCharacter": "*",
    "inputs": [
      {
        "name": "text",
        "source": "/document/content"
      }
    ],
    "outputs": [
      {
        "name": "piiEntities"
      },
      {
        "name": "maskedText"
      }
    ]
  }
```

## <a name="sample-input"></a>サンプル入力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Microsoft employee with ssn 859-98-0987 is using our awesome API's."
           }
      }
    ]
}
```

## <a name="sample-output"></a>サンプル出力

```json
{
  "values": [
    {
      "recordId": "1",
      "data" : 
      {
        "piiEntities":[ 
           { 
              "text":"859-98-0987",
              "type":"U.S. Social Security Number (SSN)",
              "subtype":"",
              "offset":28,
              "length":11,
              "score":0.65
           }
        ],
        "maskedText": "Microsoft employee with ssn *********** is using our awesome API's."
      }
    }
  ]
}
```

このスキルの出力のエンティティに対して返されるオフセットは、[Text Analytics API](../cognitive-services/text-analytics/overview.md) から直接返されます。つまり、これらを使用して元の文字列にインデックスを作成する場合は、正しい内容を抽出するために .NET の [StringInfo](/dotnet/api/system.globalization.stringinfo) クラスを使用する必要があります。  [詳細については、こちらで確認できます。](../cognitive-services/text-analytics/concepts/text-offsets.md)

## <a name="errors-and-warnings"></a>エラーと警告

ドキュメントの言語コードがサポートされていない場合、警告が返され、エンティティは抽出されません。
テキストが空の場合、警告が返されます。
テキストが 50,000 文字を超えると、最初の 50,000 文字のみが分析され、警告が発行されます。

スキルから警告が返された場合、出力 `maskedText` が空である可能性があります。これは、その出力を予期している下流のスキルに影響を与える可能性があります。 このため、スキルセットの定義を記述するときは必ず、出力が欠落していることに関連するすべての警告を調べてください。

## <a name="see-also"></a>関連項目

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)