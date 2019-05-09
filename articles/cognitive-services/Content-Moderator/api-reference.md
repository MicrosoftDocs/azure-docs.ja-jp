---
title: API リファレンス - Content Moderator
titlesuffix: Azure Cognitive Services
description: さまざまなコンテンツ モデレーションについて学習し、Content Moderator の各種 API を確認します。
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: reference
ms.date: 06/25/2017
ms.author: sajagtap
ms.openlocfilehash: 320638c958799bbf7fea73880fd3e27b6d598d23
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756056"
---
# <a name="content-moderator-api-reference"></a>Content Moderator API リファレンス

Azure Content Moderator API シリーズを使用するには次の方法で開始します ([資格情報の管理](review-tool-user-guide/credentials.md)に関するページも参照してください)。

- Azure Portal で、[Content Moderator API をサブスクライブ](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator)します。
- [Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)にサインアップします。 「[Try Content Moderator on the web (Web 上で Content Moderator を試す)](quick-start.md)」を参照してください。

## <a name="moderation-apis"></a>モデレート API

次の Content Moderator API を使用して、モデレーション後のワークフローを設定できます。

| 説明 | リファレンス |
| -------------------- |-------------|
| **Image Moderation API**<br /><br />画像をスキャンし、タグ、信頼度スコア、その他の抽出された情報を使用して、成人向けやきわどいコンテンツの可能性があるものを検出します。 <br /><br />この情報を使用して、モデレート後のワークフローで、コンテンツを公開、拒否、またはレビューします。 <br /><br />| [Image Moderation API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c "Image Moderation API リファレンス")   |
| **Text Moderation API**<br /><br />テキスト コンテンツをスキャンします。 不適切な用語と個人データが返されます。 <br /><br />この情報を使用して、モデレート後のワークフローで、コンテンツを公開、拒否、またはレビューします。<br /><br /> | [Text Moderation API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f "Text Moderation API リファレンス")   |
| **Video Moderation API**<br /><br />ビデオをスキャンし、成人向けやきわどいコンテンツの可能性があるものを検出します。 <br /><br />この情報を使用して、モデレート後のワークフローで、コンテンツを公開、拒否、またはレビューします。<br /><br /> | [Video Moderation API の概要](video-moderation-api.md "Video Moderation API の概要")   |
| **List Management API**<br /><br />画像とテキストの除外一覧と対象一覧を作成して管理します。 有効にした場合、**Image - Match** および **Text - Screen** 操作では、提出したコンテンツとカスタム一覧とのあいまい一致が照合されます。 <br /><br />効率を高めるため、機械学習ベースのモデレーション手順を省略できます。<br /><br /> | [List Management API リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/57cf755e3f9b070c105bd2c2/operations/57cf755e3f9b070868a1f675 "List Management API リファレンス")   |

## <a name="review-api"></a>Review API

Review API に含まれるコンポーネントを次に示します。

| 説明 | リファレンス |
| -------------------- |-------------|
| **ジョブ**<br /><br /> 画像コンテンツとテキスト コンテンツ両方のスキャンおよびレビュー モデレーション ワークフローを開始します。 モデレーション ジョブは、Image Moderation API および Text Moderation API を使用してコンテンツをスキャンします。 モデレーション ジョブは、定義済みのワークフローと既定のワークフローを使用してレビューを生成します。 <br /><br />ヒューマン モデレーターが自動割り当てタグと予測データを確認して、コンテンツ モデレーション意思決定を送信した後、Review API がすべての情報を API エンドポイントに送信します。<br /><br /> | [Job リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c5 "Job リファレンス")   |
| **レビュー**<br /><br />レビュー ツールを使用して、ヒューマン モデレーターのために画像またはテキストのレビューを直接作成します。<br /><br /> ヒューマン モデレーターが自動割り当てタグと予測データを確認して、コンテンツ モデレーション意思決定を送信した後、Review API がすべての情報を API エンドポイントに送信します。<br /><br /> | [Review リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/580519483f9b0709fc47f9c4 "Review リファレンス")   |
| **ワークフロー**<br /><br />自分のチームが作成するカスタム ワークフローに関する詳細を作成、更新、取得します。 レビュー ツールを使用してワークフローを定義します。 <br /> <br />通常、ワークフローは Content Moderator を使用しますが、レビュー ツールのコネクタとして利用可能なその他の特定の API も使用できます。<br /><br /> | [Workflow リファレンス](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59 "Workflow リファレンス")   |


