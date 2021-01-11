---
title: Azure PowerShell - VM ホストでエンドツーエンドの暗号化を有効にする
description: ホストでの暗号化を使用して Azure VM のエンドツーエンドの暗号化を有効にする方法。
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 08/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 6a352ecc2d2b02f03e2b55f7c5896ac905077921
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88814795"
---
# <a name="use-the-azure-powershell-module-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Azure PowerShell モジュールを使用してホストでの暗号化でエンドツーエンドでの暗号化を有効にする

ホストでの暗号化を有効にすると、VM ホスト上の格納データは、保存時に暗号化され、暗号化された状態でストレージ サービスに送られます。 ホストでの暗号化とその他のマネージド ディスクの暗号化の概要については、「[ホストでの暗号化 - VM データのエンドツーエンドの暗号化](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data)」を参照してください。

## <a name="restrictions"></a>制限

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

### <a name="supported-regions"></a>サポートされているリージョン

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

### <a name="supported-vm-sizes"></a>サポートされる VM のサイズ

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

また、プログラムによって VM のサイズを確認することもできます。 プログラムによって取得する方法については、「[サポートされている VM のサイズを確認する](#finding-supported-vm-sizes)」セクションを参照してください。

## <a name="prerequisites"></a>前提条件

VM または仮想マシン スケール セットに対してホストでの暗号化を使用できるようにするには、サブスクリプションで機能が有効になっている必要があります。 サブスクリプション ID と共に encryptionAtHost@microsoft.com に電子メールを送信して、サブスクリプションに対して機能を有効にします。

### <a name="create-an-azure-key-vault-and-diskencryptionset"></a>Azure Key Vault と DiskEncryptionSet の作成

機能が有効になったら、まだ行っていない場合は、Azure Key Vault と DiskEncryptionSet を設定する必要があります。

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-powershell](../../../includes/virtual-machines-disks-encryption-create-key-vault-powershell.md)]

## <a name="enable-encryption-at-host-for-disks-attached-to-vm-and-virtual-machine-scale-sets"></a>VM と仮想マシン スケール セットに接続されているディスクに対するホストでの暗号化を有効にする

ホストでの暗号化を有効にするには、API バージョン **2020-06-01** 以降を使用して、VM または仮想マシン スケール セットの securityProfile で新しいプロパティ EncryptionAtHost を設定します。

`"securityProfile": { "encryptionAtHost": "true" }`

## <a name="example-scripts"></a>スクリプトの例

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-customer-managed-keys"></a>カスタマー マネージド キーを使用して VM に接続されているディスクに対するホストでの暗号化を有効にする

以前に作成した DiskEncryptionSet のリソース URI を使用して、マネージド ディスク付き VM を作成します。

`<yourPassword>`、`<yourVMName>`、`<yourVMSize>`、`<yourDESName>`、`<yoursubscriptionID>`、`<yourResourceGroupName>`、`<yourRegion>` を置き換えてから、スクリプトを実行します。

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithCMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-diskEncryptionSetId "/subscriptions/<yoursubscriptionID>/resourceGroups/<yourResourceGroupName>/providers/Microsoft.Compute/diskEncryptionSets/<yourDESName>" `
-region "<yourRegion>"
```

### <a name="enable-encryption-at-host-for-disks-attached-to-a-vm-with-platform-managed-keys"></a>プラットフォーム マネージド キーを使用して VM に接続されているディスクに対するホストでの暗号化を有効にする

`<yourPassword>`、`<yourVMName>`、`<yourVMSize>`、`<yourResourceGroupName>`、`<yourRegion>` を置き換えてから、スクリプトを実行します。

```PowerShell
$password=ConvertTo-SecureString -String "<yourPassword>" -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName <yourResourceGroupName> `
-TemplateUri "https://raw.githubusercontent.com/Azure-Samples/managed-disks-powershell-getting-started/master/EncryptionAtHost/CreateVMWithDisksEncryptedAtHostWithPMK.json" `
-virtualMachineName "<yourVMName>" `
-adminPassword $password `
-vmSize "<yourVMSize>" `
-region "<yourRegion>"
```

## <a name="finding-supported-vm-sizes"></a>サポートされている VM のサイズを確認する

レガシ VM のサイズはサポートされていません。 サポートされている VM サイズの一覧は、次のいずれかで見つけることができます。

[リソースの SKU API](/rest/api/compute/resourceskus/list) を呼び出し、`EncryptionAtHostSupported` 機能が **True** に設定されていることを確認しています。

```json
    {
        "resourceType": "virtualMachines",
        "name": "Standard_DS1_v2",
        "tier": "Standard",
        "size": "DS1_v2",
        "family": "standardDSv2Family",
        "locations": [
        "CentralUSEUAP"
        ],
        "capabilities": [
        {
            "name": "EncryptionAtHostSupported",
            "value": "True"
        }
        ]
    }
```

または、[Get-AzComputeResourceSku](/powershell/module/az.compute/get-azcomputeresourcesku?view=azps-3.8.0) PowerShell コマンドレットを呼び出します。

```powershell
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
    foreach($capability in $vmSize.capabilities)
    {
        if($capability.Name -eq 'EncryptionAtHostSupported' -and $capability.Value -eq 'true')
        {
            $vmSize

        }

    }
}
```

## <a name="next-steps"></a>次のステップ

これらのリソースを作成し、構成したので、これらを使用してマネージド ディスクをセキュリティで保護することができます。 次のリンクには、個々のシナリオごとにスクリプトの例が含まれています。これは、マネージド ディスクをセキュリティで保護するために使用できます。

[Azure Resource Manager テンプレートのサンプル](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
