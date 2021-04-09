---
title: テキスト モデレート API を使用してテキストをモデレートする - Content Moderator
titleSuffix: Azure Cognitive Services
description: オンライン コンソールでテキスト モデレート API を使用して、テキストのモデレートを試します。
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: 664c4289cbfa1f6ce2fce9f9f83b0240bd2d592c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002002"
---
# <a name="moderate-text-from-the-api-console"></a>API コンソールからテキストをモデレートする

Azure Content Moderator の [Text Moderation API](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) を使用して、テキスト コンテンツの内容をスキャンし、カスタム一覧と共有一覧と比較します。

## <a name="get-your-api-key"></a>API キーを取得する

オンライン コンソールで API を試すには、サブスクリプション キーが必要です。 これは、 **[設定]** タブの **[Ocp-Apim-Subscription-Key]** ボックス内にあります。 詳細については、[概要](overview.md)に関するページを参照してください。

## <a name="navigate-to-the-api-reference"></a>API リファレンスに移動する

[テキスト モデレート API のリファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f)に移動します。 

  **[Text - Screen]** (テキスト - 選別) ページが開きます。

## <a name="open-the-api-console"></a>API コンソールを開く

**API テスト コンソールを開く場合**、実際の場所を最もよく表しているリージョンを選択します。 

  ![(テキスト - 選別) ページでのリージョンの選択](images/test-drive-region.png)

  **[Text - Screen]** \(テキスト - 選別) API コンソールが開きます。

## <a name="select-the-inputs"></a>入力を選択する

### <a name="parameters"></a>パラメーター

テキスト画面で使用するクエリ パラメーターを選択します。 この例の場合、**language** には既定値を使用します。 この操作では実行の一環として、可能性の高い言語が自動的に検出されるため、これは空白のままにしておくこともできます。

> [!NOTE]
> **language** パラメーターについては、`eng` を割り当てるか、パラメーターを空のままにして、コンピューター支援による **分類** 応答を表示します (プレビュー機能)。 **この機能でサポートされているのは英語のみです**。
>
> **不適切用語** の検出については、この記事の一覧に示したサポート対象言語の [ISO 639-3 コード](http://www-01.sil.org/iso639-3/codes.asp)を使用するか、空のままにします。

**[autocorrect]** 、 **[PII]** 、および **[classify]** (プレビュー) については、 **[true]** を選択します。 **[ListId]** フィールドは空白のままにしておきます。

  ![[Text - Screen]\(テキスト - 選別) コンソールのクエリ パラメーター](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Content type

**[Content-type]** については、選別するコンテンツの種類を選択します。 この例では、コンテンツの種類として既定値の **[text/plain]** を使用します。 **[Ocp-Apim-Subscription-Key]** ボックスにサブスクリプション キーを入力します。

### <a name="sample-text-to-scan"></a>スキャンするサンプル テキスト

**[Request body]** (要求本文) ボックスに、いくらかのテキストを入力します。 次の例は、テキスト内の意図的なタイプミスを示しています。

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 
255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.
```

## <a name="analyze-the-response"></a>応答を分析する

次の応答は、API から返されたさまざまな分析情報を示しています。 不適切である可能性のある表現、個人データ、分類 (プレビュー)、および自動修正済みのバージョンが含まれています。

> [!NOTE]
> コンピューター支援型の "分類" 機能はプレビュー段階にあり、英語のみをサポートしています。

```json
{
   "original_text":"Is this a grabage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "normalized_text":"   grabage  crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "auto_corrected_text":"Is this a garbage or crap email abcdef@abcd.com, phone: 
   6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.",
   "status":{
      "code":3000,
      "description":"OK"
   },
   "pii":{
      "email":[
         {
            "detected":"abcdef@abcd.com",
            "sub_type":"Regular",
            "text":"abcdef@abcd.com",
            "index":32
         }
      ],
      "ssn":[

      ],
      "ipa":[
         {
            "sub_type":"IPV4",
            "text":"255.255.255.255",
            "index":72
         }
      ],
      "phone":[
         {
            "country_code":"US",
            "text":"6657789887",
            "index":56
         }
      ],
      "address":[
         {
            "text":"1 Microsoft Way, Redmond, WA 98052",
            "index":89
         }
      ]
   },
   "language":"eng",
   "terms":[
      {
         "index":12,
         "original_index":21,
         "list_id":0,
         "term":"crap"
      }
   ],
   "tracking_id":"WU_ibiza_65a1016d-0f67-45d2-b838-b8f373d6d52e_ContentModerator.
   F0_fe000d38-8ecd-47b5-a8b0-4764df00e3b5"
}
```

JSON 応答に含まれるすべてのセクションの詳細説明については、[テキスト モデレート](text-moderation-api.md)に関する概念ガイドを参照してください。

## <a name="next-steps"></a>次のステップ

コードで REST API を使用するか、[.NET SDK のクイックスタート](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)に従って、アプリケーションと統合します。