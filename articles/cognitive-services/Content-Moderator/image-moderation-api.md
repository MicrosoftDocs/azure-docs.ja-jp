---
title: 画像のモデレーション - Content Moderator
titlesuffix: Azure Cognitive Services
description: Content Moderator のコンピューター支援型画像モデレーションと human-in-the-loop (人間参加) レビュー ツールを使用して、成人向けコンテンツとわいせつなコンテンツの画像をモデレートします。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 9f1df23d1f0f24787bb9267064ffd647eda2cb74
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756046"
---
# <a name="learn-image-moderation-concepts"></a>画像モデレーションの概念を確認する

Content Moderator のコンピューター支援型画像モデレーションと [human-in-the-loop (人間参加) レビュー ツール](Review-Tool-User-Guide/human-in-the-loop.md)を使用して、成人向けコンテンツとわいせつなコンテンツの画像をモデレートします。 テキスト コンテンツの画像をスキャンしてそのテキストを抽出し、顔を検出します。 カスタム リストと画像を照合し、さらにアクションを実行できます。

## <a name="evaluating-for-adult-and-racy-content"></a>成人向けコンテンツとわいせつなコンテンツの評価

**評価**操作は、0 ～ 1 の間の信頼度スコアを返します。 また、true または false を示すブール型データも返します。 これらの値は、画像に成人向けコンテンツやわいせつなコンテンツが含まれる可能性を予測します。 画像 (ファイルまたは URL) を指定して API を呼び出すと、返される応答には次の情報が含まれます。

    "ImageModeration": {
      .............
      "adultClassificationScore": 0.019196987152099609,
      "isImageAdultClassified": false,
      "racyClassificationScore": 0.032390203326940536,
      "isImageRacyClassified": false,
      ............
      ],

> [!NOTE]
> 
> - `isImageAdultClassified` は、特定の状況で露骨な性表現や成人向けの表現と考えられる画像の潜在的な存在を表します。
> - `isImageRacyClassified` は、特定の状況で性的な示唆や成人向けの表現と考えられる画像の潜在的な存在を表します。
> - スコアは 0 ～ 1 です。 このモデルでは、スコアが高いほど、そのカテゴリに該当する可能性が高いと予測しています。 このプレビューは、人がコーディングした結果ではなく、統計モデルに依存しています。 独自のコンテンツを使用してテストを行い、実際の要件に合うように各カテゴリをどのように設定するかを決めることをお勧めします。
> - ブール値は、内部スコアのしきい値に応じて true または false のどちらかになります。 ユーザーは、この値を使用するか、独自のコンテンツ ポリシーに基づいてカスタムしきい値を決めるかを見極める必要があります。

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>光学式文字認識 (OCR) でのテキストの検出

**光学式文字認識 (OCR)** 操作は、画像内のテキスト コンテンツの存在を予測し、用途の 1 つとしてテキスト モデレーション用にテキストを抽出します。 言語を指定することができます。 言語を指定しないと、検出は既定で英語を使います。

応答には次の情報が含まれています。
- 元のテキスト。
- 検出されたテキスト要素とその信頼度スコア。

抽出の例を次に示します。

    "TextDetection": {
      "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
      },
      .........
      "language": "eng",
      "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
      "candidates": []
    },


## <a name="detecting-faces"></a>顔の検出

顔の検出は、画像内の顔などの個人データの検出に役立ちます。 各画像内で、潜在的な顔および潜在的な顔の数を検出します。

応答には次の情報が含まれます。

- 顔の数
- 検出された顔の位置の一覧

抽出の例を次に示します。


    "FaceDetection": {
       ......
      "result": true,
      "count": 2,
      "advancedInfo": [
      .....
      ],
      "faces": [
        {
          "bottom": 598,
          "left": 44,
          "right": 268,
          "top": 374
        },
        {
          "bottom": 620,
          "left": 308,
          "right": 532,
          "top": 396
        }
      ]
    }

## <a name="creating-and-managing-custom-lists"></a>カスタム リストの作成と管理

多くのオンライン コミュニティでは、ユーザーが画像または他の種類のコンテンツをアップロードした後、不快感を与える項目が何日、何週、何か月も繰り返し共有される可能性があります。 同じ画像または少しだけ変更されたバージョンを複数の場所から繰り返しスキャンしてフィルターで除去するコストは、高くてエラーが発生しやすいことがあります。

同じ画像を複数回モデレートする代わりに、ブロックするコンテンツのカスタム リストに不快感を与える画像を追加します。 そうすると、コンテンツ モデレーション システムは、着信した画像をカスタム リストと比較し、それ以上の処理を停止します。

> [!NOTE]
> **画像リストの数は 5 個**まで、各リスト内の**画像の数は 10,000 個**までに制限されています。
>

Content Moderator は、カスタム画像のリストを管理するための操作を含む完全な [Image List Management API](try-image-list-api.md) を提供します。 [画像リスト API コンソール](try-image-list-api.md)を起動し、REST API コード サンプルを使用します。 また、Visual Studio および C# に精通している場合は、[.NET での画像リストのクイック スタート](image-lists-quickstart-dotnet.md)を確認してください。

## <a name="matching-against-your-custom-lists"></a>カスタム リストとの照合

照合操作では、リスト操作で作成および管理されたカスタム リストに対して受信した画像のあいまい一致を実行できます。

一致が見つかった場合、操作は一致した画像の識別子とモデレーション タグを返します。 応答には次の情報が含まれます。

- 一致スコア (0 ～ 1)
- 一致した画像
- 画像タグ (以前のモデレーション中に割り当てられたもの)
- 画像ラベル

抽出の例を次に示します。

    {
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
    }

## <a name="human-review-tool"></a>目視レビュー ツール

さらに微妙な場合は、Content Moderator の[レビュー ツール](Review-Tool-User-Guide/human-in-the-loop.md)とその API を使用し、レビューでのモデレーション結果とコンテンツをヒューマン モデレーターに示します。 モデレーターはコンピューターが割り当てたタグを調べて、その最終的な決定を確認します。

![人によるモデレーション用のイメージ レビュー](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>次の手順

[画像モデレーション API コンソール](try-image-api.md)を試験運用して、REST API コード サンプルを使用します。 また、Visual Studio および C# に精通している場合は、[.NET での画像モデレーションのクイックスタート](image-moderation-quickstart-dotnet.md)を確認します。
