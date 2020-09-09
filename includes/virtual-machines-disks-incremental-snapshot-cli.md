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
ms.openlocfilehash: cbd6f821326c86983ceb3ae5b90969e522c187fe
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204437"
---
[!INCLUDE [virtual-machines-disks-incremental-snapshots-description](virtual-machines-disks-incremental-snapshots-description.md)]

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-incremental-snapshots-restrictions](virtual-machines-disks-incremental-snapshots-restrictions.md)]

## <a name="cli"></a>CLI

Azure CLI で増分スナップショットを作成できます。Azure CLI の最新版が必要です。 

Windows では、次のコマンドを実行すると、最新版がインストールされるか、既存のインストールが最新版に更新されます。
```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```
Linux では、CLI のインストールは、オペレーティング システムのバージョンによって異なります。  Linux の特定のバージョンについては、「[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli)」を参照してください。

増分スナップショットを作成するには、[az snapshot create](https://docs.microsoft.com/cli/azure/snapshot?view=azure-cli-latest#az-snapshot-create) を使用し、`--incremental` パラメーターを指定します。

次の例では、増分スナップショットが作成され、`<yourDesiredSnapShotNameHere>`、`<yourResourceGroupNameHere>`、`<exampleDiskName>`、`<exampleLocation>` が独自の値に置換され、サンプルが実行されます。

```bash
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot create -g <yourResourceGroupNameHere> \
-n <yourDesiredSnapShotNameHere> \
-l <exampleLocation> \
--source "$sourceResourceId" \
--incremental
```

スナップショットの `SourceResourceId` プロパティと `SourceUniqueId` プロパティで同じディスクからの増分スナップショットを識別できます。 `SourceResourceId` は親ディスクの Azure Resource Manager リソース ID です。 `SourceUniqueId` はディスクの `UniqueId` プロパティから継承した値です。 ディスクを削除してから同じ名前でディスクを新規作成する場合、`UniqueId` プロパティの値が変更されます。

`SourceResourceId` と `SourceUniqueId` を利用し、特定のディスクに関連付けられているすべてのスナップショットの一覧を作成できます。 次の例では、特定のディスクに関連付けられているすべての増分スナップショットが一覧表示されますが、いくつかのセットアップが必要です。

この例では、データを問い合わせるために jq が使用されます。 このサンプルを実行するには、[jq をインストールする](https://stedolan.github.io/jq/download/)必要があります。

`<yourResourceGroupNameHere>` と `<exampleDiskName>` を実際の値に変更します。jq もインストールされていれば、次の例を利用すると、既存の増分スナップショットを一覧表示できます。

```bash
sourceUniqueId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[uniqueId]' -o tsv)

 
sourceResourceId=$(az disk show -g <yourResourceGroupNameHere> -n <exampleDiskName> --query '[id]' -o tsv)

az snapshot list -g <yourResourceGroupNameHere> -o json \
| jq -cr --arg SUID "$sourceUniqueId" --arg SRID "$sourceResourceId" '.[] | select(.incremental==true and .creationData.sourceUniqueId==$SUID and .creationData.sourceResourceId==$SRID)'
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
