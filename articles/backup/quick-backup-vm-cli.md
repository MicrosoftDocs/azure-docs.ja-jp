---
title: "Azure クイック スタート - Azure CLI で VM をバックアップする | Microsoft Docs"
description: "Azure CLI を使用して仮想マシンをバックアップする方法"
services: virtual-machines-linux, azure-backup
documentationcenter: virtual-machines
author: iainfoulds
manager: jeconnoc
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: virtual-machines-linux, azure-backup
ms.devlang: azurecli
ms.topic: hero-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/18/2017
ms.author: iainfou
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 44e9d992de3126bf989e69e39c343de50d592792
ms.openlocfilehash: b01916516d15ffee46f135e175ee4136f79acbe5
ms.contentlocale: ja-jp
ms.lasthandoff: 09/25/2017

---

# <a name="back-up-a-virtual-machine-in-azure-with-the-cli"></a>CLI を使用した Azure での仮想マシンのバックアップ
Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 データは、定期的にバックアップすることで保護することができます。 Azure Backup では、geo 冗長 Recovery コンテナーに保存できる復旧ポイントが作成されます。 この記事では、Azure CLI を使用して Azure で仮想マシン (VM) をバックアップする方法を説明します。 これらの手順は、[Azure PowerShell](quick-backup-vm-powershell.md) または [Azure Portal](quick-backup-vm-portal.md) を介して実行することもできます。

このクイック スタートでは、既存の Azure VM でバックアップを実行できます。 VM を作成する必要がある場合は、[Azure CLI を使用して VM を作成](../virtual-machines/linux/quick-create-cli.md)できます。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.18 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール](/cli/azure/install-azure-cli)」を参照してください。 


## <a name="register-the-azure-backup-resource-provider"></a>Azure Backup リソース プロバイダーの登録
Azure Backup を最初に使用するときは、[az provider register](/cli/azure/provider?view=azure-cli-latest#az_provider_register) を使ってサブスクリプション内で Azure Recovery Service プロバイダーを登録する必要があります。

```azurecli-interactive
az provider register --namespace Microsoft.RecoveryServices
```


## <a name="create-a-recovery-services-vault"></a>Recovery Services コンテナーの作成
Recovery Services コンテナーは、Azure VM などの保護された各リソースのバックアップ データを格納する論理コンテナーです。 保護されたリソースのバックアップ ジョブを実行した場合、Recovery Services コンテナー内に復旧ポイントが作成されます。 次に、この復旧ポイントのいずれかを使用して、データを特定の時点に復元できます。

**az backup vault create** を使用して Recovery Services コンテナーを作成します。 保護する VM と同じリソース グループと場所を指定します。 [VM quickstart](../virtual-machines/linux/quick-create-cli.md) を使用した場合、リソース グループの名前は *myResourceGroup*、VM の名前は *myVM* になり、リソースは *eastus* に格納されます。

```azurecli-interactive 
az backup vault create --resource-group myResourceGroup \
    --name myRecoveryServicesVault \
    --location eastus
```

既定では、コンテナーは geo 冗長ストレージ用に設定されます。 さらにデータを保護するために、このストレージの冗長性レベルでは、プライマリ リージョンから数百マイル離れたセカンダリ Azure リージョンにバックアップ データがレプリケートされます。


## <a name="enable-backup-for-an-azure-vm"></a>Azure VM のバックアップを有効にする
バックアップ ジョブがいつ実行され、復旧ポイントをどのくらいの期間格納するかを定義するポリシーを作成して使用します。 既定の保護ポリシーでは、毎日バックアップ ジョブが実行され、復旧ポイントは 30 日間保持されます。 これらの既定ポリシー値を使用して、VM をすばやく保護できます。 VM のバックアップ保護を有効にするには、**az backup protection enable—for-vm** を使用します。 保護するリソース グループと VM を指定した後、使用するポリシーを指定します。

```azurecli-interactive 
az backup protection enable-for-vm \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --vm myVM \
    --policy-name DefaultPolicy
```


## <a name="start-a-backup-job"></a>バックアップ ジョブの開始
スケジュールされた時刻にジョブを実行する既定のポリシーを待機するのではなく、バックアップを今すぐ開始するには、**az backup protection backup-now** を使用します。 この最初のバックアップ ジョブによって、完全な復旧ポイントが作成されます。 この初期バックアップ後の各バックアップ ジョブによって、増分復旧ポイントが作成されます。 増分復旧ポイントでは、前回のバックアップ以降に行われた変更のみを転送対象とすることで、高い保存効率と時間効率を実現します。

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

この最初のバックアップ ジョブでは完全な復旧ポイントが作成されるため、この処理には最大 20 分かかる場合があります。


## <a name="monitor-the-backup-job"></a>バックアップ ジョブの監視
バックアップ ジョブの状態を監視するには、**az backup job list** を使用します。

```azurecli-interactive 
az backup job list \
    --resource-group myResourceGroup \
    --vault-name myRecoveryServicesVault \
    --output table
```

出力は次の例のようになります。バックアップ ジョブが *InProgress* であることが示されています。

```
Name      Operation        Status      Item Name    Start Time UTC       Duration
--------  ---------------  ----------  -----------  -------------------  --------------
a0a8e5e6  Backup           InProgress  myvm         2017-09-19T03:09:21  0:00:48.718366
fe5d0414  ConfigureBackup  Completed   myvm         2017-09-19T03:03:57  0:00:31.191807
```

バックアップ ジョブの "*状態*" が "*完了*" と報告されると、VM は Recovery Services で保護され、完全な復旧ポイントが格納されています。


## <a name="clean-up-deployment"></a>デプロイのクリーンアップ
不要になったときに、VM の保護を無効にし、復旧ポイントと Recovery Services コンテナーを削除した後、リソース グループと関連付けられている VM リソースを削除することができます。 既存の VM を使用した場合、リソース グループと VM をそのままの状態にしておくために、最後の [az group delete](/cli/azure/group?view=azure-cli-latest#az_group_delete) コマンドを省略できます。

VM のデータを復元する方法を説明したバックアップ チュートリアルを続行する場合は、「[次のステップ](#next-steps)」に進みます。 

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
このクイック スタートでは、Recovery Services コンテナーを作成し、VM の保護を有効にし、最初の復旧ポイントを作成しました。 Azure Backup と Recovery Services にさらに学ぶには、チュートリアルを続行してください。

> [!div class="nextstepaction"]
> [複数の Azure VM をバックアップする](./tutorial-backup-vm-at-scale.md)

