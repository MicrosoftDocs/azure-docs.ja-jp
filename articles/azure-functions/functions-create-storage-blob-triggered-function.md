---
title: Blob Storage でトリガーされる Azure 関数の作成 | Microsoft Docs
description: Azure Functions を使用して、Azure Blob Storage に追加された項目によって呼び出されるサーバーレスの関数を作成します。
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: d6bff41c-a624-40c1-bbc7-80590df29ded
ms.service: azure-functions
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/01/2018
ms.author: glenga
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: cc730650f7bd570f237c06b074afb3678b54448f
ms.sourcegitcommit: 644de9305293600faf9c7dad951bfeee334f0ba3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2019
ms.locfileid: "54901464"
---
# <a name="create-a-function-triggered-by-azure-blob-storage"></a>Azure Blob Storage によってトリガーされる関数の作成

Azure Blob Storage でファイルをアップロードしたり、更新したりするときにトリガーされる関数の作成方法について説明します。

![ログ内のメッセージを表示します。](./media/functions-create-storage-blob-triggered-function/function-app-in-portal-editor.png)

## <a name="prerequisites"></a>前提条件

+ [Microsoft Azure ストレージ エクスプローラーをダウンロードしてインストールする](https://storageexplorer.com/)。
+ Azure サブスクリプション。 お持ちでない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

## <a name="create-an-azure-function-app"></a>Azure Function App の作成

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

![Function App が正常に作成されました。](./media/functions-create-first-azure-function/function-app-create-success.png)

次に、新しい Function App で関数を作成します。

<a name="create-function"></a>

## <a name="create-a-blob-storage-triggered-function"></a>Blob Storage でトリガーされる関数の作成

1. Function App を展開し、**[関数]** の横にある **[+]** ボタンをクリックします。 これが関数アプリの初めての関数の場合は、**[ポータル内]**、**[続行]** の順に選択します。 それ以外の場合は、手順 3 に進みます。

   ![Azure Portal での関数のクイック スタート ページ](./media/functions-create-storage-blob-triggered-function/function-app-quickstart-choose-portal.png)

1. **[その他のテンプレート]**、**[Finish and view templates]\(終了してテンプレートを表示\)** の順に選択します。

    ![Functions のクイック スタート: [その他のテンプレート] を選択する](./media/functions-create-storage-blob-triggered-function/add-first-function.png)

1. 検索フィールドに「`blob`」と入力し、**BLOB トリガー** テンプレートを選択します。

1. プロンプトが表示されたら、**[インストール]** を選択して Azure Storage 拡張機能とすべての依存関係を関数アプリにインストールします。 インストールが正常に完了したら、**[続行]** を選択します。

    ![バインディング拡張機能をインストールする](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal.png)

1. 画像の下の表に指定した設定を使用してください。

    ![Blob Storage でトリガーされる関数を作成します。](./media/functions-create-storage-blob-triggered-function/functions-create-blob-storage-trigger-portal-2.png)

    | Setting | 推奨値 | Description |
    |---|---|---|
    | **Name** | Function App 内で一意 | この BLOB によってトリガーされる関数の名前。 |
    | **パス**   | samples-workitems/{name}    | 監視されている Blob Storage ストレージ内の位置。 Blob のファイル名は、_name_ パラメーターとしてバインディングで渡されます。  |
    | **ストレージ アカウント接続** | AzureWebJobsStorage | Function App によって既に使用されているストレージ アカウント接続を使用するか、新しく作成できます。  |

1. **[作成]** をクリックして関数を作成します。

次に、Azure Storage アカウントに接続し、**samples-workitems** コンテナーを作成します。

## <a name="create-the-container"></a>コンテナーの作成

1. 関数で、**[統合]** をクリックし、**[ドキュメント]** を展開して、**[アカウント名]** と **[アカウント キー]** の両方をコピーします。 これらの資格情報を使用して、ストレージ アカウントに接続します。 ストレージ アカウントに既に接続している場合は、手順 4 に進みます。

    ![ストレージ アカウント接続の資格情報を取得します。](./media/functions-create-storage-blob-triggered-function/functions-storage-account-connection.png)

1. [Microsoft Azure Storage Explorer](https://storageexplorer.com/) ツールを実行し、左側の接続アイコンをクリックして、**[Use a storage account name and key] \(ストレージ アカウント名とキーを使用)** を選択し、**[次へ]** をクリックします。

    ![ストレージ アカウント エクスプローラー ツールを実行します。](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-1.png)

1. 手順 1 の **[アカウント名]** と **[アカウント キー]** を入力し、**[次へ]** をクリックし、**[接続]** をクリックします。 

    ![ストレージ資格情報を入力し、接続します。](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-connect-2.png)

1. 接続されたストレージ アカウントを展開し、**[Blob containers] \(Blob コンテナー)** を右クリックして、**[Create blob container] \(Blob コンテナーの作成)** をクリックし、「`samples-workitems`」と入力して、Enter キーを押します。

    ![ストレージ キューを作成します。](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-create-blob-container.png)

Blob コンテナーが用意されているので、このコンテナーにファイルをアップロードすることによって、関数をテストできます。

## <a name="test-the-function"></a>関数をテストする

1. Azure Portal に戻り、関数を参照して、ページ下部の **[ログ]** を展開して、ログ ストリーミングが一時停止していないことを確認します。

1. Storage Explorer で、ストレージ アカウント、**[Blob containers]** \(BLOB コンテナー\)、**[samples-workitems]** の順に展開します。 **[アップロード]**、**[Upload files...] \(ファイルをアップロードしています...)** の順にクリックします。

    ![Blob コンテナーにファイルをアップロードします。](./media/functions-create-storage-blob-triggered-function/functions-storage-manager-upload-file-blob.png)

1. **[Upload files] \(ファイルのアップロード)** ダイアログ ボックスで、**[ファイル]** フィールドをクリックします。 画像ファイルなど、ローカル コンピューター上のファイルを参照して選択し、**[開く]**、**[アップロード]** の順にクリックします。

1. 関数ログに戻り、Blob が読み取られたことを確認します。

   ![ログ内のメッセージを表示します。](./media/functions-create-storage-blob-triggered-function/functions-blob-storage-trigger-view-logs.png)

    >[!NOTE]
    > 既定の従量課金プランで Function App を実行する場合、Blob が追加されたり更新されてから関数がトリガーされるまで、最高数分間の遅延が生じることがあります。 Blob でトリガーされた関数で待機時間を短くする必要がある場合は、App Service プランで Function App を実行することを検討してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>次の手順

Blob Storage で Blob が追加または更新されたときに実行する関数を作成しました。 Blob Storage トリガーの詳細については、「[Azure Functions における Blob Storage バインディング](functions-bindings-storage-blob.md)」を参照してください。

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]