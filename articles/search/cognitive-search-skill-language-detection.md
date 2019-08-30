---
title: 言語検出コグニティブ検索スキル - Azure Search
description: 非構造化テキストを評価し、各レコードに対し、Azure Search のエンリッチメント パイプラインの分析の強度を示すスコアと共に言語識別子を返します。
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.subservice: cognitive-search
ms.openlocfilehash: 14163b959a6e91406133ca2f5a125c7e2df967ad
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69635808"
---
#   <a name="language-detection-cognitive-skill"></a>言語検出コグニティブ スキル

**言語検出**スキルは、入力テキストの言語を検出し、要求で送信されたすべてのドキュメントごとに 1 つの言語コードを報告します。 言語コードは、分析の強度を示すスコアとペアリングされます。 このスキルでは、[Text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/overview) Cognitive Services によって提供される機械学習モデルが使用されます。

この機能は、テキストの言語をその他のスキル ([感情分析スキル](cognitive-search-skill-sentiment.md)や[テキスト分割スキル](cognitive-search-skill-textsplit.md)など) への入力として提供する必要がある場合に特に便利です。

言語検出では Bing の自然言語処理ライブラリが使用され、その数は Text Analytics に記載された[サポートされている言語とリージョン](https://docs.microsoft.com/azure/cognitive-services/text-analytics/language-support)の数を超えています。 言語の正確な一覧は公開されていませんが、広域で話されているすべての言語のほか、変種、方言、および一部の地方言語や文化言語も含まれます。 使用頻度の低い言語で表されるコンテンツがある場合、[言語検出 API を試して](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7)、コードが返されるかどうか確認できます。 検出できない言語の応答は `unknown` です。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure Search のドキュメントクラッキング段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Search の価格のページ](https://go.microsoft.com/fwlink/?linkid=2042400)で説明されています。


## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.LanguageDetectionSkill

## <a name="data-limits"></a>データ制限
レコードの最大サイズは、[`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length) によって測定されるため、50,000 文字にする必要があります。 データをセンチメント アナライザーに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)を使用できます。

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
