---
title: "バックアップのために Azure Managed Disk をコピーする | Microsoft Docs"
description: "バックアップまたはディスクの問題のトラブルシューティング用に使うために Azure Managed Disk のコピーを作成する方法について説明します。"
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 2/6/2017
ms.author: rasquill
ms.openlocfilehash: c91367ef11c9d531bebac7c069d2df586607ec29
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-copy-of-a-vhd-stored-as-an-azure-managed-disk-by-using-managed-snapshots"></a>管理スナップショットを使って Azure Managed Disk として保存された VHD のコピーを作成する
バックアップのために Managed Disk のスナップショットを作成するか、スナップショットから Managed Disk を作成してトラブルシューティングのためにテスト仮想マシンに接続します。 管理スナップショットは、VM の Managed Disk の完全なポイントインタイム コピーです。 VHD の読み取り専用のコピーを作成し、既定では Standard Managed Disk として保存します。 

価格の詳細については、「[Azure Storage の価格](https://azure.microsoft.com/pricing/details/managed-disks/)」をご覧ください。 <!--Add link to topic or blog post that explains managed disks. -->

Managed Disk のスナップショットを作成するには、Azure Portal または Azure CLI 2.0 を使います。

## <a name="use-azure-cli-20-to-take-a-snapshot"></a>Azure CLI 2.0 使ってスナップショットを作成する

> [!NOTE] 
> 次の例では、Azure CLI 2.0 がインストールされていて、Azure アカウントにログインしている必要があります。

次の手順では、`az snapshot create` コマンドと `--source-disk` パラメーターを使って管理 OS ディスクのスナップショットを取得する方法を示します。 次の例では、`myResourceGroup` リソース グループの管理 OS ディスクを使って `myVM` という名前の VM が作成されているものとします。

```azure-cli
# take the disk id with which to create a snapshot
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create -g myResourceGroup --source "$osDiskId" --name osDisk-backup
```

出力は次のようになります。

```json
{
  "accountType": "Standard_LRS",
  "creationData": {
    "createOption": "Copy",
    "imageReference": null,
    "sourceResourceId": null,
    "sourceUri": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/disks/osdisk_6NexYgkFQU",
    "storageAccountId": null
  },
  "diskSizeGb": 30,
  "encryptionSettings": null,
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/snapshots/osDisk-backup",
  "location": "westus",
  "name": "osDisk-backup",
  "osType": "Linux",
  "ownerId": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "tags": null,
  "timeCreated": "2017-02-06T21:27:10.172980+00:00",
  "type": "Microsoft.Compute/snapshots"
}
```

## <a name="use-azure-portal-to-take-a-snapshot"></a>Azure Portal を使ってスナップショットを作成する 

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. 左上の **[新規]** をクリックし、**[スナップショット]** を探します。
3. [スナップショット] ブレードで **[作成]** をクリックします。
4. スナップショットの **[名前]** を入力します。
5. 既存の[リソース グループ](../../azure-resource-manager/resource-group-overview.md#resource-groups)を選択するか、新しいリソース グループの名前を入力します。 
6. Azure データセンターの場所を選択します。  
7. **[ソース ディスク]** で、スナップショットを作成する Managed Disk を選びます。
8. スナップショットの保存に使う **[アカウントの種類]** を選びます。 高パフォーマンスのディスクに保存する必要がある場合を除き、**[Standard_LRS]** をお勧めします。
9. **Create** をクリックしてください。

スナップショットを使って Managed Disk を作成し、高パフォーマンスが必要な VM に接続する計画がある場合は、`az snapshot create` コマンドで `--sku Premium_LRS` パラメーターを使います。 Premium Managed Disk として保存されるようにスナップショットが作成されます。 Premium Managed Disks はソリッド ステート ドライブ (SSD) なので高パフォーマンスですが、料金は Standard ディスク (HDD) より高くなります。


