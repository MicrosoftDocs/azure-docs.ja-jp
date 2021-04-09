---
title: Service Fabric 管理対象クラスター (プレビュー) ノードのディスク暗号化を有効にする
description: ARM テンプレートを使用して、Windows の Azure Service Fabric 管理対象クラスター ノードのディスク暗号化を有効にする方法について説明します。
ms.topic: how-to
ms.date: 02/15/2021
ms.openlocfilehash: b7e56ff8db9f94b8c6681a1a7d69a4751b3f43a5
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/17/2021
ms.locfileid: "100641020"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-preview-nodes"></a>Service Fabric 管理対象クラスター (プレビュー) ノードのディスク暗号化を有効にする

このガイドでは、Azure Resource Manager (ARM) テンプレートを使用し、[仮想マシン スケール セット](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)用の [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) 機能を使用して、Windows の Service Fabric 管理対象クラスター ノードでディスク暗号化を有効にする方法について説明します。

> [!IMPORTANT]
> 仮想マシン スケール セットのディスク暗号化プレビューでは、まだイメージのアップグレードや再イメージ化はサポートされていません。 OS イメージをアップグレードする必要がある場合は使用しないでください。

## <a name="register-for-azure-disk-encryption"></a>Azure Disk Encryption での登録

仮想マシン スケール セットのディスク暗号化プレビューには、自己登録が必要です。 次のコマンドを実行します。

```powershell
Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
```

以下を実行して、登録の状態を確認します。

```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
```

状態が *Registered* に変われば、先に進むことができます。

## <a name="provision-a-key-vault-for-disk-encryption"></a>ディスク暗号化のために Key Vault をプロビジョニングする

Azure Disk Encryption では、ディスク暗号化キーとシークレットを制御および管理するために、Azure Key Vault が必要です。 Key Vault と Service Fabric 管理対象クラスターは、同じ Azure リージョンとサブスクリプションに存在している必要があります。 これらの要件が満たされていれば、新規または既存の Key Vault をディスク暗号化用に有効にして使用することができます。

### <a name="create-key-vault-with-disk-encryption-enabled"></a>ディスク暗号化を有効にして Key Vault を作成する

ディスク暗号化用の新しい Key Vault を作成するには、次のコマンドを実行します。 Key Vault のリージョンが [Service Fabric 管理対象クラスターでサポート](faq-managed-cluster.md#what-regions-are-supported-in-the-preview)されていて、ご自身のクラスターと同じリージョンにあることを確認します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
$resourceGroupName = "<rg-name>" 
$keyvaultName = "<kv-name>" 

az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
```

---

### <a name="update-existing-key-vault-to-enable-disk-encryption"></a>既存の Key Vault を更新してディスク暗号化を有効にする

既存の Key Vault でディスク暗号化を有効にするには、次のコマンドを実行します。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```powershell
# ps 

Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az keyvault update --name keyvaultName --enabled-for-disk-encryption 
```

---

## <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>ディスク暗号化のテンプレートとパラメーター ファイルを更新する

次の手順では、[既存の管理対象クラスター](tutorial-managed-cluster-deploy.md)でディスク暗号化を有効にするために必要なテンプレートの変更について説明します。 または、テンプレート https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption を使用して、ディスク暗号化を有効にした新しい Service Fabric 管理対象クラスターをデプロイすることもできます。

1. `keyVaultResourceId` の下にはご自身のサブスクリプション、リソース グループ名、コンテナー名を代入して、次のパラメーターをテンプレートに追加します。

```json
"parameters": { 
…
    "keyVaultResourceId": { 
        "type": "string", 
        "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
        "metadata": { 
            "description": "Full resource id of the Key Vault used for disk encryption." 
        } 
    },
    "volumeType": { 
        "type": "string", 
        "defaultValue": "All", 
        "metadata": { 
            "description": "Type of the volume OS or Data to perform encryption operation" 
        }
    }
}, 
```

2. 次に、テンプレート内の管理対象クラスター ノードの種類に `AzureDiskEncryption` VM 拡張機能を追加します。

```json
"properties": { 
    "vmExtensions": [ 
        { 
            "name": "AzureDiskEncryption", 
            "properties": { 
                "publisher": "Microsoft.Azure.Security", 
                "type": "AzureDiskEncryption", 
                "typeHandlerVersion": "2.1", 
                "autoUpgradeMinorVersion": true, 
                "settings": {                     
                    "EncryptionOperation": "EnableEncryption", 
                    "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
                    "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
                    "VolumeType": "[parameters('volumeType')]" 
                } 
            } 
        } 
    ] 
} 
```

3. 最後に、*keyVaultResourceId* にご自身のサブスクリプション、リソース グループ、キー コンテナー名を代入して、パラメーター ファイルを更新します。

```json
"parameters": { 
    … 
    "keyVaultResourceId": { 
        "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
        "value": "All" 
    }    
} 
```

## <a name="deploy-and-verify-the-changes"></a>変更をデプロイして確認する

準備ができたら、変更をデプロイして管理対象クラスターでディスク暗号化を有効にします。

```powershell
$clusterName = "<clustername>" 

New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\template_diskEncryption.json -TemplateParameterFile \.parameters_diskEncryption.json -Debug -Verbose 
```

`Get-AzVmssDiskEncryption` コマンドを使用して、ノードの種類の基になるスケール セットでディスク暗号化の状態を確認できます。 まず、管理対象クラスターのサポート リソースグループ (基になる仮想ネットワーク、ロード バランサー、パブリック IP、NSG、スケール セット、ストレージ アカウントを含む) の名前を確認する必要があります。 `VmssName` は必ず、確認するクラスター ノードの種類の名前 (デプロイ テンプレートで指定) に変更してください。

```powershell
$VmssName = "NT1"
$supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName).ClusterId
Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
```

出力はこのようになります。

```console
ResourceGroupName            : SFC_########-####-####-####-############
VmScaleSetName               : NT1
EncryptionEnabled            : True
EncryptionExtensionInstalled : True
```

## <a name="next-steps"></a>次のステップ

[サンプル: Standard SKU Service Fabric 管理対象クラスター、ディスク暗号化を有効にした 1 つのノードの種類](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Windows VM 用の Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md)

[Azure Resource Manager を使用して仮想マシン スケール セットを暗号化する](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
