---
title: テキスト モデレート API を使用してテキストをモデレートする - Content Moderator
titlesuffix: Azure Cognitive Services
description: オンライン コンソールでテキスト モデレート API を使用して、テキストのモデレートを試します。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 08/05/2017
ms.author: sajagtap
ms.openlocfilehash: 794638496931f72a12fcb3bd8819b04c7e2e7c97
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877050"
---
# <a name="moderate-text-from-the-api-console"></a>API コンソールからテキストをモデレートする

Azure Content Moderator で[テキスト モデレート API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) を使用して、テキスト コンテンツをスキャンします。 この操作では、不適切な表現がないかコンテンツをスキャンし、コンテンツをカスタム ブラックリストと共有ブラックリストに対して比較します。


## <a name="get-your-api-key"></a>API キーを取得する
オンライン コンソールで API を試すには、サブスクリプション キーが必要です。 これは、**[設定]** タブの **[Ocp-Apim-Subscription-Key]** ボックス内にあります。 詳細については、[概要](overview.md)に関するページをご覧ください。

## <a name="navigate-to-the-api-reference"></a>API リファレンスに移動する
[テキスト モデレート API のリファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)に移動します。 

  **[Text - Screen]**(テキスト - 選別) ページが開きます。

## <a name="open-the-api-console"></a>API コンソールを開く
**API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。 

  ![(テキスト - 選別) ページでのリージョンの選択](images/test-drive-region.png)

  **[Text - Screen]**(テキスト - 選別) API コンソールが開きます。

## <a name="select-the-inputs"></a>入力を選択する

### <a name="parameters"></a>parameters
テキスト画面で使用するクエリ パラメーターを選択します。 この例の場合、**language** には既定値を使用します。 この操作では実行の一環として、可能性の高い言語が自動的に検出されるため、これは空白のままにしておくこともできます。

> [!NOTE]
> **language** パラメーターについては、`eng` を割り当てるか、パラメーターを空のままにして、コンピューター支援による**分類**応答を表示します (プレビュー機能)。 **この機能でサポートされているのは英語のみです**。
>
> **不適切用語**の検出については、この記事の一覧に示したサポート対象言語の [ISO 639-3 コード](http://www-01.sil.org/iso639-3/codes.asp)を使用するか、空のままにします。

**[autocorrect]**、**[PII]**、および **[classify]**(プレビュー) については、**[true]** を選択します。 **[ListId]** フィールドは空白のままにしておきます。

  ![[Text - Screen]\(テキスト - 選別) コンソールのクエリ パラメーター](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>コンテンツの種類
**[Content-type]** については、選別するコンテンツの種類を選択します。 この例では、コンテンツの種類として既定値の **[text/plain]** を使用します。 **[Ocp-Apim-Subscription-Key]** ボックスにサブスクリプション キーを入力します。

### <a name="sample-text-to-scan"></a>スキャンするサンプル テキスト
**[Request body]**(要求本文) ボックスに、いくらかのテキストを入力します。 次の例は、テキスト内の意図的なタイプミスを示しています。

> [!NOTE]
> 次のサンプル テキスト内にある無効な社会保障番号は、意図的なものです。 目的は、サンプルとなる入力と出力の形式を伝えることです。

```
    Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
    These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
    Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="text-classification-feature"></a>テキスト分類機能

次の例では、Content Moderator のコンピューター支援型テキスト分類応答を示します。 これは、望ましくない可能性のあるコンテンツの検出に役立ちます。 フラグが付いたコンテンツは、コンテキストによっては不適切と見なされる可能性があります。 これは、各カテゴリに属する可能性を伝えるだけでなく、人によるコンテンツのレビューを推奨している可能性もあります。 この機能では、トレーニング済みのモデルを使用して、侮辱、軽蔑、差別と受け取られる可能性のある言葉を識別します。 これには、俗語、省略語、不快な言葉、およびレビュー備えて意図的にスペルミスを含めた用語が含まれます。 

#### <a name="explanation"></a>説明

- `Category1` は、一定の状況において性的に露骨、または成人向けであると考えられる言葉が存在する可能性を表しています。
- `Category2` は、一定の状況において性的な連想を起こさせる、または成人向けと考えられる言葉が存在する可能性を表しています。
- `Category3` は、一定の状況において不快であると考えられる言葉が存在する可能性を表しています。
- `Score` は 0 から 1 の間です。 このモデルでは、スコアが高いほど、そのカテゴリに該当する可能性が高いと予測しています。 このプレビューは、人がコーディングした結果ではなく、統計モデルに依存しています。 独自のコンテンツを使用してテストを行い、実際の要件に合うように各カテゴリをどのように設定するかを決めることをお勧めします。
- `ReviewRecommended` は、内部スコアのしきい値に応じて true または false のどちらかになります。 ユーザーは、この値を使用するか、独自のコンテンツ ポリシーに基づいてカスタムしきい値を決めるかを見極める必要があります。

### <a name="analyze-the-response"></a>応答を分析する
次の応答は、API から返されたさまざまな分析情報を示しています。 不適切である可能性のある表現、PII、分類 (プレビュー)、および自動修正済みのバージョンが含まれています。

> [!NOTE]
> コンピューター支援型の "分類" 機能はプレビュー段階にあり、英語のみをサポートしています。

```
{
    "OriginalText": "Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
    "NormalizedText": "Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation": null,
    "PII": {
            "Email": [{
                "Detected": "abcdef@abcd.com",
                "SubType": "Regular",
                "Text": "abcdef@abcd.com",
                "Index": 32
                }],
            "IPA": [{
                "SubType": "IPV4",
                "Text": "255.255.255.255",
                "Index": 72
                }],
            "Phone": [{
                "CountryCode": "US",
                "Text": "6657789887",
                "Index": 56
                }, {
                "CountryCode": "US",
                "Text": "870 608 4000",
                "Index": 211
                }, {
                "CountryCode": "UK",
                "Text": "+44 870 608 4000",
                "Index": 207
                }, {
                "CountryCode": "UK",
                "Text": "0344 800 2400",
                "Index": 227
                }, {
                "CountryCode": "UK",
                "Text": "0800 820 3300",
                "Index": 244
            }],
         "Address": [{
                 "Text": "1 Microsoft Way, Redmond, WA 98052",
                "Index": 89
            }],
            "SSN": [{
                "Text": "999999999",
                "Index": 56
            }, {
                "Text": "999-99-9999",
                "Index": 266
            }]
        },
    "Classification": {
        "ReviewRecommended": true,
        "Category1": {
            "Score": 1.5113095059859916E-06
        },
        "Category2": {
            "Score": 0.12747249007225037
        },
        "Category3": {
            "Score": 0.98799997568130493
        }
        },
    "Language": "eng",
    "Terms": [{
            "Index": 21,
            "OriginalIndex": 21,
            "ListId": 0,
         "Term": "crap"
        }],
    "Status": {
            "Code": 3000,
            "Description": "OK",
            "Exception": null
        },
     "TrackingId": "2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

JSON 応答に含まれるすべてのセクションの詳細説明については、[テキスト モデレート API の概要](text-moderation-api.md)のページを参照してください。

## <a name="next-steps"></a>次の手順

コード内で REST API を使用するか、アプリケーションと統合するための、[テキスト モデレートに関する .NET のクイック スタート](text-moderation-quickstart-dotnet.md)を利用して作業を開始します。
