---
title: Windows VM における Azure AD を使用した Azure Disk Encryption (以前のリリース)
description: この記事では、Windows IaaS VM で Microsoft Azure Disk Encryption を有効にする手順について説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 3b7f6f63953ba09e57e4586c698e16b9abb8aa1c
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555280"
---
# <a name="azure-disk-encryption-with-azure-ad-for-windows-vms-previous-release"></a>Windows VM における Azure AD を使用した Azure Disk Encryption (以前のリリース)

**Azure Disk Encryption の新しいリリースでは、VM ディスク暗号化を有効にするために Azure AD アプリケーション パラメーターを指定する必要はありません。新しいリリースでは、暗号化を有効にする手順の途中で、Azure AD の資格情報を指定する必要がなくなりました。すべての新しい VM は、新しいリリースを使用して、Azure AD アプリケーション パラメーターを指定せずに暗号化する必要があります。新しいリリースを使用して VM のディスク暗号化を有効にする手順を表示するには、[Windows VM の Azure Disk Encryption](disk-encryption-windows.md) に関するページを参照してください。Azure AD アプリケーション パラメーターで既に暗号化された VM はまだサポートされていますが、AAD 構文を使用して保持し続ける必要があります。**


有効にできるディスク暗号化シナリオは多数あり、手順はシナリオによって異なる場合があります。 以下のセクションでは、Windows IaaS VM 用のシナリオについて詳しく説明します。 ディスク暗号化を使用する前に、[Azure Disk Encryption の前提条件](disk-encryption-overview-aad.md)を満たす必要があります。 


