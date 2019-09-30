---
title: テキスト分割コグニティブ検索スキル - Azure Search
description: Azure Search のエンリッチメント パイプラインの長さに基づいて、テキストをテキストのチャンクまたはページに分割します。
services: search
manager: nitinme
author: luiscabrer
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: luisca
ms.openlocfilehash: a6e46970b6c0fc91c464207049d2dedaaee1afdd
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265681"
---
#   <a name="text-split-cognitive-skill"></a>テキスト分割コグニティブ スキル

**テキスト分割**スキルは、テキストをテキストのチャンクに分割します。 テキストを特定の長さの文章またはページに分割するかどうかを指定できます。 このスキルは、他のスキル ダウンストリームでテキストの最大長の要件がある場合に、特に便利です。 

> [!NOTE]
> このスキルは Cognitive Services API にバインドされていないため、この使用に対しては課金されません。 ただし、1 日あたりの毎日のエンリッチメントの数を少数に制限する**無料**リソースのオプションをオーバーライドするには、引き続き [Cognitive Services リソースをアタッチ](cognitive-search-attach-cognitive-services.md)する必要があります。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.SplitSkill 

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| パラメーター名     | 説明 |
|--------------------|-------------|
| textSplitMode      | "pages" または "sentences" のいずれか | 
| maximumPageLength | textSplitMode が "pages" に設定されている場合、`String.Length` で測定されるため、これは ページの最大長を指します。 最小値は 100 です。  textSplitMode が "pages" に設定されている場合、アルゴリズムは、最大でも "maximumPageLength" のサイズであるチャンクにテキストを分割しようとします。 この場合、アルゴリズムはできる限り文の境界で文を区切ろうとするため、チャンクのサイズは "maximumPageLength" よりも少し小さくなることがあります。 | 
| defaultLanguageCode   | (省略可能) 次の言語コードのいずれか: `da, de, en, es, fi, fr, it, ko, pt`。 既定値は英語 (en) です。 次の考慮事項があります。<ul><li>languagecode-countrycode 形式を渡す場合、形式の languagecode 部分のみが使用されます。</li><li>上記のリストに言語がない場合は、分割スキルによってテキストが文字境界で分割されます。</li><li>言語コードを指定することで、中国語、日本語、韓国語などのスペースのない言語で、単語が途中で分割されるのを避けることができます。</li></ul>  |


## <a name="skill-inputs"></a>スキルの入力

| パラメーター名       | 説明      |
|----------------------|------------------|
| text  | 部分文字列に分割するテキスト。 |
| languageCode  | (省略可能) ドキュメントの言語コード。  |

## <a name="skill-outputs"></a>スキルの出力 

| パラメーター名     | 説明 |
|--------------------|-------------|
| textItems | 抽出された部分文字列の配列。 |


##  <a name="sample-definition"></a>定義例

```json
{
    "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
    "textSplitMode" : "pages", 
    "maximumPageLength": 1000,
    "defaultLanguageCode": "en",
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
            "name": "textItems",
            "targetName": "mypages"
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
            "data": {
                "text": "This is a the loan application for Joe Romero, a Microsoft employee who was born in Chile and who then moved to Australia…",
                "languageCode": "en"
            }
        },
        {
            "recordId": "2",
            "data": {
                "text": "This is the second document, which will be broken into several pages...",
                "languageCode": "en"
            }
        }
    ]
}
```

##  <a name="sample-output"></a>サンプル出力

```json
{
    "values": [
        {
            "recordId": "1",
            "data": {
                "textItems": [
                    "This is the loan…",
                    "On the second page we…"
                ]
            }
        },
        {
            "recordId": "2",
            "data": {
                "textItems": [
                    "This is the second document...",
                    "On the second page of the second doc…"
                ]
            }
        }
    ]
}
```

## <a name="error-cases"></a>エラーになる場合
言語がサポートされていない場合、警告が生成され、テキストは文字境界で分割されます。

## <a name="see-also"></a>関連項目

+ [定義済みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)
