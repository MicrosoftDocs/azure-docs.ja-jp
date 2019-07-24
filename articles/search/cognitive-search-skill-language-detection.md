---
title: 言語検出コグニティブ検索スキル - Azure Search
description: 非構造化テキストを評価し、各レコードに対し、Azure Search のエンリッチメント パイプラインの分析の強度を示すスコアと共に言語識別子を返します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.date: 02/25/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: 605f4c639cfc8c0f9732f7347532e1bd7edc055f
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2019
ms.locfileid: "57404656"
---
#   <a name="language-detection-cognitive-skill"></a>言語検出コグニティブ スキル

**言語検出**スキルは、入力テキストの言語を検出し、要求で送信されたすべてのドキュメントごとに 1 つの言語コードを報告します。 言語コードは、分析の強度を示すスコアとペアリングされます。 このスキルでは、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) Cognitive Services によって提供される機械学習モデルが使用されます。

この機能は、テキストの言語をその他のスキル ([感情分析スキル](cognitive-search-skill-sentiment.md)や[テキスト分割スキル](cognitive-search-skill-textsplit.md)など) への入力として提供する必要がある場合に特に便利です。

言語検出では Bing の自然言語処理ライブラリが使用され、その数は Text Analytics に記載された[サポートされている言語とリージョン](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support)の数を超えています。 言語の正確な一覧は公開されていませんが、広域で話されているすべての言語のほか、変種、方言、および一部の地方言語や文化言語も含まれます。 使用頻度の低い言語で表されるコンテンツがある場合、[言語検出 API を試して](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)、コードが返されるかどうか確認できます。 検出できない言語の応答は `unknown` です。

> [!NOTE]
> 2018 年 12 月 21 日より、Azure Search のスキルセットに [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)できるようになっています。 これにより、スキルセットの実行への課金を開始できます。 この日付には、ドキュメント クラッキング ステージの一部として画像抽出への課金も開始します。 ドキュメントからのテキスト抽出は、引き続き追加コストなしで提供されます。
>
> [組み込みコグニティブ スキル](cognitive-search-predefined-skills.md)の実行は、[Cognitive Services の従量制価格](https://azure.microsoft.com/pricing/details/cognitive-services)で課金されます。これは、タスクを直接実行した場合と同じ料金です。 画像の抽出は Azure Search の課金対象であり、現在はプレビュー価格で提供されています。 詳細については、「[Azure Search の価格](https://go.microsoft.com/fwlink/?linkid=2042400)」のページ、または「[請求体系について](search-sku-tier.md#how-billing-works)」を参照してください。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>データ制限
レコードの最大サイズは、`String.Length` によって測定されるため、50,000 文字にする必要があります。 データをセンチメント アナライザーに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)を使用できます。

## <a name="skill-inputs"></a>スキルの入力

パラメーターの大文字と小文字は区別されます。

| 入力     | 説明 |
|--------------------|-------------|
| text | 分析されるテキスト。|

## <a name="skill-outputs"></a>スキルの出力

| 出力名    | 説明 |
|--------------------|-------------|
| languageCode | 識別された言語の ISO 6391 言語コード。 例: "en"。 |
| languageName | 言語の名前。 例: "English"。 |
| スコア | 0 から 1 の値。 言語が正しく識別されている確率。 文章内に言語が混在している場合、スコアが 1 よりも低くなる場合があります。  |

##  <a name="sample-definition"></a>定義例

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "languageCode",
        "targetName": "myLanguageCode"
      },
      {
        "name": "languageName",
        "targetName": "myLanguageName"
      },
      {
        "name": "score",
        "targetName": "myLanguageScore"
      }

    ]
  }
```

##  <a name="sample-input"></a>サンプル入力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
           {
             "text": "Glaciers are huge rivers of ice that ooze their way over land, powered by gravity and their own sheer weight. "
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Estamos muy felices de estar con ustedes."
           }
      }
    ]
```


##  <a name="sample-output"></a>サンプル出力

```json
{
    "values": [
      {
        "recordId": "1",
        "data":
            {
              "languageCode": "en",
              "languageName": "English",
              "score": 1,
            }
      },
      {
        "recordId": "2",
        "data":
            {
              "languageCode": "es",
              "languageName": "Spanish",
              "score": 1,
            }
      }
    ]
}
```


## <a name="error-cases"></a>エラーになる場合
テキストがサポートされていない言語で表されている場合、エラーが生成され、言語識別子は返されません。

## <a name="see-also"></a>関連項目

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
