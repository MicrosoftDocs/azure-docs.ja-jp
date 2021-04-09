---
title: スケジュールに基づいて実行される関数を Azure で作成する
description: Azure portal を使用して、定義したスケジュールに基づいて実行される関数を作成する方法について説明します。
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 14d918cc41f49b954f5cabf48572db5df829fd10
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98035191"
---
# <a name="create-a-function-in-the-azure-portal-that-runs-on-a-schedule"></a>スケジュールに基づいて実行される関数を Azure portal で作成する

Azure portal を使用して、定義したスケジュールに基づき、Azure で[サーバーレスに](https://azure.microsoft.com/solutions/serverless/)実行される関数を作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

+ Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-a-function-app"></a>Function App を作成する

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

新しい関数アプリを使用する準備ができました。 次に、新しい関数アプリで関数を作成します。

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="正常に作成された関数アプリ。" border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>タイマーによってトリガーされる関数の作成

1. 関数アプリで、 **[関数]** を選択し、 **[+ 追加]** を選択します。 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Azure portal で関数を追加する。" border="true":::

1. **[タイマー トリガー]** テンプレートを選択します。 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Azure portal で [タイマー トリガー] を選択する。" border="true":::

1. 画像の下の表に示されている設定で新しいトリガーを構成し、 **[関数の作成]** を選択します。

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="タイマー トリガー テンプレートが選択されている新しい関数ページを示すスクリーンショット。" border="true":::
    
    | 設定 | 推奨値 | 説明 |
    |---|---|---|
    | **名前** | Default | タイマーによってトリガーされる関数の名前を定義します。 |
    | **[スケジュール]** | 0 \*/1 \* \* \* \* | 関数を毎分実行するようにスケジュールする 6 つのフィールドの [CRON 式](functions-bindings-timer.md#ncrontab-expressions)。 |

## <a name="test-the-function"></a>関数をテストする

1. 関数で、 **[コードとテスト]** を選択し、ログを展開します。

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Azure portal でタイマー トリガーをテストする。" border="true":::

1. ログに書き込まれた情報を表示して、実行を確認します。

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Azure portal でタイマー トリガーを表示する。" border="true":::

ここで、1 分ごとではなく 1 時間ごとに実行するように、関数のスケジュールを変更します。

## <a name="update-the-timer-schedule"></a>タイマー スケジュールの更新

1. 関数で、 **[統合]** を選択します。 ここでは、関数の入力および出力バインドを定義し、スケジュールも設定します。 

1. **[タイマー (myTimer)]** を選択します。

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Azure portal でタイマー スケジュールを更新する。" border="true":::

1. **[スケジュール]** の値を `0 0 */1 * * *` に更新し、 **[保存]** を選択します。  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Azure portal で関数のタイマー スケジュールを更新する。" border="true":::

関数が 1 時間ごと (正時) に実行されるようになりました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

スケジュールに基づいて実行する関数を作成しました。 タイマー トリガーの詳細については、[Azure Functions を使用したコード実行のスケジュール設定](functions-bindings-timer.md)に関するページを参照してください。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
