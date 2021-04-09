---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/25/2020
ms.author: glenga
ms.openlocfilehash: 894a89126d1ee3ed909134f3e0dd914166568654
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "90606636"
---
キューは、[Azure portal](../articles/storage/queues/storage-quickstart-queues-portal.md) または [Microsoft Azure Storage Explorer](https://storageexplorer.com/) で確認できます。 次の手順に従って、Azure CLI でキューを確認することもできます。

1. 関数プロジェクトの *local.setting.json* ファイルを開き、接続文字列の値をコピーします。 ターミナルまたはコマンド ウィンドウで、次のコマンドを実行して、`AZURE_STORAGE_CONNECTION_STRING` という名前の環境変数を作成し、`<MY_CONNECTION_STRING>` の代わりに実際の接続文字列を貼り付けます。 (この環境変数を作成すれば、`--connection-string` 引数を使用して接続文字列を後続の各コマンドに指定する必要はありません。)

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    export AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    $env:AZURE_STORAGE_CONNECTION_STRING = "<MY_CONNECTION_STRING>"
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    set AZURE_STORAGE_CONNECTION_STRING="<MY_CONNECTION_STRING>"
    ```
    
    ---
    
1. (省略可) [`az storage queue list`](/cli/azure/storage/queue#az-storage-queue-list) コマンドを使用して、ご利用のアカウント内のストレージ キューを表示します。 このコマンドからの出力には、`outqueue` という名前のキューが含まれています。これはこのキューに対する最初のメッセージを関数が書き込んだときに作成されたものです。
    
    ```azurecli
    az storage queue list --output tsv
    ```

1. [`az storage message get`](/cli/azure/storage/message#az-storage-message-get) コマンドを使用して、このキューからメッセージ (先ほど関数をテストするときに使用した名) を読み取ります。 このコマンドは、キューから最初のメッセージを読み取って削除します。 

    # <a name="bash"></a>[bash](#tab/bash)
    
    ```bash
    echo `echo $(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}') | base64 --decode`
    ```
    
    # <a name="powershell"></a>[PowerShell](#tab/powershell)
    
    ```powershell
    [System.Text.Encoding]::UTF8.GetString([System.Convert]::FromBase64String($(az storage message get --queue-name outqueue -o tsv --query '[].{Message:content}')))
    ```
    
    # <a name="azure-cli"></a>[Azure CLI](#tab/cmd)
    
    ```azurecli
    az storage message get --queue-name outqueue -o tsv --query [].{Message:content} > %TEMP%out.b64 && certutil -decode -f %TEMP%out.b64 %TEMP%out.txt > NUL && type %TEMP%out.txt && del %TEMP%out.b64 %TEMP%out.txt /q
    ```

    このスクリプトは、certutil を使用して、ローカルの一時ファイルから Base64 でエンコードされたメッセージのコレクションをデコードします。 出力が存在しない場合、エラーが発生しているといけないので、スクリプトから `> NUL` を削除して、certutil 出力の抑制を解除してみてください。 
    
    ---
    
    メッセージ本文は [base64 でエンコード](../articles/azure-functions/functions-bindings-storage-queue-trigger.md#encoding)された状態で保存されるため、表示するメッセージをあらかじめデコードしておく必要があります。 `az storage message get` を実行すると、メッセージがキューから削除されます。 `outqueue` にメッセージが 1 つしかない場合、このコマンドを 2 回目に実行したときにメッセージは取得されず、代わりにエラーが返されます。
