---
title: レビュー ツールを使用してコンテンツ ワークフローを定義して使用する - Content Moderator
titlesuffix: Azure Cognitive Services
description: Azure Content Moderator ワークフロー デザイナーを使用し、コンテンツ ポリシーに基づいてカスタム ワークフローとしきい値を定義できます。
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 04/04/2019
ms.author: sajagtap
ms.openlocfilehash: 006f7d6691b8872aaa7ff8ccacff484585761d00
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59795799"
---
# <a name="define-and-use-moderation-workflows"></a>モデレーション ワークフローを定義して使用する

このガイドでは、[レビュー ツール](https://contentmoderator.cognitive.microsoft.com) Web サイトで[ワークフロー](../review-api.md#workflows)を設定して使用する方法について説明します。 ワークフローは、コンテンツをより効率的に処理するために使用できるクラウドベースのカスタマイズされたフィルターです。 ワークフローで、さまざまなサービスに接続してさまざまな方法でコンテンツをフィルター処理した後、適切なアクションを実行することができます。 このガイドでは、Content Moderator コネクタ (既定で含まれています) を使用してコンテンツをフィルター処理し、一般的なモデレーション シナリオの人間によるレビューを設定する方法について説明します。

## <a name="create-a-new-workflow"></a>新しいワークフローの作成

[Content Moderator レビュー ツール](https://contentmoderator.cognitive.microsoft.com/)に移動し、サインインします。 **[設定]** タブで **[ワークフロー]** を選択します。

![ワークフローの設定](images/2-workflows-0.png)

次の画面で **[Add Workflow]\(ワークフローの追加\)** を選択します。

![ワークフローの追加](images/2-workflows-1.png)

### <a name="assign-a-name-and-description"></a>名前と説明を割り当てる

ワークフローに名前を付け、説明を入力し、ワークフローで処理するのがテキストか画像かを選択します。

![ワークフローの名前と説明](images/image-workflow-create.PNG)

### <a name="define-evaluation-criteria"></a>評価基準の定義

次の画面で、**[If]** セクションに移動します。 上部のドロップダウン メニューで **[条件]** を選択します。 これにより、ワークフローがアクションを実行する条件を構成できます。 複数の条件を使用する場合は、代わりに **[Combination]\(組み合わせ\)** を選択します。 

次にコネクタを選択します。 この例では、**Content Moderator** を使用します。 選択するコネクタに応じて、表示されるデータ出力のオプションが変わります。 他のコネクタの設定方法については、レビュー ツールの設定ガイドの「[Connectors](./configure.md#connectors)」セクションを参照してください。

![ワークフロー コネクタの選択](images/image-workflow-connect-to.PNG)

使用する目的の出力を選択し、それを確認する条件を設定します。

![ワークフロー条件の定義](images/image-workflow-condition.PNG)

### <a name="define-the-action"></a>アクションの定義

**[Then]** セクションに移動します。ここでアクションを選択します。 次の例では、画像レビューを作成してタグを割り当てます。 必要に応じて、代替 (Else) パスを追加してそのアクションを設定することもできます。

![ワークフロー アクションの定義](images/image-workflow-action.PNG)

### <a name="save-the-workflow"></a>ワークフローの保存

ワークフロー名をメモします。ワークフロー API を使用してモデレーション ジョブを開始するには名前が必要です (後述を参照してください)。 最後に、ページ上部にある **[保存]** ボタンを使用してワークフローを保存します。

## <a name="test-the-workflow"></a>ワークフローをテストする

カスタム ワークフローを定義したところで、サンプル コンテンツを使用してワークフローをテストします。 **[ワークフロー]** に移動し、対応する **[Execute Workflow]\(ワークフローの実行\)** ボタンを選択します。

![ワークフローのテスト](images/image-workflow-execute.PNG)

この[サンプル画像](https://moderatorsampleimages.blob.core.windows.net/samples/sample2.jpg)をローカル ドライブに保存します。 次に **[Choose File(s)]\(ファイルの選択\)** を選択して、画像をアップロードします。

![ランナーと引用文が示された画像](images/sample-text.jpg)

### <a name="track-progress"></a>進捗状況の追跡

次のポップアップ ウィンドウでワークフローの進行状況を確認できます。

![ワークフロー実行の追跡](images/image-workflow-job.PNG)

### <a name="verify-workflow-action"></a>ワークフロー アクションの確認

**[レビュー]** の **[画像]** タブに移動し、新しく作成された画像レビューがあることを確認します。

![画像のレビュー](images/image-workflow-review.PNG)

## <a name="next-steps"></a>次の手順

このガイドでは、Content Moderator の[レビュー ツール](https://contentmoderator.cognitive.microsoft.com)からモデレーション ワークフローを設定して使用する方法について学習しました。 次は、[REST API ガイド](../try-review-api-workflow.md)を参照して、プログラムでワークフローを作成する方法について学習してください。
