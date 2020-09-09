---
title: ナレッジ ベースと共にアクティブ ラーニングを使用する - QnA Maker
description: アクティブ ラーニングを使用してナレッジ ベースの質を向上させます。 既存の質問の削除や変更は行わずに、レビュー、承認、却下を行います。
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 004ee9d4cced0116831718b1601d5d510dea5a4d
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87090928"
---
# <a name="use-active-learning-to-improve-your-knowledge-base"></a>アクティブ ラーニングを使用してナレッジ ベースを改善する

[アクティブ ラーニング](../Concepts/active-learning-suggestions.md)を使うと、代わりの質問を提案することで、ナレッジ ベースの品質を改善できます。 ユーザーの送信が考慮されて、代わりの質問の一覧に提案として表示されます。 それらの提案を代わりの質問として追加するか拒否するかを柔軟に選択できます。

ナレッジ ベースが自動的に変更されることはありません。 変更を有効にするためには、提案を受け入れる必要があります。 これらの提案によって質問が追加されますが、既存の質問の変更や削除は行われません。


## <a name="upgrade-runtime-version-to-use-active-learning"></a>アクティブ ラーニングを使用するためにランタイム バージョンをアップグレードする

アクティブ ラーニングは、ランタイム バージョン 4.4.0 以上でサポートされています。 ナレッジ ベースが以前のバージョンで作成された場合は、この機能を使用するために[ランタイムをアップグレード](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)します。

## <a name="turn-on-active-learning-for-alternate-questions"></a>代わりの質問のためにアクティブ ラーニングを有効にする

アクティブ ラーニングは、既定では無効になっています。 これを有効にして、提案された質問を表示します。 アクティブ ラーニングを有効にした後は、クライアント アプリから QnA Maker に情報を送信する必要があります。 詳細については、「[ボットから GenerateAnswer および Train API を使用するためのアーキテクチャの流れ](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot)」を参照してください。

1. **[発行]** を選択してナレッジ ベースを発行します。 アクティブ ラーニング クエリは、GenerateAnswer API 予測エンドポイントからのみ収集されます。 QnA Maker ポータルの [テスト] ウィンドウへのクエリは、アクティブ ラーニングには影響しません。

1. QnA Maker ポータルでアクティブ ラーニングを有効にするには、右上隅に移動して自分の**名前**を選択し、[**Service settings\(サービス設定\)** ](https://www.qnamaker.ai/UserSettings) に移動します。

    ![[Service settings]\(サービス設定\) ページで、アクティブ ラーニングの提案された代わりの質問を有効にします。 右上のメニューで自分のユーザー名を選択し、[Service Settings]\(サービス設定\) を選択します。](../media/improve-knowledge-base/Endpoint-Keys.png)


1. QnA Maker サービスを見つけて **[Active Learning] (アクティブ ラーニング)** を切り替えます。

    > [!div class="mx-imgBorder"]
    > [![[Service settings]\(サービス設定\) ページで、アクティブ ラーニング機能をオンに切り替えます。機能を切り替えられないときは、サービスをアップグレードしなければならない場合があります。](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    > [!Note]
    > 上の画像の具体的なバージョンは、単なる例として表示されています。 実際のバージョンは、異なる場合があります。

    **[Active Learning]\(アクティブ ラーニング\)** が有効になると、ユーザーが送信した質問に基づいて、ナレッジ ベースから定期的に新しい質問が提案されます。 設定を再度切り替えると、 **[Active Learning] (アクティブ ラーニング)** を無効にできます。

## <a name="review-suggested-alternate-questions"></a>推奨される代わりの質問を確認する

各ナレッジ ベースの **[編集]** ページで、[代わりに推奨された質問を確認](improve-knowledge-base.md)します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [ナレッジ ベースの作成](./manage-knowledge-bases.md)