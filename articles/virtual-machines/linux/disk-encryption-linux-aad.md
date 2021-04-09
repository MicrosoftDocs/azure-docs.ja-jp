---
title: Linux IaaS VM の Azure AD アプリと Azure Disk Encryption (以前のリリース)
description: この記事では、Linux IaaS VM で Microsoft Azure Disk Encryption を有効にする手順を説明します。
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: cec391ba998165af4dd9339b719a3b73cb330c16
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104601792"
---
# <a name="enable-azure-disk-encryption-with-azure-ad-on-linux-vms-previous-release"></a>Linux VM で Azure AD を使用して Azure Disk Encryption を有効にする (以前のリリース)

Azure Disk Encryption の新しいリリースでは、VM ディスク暗号化を有効にするために Azure Active Directory (Azure AD) アプリケーション パラメーターを指定する必要はありません。 新しいリリースでは、暗号化を有効にする手順の途中で、Azure AD の資格情報を指定する必要がなくなりました。 すべての新しい VM は、新しいリリースを使用して、Azure AD アプリケーション パラメーターを指定せずに暗号化する必要があります。 新しいリリースを使用して VM のディスク暗号化を有効にする方法の手順については、[Linux VM の Azure Disk Encryption](disk-encryption-linux.md) に関するページを参照してください。 Azure AD アプリケーション パラメーターで既に暗号化された VM はまだサポートされていますが、AAD 構文を使用して保持し続ける必要があります。

