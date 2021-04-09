---
title: Content Moderator REST API クイックスタート
titleSuffix: Azure Cognitive Services
description: このクイックスタートでは、Azure Content Moderator REST API の使用を開始する方法について説明します。 法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、コンテンツ フィルタリング ソフトウェアをアプリに組み込みます。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: 859c57fb6caeee730d5ba937bacf5d29a25ca173
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510692"
---
Azure Content Moderator REST API の使用を開始します。 

Content Moderator は、不快感を与える可能性がある内容、リスクのある内容、その他望ましくない可能性のある内容を管理できる AI サービスです。 AI を利用したコンテンツ モデレーション サービスを使用して、テキスト、画像、ビデオをスキャンし、コンテンツ フラグを自動的に適用します。 人間のレビュー担当者チーム向けのオンライン モデレーター環境であるレビュー ツールに対して、自分のアプリを統合することができます。 法的規制に準拠したり、ユーザーに意図されている環境を維持したりするために、コンテンツ フィルタリング ソフトウェアをアプリに組み込みます。

Content Moderator REST API を使用して、次のことを行います。

* テキストのモデレート
* 画像のモデレート

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプション - [無料アカウントを作成します](https://azure.microsoft.com/free/cognitive-services/)
* Azure サブスクリプションを入手したら、Azure portal で <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="Content Moderator リソースを作成"  target="_blank">Content Moderator リソースを作成</a>し、キーとエンドポイントを取得します。 デプロイするまで待ち、 **[リソースに移動]** ボタンをクリックします。
    * 対象のアプリケーションを Content Moderator に接続するには、作成したリソースのキーとエンドポイントが必要です。 このクイックスタートで後に示すコードに、自分のキーとエンドポイントを貼り付けます。
    * Free 価格レベル (`F0`) を使用してサービスを試用し、後から運用環境用の有料レベルにアップグレードすることができます。
* [PowerShell バージョン 6.0 以降](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)、または同様のコマンド ライン アプリケーション。


## <a name="moderate-text"></a>テキストのモデレート

次のようなコマンドを使用すると、Content Moderator API が呼び出され、テキストの本文が分析されて、結果がコンソールに出力されます。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

コマンドをテキスト エディターにコピーし、次の変更を行います。

1. `Ocp-Apim-Subscription-Key` を、有効な Face サブスクリプション キーに割り当てます。
1. このクエリ URL の最初の部分を、ご自分のサブスクリプション キーに対応するエンドポイントに合わせて変更します。
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 必要に応じて、要求の本文を、分析するテキストの文字列に変更します。

変更を加えたら、コマンド プロンプトを開いて新しいコマンドを入力します。 

### <a name="examine-the-results"></a>結果を確認する

コンソール ウィンドウに、テキストのモデレーションの結果が JSON データとして表示されます。 次に例を示します。

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

Content Moderator 画面に表示されるテキストの属性の詳細については、[テキストのモデレーションの概念](../../text-moderation-api.md)ガイドをご覧ください。

## <a name="moderate-images"></a>画像のモデレート

次のようなコマンドを使用すると、Content Moderator API が呼び出され、リモートの画像がモデレートされて、結果がコンソールに出力されます。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

コマンドをテキスト エディターにコピーし、次の変更を行います。

1. `Ocp-Apim-Subscription-Key` を、有効な Face サブスクリプション キーに割り当てます。
1. このクエリ URL の最初の部分を、ご自分のサブスクリプション キーに対応するエンドポイントに合わせて変更します。
1. 必要に応じて、要求本文の `"Value"` URL を、モデレートするリモートの画像に変更します。

> [!TIP]
> また、バイト データを要求本文に渡すことによって、ローカルの画像をモデレートすることもできます。 これを行う方法については、[リファレンス ドキュメント](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)をご覧ください。

変更を加えたら、コマンド プロンプトを開いて新しいコマンドを入力します。 

### <a name="examine-the-results"></a>結果を確認する

コンソール ウィンドウに、画像のモデレーションの結果が JSON データとして表示されます。 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

Content Moderator 画面に表示される画像の属性の詳細については、[画像のモデレーションの概念](../../image-moderation-api.md)ガイドを参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

Cognitive Services サブスクリプションをクリーンアップして削除したい場合は、リソースまたはリソース グループを削除することができます。 リソース グループを削除すると、それに関連付けられている他のリソースも削除されます。

* [ポータル](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Content Moderator REST API を使用して、モデレーション タスクを行う方法について学習しました。 次は、概念的なガイドを読んで、画像や他のメディアのモデレーションに関する詳細について学習します。

* [画像のモデレートの概念](../../image-moderation-api.md)
* [テキスト モデレーションの概念](../../text-moderation-api.md)
* [Azure Content Moderator とは](../../overview.md)