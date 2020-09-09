---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 9e7386e21442b5a76aae656a36e2858b52ecef65
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87102799"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

### <a name="supported-regions"></a>サポートされているリージョン
[!INCLUDE [virtual-machines-disks-incremental-snapshots-regions](virtual-machines-disks-incremental-snapshots-regions.md)]

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="powershell"></a>PowerShell

Azure PowerShell を使用すると、増分スナップショットを作成できます。 Azure PowerShell の最新バージョンが必要になります。次のコマンドを実行すると、最新バージョンがインストールされるか、既存のインストールを最新のバージョンに更新します。

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

インストールが完了したら、`Connect-AzAccount` を使用して PowerShell セッションにログインします。

Azure PowerShell を利用して増分スナップショットを作成するには、構成を [New-AzSnapShotConfig](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) と `-Incremental` パラメーターで設定し、`-Snapshot` パラメーターを介してそれを [New-AzSnapshot](https://docs.microsoft.com/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) に変数として渡します。

```PowerShell
$diskName = "yourDiskNameHere>"
$resourceGroupName = "yourResourceGroupNameHere"
$snapshotName = "yourDesiredSnapshotNameHere"

# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName $diskName -ResourceGroupName $resourceGroupName

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName $resourceGroupName -SnapshotName $snapshotName -Snapshot $snapshotConfig 
```

スナップショットの `SourceResourceId` プロパティと `SourceUniqueId` プロパティで同じディスクからの増分スナップショットを識別できます。 `SourceResourceId` は親ディスクの Azure Resource Manager リソース ID です。 `SourceUniqueId` はディスクの `UniqueId` プロパティから継承した値です。 ディスクを削除してから同じ名前でディスクを新規作成する場合、`UniqueId` プロパティの値が変更されます。

`SourceResourceId` と `SourceUniqueId` を利用し、特定のディスクに関連付けられているすべてのスナップショットの一覧を作成できます。 `<yourResourceGroupNameHere>` を実際の値に変更します。次の例を利用すると、既存の増分スナップショットを一覧表示できます。

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName $resourceGroupName

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="resource-manager-template"></a>Resource Manager テンプレート

Azure Resource Manager テンプレートを使用して、増分スナップショットを作成することもできます。 apiVersion が **2019-03-01** に設定されていること、および増分プロパティも true に設定されていることを確認する必要があります。 次のスニペットは、Resource Manager テンプレートを使用して増分スナップショットを作成する方法の例です。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskName": {
      "type": "string",
      "defaultValue": "contosodisk1"
    },
  "diskResourceId": {
    "defaultValue": "<your_managed_disk_resource_ID>",
    "type": "String"
  }
  }, 
  "resources": [
  {
    "type": "Microsoft.Compute/snapshots",
    "name": "[concat( parameters('diskName'),'_snapshot1')]",
    "location": "[resourceGroup().location]",
    "apiVersion": "2019-03-01",
    "properties": {
      "creationData": {
        "createOption": "Copy",
        "sourceResourceId": "[parameters('diskResourceId')]"
      },
      "incremental": true
    }
  }
  ]
}
```

## <a name="next-steps"></a>次のステップ

.NET を使用し、増分スナップショットの差分機能を示すサンプル コードを見る場合、[増分スナップショットの差分機能で別のリージョンに Azure マネージド ディスク バックアップをコピーする](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots)方法に関するページを参照してください。