>[!IMPORTANT]
> - ディスクを暗号化する前に[スナップショットを取得](snapshot-copy-managed-disk.md)するか、バックアップを作成する (またはその両方を行う) 必要があります。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 マネージド ディスクを含む VM では、暗号化する前にバックアップが必要になります。 バックアップを作成したら、[Set-AzVMDiskEncryptionExtension cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットで -skipVmBackup パラメーターを指定して、マネージド ディスクを暗号化できます。 暗号化された VM のバックアップと復元方法の詳細については、「[暗号化された Azure VM をバックアップおよび復元する](../../backup/backup-azure-vms-encryption.md)」を参照してください。 
>
> - 暗号化を有効または無効にすると、VM が再起動する場合があります。

## <a name="enable-encryption-on-new-iaas-vms-created-from-the-marketplace"></a>Marketplace から作成された新しい IaaS VM で暗号化を有効にする
Azure の Marketplace から作成された新しい IaaS Windows VM でのディスク暗号化を有効にするには、Resource Manager テンプレートを使用します。 このテンプレートでは、Windows Server 2012 ギャラリー イメージを使用して、新しい暗号化された Windows VM を作成します。

1. [Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-create-new-vm-gallery-image)で、**[Azure に配置する]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、パラメーター、法律条項、契約を選択します。 **[購入]** をクリックして、暗号化が有効になっている新しい IaaS VM をデプロイします。

3. テンプレートをデプロイした後、任意の方法を使用して、VM の暗号化の状態を確認します。
     - Azure CLI で [az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) コマンドを使用して確認します。 

         ```azurecli-interactive 
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
         ```

     - Azure PowerShell で [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) コマンドレットを使用して確認します。 

         ```azurepowershell-interactive
         Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
         ```

     -  VM を選択し、**[設定]** 見出しの下にある **[ディスク]** をクリックして、ポータルで暗号化の状態を確認します。 **[暗号化]** の下のグラフには、暗号化が有効かどうかが示されます。 
           ![Azure portal - ディスク暗号化が有効になっている状態](../media/disk-encryption/disk-encryption-fig2.png)

次の表は、Azure AD のクライアント ID を使用して Marketplace シナリオから作成された新しい VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 | 
| --- | --- |
| adminUserName | 仮想マシンの管理者ユーザー名。 |
| adminPassword | 仮想マシンの管理者ユーザー パスワード。 |
| newStorageAccountName | OS とデータの VHD を格納するためのストレージ アカウントの名前。 |
| vmSize | VM のサイズ。 現時点では、Standard A、D、および G シリーズのみがサポートされています。 |
| virtualNetworkName | VM NIC が属している必要がある VNet の名前。 |
| subnetName | VM NIC が属している必要がある VNet のサブネットの名前。 |
| AADClientID | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント ID。 |
| AADClientSecret | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント シークレット。 |
| keyVaultURL | BitLocker キーのアップロード先となる Key Vault の URL。 これは、コマンドレット `(Get-AzKeyVault -VaultName "MyKeyVault" -ResourceGroupName "MyKeyVaultResourceGroupName").VaultURI` または Azure CLI `az keyvault show --name "MySecureVault" --query properties.vaultUri` を使用して取得できます。 |
| keyEncryptionKeyURL | 生成された BitLocker キーの暗号化に使用されるキー暗号化キーの URL (省略可能)。 </br> </br>KeyEncryptionKeyURL は省略可能なパラメーターです。 独自の KEK を使用して、Key Vault でデータ暗号化キー (パスフレーズ シークレット) の保護を強化することができます。 |
| keyVaultResourceGroup | Key Vault のリソース グループ。 |
| vmName | 暗号化操作が実行される VM の名前。 |


## <a name="enable-encryption-on-existing-or-running-iaas-windows-vms"></a><a name="bkmk_RunningWinVM"></a> 既存または実行中の IaaS Windows VM で暗号化を有効にする
このシナリオでは、テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 次のセクションでは、Azure Disk Encryption を有効にする方法を詳しく説明します。 


### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a><a name="bkmk_RunningWinVMPSH"></a> Azure PowerShell を使用して既存または実行中の VM で暗号化を有効にする 
[Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。 Azure Disk Encryption での暗号化を PowerShell コマンドレットによって有効化する方法については、ブログ投稿「[Explore Azure Disk Encryption with Azure PowerShell - Part 1](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell)」(Azure PowerShell を使用した Azure Disk Encryption の操作 - パート 1) および「[Explore Azure Disk Encryption with Azure PowerShell - Part 2](/archive/blogs/azuresecurity/explore-azure-disk-encryption-with-azure-powershell-part-2)」(Azure PowerShell を使用した Azure Disk Encryption の操作 - パート 2) をご覧ください。

-  **クライアント シークレットを使用して実行中の VM を暗号化する:** 次のスクリプトでは変数を初期化し、Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、キー コンテナー、AAD アプリ、およびクライアント シークレットが既に作成されている必要があります。 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault、My-AAD-client-ID、My-AAD-client-secret をそれぞれ実際の値に置き換えます。
     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:** Azure Disk Encryption では、ご利用のキー コンテナーで既存のキーを指定し、暗号化を有効にしたときに生成されたディスク暗号化シークレットをラップすることができます。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、Key Vault への書き込みの前に、そのキーを使用して暗号化シークレットがラップされます。 

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = ‘MyExtraSecureVM’;
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```
     
   >[!NOTE]
   > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 

- **ディスクが暗号化されていることを確認する:** IaaS VM の暗号化の状態を確認するには、[Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) コマンドレットを使用します。 
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **ディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) コマンドレットを使用します。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-existing-or-running-vms-with--azure-cli"></a><a name="bkmk_RunningWinVMCLI"></a> Azure CLI を使用して既存または実行中の VM で暗号化を有効にする
[az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) コマンドを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。

- **クライアント シークレットを使用して実行中の VM を暗号化する:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。 </br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 

- **ディスクが暗号化されていることを確認する:** IaaS VM の暗号化の状態を確認するには、[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) コマンドを使用します。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) コマンドを使用します。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```


### <a name="using-the-resource-manager-template"></a><a name="bkmk_RunningWinVMwRM"> </a>Resource Manager テンプレートの使用
[実行中の Windows VM を暗号化するための Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-windows-vm)を使用して、Azure で既存または実行中の IaaS Windows VM に対してディスク暗号化を有効にすることができます。


1. Azure クイックスタート テンプレートで、 **[Azure に配置する]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、パラメーター、法律条項、契約を選択します。 **[購入]** をクリックして、既存または実行中の IaaS VM で暗号化を有効にします。

次の表は、Azure AD のクライアント ID を使用する既存または実行中の VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| AADClientID | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント ID。 |
| AADClientSecret | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント シークレット。 |
| KeyVaultName | BitLocker キーのアップロード先となる Key Vault の名前。 コマンドレット `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` または Azure CLI コマンド `az keyvault list --resource-group "MySecureGroup"` を使用して取得できます。|
|  keyEncryptionKeyURL | 生成された BitLocker キーの暗号化に使用されるキー暗号化キーの URL。 このパラメーターは、UseExistingKek ドロップダウン リストで **nokek** を選択した場合には省略可能です。 UseExistingKek ドロップダウン リストで **kek** を選択した場合は、_keyEncryptionKeyURL_ 値を入力する必要があります。 |
| volumeType | 暗号化操作が実行されるボリュームの種類。 有効な値は _OS_、_Data_、および _All_ です。 |
| sequenceVersion | BitLocker 操作のシーケンス バージョン。 ディスク暗号化操作が同じ VM で実行されるたびに、このバージョン番号をインクリメントします。 |
| vmName | 暗号化操作が実行される VM の名前。 |


## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a>お客様が暗号化した VHD と暗号化キーから作成された新しい IaaS VM
このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 以下のセクションでは、Resource Manager テンプレートと CLI コマンドについて詳しく説明します。 

付録の手順を使用して、Azure で使用できる事前に暗号化されたイメージを準備します。 イメージを作成したら、次のセクションの手順に従って、暗号化された Azure VM を作成できます。

* [事前に暗号化された Windows VHD を準備する](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a><a name="bkmk_VHDprePSH"> </a> Azure PowerShell を使用して事前に暗号化された VHD を含む VM を暗号化する
PowerShell コマンドレット [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) を使用して、暗号化された VHD でディスク暗号化を有効にすることができます。 次の例では、一般的ないくつかのパラメーターを示します。 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>新しく追加されたデータ ディスクで暗号化を有効にする
[PowerShell を使用して Windows VM に新しいディスクを追加する](attach-disk-ps.md)ことができます。また、[Azure Portal を使用する](attach-managed-disk-portal.md)こともできます。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell を使用して新しく追加されたディスクで暗号化を有効にする
 PowerShell を使用して Windows VM 用の新しいディスクを暗号化する場合は、新しいシーケンス バージョンを指定する必要があります。 シーケンス バージョンは一意である必要があります。 次のスクリプトでは、シーケンス バージョン用の GUID が生成されます。 場合によっては、Azure Disk Encryption 拡張機能により、新しく追加されたデータ ディスクが自動的に暗号化される可能性があります。 新しいディスクがオンラインになった後に VM を再起動すると、自動的に暗号化が行われることが多いです。 通常、これは以前に VM でディスクの暗号化を実行したときに、ボリュームの種類に "All" を指定していることが原因です。 自動の暗号化が新しく追加されたデータ ディスク上で発生する場合は、新しいシーケンス バージョンで Set-AzVmDiskEncryptionExtension コマンドレットをもう一度実行することをお勧めします。 新しいデータ ディスクが自動的に暗号化されており、暗号化したくない場合は、まずすべてのドライブの暗号化を解除して、ボリュームの種類に OS を指定して新しいシーケンス バージョンで再暗号化します。 
 

-  **クライアント シークレットを使用して実行中の VM を暗号化する:** 次のスクリプトでは変数を初期化し、Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、キー コンテナー、AAD アプリ、およびクライアント シークレットが既に作成されている必要があります。 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault、My-AAD-client-ID、My-AAD-client-secret をそれぞれ実際の値に置き換えます。 この例では -VolumeType パラメーターに "All" が使用されているため、OS ボリュームとデータ ボリュームの両方が含まれます。 OS ボリュームのみを暗号化する場合は、-VolumeType パラメーターに "OS" を使用します。 

     ```azurepowershell
      $sequenceVersion = [Guid]::NewGuid();
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $aadClientID = 'My-AAD-client-ID';
      $aadClientSecret = 'My-AAD-client-secret';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;
    ```
- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:** Azure Disk Encryption では、ご利用のキー コンテナーで既存のキーを指定し、暗号化を有効にしたときに生成されたディスク暗号化シークレットをラップすることができます。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、Key Vault への書き込みの前に、そのキーを使用して暗号化シークレットがラップされます。 この例では -VolumeType パラメーターに "All" が使用されているため、OS ボリュームとデータ ボリュームの両方が含まれます。 OS ボリュームのみを暗号化する場合は、-VolumeType パラメーターに "OS" を使用します。 

     ```azurepowershell
     $sequenceVersion = [Guid]::NewGuid();
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'all' –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI を使用して新しく追加されたディスクで暗号化を有効にする
  暗号化を有効にするコマンドを実行する際に、Azure CLI コマンドによって、新しいシーケンス バージョンが自動的に提供されます。 volume-type パラメーターに使用できる値は、All、OS、および Data です。 VM の一種類のディスクについてのみ暗号化する場合は、volume-type パラメーターを OS または Data に変更する必要があります。 この例では、"All" が volume-type パラメーターに使用されています。 

-  **クライアント シークレットを使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "all"
     ```


## <a name="enable-encryption-using-azure-ad-client-certificate-based-authentication"></a>Azure AD クライアント証明書ベースの認証を使用して、暗号化を有効にします。
KEK の有無に関係なく、クライアント証明書認証を使用することができます。 PowerShell スクリプトを使用する前に、証明書が既にキー コンテナーにアップロードされており、VM にデプロイされている必要があります。 KEK も使用している場合は、KEK が既に存在している必要があります。 詳細については、前提条件に関する記事の「[Certificate-based authentication for Azure AD](disk-encryption-key-vault-aad.md#certificate-based-authentication-optional)」(Azure AD の証明書ベースの認証) セクションを参照してください。


### <a name="enable-encryption-using-certificate-based-authentication-with-azure-powershell"></a>Azure PowerShell で証明書ベースの認証を使用して暗号化を有効にする

```powershell
## Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault, and ‘MySecureVM’.

$VMRGName = 'MyVirtualMachineResourceGroup'
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

# Fill in the certificate path and the password so the thumbprint can be set as a variable. 

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
```
  
### <a name="enable-encryption-using-certificate-based-authentication-and-a-kek-with-azure-powershell"></a>Azure PowerShell で証明書ベースの認証と KEK を使用して暗号化を有効にする

```powershell
# Fill in 'MyVirtualMachineResourceGroup', 'MyKeyVaultResourceGroup', 'My-AAD-client-ID', 'MySecureVault,, 'MySecureVM’, and "KEKName.

$VMRGName = 'MyVirtualMachineResourceGroup';
$KVRGname = 'MyKeyVaultResourceGroup';
$AADClientID ='My-AAD-client-ID';
$KeyVaultName = 'MySecureVault';
$VMName = 'MySecureVM';
$keyEncryptionKeyName ='KEKName';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

## Fill in the certificate path and the password so the thumbprint can be read and set as a variable.

$certPath = '$CertPath = "C:\certificates\mycert.pfx';
$CertPassword ='Password'
$Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
$aadClientCertThumbprint = $cert.Thumbprint;

# Enable disk encryption using the client certificate thumbprint and a KEK

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

## <a name="disable-encryption"></a>暗号化を無効にする
Azure PowerShell、Azure CLI、または Resource Manager テンプレートを使用して暗号化を無効にすることができます。 

- **Azure PowerShell を使用してディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) コマンドレットを使用します。 
     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

- **Azure CLI を使用して暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) コマンドを使用します。 
     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Resource Manager テンプレートを使用して暗号化を無効にする:** 

    1. [実行中の Windows VM でディスク暗号化を無効にする](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-running-windows-vm)ためのテンプレートで **[Azure に配置する]** をクリックします。
    2. サブスクリプション、リソース グループ、場所、VM、法律条項、および契約を選択します。
    3.  **[購入]** をクリックして、実行中の Windows VM でディスク暗号化を無効にします。 

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Azure Disk Encryption の概要](disk-encryption-overview.md)
