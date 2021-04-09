---
title: クイックスタート - Azure CLI で VM をバックアップする
description: このクイック スタートでは、Azure CLI を使用して、Recovery Services コンテナーを作成し、VM の保護を有効にし、最初の復元ポイントを作成する方法について説明します。
ms.devlang: azurecli
ms.topic: quickstart
ms.date: 01/31/2019
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 1a1b11d517fdfea0aa3a0f553b63276bc20f90be
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98805456"
---
# <a name="back-up-a-virtual-machine-in-azure-with-the-azure-cli"></a>Azure CLI を使用した Azure での仮想マシンのバックアップ

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 データは、定期的にバックアップすることで保護することができます。 Azure Backup によって、geo 冗長 Recovery コンテナーに保存できる復元ポイントが作成されます。 この記事では、Azure CLI を使用して Azure で仮想マシン (VM) をバックアップする方法を説明します。 これらの手順は、[Azure PowerShell](quick-backup-vm-powershell.md) または [Azure Portal](quick-backup-vm-portal.md) を介して実行することもできます。

このクイック スタートでは、既存の Azure VM のバックアップを実行できます。 VM を作成する必要がある場合は、[Azure CLI を使用して VM を作成](../virtual-machines/linux/quick-create-cli.md)できます。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

 - このクイックスタートには、Azure CLI のバージョン 2.0.18 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーを作成する

Recovery Services コンテナーは、Azure VM などの保護された各リソースのバックアップ データを格納する論理コンテナーです。 保護されたリソースのバックアップ ジョブを実行すると、Recovery Services コンテナー内に復元ポイントが作成されます。 この復元ポイントのいずれかを使用して、データを特定の時点に復元できます。

[az backup vault create](/cli/azure/backup/vault#az-backup-vault-create) を使用して Recovery Services コンテナーを作成します。 保護する VM と同じリソース グループと場所を指定します。 [VM クイック スタート](../virtual-machines/linux/quick-create-cli.md)を使用した場合、次のものが作成されています。

- *myResourceGroup* という名前のリソース グループ
- *myVM* という名前の VM
- *eastus* に置かれた各種リソース

```azurecli-interactive
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

既定では、Recovery Services コンテナーが geo 冗長ストレージ用に設定されています。 geo 冗長ストレージでは、プライマリ リージョンから数百マイル離れたセカンダリ Azure リージョンにバックアップ データが確実にレプリケートされます。 ストレージの冗長性設定を変更する必要がある場合は、[az backup vault backup-properties set](/cli/azure/backup/vault/backup-properties#az-backup-vault-backup-properties-set) コマンドレットを使用します。

```azurecli
az backup vault backup-properties set \
    --name myRecoveryServicesVault  \
    --resource-group myResourceGroup \
    --backup-storage-redundancy "LocallyRedundant/GeoRedundant"
```

## <a name="enable-backup-for-an-azure-vm"></a>Azure VM のバックアップを有効にする

バックアップ ジョブをいつ実行し、復旧ポイントをどのくらいの期間格納するかを定義する保護ポリシーを作成します。 既定の保護ポリシーでは、毎日バックアップ ジョブが実行され、復元ポイントは 30 日間保持されます。 ポリシーのこれらの既定値を使用して、VM をすぐに保護できます。 VM のバックアップ保護を有効にするには、[az backup protection enable-for-vm](/cli/azure/backup/protection#az-backup-protection-enable-for-vm) を使用します。 保護するリソース グループと VM を指定した後、使用するポリシーを指定します。

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```

> [!NOTE]
> VM がコンテナーと同じリソース グループにない場合、myResourceGroup はコンテナーが作成されたリソース グループを参照します。 VM 名の代わりに、次に示すように VM ID を指定します。

```azurecli-interactive
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm $(az vm show -g VMResourceGroup -n MyVm --query id | tr -d '"') \
    --policy-name DefaultPolicy
```

> [!IMPORTANT]
> CLI を使用して一度に複数の VM のバックアップを有効にするときは、1 つのポリシーに 100 を超える VM が関連付けられていないことを確認します。 これは、[推奨されるベスト プラクティス](./backup-azure-vm-backup-faq.yml#is-there-a-limit-on-number-of-vms-that-can-be-associated-with-the-same-backup-policy)です。 現時点では、100 を超える VM がある場合に PowerShell クライアントは明示的にブロックしませんが、このチェックは将来追加される予定です。

## <a name="start-a-backup-job"></a>バックアップ ジョブを開始する

既定のポリシーでスケジュールされた時刻にジョブが実行されるのを待たずに、バックアップを今すぐ開始するには、[az backup protection backup-now](/cli/azure/backup/protection#az-backup-protection-backup-now) を使用します。 この初回のバックアップ ジョブによって、完全な復元ポイントが作成されます。 初回のバックアップ後のバックアップ ジョブでは、増分復元ポイントが作成されます。 増分復元ポイントでは、前回のバックアップ以降に行われた変更のみを転送対象とすることで、高い保存効率と時間効率を実現します。

VM のバックアップには、次のパラメーターが使用されます。

- `--container-name` は VM の名前です
- `--item-name` は VM の名前です
- `--retain-until` 値は、復旧ポイントを利用できるようにする最後の利用可能な日付に UTC 時刻形式 (**dd-mm-yyyy**) で設定します。

次の例では、*myVM* という名前の VM をバックアップし、復旧ポイントの期限を 2017 年 10 月 18 日に設定します。

```azurecli-interactive
az backup protection backup-now \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --retain-until 18-10-2017
```

## <a name="monitor-the-backup-job"></a>バックアップ ジョブを監視する

バックアップ ジョブの状態を監視するには、[az backup job list](/cli/azure/backup/job#az-backup-job-list) を使用します。

```azurecli-interactive
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

出力は次の例のようになります。バックアップ ジョブが *InProgress* であることがわかります。

```output
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

バックアップ ジョブの *状態* が *完了* と報告されたら、VM は Recovery Services で保護され、完全な復旧ポイントが格納されています。

## <a name="clean-up-deployment"></a>デプロイのクリーンアップ

VM が不要になったら、VM の保護を無効にし、復旧ポイントと Recovery Services コンテナーを削除した後、リソース グループと関連付けられている VM リソースを削除することができます。 既存の VM を使用した場合、リソース グループと VM をそのままの状態にしておくために、最後の [az group delete](/cli/azure/group#az-group-delete) コマンドを省略できます。

VM のデータを復元する方法について説明したバックアップ チュートリアルに取り組むには、「[次のステップ](#next-steps)」に進んでください。

```azurecli-interactive
az backup protection disable \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --container-name myVM \
    --item-name myVM \
    --delete-backup-data true
az backup vault delete \
    --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Recovery Services コンテナーを作成し、VM の保護を有効にし、最初の復元ポイントを作成しました。 Azure Backup と Recovery Services についてさらに学ぶには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [複数の Azure VM をバックアップする](./tutorial-backup-vm-at-scale.md)
