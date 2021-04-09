---
title: レビュー ツールの概念の説明 - Content Moderator
titleSuffix: Azure Cognitive Services
description: AI と目視レビューを組み合わせたモデレート作業を調整する Web サイトである Content Moderator レビュー ツールについて説明します。
services: cognitive-services
author: PatrickFarley
manager: mikemcca
ms.date: 03/15/2019
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.author: pafarley
ms.openlocfilehash: ddf4d17cd2631f5bc299e392c46bd9065bb13744
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "93146625"
---
# <a name="content-moderator-review-tool"></a>Content Moderator レビュー ツール

Azure Content Moderator からは、機械学習コンテンツ モデレーションと人間によるレビューを組み合わせるサービスが提供されます。 [レビュー ツール](https://contentmoderator.cognitive.microsoft.com) Web サイトは、そのようなサービスへの詳細なアクセスを提供するユーザーフレンドリなフロント エンドです。

## <a name="what-it-does"></a>実行内容

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)では、マシンによるモデレーション API と組み合わせて使用した場合に、コンテンツ モデレーション プロセス内で次のタスクを実行できます。

- 1 つのツール セットを使用して、複数の形式のコンテンツ (テキスト、画像、および動画) をモデレートする。
- モデレーション API の結果が出力されたときに目視[レビュー](../review-api.md#reviews)が作成されるよう自動化する。
- コンテンツ カテゴリまたはエクスペリエンス レベルによって編成された複数のレビュー チームにコンテンツ レビューを割り当てまたはエスカレートする。
- 既定またはカスタムのロジック フィルター ([ワークフロー](../review-api.md#workflows)) を使用することで、コードを記述せずに、コンテンツを並べ替えたり、追跡したりする。
- [コネクタ](./configure.md#connectors)を使用することで、Content Moderator API に加えて、Microsoft PhotoDNA、Text Analytics、および Face の各サービスを利用してコンテンツを処理する。
- コンテンツ モデレーション プロセスの主要なパフォーマンス メトリックを取得します。

## <a name="review-tool-dashboard"></a>レビュー ツールのダッシュボード

**[ダッシュボード]** タブでは、ツール内で実行されたコンテンツ レビューの主要なメトリックを確認できます。 画像、テキスト、および動画コンテンツのレビューの合計数、完了した数、および保留中の数が表示されます。 

**[Pending reviews]\(保留中のレビュー\)** テーブルには、レビューが保留されているか、完了しているユーザーとサブチームの内訳と SLA の残り時間が表示されます。 テーブル内の項目を選択すると、そのレビューに移動できます。 テーブルの上にある検索ボックスでは、チーム名で結果をフィルター処理できます。 **[フィルター]** アイコンでは、その他のメトリクスでフィルター処理できます。

**[Completed reviews]\(完了済みのレビュー\)** タブに切り替えると、処理済みまたは完了済みの項目の合計数がユーザーまたはサブチーム別に表示されます。 このデータは、保留中のレビューと同じようにフィルター処理できます。

ダッシュボードの右上隅にあるテキストをクリックすると、[Daily Personal Metrics]\(日次個人メトリクス\) が表示されます。これは、コンテンツの種類別に完了済みのレビュー数を報告するものです。

> [!div class="mx-imgBorder"]
> ![ブラウザーでのレビュー ツールのダッシュボード](images/0-dashboard.png)

## <a name="review-tool-credentials"></a>レビュー ツールの資格情報

[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)にサインアップすると、ご使用のアカウントの Azure リージョンの選択を求められます。 これは[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)によって、Azure Content Moderator サービスの無料試用版キーが生成されるためです。 REST 呼び出しまたはクライアント SDK からサービスにアクセスする目的でこのキーが必要になります。 ご自分のキーと API エンドポイント URL を確認するには、 **[管理者]** の **[資格情報]** を選択します。

> [!div class="mx-imgBorder"]
> ![Content Moderator 資格情報](images/settings-6-credentials.png)

## <a name="next-steps"></a>次のステップ

レビュー ツールのリソースにアクセスしたり、設定を変更したりする方法については、「[レビュー ツールの構成](./configure.md)」を参照してください。