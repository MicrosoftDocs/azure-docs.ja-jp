---
title: Windows VM での Azure Disk Encryption シナリオ
description: この記事では、さまざまなシナリオで Windows VM に対して Microsoft Azure Disk Encryption を有効にする手順について説明します
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 948712b684d1cd1b072862b7253d745f89b0cc56
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245928"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Windows VM での Azure Disk Encryption シナリオ

Azure Disk Encryption では、BitLocker 外部キー保護機能を使用して、Azure 仮想マシン (VM) の OS とデータ ディスクに対するボリューム暗号化が実現されます。ディスク暗号化キーとシークレットを制御および管理できるように、Azure Disk Encryption は Azure Key Vault に統合されています。 サービスの概要については、「[Azure Disk Encryption for Windows VMs](disk-encryption-overview.md)」 (Windows VM 用の Azure Disk Encryption) を参照してください。

ディスク暗号化シナリオは多数あり、手順はシナリオによって異なる場合があります。 以下のセクションでは、Windows VM 用のシナリオについて詳しく説明します。

ディスク暗号化は、[サポートされている VM サイズとオペレーティング システム](disk-encryption-overview.md#supported-vms-and-operating-systems)の仮想マシンにのみ適用できます。 また、次の前提条件を満たしている必要があります。

- [ネットワーク要件](disk-encryption-overview.md#networking-requirements)
- [グループ ポリシーの要件](disk-encryption-overview.md#group-policy-requirements)
- [暗号化キーのストレージ要件](disk-encryption-overview.md#encryption-key-storage-requirements)



>[!IMPORTANT]
> - これまで Azure AD で Azure Disk Encryption を使用して VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 詳細については、「[Azure AD での Azure Disk Encryption (以前のリリース)](disk-encryption-overview-aad.md)」を参照してください。 
>
> - ディスクを暗号化する前に[スナップショットを取得](snapshot-copy-managed-disk.md)するか、バックアップを作成する (またはその両方を行う) 必要があります。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 マネージド ディスクを含む VM では、暗号化する前にバックアップが必要になります。 バックアップを作成したら、[Set-AzVMDiskEncryptionExtension cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットで -skipVmBackup パラメーターを指定して、マネージド ディスクを暗号化できます。 暗号化された VM のバックアップと復元方法の詳細については、「[暗号化された Azure VM をバックアップおよび復元する](../../backup/backup-azure-vms-encryption.md)」を参照してください。 
>
> - 暗号化を有効または無効にすると、VM が再起動する場合があります。


## <a name="install-tools-and-connect-to-azure"></a>ツールをインストールし、Azure に接続する

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>既存または実行中の Windows VM に対して暗号化を有効にする
このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 仮想マシンを拡張するためのスキーマの情報が必要な場合は、[Windows 用 Azure Disk Encryption 拡張機能](../extensions/azure-disk-enc-windows.md)に関する記事を参照してください。

## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a>既存または実行中の IaaS Windows VM で暗号化を有効にする
テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 仮想マシンを拡張するためのスキーマの情報が必要な場合は、[Windows 用 Azure Disk Encryption 拡張機能](../extensions/azure-disk-enc-windows.md)に関する記事を参照してください。

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a>Azure PowerShell を使用して既存または実行中の VM で暗号化を有効にする 
[Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。 

-  **実行中の VM を暗号化する:** 以下のスクリプトでは変数を初期化し、Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、およびキー コンテナーが既に作成されている必要があります。 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault を実際の値に置き換えます。

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **KEK を使用して実行中の VM を暗号化する:** 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 

- **ディスクが暗号化されていることを確認する:** IaaS VM の暗号化の状態を確認するには、[Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) コマンドレットを使用します。 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **ディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) コマンドレットを使用します。 OS とデータ ディスクの両方が暗号化されている場合は、Windows VM におけるデータ ディスクの暗号化の無効化が想定どおりに機能しません。 代わりに、すべてのディスクで暗号化を無効にしてください。

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Azure CLI を使用して既存または実行中の VM で暗号化を有効にする
[az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) コマンドを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。

- **実行中の VM を暗号化する:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **KEK を使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。 </br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 

- **ディスクが暗号化されていることを確認する:** IaaS VM の暗号化の状態を確認するには、[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) コマンドを使用します。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) コマンドを使用します。 OS とデータ ディスクの両方が暗号化されている場合は、Windows VM におけるデータ ディスクの暗号化の無効化が想定どおりに機能しません。 代わりに、すべてのディスクで暗号化を無効にしてください。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```

### <a name="using-the-resource-manager-template"></a>Resource Manager テンプレートの使用

[実行中の Windows VM を暗号化するための Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm-without-aad)を使用して、Azure で既存または実行中の IaaS Windows VM に対してディスク暗号化を有効にすることができます。


1. Azure クイックスタート テンプレートで、 **[Azure に配置する]** をクリックします。

2. サブスクリプション、リソース グループ、場所、設定、法律条項、および契約を選択します。 **[購入]** をクリックして、既存または実行中の IaaS VM で暗号化を有効にします。

次の表は、既存または実行中の VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| vmName | 暗号化操作を実行する VM の名前。 |
| KeyVaultName | BitLocker キーのアップロード先となる Key Vault の名前。 コマンドレット `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` または Azure CLI コマンド `az keyvault list --resource-group "MyKeyVaultResourceGroup"` を使用して取得できます。|
| keyVaultResourceGroup | キー コンテナーが含まれているリソース グループの名前|
|  keyEncryptionKeyURL | キー暗号化キーの URL (形式は https://&lt;keyvault-name&gt;.vault.azure.net/key/&lt;key-name&gt;)。 KEK を使用しない場合、このフィールドは空白のままにします。 |
| volumeType | 暗号化操作が実行されるボリュームの種類。 有効な値は _OS_、_Data_、および _All_ です。 
| forceUpdateTag | 操作が強制実行である必要があるたびに、GUID のような一意の値を渡します。 |
| resizeOSDisk | OS パーティションは、システム ボリュームを分割する前に、完全な OS VHD が占有できるようにサイズ変更する必要があります。 |
| location | すべてのリソースの場所。 |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>お客様が暗号化した VHD と暗号化キーから作成された新しい IaaS VM

このシナリオでは、PowerShell コマンドレットまたは CLI コマンドを使用して、事前に暗号化された VHD および関連する暗号化キーから新しい VM を作成できます。 

「[Prepare a pre-encrypted Windows VHD](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)」 (事前に暗号化された Windows VHD を準備する) に記載の手順を使用します。 イメージを作成したら、次のセクションの手順に従って、暗号化された Azure VM を作成できます。


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a>Azure PowerShell を使用して事前に暗号化された VHD を含む VM を暗号化する
PowerShell コマンドレット [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) を使用して、暗号化された VHD でディスク暗号化を有効にすることができます。 次の例では、一般的ないくつかのパラメーターを示します。 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>新しく追加されたデータ ディスクで暗号化を有効にする
[PowerShell を使用して Windows VM に新しいディスクを追加する](attach-disk-ps.md)ことができます。また、[Azure Portal を使用する](attach-managed-disk-portal.md)こともできます。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell を使用して新しく追加されたディスクで暗号化を有効にする
 PowerShell を使用して Windows VM 用の新しいディスクを暗号化する場合は、新しいシーケンス バージョンを指定する必要があります。 シーケンス バージョンは一意である必要があります。 次のスクリプトでは、シーケンス バージョン用の GUID が生成されます。 場合によっては、Azure Disk Encryption 拡張機能により、新しく追加されたデータ ディスクが自動的に暗号化される可能性があります。 新しいディスクがオンラインになった後に VM を再起動すると、自動的に暗号化が行われることが多いです。 通常、これは以前に VM でディスクの暗号化を実行したときに、ボリュームの種類に "All" を指定していることが原因です。 自動の暗号化が新しく追加されたデータ ディスク上で発生する場合は、新しいシーケンス バージョンで Set-AzVmDiskEncryptionExtension コマンドレットをもう一度実行することをお勧めします。 新しいデータ ディスクが自動的に暗号化されており、暗号化したくない場合は、まずすべてのドライブの暗号化を解除して、ボリュームの種類に OS を指定して新しいシーケンス バージョンで再暗号化します。 
  
 

-  **実行中の VM を暗号化する:** 以下のスクリプトでは変数を初期化し、Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、およびキー コンテナーが既に作成されている必要があります。 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault を実際の値に置き換えます。 この例では -VolumeType パラメーターに "All" が使用されているため、OS ボリュームとデータ ボリュームの両方が含まれます。 OS ボリュームのみを暗号化する場合は、-VolumeType パラメーターに "OS" を使用します。 

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **KEK を使用して実行中の VM を暗号化する:** この例では -VolumeType パラメーターに "All" が使用されているため、OS ボリュームとデータ ボリュームの両方が含まれます。 OS ボリュームのみを暗号化する場合は、-VolumeType パラメーターに "OS" を使用します。

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI を使用して新しく追加されたディスクで暗号化を有効にする
 暗号化を有効にするコマンドを実行する際に、Azure CLI コマンドによって、新しいシーケンス バージョンが自動的に提供されます。 この例では、"All" が volume-type パラメーターに使用されています。 OS ディスクのみ暗号化する場合は、volume-type パラメーターを OS に変更する必要があります。 PowerShell 構文とは異なり、CLI では暗号化を有効にする際にユーザーが一意のシーケンス バージョンを指定する必要はありません。 CLI では独自の一意のシーケンス バージョン値が自動的に生成され、使用されます。   

-  **実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **KEK を使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```


## <a name="disable-encryption"></a>暗号化を無効にする
Azure PowerShell、Azure CLI、または Resource Manager テンプレートを使用して暗号化を無効にすることができます。 OS とデータ ディスクの両方が暗号化されている場合は、Windows VM におけるデータ ディスクの暗号化の無効化が想定どおりに機能しません。 代わりに、すべてのディスクで暗号化を無効にしてください。

- **Azure PowerShell を使用してディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) コマンドレットを使用します。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' -VolumeType "all"
     ```

- **Azure CLI を使用して暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) コマンドを使用します。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```
- **Resource Manager テンプレートを使用して暗号化を無効にする:** 

    1. [実行中の Windows VM でディスク暗号化を無効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm-without-aad)ためのテンプレートで **[Azure に配置する]** をクリックします。
    2. サブスクリプション、リソース グループ、場所、VM、ボリュームの種類、法律条項、および契約を選択します。
    3.  **[購入]** をクリックして、実行中の Windows VM でディスク暗号化を無効にします。 

## <a name="unsupported-scenarios"></a>サポートされていないシナリオ

Azure Disk Encryption は、次のシナリオ、機能、およびテクノロジには対応していません。

- Basic レベルの VM または従来の VM の作成方法を使用して作成された VM を暗号化する。
- ソフトウェア ベースの RAID システムを使用して構成されている Windows VM を暗号化する。
- オンプレミスのキー管理システムとの統合。
- Azure Files (共有ファイル システム)。
- ネットワーク ファイル システム (NFS)。
- 動的ボリューム。
- Windows Server コンテナー。これにより、コンテナーごとに動的ボリュームが作成されます。
- エフェメラル OS ディスク。
- DFS、GFS、DRDB、CephFS を含む (ただし、これだけではありません) 共有/分散ファイル システムの暗号化。

## <a name="next-steps"></a>次の手順

- [Azure Disk Encryption の概要](disk-encryption-overview.md)
- [Azure Disk Encryption のサンプル スクリプト](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption のトラブルシューティング](disk-encryption-troubleshooting.md)
