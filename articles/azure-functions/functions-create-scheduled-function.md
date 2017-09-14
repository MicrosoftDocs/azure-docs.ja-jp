---
title: "Azure でスケジュールに基づいて実行される関数を作成する | Microsoft Docs"
description: "Azure で定義したスケジュールに基づいて実行する関数を作成する方法について説明します。"
services: functions
documentationcenter: na
author: ggailey777
manager: cfowler
editor: 
tags: 
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.service: functions
ms.devlang: multiple
ms.topic: quickstart
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/31/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 4442d0038a0604d3297871907c1d05d8d3916dcf
ms.contentlocale: ja-jp
ms.lasthandoff: 06/08/2017

---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Azure でタイマーによってトリガーされる関数を作成する

Azure Functions を使用して、定義したスケジュールに基づいて実行する関数を作成する方法について説明します。

![Azure Portal での Function App の作成](./media/functions-create-scheduled-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下が必要です。

+ Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)]

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App が正常に作成されました。](./media/functions-create-first-azure-function/function-app-create-success.png)

次に、新しい Function App で関数を作成します。

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>タイマーによってトリガーされる関数の作成

1. Function App を展開し、**[関数]** の横にある **[+]** ボタンをクリックします。 これが Function App で初めての関数の場合、**[カスタム関数]** を選びます。 関数テンプレートの完全なセットが表示されます。

    ![Azure Portal での関数のクイック スタート ページ](./media/functions-create-scheduled-function/add-first-function.png)

2. 目的の言語の **TimerTrigger** テンプレートを選びます。 次の表で指定されている設定を使用してください。

    ![タイマーによってトリガーされる関数を Azure Portal で作成する。](./media/functions-create-scheduled-function/functions-create-timer-trigger.png)

    | 設定 | 推奨値 | 説明 |
    |---|---|---|
    | **関数名の指定** | TimerTriggerCSharp1 | タイマーによってトリガーされる関数の名前を定義します。 |
    | **[スケジュール](http://en.wikipedia.org/wiki/Cron#CRON_expression)** | 0 \*/1 \* \* \* \* | 関数を毎分実行するようにスケジュールする 6 つのフィールドの [CRON 式](http://en.wikipedia.org/wiki/Cron#CRON_expression)。 |

2. ページの下部にある **[Create]**」を参照してください。 選択した言語で、毎分実行する関数が作成されます。

3. ログに書き込まれたトレース情報を表示して、実行を確認します。

    ![Azure Portal の関数ログ ビューアー。](./media/functions-create-scheduled-function/functions-timer-trigger-view-logs2.png)

ここで、1 時間ごとなど、少ない頻度で実行するように、関数のスケジュールを変更できます。 

## <a name="update-the-timer-schedule"></a>タイマー スケジュールの更新

1. 関数を展開し、**[統合]** をクリックします。 ここでは、関数の入力および出力バインドを定義し、スケジュールも設定します。 

2. 「`0 0 */1 * * *`」の新しい **[スケジュール]** 値を入力し、**[保存]** をクリックします。  

![関数が、Azure Portal のタイマー スケジュールを更新します。](./media/functions-create-scheduled-function/functions-timer-trigger-change-schedule.png)

関数が 1 時間ごとに実行されるようになりました。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

スケジュールに基づいて実行する関数を作成しました。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

タイマー トリガーの詳細については、「[Azure Functions を使用したコード実行のスケジュール設定](functions-bindings-timer.md)」を参照してください。
