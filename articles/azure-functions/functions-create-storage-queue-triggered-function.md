---
title: キュー メッセージでトリガーされる Azure 関数の作成
description: Azure Functions を使用して、Azure のキューに送信されたメッセージによって呼び出されるサーバーレスの関数を作成します。
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: c4c20579f2306b61741f3c6ab1549285271435a3
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123345"
---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Azure Queue Storage によってトリガーされる関数の作成

Azure Storage キューにメッセージが送信されたときにトリガーされる関数を作成する方法について説明します。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-create-success.png" alt-text="関数アプリが正常に作成されました。" border="true":::

次に、新しい Function App で関数を作成します。

<a name="create-function"></a>

## <a name="create-a-queue-triggered-function"></a>キューによってトリガーされる関数の作成

1. **[関数]** を選択し、 **[+ 追加]** を選択して、新しい関数を追加します。

   :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-app-quickstart-choose-template.png" alt-text="Azure portal で関数テンプレートを選択します。" border="true":::

1. **Azure Queue Storage トリガー** テンプレートを選択します。

1. 画像の下の表に指定した設定を使用してください。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png" alt-text="Queue Storage でトリガーされる関数に名前を付けて、構成します。" border="true":::


    | 設定 | 推奨値 | 説明 |
    |---|---|---|
    | **名前** | Function App 内で一意 | このキューによってトリガーされる関数の名前。 |
    | **キュー名**   | myqueue-items    | ストレージ アカウント内の接続先のキューの名前。 |
    | **ストレージ アカウント接続** | AzureWebJobsStorage | Function App によって既に使用されているストレージ アカウント接続を使用するか、新しく作成できます。  |    

1. 関数を作成するには **[関数を作成する]** を選択します。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal-3.png" alt-text="Queue Storage によってトリガーされる関数を作成します。" border="true":::

次に、Azure Storage アカウントに接続して、**myqueue-items** ストレージ キューを作成します。

## <a name="create-the-queue"></a>キューを作成する

1. 関数の **[概要]** ページで、リソース グループを選択します。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-resource-group.png" alt-text="Azure portal リソースグループを選択します。" border="true":::

1. リソース グループのストレージ アカウントを見つけて、選択します。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-account-access.png" alt-text="ストレージ アカウントにアクセスします。" border="true":::

1. **[キュー]** を選択し、 **[+ キュー]** を選択します。 

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-add-queue.png" alt-text="Azure portal のストレージ アカウントにキューを追加します。" border="true":::

1. **[名前]** フィールドに「`myqueue-items`」と入力して、 **[作成]** を選択します。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-name-queue.png" alt-text="Queue Storage コンテナーに名前を付けます。" border="true":::

これでストレージ キューが作成されたので、キューにメッセージを追加して、関数をテストできます。

## <a name="test-the-function"></a>関数をテストする

1. Azure Portal に戻り、関数を参照して、ページ下部の **[ログ]** を展開して、ログ ストリーミングが一時停止していないことを確認します。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-queue-storage-log-expander.png" alt-text="Azure portal でログを展開します。" border="true":::

1. 別のブラウザー ウィンドウで、Azure portal のリソース グループに移動して、ストレージ アカウントを選択します。

1. **[キュー]** を選択し、 **[myqueue-items]** コンテナーを選択します。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue.png" alt-text="Azure portal で myqueue-items キューに移動します。" border="true":::

1. **[メッセージの追加]** を選択して、「Hello World!」と入力します。 これを **[メッセージ テキスト]** で行います。 **[OK]** を選択します。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/functions-storage-queue-test.png" alt-text="Azure portal で myqueue-items キューに移動します。" border="true":::

1. 数秒間待ってから、関数ログに戻り、新しいメッセージがキューから読み取られていることを確認します。

    :::image type="content" source="./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png" alt-text="ログ内のメッセージを表示します。" border="true":::

1. ストレージ キューに戻り、 **[更新]** を選択し、メッセージが処理されてキュー内に存在しなくなったことを確認します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

メッセージがストレージ キューに追加されたときに実行される関数を作成しました。 Queue Storage トリガーの詳細については、「[Azure Functions における Storage キュー バインド](functions-bindings-storage-queue.md)」を参照してください。

最初の関数を作成した後は、メッセージを別のキューに書き戻す出力バインディングをこの関数に追加しましょう。

> [!div class="nextstepaction"]
> [Functions を使用して Azure Storage キューにメッセージを追加する](functions-integrate-storage-queue-output-binding.md)
