---
title: テキスト翻訳コグニティブ スキル
titleSuffix: Azure Cognitive Search
description: テキストが評価され、レコードごとに、Azure Cognitive Search の AI エンリッチメント パイプラインで、指定した対象言語に翻訳されたテキストが返されます。
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 0953d750ee8b59e9889512bb64cfd276a0bbeb53
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97654866"
---
#   <a name="text-translation-cognitive-skill"></a>テキスト翻訳コグニティブ スキル

**テキスト翻訳** スキルでは、テキストが評価され、レコードごとに、指定した対象言語に翻訳されたテキストが返されます。 このスキルでは、Cognitive Services で使用可能な [Translator Text API v3.0](../cognitive-services/translator/reference/v3-0-translate.md) が使用されます。

この機能は、ドキュメント全体が 1 つの言語ではないことが予想される場合に役に立ち、そのような場合、翻訳して検索用のインデックスを作成する前に、テキストを 1 つの言語に正規化することができます。  また、同じテキストのコピーを複数の言語で使用できるようにしたいローカライズのユース ケースにも役立ちます。

[Translator Text API v3.0](../cognitive-services/translator/reference/v3-0-reference.md) は、リージョン対応の Cognitive Services ではないため、使用する Azure Cognitive Services またはアタッチされた Cognitive Services リソースと同じリージョンにデータが保持される保証はありません。

> [!NOTE]
> 処理の頻度を増やす、ドキュメントを追加する、または AI アルゴリズムを追加することによってスコープを拡大する場合は、[課金対象の Cognitive Services リソースをアタッチする](cognitive-search-attach-cognitive-services.md)必要があります。 Cognitive Services の API を呼び出すとき、および Azure Cognitive Search のドキュメント解析段階の一部として画像抽出するときに、料金が発生します。 ドキュメントからのテキストの抽出には、料金はかかりません。
>
> 組み込みスキルの実行は、既存の [Cognitive Services の従量課金制の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)で課金されます。 画像抽出の価格は、[Azure Cognitive Search の価格](https://azure.microsoft.com/pricing/details/search/)に関するページで説明されています。

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Text.TranslationSkill

## <a name="data-limits"></a>データ制限
レコードのサイズは、[`String.Length`](/dotnet/api/system.string.length) で測定して 50,000 文字以下にする必要があります。 データをテキスト翻訳スキルに送信する前に分割する必要がある場合は、[テキスト分割スキル](cognitive-search-skill-textsplit.md)の使用を検討してください。

## <a name="skill-parameters"></a>スキルのパラメーター

パラメーターの大文字と小文字は区別されます。

| 入力 | 説明 |
|---------------------|-------------|
| defaultToLanguageCode | (必須) 翻訳後の言語が明示的に指定されていないドキュメントに対する翻訳後の言語のコード。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/translator/language-support.md)を参照してください。 |
| defaultFromLanguageCode | (省略可能) 翻訳前の言語が明示的に指定されていないドキュメントに対する翻訳前の言語のコード。  defaultFromLanguageCode が指定されていない場合は、Translator Text API によって提供される自動言語検出を使用して、翻訳前の言語が決定されます。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/translator/language-support.md)を参照してください。 |
| suggestedFrom | (省略可能) fromLanguageCode 入力も defaultFromLanguageCode パラメーターも指定されておらず、自動言語検出も失敗した場合に、ドキュメントを翻訳するための翻訳前の言語のコード。  suggestedFrom 言語が指定されていない場合は、suggestedFrom 言語として英語 (en) が使用されます。 <br/> [サポートされる言語の完全な一覧](../cognitive-services/translator/language-support.md)を参照してください。 |

## <a name="skill-inputs"></a>スキルの入力

| 入力名     | 説明 |
|--------------------|-------------|
| text | 翻訳対象のテキスト。|
| toLanguageCode    | テキストの翻訳後の言語を示す文字列。 この入力を指定しない場合、defaultToLanguageCode を使ってテキストが翻訳されます。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/translator/language-support.md)を参照|
| fromLanguageCode  | テキストの現在の言語を示す文字列。 このパラメーターが指定されていない場合は、defaultFromLanguageCode (または、defaultFromLanguageCode が指定されていない場合は自動言語検出) を使ってテキストが翻訳されます。 <br/>[サポートされる言語の完全な一覧](../cognitive-services/translator/language-support.md)を参照|

## <a name="skill-outputs"></a>スキルの出力

| 出力名    | 説明 |
|--------------------|-------------|
| translatedText | translatedFromLanguageCode から translatedToLanguageCode へのテキスト翻訳の文字列の結果。|
| translatedToLanguageCode  | テキストが翻訳された後の言語コードを示す文字列。 複数の言語に翻訳していて、どのテキストがどの言語であるかを追跡できるようにしたい場合に便利です。|
| translatedFromLanguageCode    | テキストの翻訳前の言語コードを示す文字列。 この出力によって検出の結果がわかるので、自動言語検出オプションを選択した場合に便利です。|

##  <a name="sample-definition"></a>定義例

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
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
          "text": "We hold these truths to be self-evident, that all men are created equal."
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
}
```


##  <a name="sample-output"></a>サンプル出力

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>エラーと警告
翻訳前または翻訳後の言語としてサポートされていない言語コードを指定した場合、エラーが生成され、テキストは翻訳されません。
テキストが空の場合、警告が生成されます。
テキストが 50,000 文字を超えると、最初の 50,000 文字のみが翻訳され、警告が発行されます。

## <a name="see-also"></a>参照

+ [組み込みのスキル](cognitive-search-predefined-skills.md)
+ [スキルセットの定義方法](cognitive-search-defining-skillset.md)