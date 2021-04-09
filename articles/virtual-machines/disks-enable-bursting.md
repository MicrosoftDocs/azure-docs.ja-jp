---
title: オンデマンドのディスク バーストを有効にする
description: マネージド ディスクでオンデマンドのディスク バーストを有効にします。
author: albecker1
ms.author: albecker
ms.date: 03/02/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 733d441705c7c77f0667f88151e96f76975ee0b2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104596401"
---
# <a name="enable-on-demand-bursting"></a>オンデマンドのバーストを有効にする

Premium ソリッド ステート ドライブ (SSD) には、クレジット ベースのバーストとオンデマンドのバーストという、使用可能な 2 つのバースト モデルがあります。 この記事では、オンデマンドのバーストに切り替える方法を説明します。 オンデマンド モデルを使用するディスクは、プロビジョニングされた元のターゲットを超えてバーストできます。 オンデマンドのバーストは、ワークロードに必要な頻度で、最大バースト ターゲットまで発生します。 オンデマンドのバーストには追加料金が発生します。

ディスク バーストの詳細については、「[マネージド ディスクのバースト](disk-bursting.md)」を参照してください。

> [!IMPORTANT]
> クレジット ベースのバーストを使用する場合は、この記事の手順に従う必要はありません。 既定では、対象となるすべてのディスクでクレジット ベースのバーストが有効になっています。

オンデマンドのバーストを有効にする前に、次の点を理解しておいてください。

[!INCLUDE [managed-disk-bursting-regions-limitations](../../includes/managed-disk-bursting-regions-limitations.md)]

### <a name="regional-availability"></a>リージョン別の提供状況

[!INCLUDE [managed-disk-bursting-availability](../../includes/managed-disk-bursting-availability.md)]

## <a name="get-started"></a>はじめに

オンデマンドのバーストは、Azure PowerShell モジュール、Azure CLI、または Azure Resource Manager のテンプレートを使用して有効にすることができます。 次の例では、オンデマンドのバーストを有効にした新しいディスクを作成し、既存のディスクでオンデマンドのバーストを有効にする方法について説明します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

オンデマンドのバーストのコマンドレットは、バージョン 5.5.0 以降の Azure CLI モジュールで使用できます。 別の方法として、[Azure Cloud Shell](https://shell.azure.com/) を使用することもできます。
### <a name="create-an-empty-data-disk-with-on-demand-bursting"></a>オンデマンドのバーストを有効にした空のデータ ディスクを作成する

オンデマンドのバーストを有効にするには、マネージド ディスクのサイズが 512 GiB を超えている必要があります。 `<myResourceGroupDisk>` と `<myDataDisk>` のパラメーターを置き換えてから、次のスクリプトを実行して、 オンデマンドのバーストを有効にした Premium SSD を作成します。

```azurepowershell
Set-AzContext -SubscriptionName <yourSubscriptionName>

$diskConfig = New-AzDiskConfig -Location 'WestCentralUS' -CreateOption Empty -DiskSizeGB 1024 -SkuName Premium_LRS -BurstingEnabled $true

$dataDisk = New-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> -Disk $diskConfig
```

### <a name="enable-on-demand-bursting-on-an-existing-disk"></a>既存のディスクでオンデマンド バーストを有効にする

オンデマンドのバーストを有効にするには、マネージド ディスクのサイズが 512 GiB を超えている必要があります。 `<myResourceGroupDisk>` と `<myDataDisk>` のパラメーターを置き換えてから、次のコマンドを実行して、既存のディスクでオンデマンド バーストを有効にします。

```azurepowershell
New-AzDiskUpdateConfig -BurstingEnabled $true | Update-AzDisk -ResourceGroupName <myResourceGroupDisk> -DiskName <myDataDisk> //Set the flag to $false to disable on-demand bursting
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

オンデマンドのバーストのコマンドレットは、バージョン 2.19.0 以降の [Azure CLI モジュール](/cli/azure/install-azure-cli)で使用できます。 別の方法として、[Azure Cloud Shell](https://shell.azure.com/) を使用することもできます。

### <a name="create-and-attach-a-on-demand-bursting-data-disk"></a>オンデマンドのバーストを有効にしたデータ ディスクを作成して接続する

オンデマンドのバーストを有効にするには、マネージド ディスクのサイズが 512 GiB を超えている必要があります。 `<yourDiskName>`、`<yourResourceGroup>`、および `<yourVMName>` のパラメーターを置き換えてから、次のコマンドを実行して、 オンデマンドのバーストを有効にした Premium SSD を作成します。

```azurecli
az disk create -g <yourResourceGroup> -n <yourDiskName> --size-gb 1024 --sku Premium_LRS -l westcentralus --enable-bursting true

az vm disk attach --vm-name <yourVMName> --name <yourDiskName> --resource-group <yourResourceGroup>
```

### <a name="enable-on-demand-bursting-on-an-existing-disk---cli"></a>既存のディスクでオンデマンド バーストを有効にする - CLI

オンデマンドのバーストを有効にするには、マネージド ディスクのサイズが 512 GiB を超えている必要があります。 `<myResourceGroupDisk>` と `<yourDiskName>` のパラメーターを置き換えてから、次のコマンドを実行して、既存のディスクでオンデマンド バーストを有効にします。

```azurecli
az disk update --name <yourDiskName> --resource-group <yourResourceGroup> --enable-bursting true //Set the flag to false to disable on-demand bursting
```

# <a name="azure-resource-manager"></a>[Azure Resource Manager](#tab/azure-resource-manager)

`2020-09-30` ディスク API を使用して、512 GiB を超えるサイズの、新規作成または既存の Premium SSD でオンデマンドのバーストを有効にすることができます。 この `2020-09-30` API では、新しいプロパティ、`burstingEnabled` が導入されています。 既定では、このプロパティが False に設定されています。 次のサンプル テンプレートでは、ディスク バーストを有効にした 1TiB の Premium SSD が米国中西部で作成されています。

```
{
  "$schema": "http://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "diskSkuName": {
        "type": "string",
        "defaultValue": "Premium_LRS" //Supported on premium SSDs only
},
    "dataDiskSizeInGb": {
      "type": "string",
      "defaultValue": "1024" //Supported on disk size > 512 GiB
},
    "location": {
      "type": "string",
      "defaultValue": "westcentralus" //Preview regions: West Central US
},
"diskApiVersion": {
      "type": "string",
      "defaultValue": "2020-09-30" //Preview supported version: 2020-09-30 or above
    }
  },
  "resources": [
    {
      "apiVersion": "[parameters('diskApiVersion')]",
      "type": "Microsoft.Compute/disks",
      "name": "[parameters('diskName')]",
      "location": "[parameters(location)]",
      "properties": {
        "creationData": {
          "createOption": "Empty"
        },
        "diskSizeGB": "[parameters('dataDiskSizeInGb')]",
        "burstingEnabled": "true" //Feature flag to enable disk bursting on disks > 512 GiB
      },
      "sku": {
        "name": "[parameters('diskSkuName')]"
      }
    ]
}
```
---
 
## <a name="next-steps"></a>次のステップ

バースト リソースに関する分析情報を得る方法については、[ディスク バーストのメトリック](disks-metrics.md)に関する記事を参照してください。