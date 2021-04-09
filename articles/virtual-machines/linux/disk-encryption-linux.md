---
title: Linux VM での Azure Disk Encryption シナリオ
description: この記事では、さまざまなシナリオで Linux VM に対して Microsoft Azure Disk Encryption を有効にする手順について説明します
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 6ebec78d5a9e82cc8a2f6ceb020a8b9552d6311e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604017"
---
# <a name="azure-disk-encryption-scenarios-on-linux-vms"></a>Linux VM での Azure Disk Encryption シナリオ

Linux 仮想マシン (VM) に対する Azure Disk Encryption では、Linux の DM-Crypt 機能を使用して、OS ディスクとデータ ディスクの完全なディスク暗号化を提供します。 また、EncryptFormatAll 機能を使用すると、一時的なディスクの暗号化を行うことができます。

Azure Disk Encryption は、ディスクの暗号化キーとシークレットを制御および管理できるように、[Azure Key Vault](disk-encryption-key-vault.md) と統合されています。 サービスの概要については、「[Linux VM に対する Azure Disk Encryption](disk-encryption-overview.md)」を参照してください。

ディスク暗号化は、[サポートされている VM サイズとオペレーティング システム](disk-encryption-overview.md#supported-vms-and-operating-systems)の仮想マシンにのみ適用できます。 また、次の前提条件を満たしている必要があります。

- [追加の VM 要件](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [ネットワーク要件](disk-encryption-overview.md#networking-requirements)
- [暗号化キーのストレージ要件](disk-encryption-overview.md#encryption-key-storage-requirements)

いずれの場合でも、ディスクを暗号化する前に[スナップショットを取得](snapshot-copy-managed-disk.md)するか、バックアップを作成する (またはその両方を行う) 必要があります。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 マネージド ディスクを含む VM では、暗号化する前にバックアップが必要になります。 バックアップを作成したら、[Set-AzVMDiskEncryptionExtension cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットで -skipVmBackup パラメーターを指定して、マネージド ディスクを暗号化できます。 暗号化された VM のバックアップと復元方法の詳細については、[Azure Backup](../../backup/backup-azure-vms-encryption.md) に関する記事を参照してください。 

>[!WARNING]
> - これまで Azure AD で Azure Disk Encryption を使用して VM を暗号化していた場合は、引き続きこのオプションを使用して VM を暗号化する必要があります。 詳細については、「[Azure AD での Azure Disk Encryption (以前のリリース)](disk-encryption-overview-aad.md)」を参照してください。 
>
> - Linux OS ボリュームを暗号化する場合、VM は利用不可と見なす必要があります。 暗号化プロセス中にアクセスする必要がある、開かれたファイルがブロックされる問題を回避するために、暗号化の進行中は SSH login を避けることを強くお勧めします。 進行状況を確認するには、[Get-AzVMDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) PowerShell コマンドレットまたは [vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) CLI コマンドを使用します。 30GB の OS ボリュームに対するこのプロセスの実行には、数時間に加え、データ ボリュームを暗号化するための時間がかかることが予測されます。 データ ボリュームの暗号化の時間は、暗号化形式の全オプションが使用されている場合を除いて、データ ボリュームのサイズと数量に比例します。 
> - Linux VM での暗号化の無効化は、データ ボリュームに対してのみサポートされます。 OS ボリュームが暗号化されている場合、暗号化の無効化はデータ ボリュームまたは OS ボリュームではサポートされません。  

## <a name="install-tools-and-connect-to-azure"></a>ツールをインストールし、Azure に接続する

Azure Disk Encryption は、[Azure CLI](/cli/azure) と [Azure PowerShell](/powershell/azure/new-azureps-module-az) を使用して、有効にして管理することができます。 そのためには、ツールをローカルにインストールし、Azure サブスクリプションに接続する必要があります。

### <a name="azure-cli"></a>Azure CLI

[Azure CLI 2.0](/cli/azure) は、Azure リソースを管理するためのコマンドライン ツールです。 CLI は、データのクエリを柔軟に実行し、長時間実行される操作を非ブロッキング プロセスとしてサポートし、スクリプトが簡単になるように設計されています。 「[Azure CLI のインストール](/cli/azure/install-azure-cli)」の手順に従って、これをローカルにインストールすることができます。

 

[Azure CLI を使用して Azure アカウントにログインする](/cli/azure/authenticate-azure-cli)には、[az login](/cli/azure/reference-index#az_login) コマンドを使用します。

```azurecli
az login
```

サインインするテナントを選択するには、以下を使用します。
    
```azurecli
az login --tenant <tenant>
```

複数のサブスクリプションがあり、特定のサブスクリプションを指定する場合は、[az account list](/cli/azure/account#az-account-list) を使用してサブスクリプションの一覧を表示し、[az account set](/cli/azure/account#az-account-set) を使用して指定します。
     
```azurecli
az account list
az account set --subscription "<subscription name or ID>"
```

詳しくは、[Azure CLI 2.0 の概要](/cli/azure/get-started-with-azure-cli)に関する記事をご覧ください。 

### <a name="azure-powershell"></a>Azure PowerShell
[Azure PowerShell az モジュール](/powershell/azure/new-azureps-module-az)には、Azure リソースの管理に [Azure Resource Manager](../../azure-resource-manager/management/overview.md) モデルを使う一連のコマンドレットが用意されています。 これは、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用してブラウザーで使用することも、「[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)」の手順に従ってご使用のローカル マシンにインストールすることもできます。 

既にローカルにインストールされている場合、Azure Disk Encryption を構成するには、最新バージョンの Azure PowerShell SDK を使用します。 [Azure PowerShell リリース](https://github.com/Azure/azure-powershell/releases)の最新バージョンをダウンロードします。

[Azure PowerShell を使用して Azure アカウントにサインインする](/powershell/azure/authenticate-azureps)には、[Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) コマンドレットを使用します。

```powershell
Connect-AzAccount
```

複数のサブスクリプションがあり、1 つを指定する場合は、[Get-AzSubscription](/powershell/module/Az.Accounts/Get-AzSubscription) コマンドレットを使用してそれらを一覧表示し、次に [Set-AzContext](/powershell/module/az.accounts/set-azcontext) コマンドレットを使用します。

```powershell
Set-AzContext -Subscription -Subscription <SubscriptionId>
```

[Get-AzContext](/powershell/module/Az.Accounts/Get-AzContext) コマンドレットを実行すると、正しいサブスクリプションが選択されていることが確認されます。

Azure Disk Encryption コマンドレットがインストールされていることを確認するには、[Get-command](/powershell/module/microsoft.powershell.core/get-command) コマンドレットを使用します。
     
```powershell
Get-command *diskencryption*
```
詳細については、「[Azure PowerShell の使用に関するページ](/powershell/azure/get-started-azureps)」を参照してください。 

## <a name="enable-encryption-on-an-existing-or-running-linux-vm"></a>既存または実行中の Linux VM に対して暗号化を有効にする
このシナリオでは、Resource Manager テンプレート、PowerShell コマンドレット、または CLI コマンドを使用して、暗号化を有効にすることができます。 仮想マシンを拡張するためのスキーマの情報が必要な場合は、[Linux 用 Azure Disk Encryption 拡張機能](../extensions/azure-disk-enc-linux.md)に関する記事を参照してください。

>[!IMPORTANT]
 >Azure Disk Encryption を有効にする前に、Azure Disk Encryption 以外を使用して、マネージド ディスク ベースの VM インスタンスのスナップショットまたはバックアップ (またはその両方) を作成する必要があります。 マネージド ディスクのスナップショットは、ポータルから作成することも、[Azure Backup](../../backup/backup-azure-vms-encryption.md) を使用して作成することもできます。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 バックアップを作成したら、Set-AzVMDiskEncryptionExtension コマンドレットを使用し、-skipVmBackup パラメーターを指定してマネージド ディスクを暗号化できます。 バックアップが作成されておらず、このパラメーターを指定していない場合、マネージド ディスク ベースの VM に対して、Set-AzVMDiskEncryptionExtension コマンドを実行するとエラーが発生します。 
>
>暗号化を有効または無効にすると、VM が再起動する場合があります。 
>

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-azure-cli"></a>Azure CLI を使用して既存または実行中の Linux VM で暗号化を有効にする 

暗号化された VHD でのディスク暗号化は、[Azure CLI](/cli/azure/) コマンド ライン ツールをインストールして使用することで有効化できます。 これは、[Azure Cloud Shell](../../cloud-shell/overview.md) を使用してブラウザーで使用することも、ローカル コンピューターにインストールして PowerShell セッションで使用することもできます。 Azure 内にある既存または実行中の Linux VM で暗号化を有効にするには、次の CLI コマンドを使用します。

[az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_show) コマンドを使用して、Azure で実行中の仮想マシンで暗号化を有効にします。

- **実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
     ```

- **KEK を使用して実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br>
key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 

- **ディスクが暗号化されていることを確認する:** VM の暗号化の状態を確認するには、[az vm encryption show](/cli/azure/vm/encryption#az-vm-encryption-show) コマンドを使用します。 

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

- **暗号化を無効にする:** 暗号化を無効にするには、[az vm encryption disable](/cli/azure/vm/encryption#az-vm-encryption-disable) コマンドを使用します。 Linux VM 用のデータ ボリュームでのみ、暗号化を無効にすることができます。

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "data"
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-using-powershell"></a>PowerShell を使用して既存または実行中の Linux VM で暗号化を有効にする
[Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットを使用して、Azure で実行中の仮想マシンで暗号化を有効にします。 ディスクを暗号化する前に、[スナップショット](snapshot-copy-managed-disk.md)の作成または [Azure Backup](../../backup/backup-azure-vms-encryption.md) で VM のバックアップの作成、あるいはその両方を行います。 実行中の Linux VM を暗号化するために、PowerShell スクリプトで -skipVmBackup パラメーターが既に指定されています。

-  **実行中の VM を暗号化する:** 以下のスクリプトでは変数を初期化し、Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、およびキー コンテナーが作成されている必要があります。 MyVirtualMachineResourceGroup、MySecureVM、MySecureVault を実際の値に置き換えます。 -VolumeType parameter を変更し、暗号化するディスクを指定します。

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();  

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```
- **KEK を使用して実行中の VM を暗号化する:** OS ディスクではなく、データ ディスクを暗号化する場合は、-VolumeType パラメーターの追加が必要になることがあります。 

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType '[All|OS|Data]' -SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 
    
- **ディスクが暗号化されていることを確認する:** VM の暗号化の状態を確認するには、[Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) コマンドレットを使用します。 
    
     ```azurepowershell-interactive 
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```
    
- **ディスク暗号化を無効にする:** 暗号化を無効にするには、[Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) コマンドレットを使用します。 Linux VM 用のデータ ボリュームでのみ、暗号化を無効にすることができます。
     
     ```azurepowershell-interactive 
     Disable-AzVMDiskEncryption -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

### <a name="enable-encryption-on-an-existing-or-running-linux-vm-with-a-template"></a>テンプレートを使用して既存または実行中の Linux VM で暗号化を有効にする

Azure 内にある既存または実行中の Linux VM でのディスク暗号化は、[Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-linux-vm-without-aad)を使用して有効化できます。

1. Azure クイックスタート テンプレートで、 **[Azure に配置する]** をクリックします。

2. サブスクリプション、リソース グループ、リソース グループの場所、パラメーター、法律条項、契約を選択します。 **[作成]** をクリックして、既存または実行中の VM で暗号化を有効にします。

次の表は、既存または実行中の VM に対する、Resource Manager テンプレートのパラメーターをまとめたものです。

| パラメーター | 説明 |
| --- | --- |
| vmName | 暗号化操作を実行する VM の名前。 |
| KeyVaultName | 暗号化キーのアップロード先となるキー コンテナーの名前。 コマンドレット `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` または次の Azure CLI コマンド `az keyvault list --resource-group "MyKeyVaultResourceGroupName"` を使用して取得できます。|
| keyVaultResourceGroup | キー コンテナーが含まれているリソース グループの名前。 |
|  keyEncryptionKeyURL | 暗号化キーの暗号化に使用されるキー暗号化キーの URL。 このパラメーターは、UseExistingKek ドロップダウン リストで **nokek** を選択した場合には省略可能です。 UseExistingKek ドロップダウン リストで **kek** を選択した場合は、_keyEncryptionKeyURL_ 値を入力する必要があります。 |
| volumeType | 暗号化操作が実行されるボリュームの種類。 有効な値は _OS_、_Data_、および _All_ です。 
| forceUpdateTag | 操作が強制実行である必要があるたびに、GUID のような一意の値を渡します。 |
| location | すべてのリソースの場所。 |

Linux VM ディスク暗号化テンプレートの構成の詳細については、「[Linux 用 Azure Disk Encryption](../extensions/azure-disk-enc-linux.md)」を参照してください。

## <a name="use-encryptformatall-feature-for-data-disks-on-linux-vms"></a>Linux VM 上のデータ ディスクに対して EncryptFormatAll 機能を使用する

**EncryptFormatAll** パラメーターを使用すると、Linux データ ディスクを暗号化する時間が短縮されます。 特定の条件を満たしているパーティションが現行のファイル システムと共にフォーマットされた後、コマンドの実行前の場所に再度マウントされます。 条件を満たすデータ ディスクを除外する場合は、コマンドの実行前にそのディスクをマウント解除できます。

 このコマンドを実行すると、以前にマウントされたドライブがフォーマットされ、現在空になったドライブ上で暗号化レイヤーが開始されます。 このオプションを選択すると、VM に接続されている一時的なディスクも暗号化されます。 リセットされた一時的なディスクは、次の機会に Azure Disk Encryption ソリューションによって、VM 用に再フォーマットおよび再暗号化されます。 リソース ディスクを暗号化すると、[Microsoft Azure Linux エージェント](../extensions/agent-linux.md)ではリソース ディスクの管理やスワップ ファイルの有効化を行うことができなくなりますが、スワップ ファイルは手動で構成できます。

>[!WARNING]
> VM のデータ ボリュームに必要なデータがある場合は、EncryptFormatAll を使用しないでください。 ディスクをマウント解除することで、そのディスクを暗号化の対象から除外できます。 運用環境の VM で EncryptFormatAll を使用する前に、EncryptFormatAll をテスト用 VM で試し、機能パラメーターとその意味について理解しておく必要があります。 EncryptFormatAll オプションはデータ ディスクをフォーマットするため、ディスク上のデータはすべて失われます。 手順を進める前に、除外するディスクが適切にマウント解除されていることを確認してください。 </br></br>
 >暗号化の設定の更新中にこのパラメーターを設定すると、実際の暗号化の前に再起動が行われる可能性があります。 その場合、フォーマットしないディスクを fstab ファイルから削除する必要があります。 同様に、暗号化操作を開始する前に、暗号化フォーマットするパーティションを fstab ファイルに追加する必要があります。 

### <a name="encryptformatall-criteria"></a>EncryptFormatAll 条件
パーティションが以下の条件を **すべて** 満たしている場合に限り、このパラメーターはすべてのパーティションを確認して暗号化します。
- ルート/OS/ブート パーティションではない
- まだ暗号化されていない
- BEK ボリュームではない
- RAID ボリュームではない
- LVM ボリュームではない
- マウントされている

RAID または LVM ボリュームではなく RAID または LVM ボリュームを構成するディスクを暗号化します。

### <a name="use-the-encryptformatall-parameter-with-azure-cli"></a>Azure CLI で EncryptFormatAll パラメーターを使用する
[az vm encryption enable](/cli/azure/vm/encryption#az-vm-encryption-enable) コマンドを使用して、Azure で実行中の仮想マシンで暗号化を有効にします。

-  **EncryptFormatAll を使用して、実行中の VM を暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "data" --encrypt-format-all
     ```

### <a name="use-the-encryptformatall-parameter-with-a-powershell-cmdlet"></a>EncryptFormatAll パラメーターを PowerShell コマンドレットで使用する
[Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットを EncryptFormatAll パラメーターと共に使用します。 

**EncryptFormatAll を使用して、実行中の VM を暗号化する:** たとえば、以下のスクリプトでは変数を初期化し、EncryptFormatAll パラメーターを指定して Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、およびキー コンテナーが作成されている必要があります。 MyVirtualMachineResourceGroup、MySecureVM、MySecureVault を実際の値に置き換えます。
  
```azurepowershell
$KVRGname = 'MyKeyVaultResourceGroup';
$VMRGName = 'MyVirtualMachineResourceGroup';
$vmName = 'MySecureVM';
$KeyVaultName = 'MySecureVault';
$KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
$diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
$KeyVaultResourceId = $KeyVault.ResourceId;

Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "data" -EncryptFormatAll
```


### <a name="use-the-encryptformatall-parameter-with-logical-volume-manager-lvm"></a>EncryptFormatAll パラメーターを論理ボリューム マネージャー (LVM) で使用する 
LVM-on-crypt のセットアップをお勧めします。 以下に示すすべての例では、デバイス パスとマウント ポイントをユース ケースに適した値に置き換えます。 このセットアップは次の手順で行うことができます。

1.  VM を構成するデータ ディスクを追加します。

1. それらのディスクをフォーマットおよびマウントして fstab ファイルに追加します。

1. パーティションの標準を選択し、ドライブ全体にわたるパーティションを作成してから、パーティションをフォーマットします。 ここでは、Azure によって生成されたシンボリック リンクを使用します。 シンボリック リンクを使用すると、デバイス名の変更に関連する問題を回避できます。 詳細については、[デバイス名の問題のトラブルシューティング](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)に関する記事を参照してください。
    
    ```bash
    parted /dev/disk/azure/scsi1/lun0 mklabel gpt
    parted -a opt /dev/disk/azure/scsi1/lun0 mkpart primary ext4 0% 100%
    
    mkfs -t ext4 /dev/disk/azure/scsi1/lun0-part1
    ```

1. ディスクをマウントします。

    ```bash
    mount /dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint
    ````
    
    fstab ファイルに追加します。

    ```bash
    echo "/dev/disk/azure/scsi1/lun0-part1 /mnt/mountpoint ext4 defaults,nofail 0 2" >> /etc/fstab
    ```
    
1. -EncryptFormatAll を指定して Azure PowerShell の [Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) コマンドレットを実行し、これらのディスクを暗号化します。

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data
    ```

    キー暗号化キー (KEK) を使用する場合は、次のように、KEK の URI とキー コンテナーの ResourceID を -KeyEncryptionKeyUrl パラメーターと -KeyEncryptionKeyVaultId パラメーターにそれぞれ渡します。

    ```azurepowershell-interactive
    $KeyVault = Get-AzKeyVault -VaultName "MySecureVault" -ResourceGroupName "MySecureGroup"
    $KEKKeyVault = Get-AzKeyVault -VaultName "MyKEKVault" -ResourceGroupName "MySecureGroup"
    $KEK = Get-AzKeyVaultKey -VaultName "myKEKVault" -KeyName "myKEKName"
    
    Set-AzVMDiskEncryptionExtension -ResourceGroupName "MySecureGroup" -VMName "MySecureVM" -DiskEncryptionKeyVaultUrl $KeyVault.VaultUri -DiskEncryptionKeyVaultId $KeyVault.ResourceId -EncryptFormatAll -SkipVmBackup -VolumeType Data -KeyEncryptionKeyUrl $$KEK.id -KeyEncryptionKeyVaultId $KEKKeyVault.ResourceId
    ```

1. これらの新しいディスク上に LVM を設定します。 VM の起動が完了した後で、暗号化されたドライブのロックが解除されます。 そのため、LVM のマウントをその後に遅延させる必要があります。

## <a name="new-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a>お客様が暗号化した VHD と暗号化キーから作成された新しい VM
このシナリオでは、PowerShell コマンドレットまたは CLI コマンドを使用して、暗号化を有効にすることができます。 

Azure で使用できる事前に暗号化されたイメージを準備するには、Azure Disk Encryption と同じスクリプトの手順を使用します。 イメージを作成したら、次のセクションの手順に従って、暗号化された Azure VM を作成できます。

* [事前に暗号化された Linux VHD を準備する](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-linux-vhd)

>[!IMPORTANT]
 >Azure Disk Encryption を有効にする前に、Azure Disk Encryption 以外を使用して、マネージド ディスク ベースの VM インスタンスのスナップショットまたはバックアップ (またはその両方) を作成する必要があります。 マネージド ディスクのスナップショットは、ポータルから作成できます。[Azure Backup](../../backup/backup-azure-vms-encryption.md) を使用することもできます。 バックアップがあると、暗号化中に予期しないエラーが発生した場合に、回復オプションを使用できるようになります。 バックアップを作成したら、Set-AzVMDiskEncryptionExtension コマンドレットを使用し、-skipVmBackup パラメーターを指定してマネージド ディスクを暗号化できます。 バックアップが作成されておらず、このパラメーターを指定していない場合、マネージド ディスク ベースの VM に対して、Set-AzVMDiskEncryptionExtension コマンドを実行するとエラーが発生します。 
>
> 暗号化を有効または無効にすると、VM が再起動する場合があります。 



### <a name="use-azure-powershell-to-encrypt-vms-with-pre-encrypted-vhds"></a>Azure PowerShell を使用して事前に暗号化された VHD で VM を暗号化する 
PowerShell コマンドレット [Set-AzVMOSDisk](/powershell/module/Az.Compute/Set-AzVMOSDisk#examples) を使用して、暗号化された VHD でディスク暗号化を有効にすることができます。 次の例では、一般的ないくつかのパラメーターを示します。 

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Linux -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>新しく追加されたデータ ディスクで暗号化を有効にする

[az vm disk attach](add-disk.md) を使用して、または [Azure Portal](attach-disk-portal.md) から新しいデータ ディスクを追加できます。 暗号化の前に、新しく接続されたデータ ディスクをマウントする必要があります。 暗号化の実行中はデータ ドライブが使用できなくなるため、そのドライブの暗号化を要求する必要があります。 

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI を使用して新しく追加されたディスクで暗号化を有効にする

 VM が以前に "All" で暗号化された場合、--volume-type パラメーターは "All" のままになっているはずです。 All には OS とデータ ディスクの両方が含まれます。 VM が以前にボリュームの種類 "OS" で暗号化された場合は、--volume-type パラメーターを "All" に変更して、OS と新しいデータ ディスクの両方が含まれるようにする必要があります。 VM がボリュームの種類 "Data" でのみ暗号化された場合、以下に示すように VM は "Data" のままになっている可能性があります。 新しいデータ ディスクを VM に追加してアタッチするだけでは、暗号化の準備としては不十分です。 暗号化を有効にする前に、新しくアタッチしたディスクをフォーマットして、VM 内で適切にマウントする必要もあります。 Linux では、[永続的なブロック デバイス名](../troubleshooting/troubleshoot-device-names-problems.md)を使用して /etc/fstab にディスクをマウントする必要があります。  

PowerShell 構文とは異なり、CLI では暗号化を有効にする際にユーザーが一意のシーケンス バージョンを指定する必要はありません。 CLI では独自の一意のシーケンス バージョン値が自動的に生成され、使用されます。

-  **実行中の VM のデータ ボリュームを暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "Data"
     ```

- **KEK を使用し、実行中の VM のデータ ボリュームを暗号化する:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "Data"
     ```

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell を使用して新しく追加されたディスクで暗号化を有効にする
 PowerShell を使用して Linux 用の新しいディスクを暗号化する場合は、新しいシーケンス バージョンを指定する必要があります。 シーケンス バージョンは一意である必要があります。 次のスクリプトでは、シーケンス バージョン用の GUID が生成されます。 ディスクを暗号化する前に、[スナップショット](snapshot-copy-managed-disk.md)の作成または [Azure Backup](../../backup/backup-azure-vms-encryption.md) で VM のバックアップの作成、あるいはその両方を行います。 新しく追加されたデータ ディスクを暗号化するために、PowerShell スクリプトで -skipVmBackup パラメーターが既に指定されています。
 

-  **実行中の VM のデータ ボリュームを暗号化する:** 以下のスクリプトでは変数を初期化し、Set-AzVMDiskEncryptionExtension コマンドレットを実行します。 前提条件として、リソース グループ、VM、およびキー コンテナーが既に作成されている必要があります。 MyVirtualMachineResourceGroup、MySecureVM、MySecureVault を実際の値に置き換えます。 -VolumeType パラメーターに使用できる値は、All、OS、Data です。 VM が以前にボリュームの種類 "OS" または "All" で暗号化された場合は、-VolumeType パラメーターを "All" に変更して、OS と新しいデータ ディスクの両方が含まれるようにする必要があります。

      ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
      ```
- **KEK を使用し、実行中の VM のデータ ボリュームを暗号化する:** -VolumeType パラメーターに使用できる値は、All、OS、Data です。 VM が以前にボリュームの種類 "OS" または "All" で暗号化された場合は、-VolumeType パラメーターを All に変更して、OS と新しいデータ ディスクの両方が含まれるようにする必要があります。

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

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType 'data' –SequenceVersion $sequenceVersion -skipVmBackup;
     ```

    >[!NOTE]
    > disk-encryption-keyvault パラメーターの値の構文は、/subscriptions/[subscription-id-guid]/resourceGroups/[KVresource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name] という完全な識別子の文字列です。</br> key-encryption-key パラメーターの値の構文は、 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id] という KEK への完全な URI です。 


## <a name="disable-encryption-for-linux-vms"></a>Linux VM に対して暗号化を無効にする
[!INCLUDE [disk-encryption-disable-encryption-cli](../../../includes/disk-encryption-disable-cli.md)]

## <a name="unsupported-scenarios"></a>サポートされていないシナリオ

Azure Disk Encryption は、次の Linux のシナリオ、機能、およびテクノロジには対応していません。

- Basic レベルの VM または従来の VM の作成方法を使用して作成された VM を暗号化する。
- OS ドライブが暗号化されている場合に Linux VM の OS ドライブまたはデータ ドライブで暗号化を無効にする。
- Linux 仮想マシン スケール セットの OS ドライブを暗号化する。
- Linux VM のカスタム イメージを暗号化する。
- オンプレミスのキー管理システムとの統合。
- Azure Files (共有ファイル システム)。
- ネットワーク ファイル システム (NFS)。
- 動的ボリューム。
- エフェメラル OS ディスク。
- 次のもの (ただし、限定されない) の共有/分散ファイル システムの暗号化:DFS、GFS、DRDB、CephFS。
- 暗号化された VM を別のサブスクリプションまたはリージョンに移動する。
- 暗号化された VM のイメージまたはスナップショットを作成し、それを使用して追加の VM をデプロイする。
- カーネル クラッシュ ダンプ (kdump)。
- Oracle ACFS (ASM クラスター ファイル システム)。
- Lsv2 シリーズ VM の NVMe ディスク (参照: [LSv2 シリーズ](../lsv2-series.md))。
- "マウント ポイントが入れ子になっている"、つまり、1 つのパスに複数のマウント ポイントがある ("/1stmountpoint/data/2stmountpoint" など) VM。
- OS フォルダーの上にデータ ドライブがマウントされている VM。
- データ ディスクを使用してルート (OS ディスク) 論理ボリュームが拡張された VM。
- 書き込みアクセラレータ ディスクを備えた M シリーズの VM。
- [カスタマー マネージド キーを使用したサーバー側暗号化](../disk-encryption.md) (SSE + CMK) で暗号化されたディスクがある VM に ADE を適用する。 ADE で暗号化された VM 上のデータ ディスクに SSE + CMK を適用することも、サポートされていないシナリオです。
- ADE で暗号化されている、または ADE で暗号化 **されたことがある** VM を、[カスタマー マネージド キーを使用したサーバー側暗号化](../disk-encryption.md)に移行する。
- フェールオーバー クラスター内の VM を暗号化する。

## <a name="next-steps"></a>次のステップ

- [Azure Disk Encryption の概要](disk-encryption-overview.md)
- [Azure Disk Encryption のサンプル スクリプト](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption のトラブルシューティング](disk-encryption-troubleshooting.md)