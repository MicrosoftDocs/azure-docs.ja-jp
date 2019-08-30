---
title: Azure でスケジュールに基づいて実行される関数を作成する | Microsoft Docs
description: Azure で定義したスケジュールに基づいて実行する関数を作成する方法について説明します。
services: functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/28/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: 13aa1f8cd643d579734cc603bbc81519a690fe59
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70085862"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Azure でタイマーによってトリガーされる関数を作成する

Azure Functions を使用して、定義したスケジュールに基づいて実行する[サーバーレス](https://azure.microsoft.com/solutions/serverless/)関数を作成する方法について説明します。

![Azure Portal での Function App の作成](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

+ Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App が正常に作成されました。](./media/functions-create-first-azure-function/function-app-create-success.png)

次に、新しい Function App で関数を作成します。

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>タイマーによってトリガーされる関数の作成

1. Function App を展開し、 **[関数]** の横にある **[+]** ボタンをクリックします。 これが関数アプリの初めての関数の場合は、 **[ポータル内]** 、 **[続行]** の順に選択します。 それ以外の場合は、手順 3 に進みます。

   ![Azure Portal での関数のクイック スタート ページ](./media/functions-create-scheduled-function/function-app-quickstart-choose-portal.png)

2. **[その他のテンプレート]** 、 **[Finish and view templates]\(終了してテンプレートを表示\)** の順に選択します。

    ![Functions のクイック スタート: [その他のテンプレート] を選択する](./media/functions-create-scheduled-function/add-first-function.png)

3. 検索フィールドに「`timer`」と入力し、次の画像の下の表に示されている設定で新しいトリガーを構成します。

    ![タイマーによってトリガーされる関数を Azure Portal で作成する。](./media/functions-create-scheduled-function/functions-create-timer-trigger-2.png)

    | Setting | 推奨値 | Description |
    |---|---|---|
    | **Name** | Default | タイマーによってトリガーされる関数の名前を定義します。 |
    | **スケジュール** | 0 \*/1 \* \* \* \* | 関数を毎分実行するようにスケジュールする 6 つのフィールドの [CRON 式](functions-bindings-timer.md#ncrontab-expressions)。 |

4. **Create** をクリックしてください。 選択した言語で、毎分実行する関数が作成されます。

5. ログに書き込まれたトレース情報を表示して、実行を確認します。

    ![Azure Portal の関数ログ ビューアー。](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

ここで、1 分ごとではなく 1 時間ごとに実行するように、関数のスケジュールを変更します。

## <a name="update-the-timer-schedule"></a>タイマー スケジュールの更新

1. 関数を展開し、 **[統合]** をクリックします。 ここでは、関数の入力および出力バインドを定義し、スケジュールも設定します。 

2. `0 0 */1 * * *` の新しい 1 時間ごとの **[スケジュール]** 値を入力し、 **[保存]** をクリックします。  

![関数が、Azure Portal のタイマー スケジュールを更新します。](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

関数が 1 時間ごとに実行されるようになりました。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次の手順

スケジュールに基づいて実行する関数を作成しました。 タイマー トリガーの詳細については、[Azure Functions を使用したコード実行のスケジュール設定](functions-bindings-timer.md)に関するページを参照してください。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
