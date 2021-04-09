---
title: インクルード ファイル
description: インクルード ファイル
services: virtual-machines
author: msmbaldwin
ms.service: virtual-machines
ms.topic: include
ms.date: 10/06/2019
ms.author: mbaldwin
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6f8cfc314c75221a88b58095cc71ea685280ac49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102501017"
---
## <a name="create-a-resource-group"></a>リソース グループを作成する

*リソース グループが既にある場合は、「[キー コンテナーを作成する](#create-a-key-vault)」に進むことができます。*

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

リソース グループを作成するには、[az group create](/cli/azure/group#az-group-create) Azure CLI コマンド、または [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) Azure PowerShell コマンドを使用するか、[Azure portal ](https://portal.azure.com) から行います。

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az group create --name "myResourceGroup" --location eastus
```
### <a name="azure-powershell"></a>Azure PowerShell
```azurepowershell-interactive
New-AzResourceGroup -Name "myResourceGroup" -Location "EastUS"
```

## <a name="create-a-key-vault"></a>Key Vault を作成します

*キー コンテナーが既にある場合は、「[キー コンテナーに高度なアクセス ポリシーを設定する](#set-key-vault-advanced-access-policies)」に進むことができます。*

キー コンテナーを作成するには、[az keyvault create](/cli/azure/keyvault#az-keyvault-create) Azure CLI コマンド、[New-AzKeyvault](/powershell/module/az.keyvault/new-azkeyvault) Azure Powershell コマンド、[Azure portal](https://portal.azure.com)、または [Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create) のいずれかを使用します。

>[!WARNING]
> キー コンテナーと VM は、同じサブスクリプションに配置する必要があります。 また、暗号化シークレットがリージョンの境界を確実に超えないようにするため、Azure Disk Encryption では Key Vault と VM を同じリージョンに併置する必要もあります。 暗号化する VM と同じサブスクリプションとリージョン内に Key Vault を作成して使用します。 

各キー コンテナーには一意の名前が必要です。 次の例では、<your-unique-keyvault-name> をお使いのキー コンテナーの名前に置き換えてください。

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してキー コンテナーを作成する場合は、"--enabled-for-disk-encryption" フラグを追加します。

```azurecli-interactive
az keyvault create --name "<your-unique-keyvault-name>" --resource-group "myResourceGroup" --location "eastus" --enabled-for-disk-encryption
```

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell を使用してキー コンテナーを作成する場合は、"-EnabledForDiskEncryption" フラグを追加します。

```azurepowershell-interactive
New-AzKeyvault -name "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup" -Location "eastus" -EnabledForDiskEncryption
```
### <a name="resource-manager-template"></a>Resource Manager テンプレート

[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create)を使用してもキー コンテナーを作成できます。

1. Azure クイックスタート テンプレートで、 **[Azure に配置する]** をクリックします。
2. サブスクリプション、リソース グループ、リソース グループの場所、キー コンテナー名、オブジェクト ID、法律条項および契約を選択し、 **[購入]** をクリックします。 


##  <a name="set-key-vault-advanced-access-policies"></a>キー コンテナーに高度なアクセス ポリシーを設定する

Azure プラットフォームには、Key Vault 内の暗号化キーまたはシークレットへのアクセス権を付与する必要があります。これにより、ボリュームをブートして暗号化する際に、それらの情報を VM に提供できるようになります。 

作成時に (前の手順で示したように) ディスクの暗号化、デプロイ、またはテンプレートのデプロイに対してキー コンテナーを有効にしなかった場合は、その高度なアクセス ポリシーを更新する必要があります。  

### <a name="azure-cli"></a>Azure CLI

[az keyvault update](/cli/azure/keyvault#az-keyvault-update) を使用し、キー コンテナーのディスク暗号化を有効にします。 

 - **Key Vault のディスク暗号化を有効にする:** Enabled-for-disk-encryption が必要です。 

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **必要に応じて Key Vault のデプロイを有効にする:** 仮想マシンの作成時など、このキー コンテナーがリソース作成時に参照される場合、Microsoft.Compute リソース プロバイダーがこのキー コンテナーからシークレットを取得できるようにします。

     ```azurecli-interactive
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **必要に応じて Key Vault のテンプレートのデプロイを有効にする:** Resource Manager がコンテナーからシークレットを取得することを許可します。
     ```azurecli-interactive  
     az keyvault update --name "<your-unique-keyvault-name>" --resource-group "MyResourceGroup" --enabled-for-template-deployment "true"
     ```

###  <a name="azure-powershell"></a>Azure PowerShell
 キー コンテナーの PowerShell コマンドレット [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) を使用してキー コンテナーのディスク暗号化を有効にします。

  - **Key Vault のディスク暗号化を有効にする:** Azure Disk Encryption には EnabledForDiskEncryption が必要です。
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDiskEncryption
     ```

  - **必要に応じて Key Vault のデプロイを有効にする:** 仮想マシンの作成時など、このキー コンテナーがリソース作成時に参照される場合、Microsoft.Compute リソース プロバイダーがこのキー コンテナーからシークレットを取得できるようにします。

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForDeployment
     ```

  - **必要に応じて Key Vault のテンプレートのデプロイを有効にする:** テンプレートのデプロイでこのキー コンテナーが参照される場合、Azure Resource Manager がこのキー コンテナーからシークレットを取得できるようにします。

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "MyResourceGroup" -EnabledForTemplateDeployment
     ```

### <a name="azure-portal"></a>Azure portal

1. キー コンテナーを選択し、 **[アクセス ポリシー]** に移動し、 **[クリックして高度なアクセス ポリシーを表示する]** を選択します。
2. **[ボリューム暗号化に対して Azure Disk Encryption へのアクセスを有効にする]** というボックスをオンにします。
3. 必要に応じて、 **[展開に対して Azure Virtual Machines へのアクセスを有効にする]** と **[テンプレートの展開に対して Azure Resource Manager へのアクセスを有効にする]** の一方または両方をオンにします。 
4. **[保存]** をクリックします。

    ![Azure Key Vault の高度なアクセス ポリシー](../articles/virtual-machines/media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-kek"></a>キー暗号化キー (KEK) を設定する

暗号化キーのセキュリティに対する追加レイヤーとしてキー暗号化キー (KEK) を使用する場合は、キー コンテナーに KEK を追加します。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、Key Vault への書き込みの前に、そのキーを使用して暗号化シークレットがラップされます。

新しい KEK を生成するには、Azure CLI [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) コマンド、Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) コマンドレット、または [Azure portal](https://portal.azure.com/) を使用します。 RSA キーの種類を生成する必要があります。Azure Disk Encryption では、楕円曲線キーの使用はまだサポートされていません。

代わりに、オンプレミスのキー管理 HSM から KEK をインポートすることもできます。 詳細については、[Key Vault](../articles/key-vault/keys/hsm-protected-keys.md) のドキュメントを参照してください。

キー コンテナー KEK の URL はバージョン管理されている必要があります。 Azure では、このバージョン管理制限が適用されます。 有効なシークレットと KEK URI については、次の例をご覧ください。

* 有効なシークレット URL の例: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
* 有効な KEK URL の例: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

Azure Disk Encryption では、ポート番号をキー コンテナー シークレットおよび KEK URL の一部として指定することはサポートされません。 サポートされない Key Vault URL とサポートされる Key Vault URL の例については、以下の例を参照してください。

  * 許容されるキー コンテナー URL: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * 許容されないキー コンテナー URL *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="azure-cli"></a>Azure CLI

Azure CLI [az keyvault key create](/cli/azure/keyvault/key#az-keyvault-key-create) コマンドを使用して新しい KEK を生成し、キー コンテナーに格納します。

```azurecli-interactive
az keyvault key create --name "myKEK" --vault-name "<your-unique-keyvault-name>" --kty RSA
```

代わりに、Azure CLI [az keyvault key import](/cli/azure/keyvault/key#az-keyvault-key-import) コマンドを使用して秘密キーをインポートすることもできます。

どちらの場合も、Azure CLI [az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) --key-encryption-key パラメーターに KEK の名前を指定します。 

```azurecli-interactive
az vm encryption enable -g "MyResourceGroup" --name "myVM" --disk-encryption-keyvault "<your-unique-keyvault-name>" --key-encryption-key "myKEK"
```

###  <a name="azure-powershell"></a>Azure PowerShell 

Azure PowerShell [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) コマンドレットを使用して新しい KEK を生成し、キー コンテナーに格納します。

 ```powershell-interactive
Add-AzKeyVaultKey -Name "myKEK" -VaultName "<your-unique-keyvault-name>" -Destination "HSM"
```

代わりに、Azure PowerShell [az keyvault key import](/cli/azure/keyvault/key#az-keyvault-key-import) コマンドを使用して秘密キーをインポートすることもできます。

どちらの場合も、KEK キー コンテナーの ID とご自分の KEK の URL を Azure PowerShell [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) -KeyEncryptionKeyVaultId パラメーターと -KeyEncryptionKeyUrl パラメーターに指定します。 この例では、ディスク暗号化キーと KEK の両方に同じキー コンテナーを使用していることを前提としています。

 ```powershell-interactive
$KeyVault = Get-AzKeyVault -VaultName "<your-unique-keyvault-name>" -ResourceGroupName "myResourceGroup"
$KEK = Get-AzKeyVaultKey -VaultName "<your-unique-keyvault-name>" -Name "myKEK"

Set-AzVMDiskEncryptionExtension -ResourceGroupName MyResourceGroup -VMName "MyVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyVaultId $KeyVault.ResourceId -KeyEncryptionKeyUrl $KEK.Id -SkipVmBackup -VolumeType All
 ```