有効にできるディスク暗号化シナリオは多数あり、手順はシナリオによって異なる場合があります。 以下のセクションでは、Linux IaaS VM 用のシナリオについて詳しく説明します。 ディスク暗号化は、[サポートされている VM サイズとオペレーティング システム](disk-encryption-overview.md#supported-vms-and-operating-systems)の仮想マシンにのみ適用できます。 また、次の前提条件を満たしている必要があります。

- [追加の VM 要件](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [ネットワークとグループ ポリシー](disk-encryption-overview-aad.md#networking-and-group-policy)
- [暗号化キーのストレージ要件](disk-encryption-overview-aad.md#encryption-key-storage-requirements)

ディスクを暗号化する前に、[スナップショット](snapshot-copy-managed-disk.md)を作成するか、バックアップを作成するか、もしくはその両方を実行します。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 マネージド ディスクを含む VM では、暗号化する前にバックアップが必要になります。 バックアップを作成した後、Set-AzVMDiskEncryptionExtension コマンドレットで -skipVmBackup パラメーターを指定して、マネージド ディスクを暗号化できます。 暗号化された VM のバックアップと復元方法の詳細については、[Azure Backup](../../backup/backup-azure-vms-encryption.md) に関する記事を参照してください。 

>[!WARNING]
 > - これまで [Azure AD アプリで Azure Disk Encryption](disk-encryption-overview-aad.md) を使用してこの VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 この暗号化された VM に対して [Azure Disk Encryption](disk-encryption-overview.md) を使用することはできません。それはサポートされていないシナリオであり、暗号化された VM 用の Azure AD アプリケーションからの切り替えはまだサポートされていないことを意味します。
 > - 暗号化シークレットがリージョンの境界を越えないようにするため、Azure Disk Encryption ではキー コンテナーと VM を同じリージョンに併置する必要があります。 暗号化する VM と同じリージョン内にキー コンテナーを作成して使用します。
 > - Linux OS ボリュームの暗号化には数時間かかる場合があります。 通常は、データ ボリュームの暗号化よりも Linux OS ボリュームの暗号化に時間がかかります。
> - Linux OS ボリュームを暗号化する場合、VM は利用不可と見なす必要があります。 暗号化プロセス中にアクセスする必要がある、開かれたファイルがブロックされることを回避するために、暗号化の進行中は SSH login を避けることを強くお勧めします。 進行状況を確認するために、[Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) コマンドまたは [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) コマンドを使用します。 30GB の OS ボリュームに対するこのプロセスの実行には数時間かかり、加えてデータ ボリュームを暗号化するための時間がかかることが予測されます。 データ ボリュームの暗号化の時間は、**暗号化形式の全て** のオプションが使用されている場合を除いて、データ ボリュームのサイズと数量に比例します。 
 > - Linux VM での暗号化の無効化は、データ ボリュームに対してのみサポートされます。 OS ボリュームが暗号化されている場合、暗号化の無効化はデータ ボリュームまたは OS ボリュームではサポートされません。 

 

## <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm"></a><a name="bkmk_RunningLinux"> </a> 既存または実行中の IaaS Linux VM で暗号化を有効にする

このシナリオでは、Azure Resource Manager テンプレート、PowerShell コマンドレット、または Azure CLI コマンドを使用して、暗号化を有効にすることができます。 

>[!IMPORTANT]
 >Azure Disk Encryption を有効にする前に、Azure Disk Encryption 以外を使用して、マネージド ディスク ベースの VM インスタンスのスナップショットまたはバックアップを作成する必要があります。 Azure portal からマネージド ディスクのスナップショットを取得することも、[Azure Backup](../../backup/backup-azure-vms-encryption.md) を使用することもできます。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 バックアップを作成した後、Set-AzVMDiskEncryptionExtension コマンドレットを使用して -skipVmBackup パラメーターを指定して、マネージド ディスクを暗号化できます。 バックアップが作成されておらず、このパラメーターを指定していない場合、マネージド ディスク ベースの VM に対して、Set-AzVMDiskEncryptionExtension コマンドを実行するとエラーが発生します。 
>
>暗号化を有効または無効にすると、VM が再起動する場合があります。 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-the-azure-cli"></a><a name="bkmk_RunningLinuxCLI"> </a>Azure CLI を使用して既存または実行中の Linux VM で暗号化を有効にする 
暗号化された VHD でのディスク暗号化は、[Azure CLI 2.0](/cli/azure) コマンドライン ツールをインストールして使用することで有効化できます。 これは、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用してブラウザーで使用することも、ローカル コンピューターにインストールして PowerShell セッションで使用することもできます。 Azure 内にある既存または実行中の IaaS Linux VM で暗号化を有効にするには、次の CLI コマンドを使用します。

[az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) コマンドを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。

-  **クライアント シークレットを使用して実行中の VM を暗号化する:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:**
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

   >[!NOTE]
   > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> </br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。

- **ディスクが暗号化されていることを確認する:** IaaS VM の暗号化の状態を確認するには、[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) コマンドを使用します。 

     ```azurecli-interactive
         az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) コマンドを使用します。 Linux VM 用のデータ ボリュームでのみ、暗号化を無効にすることができます。
    
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type DATA
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-by-using-powershell"></a><a name="bkmk_RunningLinuxPSH"> </a> PowerShell を使用して既存または実行中の Linux VM で暗号化を有効にする
[Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットを使用して、Azure で実行中の IaaS 仮想マシンで暗号化を有効にします。 ディスクを暗号化する前に、[スナップショット](snapshot-copy-managed-disk.md)の作成または [Azure Backup](../../backup/backup-azure-vms-encryption.md) で VM のバックアップの作成、あるいはその両方を行います。 実行中の Linux VM を暗号化するために、PowerShell スクリプトで -skipVmBackup パラメーターが既に指定されています。

- **クライアント シークレットを使用して実行中の VM を暗号化する:** 以下のスクリプトでは変数を初期化し、Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、キー コンテナー、Azure AD アプリ、およびクライアント シークレットが既に作成されている必要があります。 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、My-AAD-client-ID、My-AAD-client-secret をそれぞれ実際の値に置き換えます。 -VolumeType parameter を変更し、暗号化するディスクを指定します。

     ```azurepowershell
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $KVRGname = 'MyKeyVaultResourceGroup';
         $vmName = 'MySecureVM';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:** Azure Disk Encryption では、ご利用のキー コンテナーで既存のキーを指定し、暗号化を有効にしたときに生成されたディスク暗号化シークレットをラップすることができます。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、キー コンテナーへの書き込みの前に、そのキーを使用して暗号化シークレットがラップされます。 -VolumeType parameter を変更し、暗号化するディスクを指定します。 

     ```azurepowershell
         $KVRGname = 'MyKeyVaultResourceGroup';
         $VMRGName = 'MyVirtualMachineResourceGroup';
         $aadClientID = 'My-AAD-client-ID';
         $aadClientSecret = 'My-AAD-client-secret';
         $KeyVaultName = 'MySecureVault';
         $keyEncryptionKeyName = 'MyKeyEncryptionKey';
         $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
         $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
         $KeyVaultResourceId = $KeyVault.ResourceId;
         $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

  >[!NOTE]
  > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> </br>
  key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 
    
- **ディスクが暗号化されていることを確認する:** IaaS VM の暗号化の状態を確認するには、[Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) コマンドレットを使用します。 
    
     ```azurepowershell-interactive 
         Get-AzVmDiskEncryptionStatus -ResourceGroupName MyVirtualMachineResourceGroup -VMName MySecureVM
     ```
    
- **ディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) コマンドレットを使用します。 Linux VM 用のデータ ボリュームでのみ、暗号化を無効にすることができます。
     
     ```azurepowershell-interactive 
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```


### <a name="enable-encryption-on-an-existing-or-running-iaas-linux-vm-with-a-template"></a><a name="bkmk_RunningLinux"> </a> テンプレートを使用して既存または実行中の IaaS Linux VM で暗号化を有効にする

Azure 内にある既存または実行中の IaaS Linux VM でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm)を使用して有効化できます。

1. Azure クイックスタート テンプレートで、 **[Azure に配置する]** を選択します。

2. サブスクリプション、リソース グループ、リソース グループの場所、パラメーター、法律条項、契約を選択します。 **[作成]** を選択して、既存または実行中の IaaS VM で暗号化を有効にします。

次の表は、Azure AD のクライアント ID を使用する既存または実行中の VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| AADClientID | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント ID。 |
| AADClientSecret | Key Vault にシークレットを書き込むためのアクセス許可を持つ Azure AD アプリケーションのクライアント シークレット。 |
| KeyVaultName | キーのアップロード先となる Key Vault の名前。 これは、Azure CLI コマンド `az keyvault show --name "MySecureVault" --query KVresourceGroup` を使用して取得できます。 |
|  keyEncryptionKeyURL | 生成されたキーの暗号化に使用されるキー暗号化キーの URL。 このパラメーターは、**UseExistingKek** ドロップダウン リストで **nokek** を選択した場合には省略可能です。 **UseExistingKek** ドロップダウン リストで **kek** を選択した場合は、_keyEncryptionKeyURL_ 値を入力する必要があります。 |
| volumeType | 暗号化操作が実行されるボリュームの種類。 サポートされている有効な値は _OS_ または _All_ です。 (OS ディスクおよびデータ ディスクに対してサポートされる Linux ディストリビューションとそのバージョンについては、前述の前提条件セクションを参照してください)。 |
| sequenceVersion | BitLocker 操作のシーケンス バージョン。 ディスク暗号化操作が同じ VM で実行されるたびに、このバージョン番号をインクリメントします。 |
| vmName | 暗号化操作が実行される VM の名前。 |
| パスフレーズ | データ暗号化キーとして強力なパスフレーズを入力します。 |



## <a name="use-the-encryptformatall-feature-for-data-disks-on-linux-iaas-vms"></a><a name="bkmk_EFA"> </a>Linux IaaS VM 上のデータ ディスクに対して EncryptFormatAll 機能を使用する
EncryptFormatAll パラメーターを使用すると、Linux データ ディスクを暗号化する時間が短縮されます。 特定の条件を満たすパーティションが (現在のファイル システムで) フォーマットされます。 続いて、コマンドの実行前にそれらが元の場所に再マウントされます。 条件を満たすデータ ディスクを除外する場合は、コマンドの実行前にそのディスクをマウント解除できます。

 このコマンドを実行すると、あらかじめマウントされたドライブがフォーマットされます。 その後、空になったドライブ上で暗号化レイヤーが開始されます。 このオプションを選択すると、VM に接続されている一時的なディスクも暗号化されます。 リセットされた一時的なドライブは、次の機会に Azure Disk Encryption ソリューションによって、VM 用に再フォーマットおよび再暗号化されます。

>[!WARNING]
> VM のデータ ボリュームに必要なデータがある場合は、EncryptFormatAll を使用しないでください。 ディスクをマウント解除することで、そのディスクを暗号化の対象から除外できます。 運用環境の VM で EncryptFormatAll を使用する前に、EncryptFormatAll をテスト用 VM で試し、機能パラメーターとその意味について理解してください。 EncryptFormatAll オプションはデータ ディスクをフォーマットするため、ディスク上のデータはすべて失われます。 手順を進める前に、除外するディスクが確実にマウント解除されていることを確認してください。 </br></br>
 >暗号化の設定の更新中にこのパラメーターを設定すると、実際の暗号化の前に再起動が行われる可能性があります。 その場合、フォーマットしたくないディスクを fstab ファイルから削除する必要があります。 同様に、暗号化操作を開始する前に、暗号化フォーマットしたいパーティションを fstab ファイルに追加する必要があります。 

### <a name="encryptformatall-criteria"></a><a name="bkmk_EFACriteria"> </a> EncryptFormatAll 条件
パーティションが以下の条件を *すべて* 満たしている場合に限り、このパラメーターはすべてのパーティションを確認して暗号化します。 
- ルート/OS/ブート パーティションではない
- まだ暗号化されていない
- BEK ボリュームではない
- RAID ボリュームではない
- LVM ボリュームではない
- マウントされている

RAID または LVM ボリュームではなく RAID または LVM ボリュームを構成するディスクを暗号化します。

### <a name="use-the-encryptformatall-parameter-with-a-template"></a><a name="bkmk_EFATemplate"> </a> EncryptFormatAll パラメーターをテンプレートで使用する
EncryptFormatAll オプションを使用するには、Linux VM を暗号化する既存の Azure Resource Manager テンプレートを使用して、AzureDiskEncryption リソースの **EncryptionOperation** フィールドを変更します。

1. たとえば、[実行中の Linux IaaS VM を暗号化する Resource Manager テンプレート](https://github.com/vermashi/azure-quickstart-templates/tree/encrypt-format-running-linux-vm/201-encrypt-running-linux-vm)を使用します。 
2. Azure クイックスタート テンプレートで、 **[Azure に配置する]** を選択します。
3. **EncryptionOperation** フィールドを **EnableEncryption** から **EnableEncryptionFormatAl** に変更します。
4. サブスクリプション、リソース グループ、リソース グループの場所、その他のパラメーター、法律条項、および契約を選択します。 **[作成]** を選択して、既存または実行中の IaaS VM で暗号化を有効にします。


### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a><a name="bkmk_EFAPSH"> </a> EncryptFormatAll パラメーターを PowerShell コマンドレットで使用する
[Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットを EncryptFormatAll パラメーターと共に使用します。

**クライアント シークレットと EncryptFormatAll を使用して、実行中の VM を暗号化する:** たとえば、以下のスクリプトでは変数を初期化し、EncryptFormatAll パラメーターを指定して Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、キー コンテナー、Azure AD アプリ、およびクライアント シークレットが既に作成されている必要があります。 MyKeyVaultResourceGroup、MyVirtualMachineResourceGroup、MySecureVM、MySecureVault、My-AAD-client-ID、My-AAD-client-secret をそれぞれ実際の値に置き換えます。
  
   ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup'; 
     $aadClientID = 'My-AAD-client-ID';
     $aadClientSecret = 'My-AAD-client-secret';
     $KeyVaultName = 'MySecureVault';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
      
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -EncryptFormatAll
   ```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a><a name="bkmk_EFALVM"> </a> EncryptFormatAll パラメーターを論理ボリューム マネージャー (LVM) で使用する 
LVM-on-crypt のセットアップをお勧めします。 以下に示すすべての例では、デバイス パスとマウント ポイントをユース ケースに適したものに置き換えてください。 このセットアップは次の手順で行うことができます。

- VM を構成するデータ ディスクを追加します。
- それらのディスクをフォーマットおよびマウントして fstab ファイルに追加します。

    1. 新しく追加されたディスクをフォーマットします。 ここでは、Azure によって生成されたシンボリック リンクを使用します。 シンボリック リンクを使用すると、デバイス名の変更に関連する問題を回避できます。 詳細については、[デバイス名の問題のトラブルシューティング](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)に関する記事を参照してください。
    
        ```console
        mkfs -t ext4 /dev/disk/azure/scsi1/lun0
        ```

    2. ディスクをマウントします。

        ```console
        mount /dev/disk/azure/scsi1/lun0 /mnt/mountpoint
        ```

    3. fstab に追加します。

        ```console
        echo "/dev/disk/azure/scsi1/lun0 /mnt/mountpoint ext4 defaults,nofail 1 2" >> /etc/fstab
        ```

    4. -EncryptFormatAll を指定して Set-AzVMDiskEncryptionExtension PowerShell コマンドレットを実行し、これらのディスクを暗号化します。

       ```azurepowershell-interactive
        Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl "https://mykeyvault.vault.azure.net/" -EncryptFormatAll
        ```

    5. これらの新しいディスク上に LVM を設定します。 VM の起動が完了した後で、暗号化されたドライブのロックが解除されます。 そのため、LVM のマウントをその後に遅延させる必要があります。




## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a><a name="bkmk_VHDpre"> </a> お客様が暗号化した VHD と暗号化キーから作成された新しい IaaS VM
このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 以下のセクションでは、Resource Manager テンプレートと CLI コマンドについて詳しく説明します。 

付録の手順を使用して、Azure で使用できる事前に暗号化されたイメージを準備します。 イメージを作成したら、次のセクションの手順に従って、暗号化された Azure VM を作成できます。

* [事前に暗号化された Linux VHD を準備する](disk-encryption-sample-scripts.md)

>[!IMPORTANT]
 >Azure Disk Encryption を有効にする前に、Azure Disk Encryption 以外を使用して、マネージド ディスク ベースの VM インスタンスのスナップショットまたはバックアップを作成する必要があります。 ポータルからマネージド ディスクのスナップショットを取得することも、[Azure Backup](../../backup/backup-azure-vms-encryption.md)を使用することもできます。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 バックアップを作成した後、Set-AzVMDiskEncryptionExtension コマンドレットを使用して -skipVmBackup パラメーターを指定して、マネージド ディスクを暗号化できます。 バックアップが作成されておらず、このパラメーターを指定していない場合、マネージド ディスク ベースの VM に対して、Set-AzVMDiskEncryptionExtension コマンドを実行するとエラーが発生します。 
>
>暗号化を有効または無効にすると、VM が再起動する場合があります。



### <a name="use-azure-powershell-to-encrypt-iaas-vms-with-pre-encrypted-vhds"></a><a name="bkmk_VHDprePSH"> </a> Azure PowerShell を使用して事前に暗号化された VHD で IaaS VM を暗号化する 
PowerShell コマンドレット [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) を使用して、暗号化された VHD でディスク暗号化を有効にすることができます。 次の例では、一般的ないくつかのパラメーターを示します。 

```powershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>新しく追加されたデータ ディスクで暗号化を有効にする
[az vm disk attach](add-disk.md) を使用して、または [Azure portal](attach-disk-portal.md) を通じて新しいデータ ディスクを追加できます。 暗号化の前に、新しく接続されたデータ ディスクをマウントする必要があります。 暗号化の実行中はデータ ドライブが使用できなくなるため、そのドライブの暗号化を要求する必要があります。 

### <a name="enable-encryption-on-a-newly-added-disk-with-the-azure-cli"></a>Azure CLI を使用して新しく追加されたディスクで暗号化を有効にする
 VM が以前に "All" で暗号化された場合、--volume-type パラメーターは All のままにしておく必要があります。 All には OS とデータ ディスクの両方が含まれます。 VM が以前にボリュームの種類 "OS" で暗号化された場合は、--volume-type パラメーターを All に変更して、OS と新しいデータ ディスクの両方が含まれるようにする必要があります。 VM がボリュームの種類 "Data" でのみ暗号化された場合、ここに示すように Data のままにできます。 新しいデータ ディスクを VM に追加してアタッチするだけでは、暗号化の準備としては不十分です。 暗号化を有効にする前に、新しくアタッチしたディスクをフォーマットして、VM 内で適切にマウントする必要もあります。 Linux では、[永続的なブロック デバイス名](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)を使用して /etc/fstab にディスクをマウントする必要があります。 

PowerShell 構文とは異なり、CLI では暗号化を有効にするときに一意のシーケンス バージョンを指定する必要はありません。 CLI では独自の一意のシーケンス バージョン値が自動的に生成され、使用されます。

-  **クライアント シークレットを使用して実行中の VM を暗号化する:** 
    
     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI/my Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
         az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --aad-client-id "<my spn created with CLI which is the Azure AD ClientID>"  --aad-client-secret "My-AAD-client-secret" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell を使用して新しく追加されたディスクで暗号化を有効にする
 PowerShell を使用して Linux 用の新しいディスクを暗号化する場合は、新しいシーケンス バージョンを指定する必要があります。 シーケンス バージョンは一意である必要があります。 次のスクリプトでは、シーケンス バージョン用の GUID が生成されます。 
 

-  **クライアント シークレットを使用して実行中の VM を暗号化する:** 以下のスクリプトでは変数を初期化し、Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、キー コンテナー、Azure AD アプリ、およびクライアント シークレットが既に作成されている必要があります。 MyVirtualMachineResourceGroup、MyKeyVaultResourceGroup、MySecureVM、MySecureVault、My-AAD-client-ID、My-AAD-client-secret をそれぞれ実際の値に置き換えます。 -VolumeType パラメーターは、OS ディスクではなく、データ ディスクに設定されます。 VM が以前にボリュームの種類 "OS" または "All" で暗号化された場合は、-VolumeType パラメーターを All に変更して、OS と新しいデータ ディスクの両方が含まれるようにする必要があります。

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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```
- **クライアント シークレットをラップするために KEK を使用して実行中の VM を暗号化する:** Azure Disk Encryption では、ご利用のキー コンテナーで既存のキーを指定し、暗号化を有効にしたときに生成されたディスク暗号化シークレットをラップすることができます。 キー暗号化キーが指定されている場合、Azure Disk Encryption では、キー コンテナーへの書き込みの前に、そのキーを使用して暗号化シークレットがラップされます。 -VolumeType パラメーターは、OS ディスクではなく、データ ディスクに設定されます。 VM が以前にボリュームの種類 "OS" または "All" で暗号化された場合は、-VolumeType パラメーターを All に変更して、OS と新しいデータ ディスクの両方が含まれるようにする必要があります。

     ```azurepowershell
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
         $sequenceVersion = [Guid]::NewGuid();
    
         Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion;
     ```


>[!NOTE]
> disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。 </br> </br>
key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。

## <a name="disable-encryption-for-linux-vms"></a>Linux VM に対して暗号化を無効にする
Azure PowerShell、Azure CLI、または Resource Manager テンプレートを使用して暗号化を無効にすることができます。 

>[!IMPORTANT]
>Linux VM での Azure Disk Encryption による暗号化の無効化は、データ ボリュームに対してのみサポートされます。 OS ボリュームが暗号化されている場合、暗号化の無効化はデータ ボリュームまたは OS ボリュームではサポートされません。 

- **Azure PowerShell を使用してディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzureRmVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) コマンドレットを使用します。 
     ```azurepowershell-interactive
         Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM' [--volume-type {ALL, DATA, OS}]
     ```

- **Azure CLI を使用して暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) コマンドを使用します。 
     ```azurecli-interactive
         az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type [ALL, DATA, OS]
     ```
- **Resource Manager テンプレートを使用して暗号化を無効にする:** 暗号化を無効にするには、「[Disable encryption on a running Linux VM](https://aka.ms/decrypt-linuxvm)」 (実行中の Linux VM での暗号化を無効にする) テンプレートを使用します。
     1. **[Azure に配置する]** を選択します。
     2. サブスクリプション、リソース グループ、場所、VM、法律条項、および契約を選択します。
     3. **[購入]** をクリックして、実行中の Windows VM でディスク暗号化を無効にします。 


## <a name="next-steps"></a>次のステップ

- [Linux 用の Azure Disk Encryption の概要](disk-encryption-overview-aad.md)
- [Azure AD を使用した Azure Disk Encryption 用のキー コンテナーの作成と構成 (以前のリリース)](disk-encryption-key-vault-aad.md)