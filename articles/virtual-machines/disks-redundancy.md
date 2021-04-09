---
title: Azure マネージド ディスクの冗長性オプション
description: Azure マネージド ディスクのゾーン冗長ストレージとローカル冗長ストレージについて説明します。
author: roygara
ms.author: rogarana
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: f0f3baf1bf56f958408f789961812c0555f289f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102043645"
---
# <a name="redundancy-options-for-managed-disks"></a>マネージド ディスクの冗長性オプション

Azure マネージド ディスクには、プレビューとしてのゾーン冗長ストレージ (ZRS) と、ローカル冗長ストレージという 2 つのストレージ冗長性オプションが用意されています。 ZRS では、ローカル冗長ストレージ (LRS) より高いマネージド ディスクの可用性を提供します。 ただし、LRS ディスクでは 1 つのデータ センター内の 3 つのコピーにデータを同期的に書き込むため、LRS ディスクの書き込み待機時間は ZRS ディスクより優れています。

## <a name="locally-redundant-storage-for-managed-disks"></a>マネージド ディスクのローカル冗長ストレージ

ローカル冗長ストレージ (LRS) では、選択されたリージョンにある 1 つのデータ センター内でデータを 3 回レプリケートします。 LRS は、サーバー ラックとドライブの障害からデータを保護します。 

自然災害やハードウェアの問題のために発生する可能性のあるゾーン全体の障害から、LRS ディスクを使用してアプリケーションを保護できる方法がいくつかあります。
- 2 つのゾーンにデータを同期的に書き込み、災害期間中は別のゾーンに自動的にフェールオーバーできる SQL Server AlwaysOn などのアプリケーションを使用します。
- ZRS スナップショットを使用して LRS ディスクの頻繁なバックアップを取得します。
- [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md) 経由で LRS ディスクのゾーン間のディザスター リカバリーを有効にします。 ただし、ゾーン間のディザスター リカバリーではゼロ RPO (回復ポイントの目標) は提供されません。

ワークフローでゾーン間のアプリケーション レベルの同期書き込みがサポートされていないか、またはアプリケーションがゼロ RPO を満たす必要がある場合は、ZRS ディスクが理想的です。

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>マネージド ディスクのゾーン冗長ストレージ (プレビュー)

ゾーン冗長ストレージ (ZRS) では、選択されたリージョンにある 3 つの Azure 可用性ゾーン間で Azure マネージド ディスクを同期的にレプリケートします。 各可用性ゾーンは、独立した電源、冷却装置、ネットワークを備えた独立した物理的な場所です。 

ZRS ディスクを使用すると、可用性ゾーン内の障害からの回復が可能になります。 ゾーン全体が停止した場合は、ZRS ディスクを別のゾーン内の VM にアタッチできます。 また、ZRS ディスクを共有ディスクとして使用すると、SQL FCI、SAP ASCS/SCS、GFS2 などのクラスター化または分散アプリケーションの可用性を向上させることもできます。 共有 ZRS ディスクを異なるゾーン内のプライマリおよびセカンダリ VM にアタッチすると、ZRS と[可用性ゾーン](../availability-zones/az-overview.md)の両方を利用できます。 プライマリ ゾーンで障害が発生した場合は、[SCSI 永続的予約](disks-shared-enable.md#supported-scsi-pr-commands)を使用して、セカンダリ VM にすばやくフェールオーバーできます。

### <a name="limitations"></a>制限事項

プレビュー期間中、マネージド ディスクの ZRS には次の制限があります。

- Premium ソリッド ステート ドライブ (SSD) と Standard SSD でのみサポートされます。
- 現在は EastUS2EUAP リージョンでのみ使用できます。
- ZRS ディスクは、`2020-12-01` API を使用した Azure Resource Manager テンプレートでのみ作成できます。

プレビューには[ここ](https://aka.ms/ZRSDisksPreviewSignUp)でサインアップします。

### <a name="billing-implications"></a>課金への影響

詳細については、[Azure の価格ページ](https://azure.microsoft.com/pricing/details/managed-disks/)を参照してください。

### <a name="comparison-with-other-disk-types"></a>他のディスクの種類との比較

書き込み待機時間が長い点を除き、ZRS を使用したディスクは LRS を使用したディスクと同じです。 これらのパフォーマンス ターゲットは同じです。

### <a name="create-zrs-managed-disks"></a>ZRS マネージド ディスクを作成する

ZRS ディスクを作成するには、Azure Resource Manager テンプレートで `2020-12-01` API を使用してください。

#### <a name="create-a-vm-with-zrs-disks"></a>ZRS ディスクを使用して VM を作成する

```
$vmName = "yourVMName" 
$adminUsername = "yourAdminUsername"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_ZRS"
$dataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "yourResourceGroupName"

New-AzResourceGroup -Name $resourceGroupName -Location $region
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMWithZRSDataDisks.json" `
-resourceName $vmName `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $dataDiskType
```

#### <a name="create-vms-with-a-shared-zrs-disk-attached-to-the-vms-in-different-zones"></a>異なるゾーン内の VM にアタッチされた共有 ZRS ディスクを使用して VM を作成する

```
$vmNamePrefix = "yourVMNamePrefix"
$adminUsername = "yourAdminUserName"
$adminPassword = ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$osDiskType = "StandardSSD_LRS"
$sharedDataDiskType = "Premium_ZRS"
$region = "eastus2euap"
$resourceGroupName = "zrstesting1"

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMsWithASharedDisk.json" `
-vmNamePrefix $vmNamePrefix `
-adminUsername $adminUsername `
-adminPassword $adminPassword `
-region $region `
-osDiskType $osDiskType `
-dataDiskType $sharedDataDiskType
```

#### <a name="create-a-virtual-machine-scale-set-with-zrs-disks"></a>ZRS ディスクを使用して仮想マシン スケール セットを作成する

```
$vmssName="yourVMSSName"
$adminUsername="yourAdminName"
$adminPassword=ConvertTo-SecureString "yourAdminPassword" -AsPlainText -Force
$region="eastus2euap"
$osDiskType="StandardSSD_LRS"
$dataDiskType="Premium_ZRS"

New-AzResourceGroupDeployment -ResourceGroupName zrstesting `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/ZRSDisks/CreateVMSSWithZRSDisks.json" `
-vmssName "yourVMSSName" `
-adminUsername "yourAdminName" `
-adminPassword $password `
-region "eastus2euap" `
-osDiskType "StandardSSD_LRS" `
-dataDiskType "Premium_ZRS" `
```

## <a name="next-steps"></a>次のステップ

- これらのサンプル [Azure Resource Manager テンプレートを使用して、ZRS ディスクを使用する VM を作成します](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/ZRSDisks)。