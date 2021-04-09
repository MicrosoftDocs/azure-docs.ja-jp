---
title: チュートリアル - Azure CLI を使用した VM の復元
description: Azure でバックアップおよび Recovery Services を使用して、ディスクを復元し、回復した VM を作成する方法について説明します。
ms.topic: tutorial
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 999682c9bf4a4d70d886f0e85cede99f215aa046
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "97694717"
---
# <a name="restore-a-vm-with-azure-cli"></a>Azure CLI を使用した VM の復元

Azure Backup では、geo 冗長 Recovery コンテナーに保存される復旧ポイントが作成されます。 復旧ポイントから復元するときは、VM 全体または個々のファイルを復元することができます。 この記事では、CLI を使用して完全な VM を復元する方法について説明します。 このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
>
> * 復旧ポイントをリストして選択する
> * 復旧ポイントからディスクを復元する
> * 復元されたディスクから VM を作成する

PowerShell を使用してディスクを復元し、回復した VM を作成する方法については、[PowerShell による Azure VM のバックアップと復元](backup-azure-vms-automation.md#restore-an-azure-vm)に関するページを参照してください。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - このチュートリアルには、Azure CLI のバージョン 2.0.18 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

 - このチュートリアルでは、Azure Backup で保護されている Linux VM が必要です。 誤って削除した VM の復旧プロセスをシミュレートするには、復旧ポイントのディスクから VM を作成します。 Azure Backup で保護されている Linux VM が必要な場合は、「[Back up a virtual machine in Azure with the CLI](quick-backup-vm-cli.md)」 (CLI による Azure での仮想マシンのバックアップ) を参照してください。

## <a name="backup-overview"></a>Backup の概要

Azure でバックアップが開始されると、VM のバックアップ拡張機能により、特定の時点のスナップショットが取得されます。 バックアップ拡張機能は、最初のバックアップが要求されたときに VM にインストールされます。 Azure Backup は、バックアップが行われるときに VM が実行されていない場合に、基になるストレージのスナップショットも取得します。

既定では、Azure Backup はファイル システム整合性バックアップを取得します。 Azure Backup がスナップショットを取得すると、データは Recovery Services コンテナーに転送されます。 効率を最大に高めるために、Azure Backup は前回のバックアップ以降に変更されたデータ ブロックを特定し、そのデータのみを転送します。

データ転送が完了すると、スナップショットが削除され、回復ポイントが作成されます。

## <a name="list-available-recovery-points"></a>使用可能な復旧ポイントをリストする

ディスクを復元するには、回復データのソースとして復旧ポイントを選択します。 既定のポリシーでは復旧ポイントが毎日作成され、30 日間保持されるため、一連の復旧ポイントを保持し、復旧の特定の時点を選択することができます。

使用可能な復旧ポイントのリストを表示するには、[az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) を使用します。 ディスクの復旧には、復旧ポイント **名** が使用されます。 このチュートリアルでは、使用可能な最新の復旧ポイントが必要です。 `--query [0].name` パラメーターでは、次のように最新の復旧ポイント名が選択されます。

```azurecli-interactive
az backup recoverypoint list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --backup-management-type AzureIaasVM \
    --container-name myVM \
    --item-name myVM \
    --query [0].name \
    --output tsv
```

## <a name="restore-a-vm-disk"></a>VM ディスクを復元する

> [!IMPORTANT]
> マネージド ディスクの復元などの高速復元のすべてのベネフィットを利用できるよう、Az CLI バージョン 2.0.74 以降を使用することを非常に強くお勧めします。 常に最新バージョンを使用することをお勧めします。

### <a name="managed-disk-restore"></a>マネージド ディスクの復元

バックアップされた VM にマネージド ディスクが存在し、復旧ポイントからマネージド ディスクを復元したい場合は、最初に Azure ストレージ アカウントを指定します。 このストレージ アカウントは、後で復元されたディスクから VM をデプロイするために使用できる、VM の構成とデプロイ テンプレートを格納するために使用されます。 次に、マネージド ディスクの復元先のターゲット リソース グループも指定します。

1. ストレージ アカウントを作成するには、[az storage account create](/cli/azure/storage/account#az-storage-account-create) を使用します。 ストレージ アカウント名はすべて小文字で、グローバルに一意である必要があります。 *mystorageaccount* は、次のように独自の一意の名前に置き換えます。

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks) を使用して、復旧ポイントからディスクを復元します。 *mystorageaccount* は、前述のコマンドで作成したストレージ アカウントの名前に置き換えます。 *myRecoveryPointName* は、前述の [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) コマンドから出力を取得した復旧ポイント名に置き換えます。 ***マネージド ディスクの復元先となるターゲット リソース グループも指定します***。

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --target-resource-group targetRG
    ```

    > [!WARNING]
    > **target-resource-group** を指定しないと、マネージド ディスクは、指定したストレージ アカウントにアンマネージド ディスクとして復元されます。 ディスク全体の復元にかかる時間は、指定したストレージ アカウントに依存するため、これは復元時間に大きく影響します。 target-resource-group パラメーターが指定されている場合にのみ、インスタント リストアのベネフィットが得られます。 マネージド ディスクをアンマネージドとして復元する場合は、次に示すように、**target-resource-group** パラメーターを指定せず、**restore-as-unmanaged-disk** パラメーターを代わりに指定してください。 このパラメーターは、az 3.4.0 以降で利用できます。

    ```azurecli-interactive
    az backup restore restore-disks \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --storage-account mystorageaccount \
    --rp-name myRecoveryPointName \
    --restore-as-unmanaged-disk
    ```

これにより、マネージド ディスクがアンマネージド ディスクとして指定のストレージ アカウントに復元され、"インスタント" リストア機能は利用されなくなります。 CLI の将来のバージョンでは、**target-resource-group** パラメーターまたは **restore-as-unmanaged-disk** パラメーターのどちらかを指定することが必須となります。

### <a name="unmanaged-disks-restore"></a>アンマネージド ディスクの復元

バックアップされた VM にアンマネージド ディスクが存在し、復旧ポイントからディスクを復元したい場合は、最初に Azure ストレージ アカウントを指定します。 このストレージ アカウントは、後で復元されたディスクから VM をデプロイするために使用できる、VM の構成とデプロイ テンプレートを格納するために使用されます。 既定では、アンマネージド ディスクは元のストレージ アカウントに復元されます。 すべてのアンマネージド ディスクを 1 つの場所に復元したい場合は、指定したストレージ アカウントをそれらのディスクのステージング場所として使用することもできます。

追加手順では、VM を作成するために復元されたディスクが使用されます。

1. ストレージ アカウントを作成するには、[az storage account create](/cli/azure/storage/account#az-storage-account-create) を使用します。 ストレージ アカウント名はすべて小文字で、グローバルに一意である必要があります。 *mystorageaccount* は、次のように独自の一意の名前に置き換えます。

    ```azurecli-interactive
    az storage account create \
        --resource-group myResourceGroup \
        --name mystorageaccount \
        --sku Standard_LRS
    ```

2. [az backup restore restore-disks](/cli/azure/backup/restore#az-backup-restore-restore-disks) を使用して、復旧ポイントからディスクを復元します。 *mystorageaccount* は、前述のコマンドで作成したストレージ アカウントの名前に置き換えます。 *myRecoveryPointName* は、前述の [az backup recoverypoint list](/cli/azure/backup/recoverypoint#az-backup-recoverypoint-list) コマンドから出力を取得した復旧ポイント名に置き換えます。

    ```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName
    ```

前に説明したように、アンマネージド ディスクは元のストレージ アカウントに復元されます。 これにより、復元のパフォーマンスが最善になります。 ただし、すべてのアンマネージド ディスクを特定のストレージ アカウントに復元する必要がある場合は、次に示すように関連フラグを使用します。

```azurecli-interactive
    az backup restore restore-disks \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --storage-account mystorageaccount \
        --rp-name myRecoveryPointName \
        --restore-to-staging-storage-account
    ```

## Monitor the restore job

To monitor the status of restore job, use [az backup job list](/cli/azure/backup/job#az-backup-job-list):

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

出力は次の例のようになります。復元ジョブが *進行中* であることが示されています。

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
7f2ad916  Restore          InProgress  myvm         2017-09-19T19:39:52  0:00:34.520850
a0a8e5e6  Backup           Completed   myvm         2017-09-19T03:09:21  0:15:26.155212
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

復元ジョブの *[状態]* が *[完了]* になっている場合は、必要な情報 (VM の構成とデプロイ テンプレート) がストレージ アカウントに復元されています。

## <a name="create-a-vm-from-the-restored-disk"></a>復元されたディスクから VM を作成する

最後のステップでは、復元されたディスクから VM を作成します。 指定したストレージ アカウントにダウンロードされたデプロイ テンプレートを使用して、VM を作成できます。

### <a name="fetch-the-job-details"></a>ジョブの詳細を取得する

結果のジョブの詳細には、クエリを実行してデプロイできるテンプレート URI が示されます。 トリガーされた復元ジョブについてのさらに詳細な情報を取得するには、job show コマンドを使用します。

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414
```

このクエリの出力ではすべての詳細が表示されますが、ここで関心があるのはストレージ アカウントの内容のみです。 Azure CLI の[クエリ機能](/cli/azure/query-azure-cli)を使用して、関連する詳細を取得できます

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag

{
  "Config Blob Container Name": "myVM-daa1931199fd4a22ae601f46d8812276",
  "Config Blob Name": "config-myVM-1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414.json",
  "Config Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/config-appvm8-1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json",
  "Job Type": "Recover disks",
  "Recovery point time ": "12/25/2019 10:07:11 PM",
  "Target Storage Account Name": "mystorageaccount",
  "Target resource group": "mystorageaccountRG",
  "Template Blob Uri": "https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
}
```

### <a name="fetch-the-deployment-template"></a>デプロイ テンプレートをフェッチする

テンプレートは、顧客のストレージ アカウントと指定されたコンテナーの下にあるため、直接アクセスすることはできません。 このテンプレートにアクセスするには (一時的な SAS トークンと共に) 完全な URL が必要です。

最初に、ジョブの詳細からテンプレートの BLOB URI を抽出します

```azurecli-interactive
az backup job show \
    -v myRecoveryServicesVault \
    -g myResourceGroup \
    -n 1fc2d55d-f0dc-4ca6-ad48-aca0fe5d0414 \
    --query properties.extendedInfo.propertyBag."""Template Blob Uri"""

"https://mystorageaccount.blob.core.windows.net/myVM-daa1931199fd4a22ae601f46d8812276/azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json"
```

テンプレートの BLOB URI は次のような形式であり、テンプレート名が抽出されます

```https
https://<storageAccountName.blob.core.windows.net>/<containerName>/<templateName>
```

したがって、上の例では、テンプレート名は ```azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json```、コンテナー名は ```myVM-daa1931199fd4a22ae601f46d8812276``` です

ここで、このコンテナーとテンプレートの SAS トークンを取得します (詳しくは[こちら](../azure-resource-manager/templates/secure-template-with-sas-token.md?tabs=azure-cli#provide-sas-token-during-deployment)をご覧ください)

```azurecli-interactive
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group mystorageaccountRG \
    --name mystorageaccount \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
   --container-name myVM-daa1931199fd4a22ae601f46d8812276 \
    --name azuredeploy1fc2d55d-f0dc-4ca6-ad48-aca0519c0232.json \
    --output tsv \
    --connection-string $connection)
```

### <a name="deploy-the-template-to-create-the-vm"></a>テンプレートをデプロイして VM を作成する

次に、[こちら](../azure-resource-manager/templates/deploy-cli.md)の説明に従って、テンプレートをデプロイして VM を作成します。

```azurecli-interactive
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri $url?$token
```

VM が回復したディスクから作成されていることを確認するには、次のように、[az vm list](/cli/azure/vm#az-vm-list) を使用してリソース グループの VM をリストします。

```azurecli-interactive
az vm list --resource-group myResourceGroup --output table
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、復旧ポイントからディスクを復元し、ディスクから VM を作成しました。 以下の方法を学習しました。

> [!div class="checklist"]
>
> * 復旧ポイントをリストして選択する
> * 復旧ポイントからディスクを復元する
> * 復元されたディスクから VM を作成する

次のチュートリアルに進み、復旧ポイントからの個々のファイルの復元について学習してください。

> [!div class="nextstepaction"]
> [Azure の仮想マシンにファイルを復元する](tutorial-restore-files.md)
