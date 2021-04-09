---
title: レビュー、ワークフロー、ジョブの概念 - Content Moderator
titleSuffix: Azure Cognitive Services
description: この記事では、レビュー ツールの主要な概念であるレビュー、ワークフロー、ジョブについて説明します。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 43c39c40af6e02861211a8666fefa57c34072f32
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "96905198"
---
# <a name="content-moderation-reviews-workflows-and-jobs"></a>コンテンツ モデレーションのレビュー、ワークフロー、ジョブ

Content Moderator は、マシン支援型モデレーションと人間参加機能を組み合わせて、現実的なシナリオに最適なモデレーション プロセスを作成します。 これは、クラウドベースの[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)を介して行われます。 このガイドでは、レビュー ツールの主要な概念であるレビュー、ワークフロー、ジョブについて説明します。

## <a name="reviews"></a>レビュー

レビューでは、コンテンツがレビュー ツールにアップロードされます。 それをレビューするには、ダッシュボード上にある **[レビュー]** タブで該当するコンテンツの種類をクリックします。 レビュー画面から、適用されたタグを変更し、必要に応じて独自のカスタム タグを適用することができます。 レビューを送信すると、結果が指定のコールバック エンドポイントに送信され、コンテンツはサイトから削除されます。

> [!div class="mx-imgBorder"]
> ![[レビュー] ドロップダウン メニューが強調表示されています。 コンテンツの種類 (画像、テキスト、動画) が表示されています。](./Review-Tool-user-Guide/images/review-tab.png)

### <a name="manage-reviews"></a>レビューを管理する

ダッシュボードから、 **[管理者]**  ->  **[レビューの管理]** の順に移動して管理画面を表示します。 ここには、すべてのレビュー (保留中および完了) の一覧が表示されます。

各レビューに対する 3 つのドットの **[アクション]** ボタンをクリックすると、レビュー画面にアクセスしたり、そのレビューの履歴を調べたりできます。

> [!div class="mx-imgBorder"]
> ![[レビュー] 画面上のレビュー ツール Web サイト](./Review-Tool-user-Guide/images/manage-reviews.png)

**[検索]** ツールバーを使用すると、レビューの状態、タグ、コンテンツの種類、サブチーム、割り当てられたユーザー、および作成または変更日などのさまざまなカテゴリで、レビューを並べ替えることができます。

> [!div class="mx-imgBorder"]
> ![検索ツール バーが表示されています。 レビューの状態やタグなどの検索基準を入力するためのさまざまなコンボ ボックスがあります。](./Review-Tool-user-Guide/images/review-search.png)

レビューの作成を開始する方法については、[レビュー ツール ガイド](./review-tool-user-guide/review-moderated-images.md)を参照してください。プログラムでの実行方法については、[API コンソール ガイド](./try-review-api-review.md)を参照してください。

## <a name="workflows"></a>ワークフロー

ワークフローは、コンテンツのクラウドベースのカスタマイズされたフィルターです。 ワークフローでは、さまざまなサービスに接続してさまざまな方法でコンテンツをフィルター処理した後、適切なアクションを実行することができます。 Content Moderator コネクタを使用すると、ワークフローでモデレーション タグを自動的に適用し、送信されたコンテンツを使用してレビューを作成できます。

### <a name="view-workflows"></a>ワークフローを表示する

既存のワークフローを表示するには、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)に移動して **[管理者]**  >  **[ワークフロー]** の順に選択します。

> [!div class="mx-imgBorder"]
> ![既定のワークフロー](images/default-workflow-list.png)

ワークフローは JSON 文字列として定義されているため、プログラムでアクセスできるようになります。 ワークフローに対して **[編集]** オプションを選択してから **[JSON]** タブを選択すると、次のような JSON 式が表示されます。

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

ワークフローの作成と使用を開始する方法については、[レビュー ツール ガイド](./review-tool-user-guide/workflows.md)を参照してください。プログラムでの実行方法については、[API コンソール ガイド](./try-review-api-workflow.md)を参照してください。

## <a name="jobs"></a>ジョブ

モデレーション ジョブはコンテンツ モデレーション、ワークフロー、レビューの機能に対するある種のラッパーとして機能します。 このジョブは、Content Moderator の画像モデレーション API やテキスト モデレーション API を使用してコンテンツをスキャンし、指定したワークフローに対して確認します。 ワークフローの結果に基づいて、[レビュー ツール](./review-tool-user-guide/human-in-the-loop.md)でコンテンツのレビューを作成する場合と作成しない場合があります。 レビューとワークフローの両方をそれぞれの API を使用して作成および構成することができますが、ジョブ API を使用すると、プロセス全体の詳細なレポートを取得できます (指定したコールバック エンドポイントに送信できます)。

ジョブの使用を開始する方法については、[API コンソール ガイド](./try-review-api-job.md)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Job API コンソール](try-review-api-job.md)を試験運用して、REST API コード サンプルを使用してください。 Visual Studio と C# に精通している場合は、[ジョブの .NET クイック スタート](moderation-jobs-quickstart-dotnet.md)も確認してください。 
* レビューについては、[Review API コンソール](try-review-api-review.md)に関するページから始めて、REST API コード サンプルを使用してください。 次に、[.NET クイックスタート](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)のレビューに関するセクションを参照してください。
* 動画レビューについては、[動画レビューのクイック スタート](video-reviews-quickstart-dotnet.md)を参照し、[動画レビューにトランスクリプトを追加する方法](video-transcript-reviews-quickstart-dotnet.md)に関するページを参照してください。