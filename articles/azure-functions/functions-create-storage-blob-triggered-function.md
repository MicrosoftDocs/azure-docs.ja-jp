---
title: Blob Storage でトリガーされる Azure 関数の作成
description: Azure Functions を使用して、Blob Storage コンテナーに追加された項目によって呼び出されるサーバーレスの関数を作成します。
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.topic: how-to
ms.date: 10/01/2018
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: bf6865d2756579f457dded90b247326d2eec137c
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123124"
---
# <a name="create-a-function-in-azure-thats-triggered-by-blob-storage"></a>Blob Storage でトリガーされる Azure 関数の作成

Blob Storage コンテナーでファイルをアップロードしたり、更新したりするときにトリガーされる関数の作成方法について説明します。

## <a name="prerequisites"></a>前提条件

+ Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

これで、新しい関数アプリを作成できました。

:::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-create-success.png" alt-text="関数アプリが正常に作成されました。" border="true":::

次に、新しい Function App で関数を作成します。

<a name="create-function"></a>

## <a name="create-an-azure-blob-storage-triggered-function"></a>Azure Blob Storage でトリガーされる関数の作成

1. **[関数]** を選択し、 **[+ 追加]** を選択して、新しい関数を追加します。

   :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-template.png" alt-text="Azure portal で関数テンプレートを選択します。" border="true":::

1. **Azure Blob Storage トリガー** テンプレートを選択します。

1. 画像の下の表に指定した設定を使用してください。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png" alt-text="Blob Storage でトリガーされる関数に名前を付けて、構成します。" border="true":::

    | 設定 | 推奨値 | 説明 |
    |---|---|---|
    | **新規関数** | Function App 内で一意 | この BLOB によってトリガーされる関数の名前。 |
    | **パス**   | samples-workitems/{name}    | 監視されている Blob Storage ストレージ内の位置。 Blob のファイル名は、_name_ パラメーターとしてバインディングで渡されます。  |
    | **ストレージ アカウント接続** | AzureWebJobsStorage | Function App によって既に使用されているストレージ アカウント接続を使用するか、新しく作成できます。  |

1. 関数を作成するには **[関数を作成する]** を選択します。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-3.png" alt-text="Blob Storage でトリガーされる関数を作成します。" border="true":::

次に、**samples-workitems** コンテナーを作成します。

## <a name="create-the-container"></a>コンテナーの作成

1. 関数の **[概要]** ページで、リソース グループを選択します。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-resource-group.png" alt-text="Azure portal リソースグループを選択します。" border="true":::

1. リソース グループのストレージ アカウントを見つけて、選択します。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-account-access.png" alt-text="ストレージ アカウントにアクセスします。" border="true":::

1. **[コンテナー]** を選択し、 **[+ コンテナー]** を選択します。 

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-add-container.png" alt-text="Azure portal のストレージ アカウントにコンテナーを追加します。" border="true":::

1. **[名前]** フィールドに「`samples-workitems`」と入力して、 **[作成]** を選択します。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-name-blob-container.png" alt-text="ストレージ コンテナーに名前を付けます。" border="true":::

Blob コンテナーが用意されているので、このコンテナーにファイルをアップロードすることによって、関数をテストできます。

## <a name="test-the-function"></a>関数をテストする

1. Azure Portal に戻り、関数を参照して、ページ下部の **[ログ]** を展開して、ログ ストリーミングが一時停止していないことを確認します。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-log-expander.png" alt-text="Azure portal でログを展開します。" border="true":::

1. 別のブラウザー ウィンドウで、Azure portal のリソース グループに移動して、ストレージ アカウントを選択します。

1. **[コンテナー]** を選択し、**samples-workitems** コンテナーを選択します。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-container.png" alt-text="Azure portal で samples-workitems コンテナーに移動します。" border="true":::

1. **[アップロード]** を選択し、フォルダー アイコンを選択して、アップロードするファイルを選択します。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png" alt-text="BLOB コンテナーにファイルをアップロードします。" border="true":::

1. 画像ファイルなど、ローカル コンピューター上のファイルを参照して、そのファイルを選択します。 **[開く]** 、 **[アップロード]** の順に選択します。

1. 関数ログに戻り、Blob が読み取られたことを確認します。

    :::image type="content" source="./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png" alt-text="ログ内のメッセージを表示します。" border="true":::

    >[!NOTE]
    > 既定の従量課金プランで Function App を実行する場合、Blob が追加されたり更新されてから関数がトリガーされるまで、最高数分間の遅延が生じることがあります。 Blob でトリガーされた関数で待機時間を短くする必要がある場合は、App Service プランで Function App を実行することを検討してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次のステップ

Blob Storage で Blob が追加または更新されたときに実行する関数を作成しました。 Blob Storage トリガーの詳細については、「[Azure Functions における Blob Storage バインディング](functions-bindings-storage-blob.md)」を参照してください。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
