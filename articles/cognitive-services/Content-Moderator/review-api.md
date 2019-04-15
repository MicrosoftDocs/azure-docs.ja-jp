---
title: レビュー、ワークフロー、ジョブの概念 - Content Moderator
titlesuffix: Azure Cognitive Services
description: レビュー、ワークフロー、ジョブについて説明します
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: c1d4ef640e2ae072dacba7a665b6689e3224c55c
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756289"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>コンテンツ モデレーションのレビュー、ワークフロー、ジョブ

Content Moderator は、マシン支援型モデレーションと人間参加機能を組み合わせて、現実的なシナリオに最適なモデレーション プロセスを作成します。 これは、クラウドベースの[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)を介して行われます。 このガイドでは、レビュー ツールの主要な概念であるレビュー、ワークフロー、ジョブについて説明します。

## <a name="reviews"></a>レビュー

レビューでは、コンテンツがレビュー ツールにアップロードされ、**[レビュー]** タブに表示されます。ここから、ユーザーは適用されたタグを変更し、必要に応じて独自のカスタム タグを適用することができます。 ユーザーがレビューを送信すると、結果が指定のコールバック エンドポイントに送信され、コンテンツはサイトから削除されます。

![レビュー ツールの Web サイトがブラウザーで開き、[レビュー] タブに表示されます。](./Review-Tool-user-Guide/images/image-workflow-review.png)

レビューの作成を開始する方法については、[レビュー ツール ガイド](./review-tool-user-guide/review-moderated-images.md)のページを参照してください。プログラムでの実行方法については、[REST API ガイド](./try-review-api-review.md)のページを参照してください。

## <a name="workflows"></a>Workflows

ワークフローは、コンテンツのクラウドベースのカスタマイズされたフィルターです。 ワークフローで、さまざまなサービスに接続してさまざまな方法でコンテンツをフィルター処理した後、適切なアクションを実行することができます。 Content Moderator コネクタを使用すると、ワークフローでモデレーション タグを自動的に適用し、送信されたコンテンツを使用してレビューを作成できます。

### <a name="view-workflows"></a>ワークフローを表示する

既存のワークフローを表示するには、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)に移動して **[設定]** > **[ワークフロー]** の順に選択します。

![既定のワークフロー](images/default-workflow-listed.PNG)

ワークフローは JSON 文字列として完全に記述することができるため、プログラムでアクセスできます。 ワークフローに対して **[編集]** オプションを選択してから **[JSON]** タブを選択すると、次のような JSON 式が表示されます。

```json
{
    "Type": "Logic",
    "If": {
        "ConnectorName": "moderator",
        "OutputName": "isAdult",
        "Operator": "eq",
        "Value": "true",
        "Type": "Condition"
        },
    "Then": {
    "Perform": [
    {
        "Name": "createreview",
        "CallbackEndpoint": null,
        "Tags": []
    }
    ],
    "Type": "Actions"
    }
}
```

ワークフローの作成と使用を開始する方法については、[レビュー ツール ガイド](./review-tool-user-guide/workflows.md)のページを参照してください。プログラムでの実行方法については、[REST API ガイド](./try-review-api-workflow.md)のページを参照してください。

## <a name="jobs"></a>[ジョブ]

モデレーション ジョブはコンテンツ モデレーション、ワークフロー、レビューの機能に対するある種のラッパーとして機能します。 このジョブは、Content Moderator の画像モデレーション API やテキスト モデレーション API を使用してコンテンツをスキャンし、指定したワークフローに対して確認します。 ワークフローの結果に基づいて、[レビュー ツール](./review-tool-user-guide/human-in-the-loop.md)でコンテンツのレビューを作成する場合と作成しない場合があります。 レビューとワークフローの両方をそれぞれの API を使用して作成および構成することができますが、ジョブ API を使用すると、プロセス全体の詳細なレポートを取得できます (指定したコールバック エンドポイントに送信できます)。

ジョブの使用を開始する方法については、[REST API ガイド](./try-review-api-job.md)のページを参照してください。

## <a name="next-steps"></a>次の手順

* [Job API コンソール](try-review-api-job.md)を試験運用して、REST API コード サンプルを使用してください。 Visual Studio と C# に精通している場合は、[ジョブの .NET クイック スタート](moderation-jobs-quickstart-dotnet.md)も確認してください。 
* レビューについては、[Review API コンソール](try-review-api-review.md)に関するページから始めて、REST API コード サンプルを使用してください。 次に、[レビュー の.NET クイック スタート](moderation-reviews-quickstart-dotnet.md)を参照してください。
* 動画レビューについては、[動画レビューのクイック スタート](video-reviews-quickstart-dotnet.md)を参照し、[動画レビューにトランスクリプトを追加する方法](video-transcript-reviews-quickstart-dotnet.md)に関するページを参照してください。
