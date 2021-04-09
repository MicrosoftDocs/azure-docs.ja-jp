---
title: Azure Migrate Server Migration を使用して、サーバー側暗号化 (SSE) とカスタマー マネージド キー (CMK) により VMware 仮想マシンを Azure に移行する
description: Azure Migrate Server Migration を使用して、サーバー側暗号化 (SSE) とカスタマー マネージド キー (CMK) により VMware VM を Azure に移行する方法について説明します。
author: anvar-ms
ms.author: anvar
ms.manager: bsiva
ms.topic: how-to
ms.date: 03/12/2020
ms.openlocfilehash: 8a174c3b2bfb390eb7d691ae1bdcb0e28dde9032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96751089"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>サーバー側暗号化とカスタマー マネージド キーを使用して VMware VM を Azure VM に移行する

この記事では、Azure Migrate Server Migration (エージェントレス レプリケーション) を使用して、サーバー側暗号化 (SSE) とカスタマー マネージド キー (CMK) によって暗号化されたディスクで、VMware VM を Azure 仮想マシンに移行する方法について説明します。

Azure Migrate Server Migration ポータルでの操作によって、[エージェントレス レプリケーションを使用して VMware VM を Azure に移行](tutorial-migrate-vmware.md)できます。 現在、ポータルの操作では、Azure でレプリケートされたディスクに対して SSE と CMK を有効にすることはできません。 レプリケートされたディスクに対して SSE と CMK を有効にする機能は、現在、REST API を通じてのみ使用できます。 この記事では、VMware VM をレプリケートし、SSE と CMK を使用するように Azure でレプリケートされたディスクを構成するために、[Azure Resource Manager テンプレート](../azure-resource-manager/templates/overview.md)を作成してデプロイする方法について説明します。

この記事の例では、[Azure PowerShell](/powershell/azure/new-azureps-module-az) を使用して、Resource Manager テンプレートの作成とデプロイに必要なタスクを実行します。

カスタマー マネージド キー (CMK) を使用したマネージド ディスクのサーバー側暗号化 (SSE) の詳細を[ご覧ください](../virtual-machines/disk-encryption.md)。

## <a name="prerequisites"></a>前提条件

- ツールの要件を理解するために、エージェントレス レプリケーションを使用した VMware VM から Azure への移行に関する[チュートリアルを確認してください](tutorial-migrate-vmware.md)。
- [こちらの手順に従って](./create-manage-projects.md)、Azure Migrate プロジェクトを作成し、**Azure Migrate:Server Migration** ツールをプロジェクトに追加します。
- [こちらの手順に従って](how-to-set-up-appliance-vmware.md)、オンプレミス環境で VMware 用の Azure Migrate アプライアンスを設定し、検出を完了します。

## <a name="prepare-for-replication"></a>レプリケーションの準備

VM の検出が完了すると、[サーバーの移行] タイルの [検出済みサーバー] 行に、アプライアンスによって検出された VMware VM の数が表示されます。

VM のレプリケートを開始する前に、レプリケーション インフラストラクチャを準備する必要があります。

1. ターゲット リージョンに Service Bus インスタンスを作成します。 Service Bus は、Server Migration サービスと通信してレプリケーションと移行を調整するために、オンプレミスの Azure Migrate アプライアンスによって使用されます。
2. レプリケーションから操作ログを転送するためのストレージ アカウントを作成します。
3. Azure Migrate アプライアンスがレプリケーション データをアップロードする先のストレージ アカウントを作成します。
4. Key Vault を作成し、手順 3 と 4 で作成したストレージ アカウントで BLOB アクセス用の共有アクセス署名トークンを管理するようにその Key Vault を構成します。
5. 手順 1 で作成した Service Bus の共有アクセス署名トークンを生成し、前の手順で作成した Key Vault でそのトークンのシークレットを作成します。
6. Key Vault アクセス ポリシーを作成して、オンプレミスの Azure Migrate アプライアンス (アプライアンスの AAD アプリを使用) と Server Migration Service に Key Vault へのアクセス権を付与します。
7. レプリケーション ポリシーを作成し、前の手順で作成したレプリケーション インフラストラクチャの詳細を使用して Server Migration Service を構成します。

レプリケーション インフラストラクチャは、移行のターゲット Azure リージョンと、VM の移行先となるターゲット Azure サブスクリプションに作成する必要があります。

Server Migration ポータルでの操作により、プロジェクトで VM を初めてレプリケートするときに、レプリケーション インフラストラクチャの準備が自動的に行われるため、この準備は簡単です。 この記事は、ポータルでの操作を使用して 1 つ以上の VM を既にレプリケートしていること、およびレプリケーション インフラストラクチャが既に作成されていることを前提としています。 ここでは、既存のレプリケーション インフラストラクチャの詳細を検出する方法と、CMK を使用したレプリケーションの設定に使用する Resource Manager テンプレートへの入力としてこれらの詳細を使用する方法について説明します。

### <a name="identifying-replication-infrastructure-components"></a>レプリケーション インフラストラクチャのコンポーネントを識別する

