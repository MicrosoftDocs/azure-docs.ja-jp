---
title: REST API コンソールでモデレーション レビューを作成する - Content Moderator
titleSuffix: Azure Cognitive Services
description: Azure Content Moderator Review API を使用して、人によるモデレーションのために画像レビューまたはテキスト レビューを作成します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: pafarley
ms.openlocfilehash: 479c7c455f07d098edd327196803e85df24dfb6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905139"
---
# <a name="create-human-reviews-api-console"></a>人によるレビューの作成 (API コンソール)

[レビュー](./review-api.md#reviews)により、人であるモデレーターがアクセス可能なコンテンツが格納されて表示されます。 ユーザーがレビューを完了すると、結果が指定のコールバック エンドポイントに送信されます。 このガイドでは、レビュー REST API を API コンソール経由で使用してレビューを設定する方法について説明します。 API の構造を理解すれば、これらの呼び出しを REST と互換性のあるプラットフォームに簡単に移植することができます。

## <a name="prerequisites"></a>前提条件

- Content Moderator [レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) サイトにサインインするか、アカウントを作成します。

## <a name="create-a-review"></a>レビューを作成する

レビューを作成するには、 **[Review - Create](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4)** API リファレンス ページに移動し、ご自分のキーのリージョンのボタン ([レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)の **[資格情報]** ページのエンドポイント URL 内にあります) を選択します。 これにより API コンソールが開始されます。ここで REST API 呼び出しを簡単に構築し実行することができます。

![Review - Get リージョン選択](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>REST 呼び出しパラメーターを入力する

**teamName** と **Ocp-Apim-Subscription-Key** の値を入力します。

- **teamName**:[レビュー ツール](https://contentmoderator.cognitive.microsoft.com/) アカウントの設定時に作成したチーム ID (レビュー ツールの [資格情報] 画面の **[ID]** フィールド内にあります)。
- **Ocp-Apim-Subscription-Key**: ご自分の Content Moderator キー。 [レビュー ツール](https://contentmoderator.cognitive.microsoft.com)の **[設定]** タブ内にあります。

### <a name="enter-a-review-definition"></a>レビューの定義を入力する

**[要求本文]** ボックスを編集して、JSON 要求を次のフィールドとともに入力します。

- **Metadata**:コールバック エンドポイントに返される、カスタムのキー/値のペア。 キーが[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)で定義された短いコードの場合は、タグとして表示されます。
- **Content**: イメージや動画コンテンツの場合、コンテンツを指す URL 文字列です。 テキスト コンテンツの場合、実際のテキスト文字列です。
- **ContentId**:カスタム識別子の文字列。 この文字列は API に渡され、コールバックで返されます。 これは、内部識別子またはメタデータをモデレーション ジョブの結果に関連付けるために役立ちます。
- **CallbackEndpoint**: (省略可能) レビューが完了したときにコールバック情報を受信する URL。

既定の要求本文は、作成可能なさまざまな種類のレビューの例を示します。

```json
[Image]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Image",
    "Content": "<Content Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Text]
[
  {
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      }
    ],
    "Type": "Text",
    "Content": "<Your Text Content>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>"
  }
]
[Video]
[
  {
    "VideoFrames":[
      {
          "Id": "<Frame Id>",
          "Timestamp": "<Frame Timestamp",
          "FrameImage":"<Frame Image URL",
          "Metadata": [
            {
              "Key": "<Key>",
              "Value": "<Value"
            }
          ],
          "ReviewerResultTags": [
          ]
    ], 
    "Metadata": [
      {
        "Key": "string",
        "Value": "string"
      },
      //For encrypted Videos
        {
          "Key": "protectedType",
          "Value": "AES or FairPlay or Widevine or Playready"
        },
        {
          "Key": "authenticationToken",
          "Value": "your viewtoken(In case of Video Indexer AES encryption type, this value is viewtoken from breakdown json)"
        },
      //For FairPlay encrypted type video include certificateUrl as well
        {
          "Key": "certificateUrl",
          "Value": "your certificate url"
        }
    ],
    "Type": "Video",
    "Content": "<Stream Url>",
    "ContentId": "<Your identifier for this content>",
    "CallbackEndpoint": "<Url where you would receive callbacks>",
    [Optional]
    "Timescale": "<Timescale of the video>
  }
]
```

### <a name="submit-your-request"></a>要求を送信する
  
**[送信]** を選択します。 操作が成功すると、 **[応答の状態]** は `200 OK` になり、 **[応答のコンテンツ]** ボックスにはレビューの ID が表示されます。 次の手順で使用するためにこの ID をコピーします。

![[Review - Create]\(Review - Create\) コンソールの [応答のコンテンツ] ボックスに表示されるレビュー ID](images/test-drive-review-2.PNG)

### <a name="examine-the-new-review"></a>新しいコードを確認する

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)で、 **[確認]**  >  **[イメージ]** / **[テキスト]** / **[ビデオ]** を (使用したコンテンツに応じて) 選択します。 アップロードしたコンテンツが表示され、人によるレビューが可能な状態になります。

![レビュー ツール画像、サッカー ボール](images/test-drive-review-5.PNG)

## <a name="get-review-details"></a>レビューの詳細を取得する

既存のレビューに関する詳細を取得するには、[Review - Get](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c2) API リファレンス ページに移動し、ご自分のリージョン (キーが管理されているリージョン) のボタンを選択します。

![[Workflow - Get]\(ワークフロー - 取得\) のリージョン選択](images/test-drive-region.png)

上記のセクションと同様に、REST 呼び出しパラメーターを入力します。 この手順で、**reviewId** はレビューを作成するときに受信した一意の ID 文字列です。

![[Review - Create]\(Review - Create\) コンソールの取得結果](images/test-drive-review-3.PNG)
  
**[送信]** を選択します。 操作が成功すると、 **[応答の状態]** は `200 OK` になり、 **[応答のコンテンツ]** ボックスには JSON 形式でのレビュー詳細が、次のように表示されます。

```json
{  
  "reviewId":"201712i46950138c61a4740b118a43cac33f434",
  "subTeam":"public",
  "status":"Complete",
  "reviewerResultTags":[  
    {  
      "key":"a",
      "value":"False"
    },
    {  
      "key":"r",
      "value":"True"
    },
    {  
      "key":"sc",
      "value":"True"
    }
  ],
  "createdBy":"<teamname>",
  "metadata":[  
    {  
      "key":"sc",
      "value":"true"
    }
  ],
  "type":"Image",
  "content":"https://reviewcontentprod.blob.core.windows.net/<teamname>/IMG_201712i46950138c61a4740b118a43cac33f434",
  "contentId":"0",
  "callbackEndpoint":"<callbackUrl>"
}
```

応答内の次のフィールドをメモします。

- **status**
- **reviewerResultTags**: 人によるレビュー チームによってタグが手動で追加された場合に、表示されます (**createdBy** フィールドが表示されます)。
- **metadata**: 人によるレビュー チームが変更を加える前に、最初にレビューに追加されたタグが表示されます。

## <a name="next-steps"></a>次のステップ

このガイドでは、REST API を使用してコンテンツ モデレーション レビューを作成する方法について説明しました。 次に、レビューを、[E コマース モデレーション](./ecommerce-retail-catalog-moderation.md) チュートリアルなどのエンドツーエンドのモデレーション シナリオに統合します。