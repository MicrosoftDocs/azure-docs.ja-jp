---
title: Azure Backup での VM ディスクの復元
description: Azure でバックアップおよび Recovery Services を使用して、ディスクを復元し、回復した VM を作成する方法について説明します。
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: tutorial
ms.date: 01/31/2019
ms.author: dacurwin
ms.custom: mvc
ms.openlocfilehash: ddbf2e5349a77a45155fafd07da5489d0073b093
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876394"
---
# <a name="restore-a-disk-and-create-a-recovered-vm-in-azure"></a>Azure でディスクを復元し、回復した VM を作成する
Azure Backup では、geo 冗長 Recovery コンテナーに保存される復旧ポイントが作成されます。 復旧ポイントから復元するときは、VM 全体または個々のファイルを復元することができます。 この記事では、CLI を使用して完全な VM を復元する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * 復旧ポイントをリストして選択する
> * 復旧ポイントからディスクを復元する
> * 復元されたディスクから VM を作成する

PowerShell を使用してディスクを復元し、回復した VM を作成する方法については、[PowerShell による Azure VM のバックアップと復元](backup-azure-vms-automation.md#restore-an-azure-vm)に関するページを参照してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.18 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 


## <a name="prerequisites"></a>前提条件
このチュートリアルでは、Azure Backup で保護されている Linux VM が必要です。 誤って削除した VM の復旧プロセスをシミュレートするには、復旧ポイントのディスクから VM を作成します。 Azure Backup で保護されている Linux VM が必要な場合は、「[Back up a virtual machine in Azure with the CLI](quick-backup-vm-cli.md)」 (CLI による Azure での仮想マシンのバックアップ) を参照してください。


## <a name="backup-overview"></a>Backup の概要
Azure でバックアップが開始されると、VM のバックアップ拡張機能により、特定の時点のスナップショットが取得されます。 バックアップ拡張機能は、最初のバックアップが要求されたときに VM にインストールされます。 Azure Backup は、バックアップが行われるときに VM が実行されていない場合に、基になるストレージのスナップショットも取得します。

既定では、Azure Backup はファイル システム整合性バックアップを取得します。 Azure Backup がスナップショットを取得すると、データは Recovery Services コンテナーに転送されます。 効率を最大に高めるために、Azure Backup は前回のバックアップ以降に変更されたデータ ブロックを特定し、そのデータのみを転送します。

データ転送が完了すると、スナップショットが削除され、回復ポイントが作成されます。


## <a name="list-available-recovery-points"></a>使用可能な復旧ポイントをリストする
ディスクを復元するには、回復データのソースとして復旧ポイントを選択します。 既定のポリシーでは復旧ポイントが毎日作成され、30 日間保持されるため、一連の復旧ポイントを保持し、復旧の特定の時点を選択することができます。 

使用可能な復旧ポイントのリストを表示するには、[az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) を使用します。 ディスクの復旧には、復旧ポイント**名**が使用されます。 このチュートリアルでは、使用可能な最新の復旧ポイントが必要です。 `--query [0].name` パラメーターでは、次のように最新の復旧ポイント名が選択されます。

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```


## <a name="restore-a-vm-disk"></a>VM ディスクを復元する
復旧ポイントからディスクを復元するには、まず、Azure ストレージ アカウントを作成します。 このストレージ アカウントは、復元されたディスクの格納に使用されます。 追加手順では、VM を作成するために復元されたディスクが使用されます。

1. ストレージ アカウントを作成するには、[az storage account create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create) を使用します。 ストレージ アカウント名はすべて小文字で、グローバルに一意である必要があります。 *mystorageaccount* は、次のように独自の一意の名前に置き換えます。

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. [az backup restore restore-disks](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-disks) を使用して、復旧ポイントからディスクを復元します。 *mystorageaccount* は、前述のコマンドで作成したストレージ アカウントの名前に置き換えます。 *myRecoveryPointName* は、前述の [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) コマンドから出力を取得した復旧ポイント名に置き換えます。

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```


## <a name="monitor-the-restore-job"></a>復元ジョブを監視する
復元ジョブの状態を監視するには、[az backup job list](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) を使用します。

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

出力は次の例のようになります。復元ジョブが*進行中*であることが示されています。

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

復元ジョブの*状態*が*完了*と報告されている場合、ディスクはストレージ アカウントに復元されています。


## <a name="convert-the-restored-disk-to-a-managed-disk"></a>復元されたディスクを管理ディスクに変換する
復元ジョブでは管理されていないディスクが作成されます。 ディスクから VM を作成するには、まず、マネージド ディスクに変換する必要があります。

1. [az storage account show-connection-string](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-show-connection-string) を使用して、ストレージ アカウントの接続情報を取得します。 *mystorageaccount* は、次のように、使用しているストレージ アカウントの名前に置き換えます。
    
    ```azurecli-interactive
    export AZURE_STORAGE_CONNECTION_STRING=$( az storage account show-connection-string \
        --resource-group myResourceGroup \
        --output tsv \
        --name mystorageaccount )
    ```

2. 管理されていないディスクはストレージ アカウントで保護されます。 次のコマンドは管理されていないディスクに関する情報を取得し、*uri* という名前の変数を作成します。この変数は、管理ディスクを作成する際に次の手順で使用されます。

    ```azurecli-interactive
    container=$(az storage container list --query [0].name -o tsv)
    blob=$(az storage blob list --container-name $container --query [0].name -o tsv)
    uri=$(az storage blob url --container-name $container --name $blob -o tsv)
    ```

3. これで、[az disk create](https://docs.microsoft.com/cli/azure/disk?view=azure-cli-latest#az-disk-create) を使用して回復したディスクから管理ディスクを作成できます。 前の手順の *uri* 変数は、管理ディスクのソースとして使用されます。

    ```azurecli-interactive
    az disk create \
        --resource-group myResourceGroup \
        --name myRestoredDisk \
        --source $uri
    ```

4. 回復したディスクから管理ディスクを作成したら、[az storage account delete](/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-delete) を使用して、管理されていないディスクとストレージ アカウントをクリーンアップします。 *mystorageaccount* は、次のように、使用しているストレージ アカウントの名前に置き換えます。

    ```azurecli-interactive
    az storage account delete \
        --resource-group myResourceGroup \
        --name mystorageaccount
    ```


## <a name="create-a-vm-from-the-restored-disk"></a>復元されたディスクから VM を作成する
最後の手順では、管理ディスクから VM を作成します。

1. 次のように、[az vm create](/cli/azure/vm?view=azure-cli-latest#az-vm-create) を使用して管理ディスクから VM を作成します。

    ```azurecli-interactive
    az vm create \
        --resource-group myResourceGroup \
        --name myRestoredVM \
        --attach-os-disk myRestoredDisk \
        --os-type linux
    ```

2. VM が回復したディスクから作成されていることを確認するには、次のように、[az vm list](/cli/azure/vm?view=azure-cli-latest#az-vm-list) を使用してリソース グループの VM をリストします。

    ```azurecli-interactive
    az vm list --resource-group myResourceGroup --output table
    ```


## <a name="next-steps"></a>次の手順
このチュートリアルでは、復旧ポイントからディスクを復元し、ディスクから VM を作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * 復旧ポイントをリストして選択する
> * 復旧ポイントからディスクを復元する
> * 復元されたディスクから VM を作成する

次のチュートリアルに進み、復旧ポイントからの個々のファイルの復元について学習してください。

> [!div class="nextstepaction"]
> [Azure の仮想マシンにファイルを復元する](tutorial-restore-files.md)

