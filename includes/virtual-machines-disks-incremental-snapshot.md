---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 09/23/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: ee8a711a867f8abdc831b0d1d9d0b504b1104955
ms.sourcegitcommit: 0486aba120c284157dfebbdaf6e23e038c8a5a15
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/26/2019
ms.locfileid: "71310122"
---
# <a name="creating-an-incremental-snapshot-preview-for-managed-disks"></a>マネージド ディスクの増分スナップショット (プレビュー) の作成

増分スナップショット (プレビュー) は、マネージド ディスクの特定の時点のバックアップであり、取得時に、最後のスナップショット以降のすべての変更のみで構成されます。 増分スナップショットをダウンロードまたは使用しようとすると、完全な VHD が使用されます。 マネージド ディスク スナップショットに対するこの新しい機能により、必要に応じて個別のスナップショットごとにディスク全体を保存する必要がなくなるため、コスト効率が向上する可能性があります。 通常のスナップショットと同じように、増分スナップショットを使用して、完全なマネージド ディスクを作成したり、通常のスナップショットを作成したりできます。

増分スナップショットと通常のスナップショットには、いくつかの違いがあります。 増分スナップショットでは、ディスクのストレージの種類に関係なく、常に Standard HDD ストレージが使用されますが、通常のスナップショットでは Premium SSD を使用できます。 VM のデプロイをスケールアップするために Premium Storage で通常のスナップショットを使用している場合は、[Shared Image Gallery](../articles/virtual-machines/linux/shared-image-galleries.md) の Standard Storage でカスタム イメージを使用することをお勧めします。 これは、より低コストでさらに大規模なスケールを実現するのに役立ちます。 さらに、増分スナップショットは、[ゾーン冗長ストレージ](../articles/storage/common/storage-redundancy-zrs.md) (ZRS) を使用すると、信頼性が向上する可能性があります。 選択したリージョンで ZRS が使用可能な場合、増分スナップショットは ZRS を自動的に使用します。 そのリージョンで ZRS が使用できない場合、スナップショットは既定で[ローカル冗長ストレージ](../articles/storage/common/storage-redundancy-lrs.md) (LRS) に設定されます。 この動作をオーバーライドして手動で選択することもできますが、これはお勧めしません。

増分スナップショットには、マネージド ディスクで独自に使用できる差分機能も用意されています。 これにより、同じマネージド ディスクの 2 つの増分スナップショット間の変更内容を、ブロック レベルまで取得できます。 スナップショットを複数のリージョンにわたってコピーするとき、この機能を使用してデータ フットプリントを削減できます。

まだプレビューにサインアップしておらず、増分スナップショットの使用を開始する場合は、パブリック プレビューにアクセスするために AzureDisks@microsoft.com までメールでお問い合わせください。

## <a name="restrictions"></a>制限

- 増分スナップショットは現在、米国中西部でのみ利用できます。
- 増分スナップショットは、現時点ではディスクのサイズを変更した後に作成することはできません。
- 増分スナップショットは、現時点ではサブスクリプション間で移動できません。
- 現時点では、任意の時点で特定のスナップショット ファミリの最大 5 つのスナップショットの SAS URI のみを生成することができます。
- 特定のディスクの増分スナップショットを、そのディスクのサブスクリプションの外部で作成することはできません。
- ディスクあたり最大 7 つの増分スナップショットを 5 分ごとに作成できます。
- 1 つのディスクに対して合計 200 の増分スナップショットを作成できます。

## <a name="powershell"></a>PowerShell

Azure PowerShell を使用すると、増分スナップショットを作成できます。 Azure PowerShell の最新バージョンが必要になります。次のコマンドを実行すると、最新バージョンがインストールされるか、既存のインストールを最新のバージョンに更新します。

```PowerShell
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

インストールが完了したら、`az login` を使用して PowerShell セッションにログインします。

Azure PowerShell を利用して増分スナップショットを作成するには、構成を [New-AzSnapShotConfig](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshotconfig?view=azps-2.7.0) と `-Incremental` パラメーターで設定し、`-Snapshot` パラメーターを介してそれを [New-AzSnapshot](https://docs.microsoft.com/en-us/powershell/module/az.compute/new-azsnapshot?view=azps-2.7.0) に変数として渡します。

`<yourDiskNameHere>`、`<yourResourceGroupNameHere>`、および `<yourDesiredSnapShotNameHere>` を実際の値に置き換えて、次のスクリプトを使用して増分スナップショットを作成できます。

```PowerShell
# Get the disk that you need to backup by creating an incremental snapshot
$yourDisk = Get-AzDisk -DiskName <yourDiskNameHere> -ResourceGroupName <yourResourceGroupNameHere>

# Create an incremental snapshot by setting the SourceUri property with the value of the Id property of the disk
$snapshotConfig=New-AzSnapshotConfig -SourceUri $yourDisk.Id -Location $yourDisk.Location -CreateOption Copy -Incremental 
New-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere> -SnapshotName <yourDesiredSnapshotNameHere> -Snapshot $snapshotConfig 
```

スナップショットの `SourceResourceId` プロパティと `SourceUniqueId` プロパティで同じディスクからの増分スナップショットを識別できます。 `SourceResourceId` は親ディスクの Azure Resource Manager リソース ID です。 `SourceUniqueId` はディスクの `UniqueId` プロパティから継承した値です。 ディスクを削除してから同じ名前でディスクを新規作成する場合、`UniqueId` プロパティの値が変更されます。

`SourceResourceId` と `SourceUniqueId` を利用し、特定のディスクに関連付けられているすべてのスナップショットの一覧を作成できます。 `<yourResourceGroupNameHere>` を実際の値に変更します。次の例を利用すると、既存の増分スナップショットを一覧表示できます。

```PowerShell
$snapshots = Get-AzSnapshot -ResourceGroupName <yourResourceGroupNameHere>

$incrementalSnapshots = New-Object System.Collections.ArrayList
foreach ($snapshot in $snapshots)
{
    
    if($snapshot.Incremental -and $snapshot.CreationData.SourceResourceId -eq $yourDisk.Id -and $snapshot.CreationData.SourceUniqueId -eq $yourDisk.UniqueId){

        $incrementalSnapshots.Add($snapshot)
    }
}

$incrementalSnapshots
```

## <a name="cli"></a>CLI

Azure CLI で増分スナップショットを作成できます。Azure CLI の最新版が必要です。 次のコマンドを実行すると、最新版がインストールされるか、既存のインストールが最新版に更新されます。

```PowerShell
Invoke-WebRequest -Uri https://aka.ms/installazurecliwindows -OutFile .\AzureCLI.msi; Start-Process msiexec.exe -Wait -ArgumentList '/I AzureCLI.msi /quiet'
```

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

## <a name="next-steps"></a>次の手順

まだプレビューにサインアップしておらず、増分スナップショットの使用を開始する場合は、パブリック プレビューにアクセスするために AzureDisks@microsoft.com までメールでお問い合わせください。