1. Azure portal で、リソース グループ ページにアクセスし、Azure Migrate プロジェクトを作成したリソース グループを選択します。
2. 左側のメニューから **[デプロイ]** を選択し、 *"Microsoft.MigrateV2.VMwareV2EnableMigrate"* という文字列で始まるデプロイ名を検索します。 このプロジェクトで VM のレプリケーションを設定するために、ポータルでの操作によって作成された Resource Manager テンプレートの一覧が表示されます。 このようなテンプレートのいずれかをダウンロードし、それをベースとして使用して、CMK を使用したレプリケーション用のテンプレートを準備します。
3. テンプレートをダウンロードするには、前の手順の文字列パターンに一致するデプロイを選択し、左側のメニューで **[テンプレート]** を選択し、上部のメニューで **[ダウンロード]** をクリックします。 template.json ファイルをローカルに保存します。 最後の手順でこのテンプレート ファイルを編集します。

## <a name="create-a-disk-encryption-set"></a>ディスク暗号化セットを作成する

ディスク暗号化セット オブジェクトによって、SSE に使用する CMK を含む Key Vault にマネージド ディスクがマップされます。 CMK を使用して VM をレプリケートするには、ディスク暗号化セットを作成し、それを入力としてレプリケーション操作に渡します。

Azure PowerShell を使用してディスク暗号化セットを作成するには、[こちら](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md)の例に従ってください。 ディスク暗号化セットが、VM の移行先となるターゲット サブスクリプションと、移行のターゲット Azure リージョンに作成されていることを確認します。

ディスク暗号化セットは、カスタマー マネージド キーでマネージド ディスクを暗号化するように、またはカスタマー マネージド キーとプラットフォーム キーの両方で二重に暗号化するように構成できます。 保存時の二重暗号化オプションを使用するには、[こちら](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md)で説明しているようにディスク暗号化セットを構成します。

次に示す例では、カスタマー マネージド キーを使用するようにディスク暗号化セットを構成しています。

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>移行する VMware VM の詳細を取得する

この手順では、Azure PowerShell を使用して、移行する必要がある VM の詳細を取得します。 これらの詳細は、レプリケーション用の Resource Manager テンプレートを作成するために使用します。 具体的には、次の 2 つのプロパティが対象です。

- 検出された VM のマシン リソース ID。
- VM のディスクとそのディスクの識別子の一覧。

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

VM が検出された Azure Migrate アプライアンスに対応する SiteId 文字列の値をコピーします。 上記の例で、SiteId は *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"* です。

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

移行するマシンの ResourceId、name、および disk uuid の値をコピーします。
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>レプリケーション用の Resource Manager テンプレートを作成する

- 任意のエディターで、手順「**レプリケーション インフラストラクチャのコンポーネントを識別する**」でダウンロードした Resource Manager テンプレート ファイルを開きます。
- 種類が *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"* であるリソースを除くすべてのリソースの定義をテンプレートから削除します。
- 上記の種類のリソース定義が複数ある場合は、1 つを除きすべてを削除します。 リソース定義から **dependsOn** プロパティの定義を削除します。
- この手順を終えると、次の例のような、同じ一連のプロパティを持つファイルになります。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- リソース定義で **name** プロパティを編集します。 name プロパティの最後の "/" に続く文字列を、(前の手順の) *$machine.Name* の値に置き換えます。
- **properties.providerSpecificDetails.vmwareMachineId** プロパティの値を、(前の手順の) *$machine.ResourceId* の値に変更します。
- **targetResourceGroupId**、**targetNetworkId**、**Targetsubnet Name** の値を、ターゲット リソース グループ ID、ターゲット仮想ネットワークのリソース ID、ターゲットのサブネット名にそれぞれ設定します。
- この VM に Azure ハイブリッド特典を適用するには、**licenseType** の値を "WindowsServer" に設定します。 この VM が Azure ハイブリッド特典に適合しない場合は、**licenseType** の値を NoLicenseType に設定します。
- **targetVmName** プロパティの値を、移行対象の VM に必要な Azure 仮想マシンの名前に変更します。
- 必要に応じて、**targetVmName** プロパティの下に **targetVmSize** という名前のプロパティを追加します。 **targetVmSize** プロパティの値を、移行対象の VM に必要な Azure 仮想マシンのサイズに変更します。
- **disksToInclude** プロパティは、1 つのオンプレミス ディスクを表す各リスト項目とレプリケーションのディスク入力の一覧です。 オンプレミス VM 上のディスクの数と同じ数のリスト項目を作成します。 リスト項目の **diskId** プロパティを、前の手順で特定したディスクの uuid に置き換えます。 VM の OS ディスクについては **isOSDisk** 値を "true" に設定し、他のすべてのディスクについては "false" に設定します。 **logStorageAccountId** および **logStorageAccountSasSecretName** プロパティは変更せずそのままにします。 **diskType** 値を、ディスクに使用する Azure マネージド ディスクの種類 (*Standard_LRS、Premium_LRS、StandardSSD_LRS*) に設定します。 CMK を使用して暗号化する必要があるディスクの場合は、**diskEncryptionSetId** という名前のプロパティを追加し、この値を手順「*ディスク暗号化セットを作成する*」で作成したディスク暗号化セットのリソース ID ( **$des.Id**) に設定します。
- 編集したテンプレート ファイルを保存します。 上記の例で、編集したテンプレート ファイルは次のようになります。

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>レプリケーションを設定する

これで、編集した Resource Manager テンプレートをプロジェクト リソース グループにデプロイして、VM のレプリケーションを設定できます。 [Azure Resource Manager テンプレートと Azure PowerShell を使用してリソースをデプロイする](../azure-resource-manager/templates/deploy-powershell.md)方法を参照してください。

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>次のステップ

ポータルでの操作を通じて[レプリケーションの状態を監視](tutorial-migrate-vmware.md#track-and-monitor)し、テスト移行および移行を実行します。