---
title: "Azure で Windows VM のディスクを暗号化する | Microsoft Docs"
description: "セキュリティを強化するために、Azure PowerShell を使用して Windows VM の仮想ディスクを暗号化する方法"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/10/2017
ms.author: iainfou
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 14130a87a7a4262307f9e8d9c0d0f8c057e9810b
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-encrypt-virtual-disks-on-a-windows-vm"></a>Windows VM の仮想ディスクを暗号化する方法
仮想マシン (VM) のセキュリティとコンプライアンスを強化するために、Azure の仮想ディスクを暗号化できます。 ディスクは、Azure Key Vault で保護されている暗号化キーを使って暗号化されます。 これらの暗号化キーを制御し、その使用を監査することができます。 この記事では、Azure PowerShell を使用して Windows VM の仮想ディスクを暗号化する方法について詳しく説明します。 [Azure CLI 2.0 を使って Linux VM のディスクを暗号化する](../linux/encrypt-disks.md)こともできます。

## <a name="overview-of-disk-encryption"></a>ディスク暗号化の概要
Windows VM の仮想ディスクは、Bitlocker を使って暗号化します。 Azure の仮想ディスクを暗号化するための料金はかかりません。 暗号化キーは、ソフトウェア保護を使って Azure Key Vault に格納されます。または、FIPS 140-2 レベル 2 標準に認定された Hardware Security Module (HSM) でキーをインポートまたは生成することもできます。 これらの暗号化キーは、VM に接続された仮想ディスクの暗号化/暗号化解除に使われます。 これらの暗号化キーの制御を維持し、その使用を監査することができます。 Azure Active Directory サービス プリンシパルは、VM の電源がオンまたはオフになったときにこれらの暗号化キーを発行するためのセキュリティで保護されたメカニズムを提供します。

VM 暗号化のプロセスは次のとおりです。

1. Azure Key Vault で暗号化キーを作成します。
2. ディスクの暗号化に使うことができるように暗号化キーを構成します。
3. Azure Key Vault から暗号化キーを読み取るには、適切なアクセス許可を持つ Azure Active Directory サービス プリンシパルを作成します。
4. Azure Active Directory サービス プリンシパルと、使用する適切な暗号化キーを指定して、仮想ディスクを暗号化するコマンドを発行します。
5. Azure Active Directory サービス プリンシパルは、Azure Key Vault に必要な暗号化キーを要求します。
6. 提供された暗号化キーを使って仮想ディスクが暗号化されます。

## <a name="encryption-process"></a>暗号化プロセス
ディスクの暗号化は、以下の他のコンポーネントに依存します。

* **Azure Key Vault** - ディスクの暗号化/暗号化解除プロセスに使われる暗号化キーとシークレットの保護に使われます。 
  * Azure Key Vault が既に存在する場合は、それを使うことができます。 Key Vault をディスク暗号化専用にする必要はありません。
  * 管理境界とキーの可視性を分離するため、専用の Key Vault を作成してもかまいません。
* **Azure Active Directory** - 必要な暗号化キーの安全な交換と、要求されたアクションの認証を処理します。 
  * 通常は、既存の Azure Active Directory インスタンスを使ってアプリケーションを保持できます。
  * サービス プリンシパルは、適切な暗号化キーを要求し、発行するセキュリティで保護されたメカニズムを提供します。 Azure Active Directory と統合する実際のアプリケーションを開発しているのではありません。

## <a name="requirements-and-limitations"></a>要件と制限
ディスク暗号化のサポートされるシナリオと要件:

* Azure Marketplace イメージまたはカスタムの VHD イメージから新しい Windows VM の暗号化を有効にします。
* Azure で既存の Windows VM の暗号化を有効にします。
* 記憶域スペースを使用して構成されている Windows VM の暗号化を有効にします。
* Windows VM の OS およびデータ ドライブの暗号化を無効にします。
* すべてのリソース (Key Vault、ストレージ アカウント、VM など) は、同じ Azure リージョンとサブスクリプションに存在している必要があります。
* A、D、DS、G、G シリーズ VM などのStandard レベルの VM。

現在、ディスクの暗号化は次のシナリオではサポートされていません。

* Basic レベルの VM。
* クラシック デプロイメント モデルで作成された VM。
* 既に暗号化されている VM での暗号化キーの更新。
* オンプレミスの Key Management Service との統合。

## <a name="create-azure-key-vault-and-keys"></a>Azure Key Vault とキーを作成する
開始する前に、最新バージョンの Azure PowerShell モジュールがインストールされていることを確認してください。 詳細については、「 [Azure PowerShell のインストールと構成の方法](/powershell/azure/overview)」を参照してください。 コマンドの例全体を通して、パラメーターの例を実際の名前、場所、およびキーの値に置き換えます。 次の例では、*myResourceGroup*、*myKeyVault*、*myVM* などの規約を使用します。

最初に、暗号化キーを格納する Azure Key Vault を作成します。 Azure Key Vault は、キー、シークレット、パスワードを格納して、アプリケーションとサービスに安全に実装できるようにします。 仮想ディスクの暗号化では、仮想ディスクの暗号化または暗号化解除に使われる暗号化キーを格納するために Key Vault を作成します。 

[Register-AzureRmResourceProvider](/powershell/module/azurerm.resources/register-azurermresourceprovider) を使用して Azure サブスクリプションで Azure Key Vault プロバイダーを有効にし、[New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) を使用してリソース グループを作成します。 次の例では、リソース グループ名 *myResourceGroup* を場所 *East US* に作成します。

```powershell
$rgName = "myResourceGroup"
$location = "East US"

Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.KeyVault"
New-AzureRmResourceGroup -Location $location -Name $rgName
```

暗号化キーおよびストレージや VM 自体などの関連するコンピューティング リソースを格納する Azure Key Vault は、同じリージョンに存在する必要があります。 [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/new-azurermkeyvault) を使用して Azure Key Vault を作成し、ディスクの暗号化で使用するために Key Vault を有効にします。 次のように、*keyVaultName* に一意の Key Vault 名を指定します。

```powershell
$keyVaultName = "myUniqueKeyVaultName"
New-AzureRmKeyVault -Location $location `
    -ResourceGroupName $rgName `
    -VaultName $keyVaultName `
    -EnabledForDiskEncryption
```

ソフトウェアまたはハードウェア セキュリティ モデル (HSM) の保護を使って、暗号化キーを格納できます。 HSM を使うには、Premium Key Vault が必要です。 ソフトウェアで保護されたキーを格納する Standard Key Vault ではなく Premium Key Vault を作成するには、追加コストがかかります。 Premium Key Vault を作成するには、前の手順で *-Sku "Premium"* パラメーターを追加します。 ここでは Standard Key Vault を作成したので、次の例ではソフトウェアで保護されたキーを使います。 

どちらの保護モデルでも、VM が起動して仮想ディスクを復号化するときに、Azure プラットフォームは暗号化キーを要求するためのアクセスを許可される必要があります。 [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) を使用して、Key Vault に暗号化キーを作成します。 次の例では、*myKey* という名前のキーを作成します。

```powershell
Add-AzureKeyVaultKey -VaultName $keyVaultName `
    -Name "myKey" `
    -Destination "Software"
```


## <a name="create-the-azure-active-directory-service-principal"></a>Azure Active Directory サービス プリンシパルを作成する
仮想ディスクを暗号化または暗号化解除するときは、認証と Key Vault の暗号化キーの交換を処理するアカウントを指定します。 このアカウント (Azure Active Directory サービス プリンシパル) を使用して、Azure プラットフォームは VM の代わりに適切な暗号化キーを要求できます。 サブスクリプションでは既定の Azure Active Directory インスタンスを使うことができますが、多くの場合、専用の Azure Active Directory ディレクトリが使われます。

[New-AzureRmADServicePrincipal](/powershell/module/azurerm.resources/new-azurermadserviceprincipal) を使用して、Azure Active Directory にサービス プリンシパルを作成します。 安全なパスワードを作成するには、「[Azure Active Directory のパスワード ポリシーと制限](../../active-directory/active-directory-passwords-policy.md)」に従ってください。

```powershell
$appName = "My App"
$securePassword = "P@ssword!"
$app = New-AzureRmADApplication -DisplayName $appName `
    -HomePage "https://myapp.contoso.com" `
    -IdentifierUris "https://contoso.com/myapp" `
    -Password $securePassword
New-AzureRmADServicePrincipal -ApplicationId $app.ApplicationId
```

仮想ディスクを正常に暗号化または暗号化解除するには、Key Vault に格納されている暗号化キーに対するアクセス許可を設定して、Azure Active Directory サービス プリンシパルにキーの読み取りを許可する必要があります。 [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) を使用して、Key Vault に対するアクセス許可を設定します。

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyvaultName `
    -ServicePrincipalName $app.ApplicationId `
    -PermissionsToKeys "WrapKey" `
    -PermissionsToSecrets "Set"
```


## <a name="create-virtual-machine"></a>仮想マシンの作成
暗号化プロセスをテストするために、VM を作成してみましょう。 次の例では、*Windows Server 2016 Datacenter* イメージを使用して、*myVM* という名前の VM を作成します。

```powershell
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $rgName `
    -Location $location `
    -Name myVnet `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

$pip = New-AzureRmPublicIpAddress -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Static `
    -IdleTimeoutInMinutes 4 `
    -Name "mypublicdns$(Get-Random)"

$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 1000 `
    -SourceAddressPrefix * `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 3389 `
    -Access Allow

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName `
    -Location $location `
    -Name myNetworkSecurityGroup `
    -SecurityRules $nsgRuleRDP

$nic = New-AzureRmNetworkInterface -Name myNic `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id `
    -NetworkSecurityGroupId $nsg.Id

$cred = Get-Credential

$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Windows -ComputerName myVM -Credential $cred | `
Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer `
    -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vmConfig
```


## <a name="encrypt-virtual-machine"></a>仮想マシンを暗号化する
仮想ディスクを暗号化するには、これまでのコンポーネントをすべてまとめます。

1. Azure Active Directory サービス プリンシパルとパスワードを指定します。
2. 暗号化されたディスクのメタデータを格納する Key Vault を指定します。
3. 実際の暗号化と暗号化解除に使う暗号化キーを指定します。
4. OS ディスク、データ ディスク、またはすべてのディスクのいずれを暗号化するかを指定します。

Azure Key Vault のキーと Azure Active Directory サービス プリンシパルの資格情報を使用して、[Set-AzureRmVMDiskEncryptionExtension](/powershell/module/azurerm.compute/set-azurermvmdiskencryptionextension) で VM を暗号化します。 次の例は、すべてのキー情報を取得した後、*myVM* という名前の VM を暗号化します。

```powershell
$keyVault = Get-AzureRmKeyVault -VaultName $keyVaultName -ResourceGroupName $rgName;
$diskEncryptionKeyVaultUrl = $keyVault.VaultUri;
$keyVaultResourceId = $keyVault.ResourceId;
$keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $keyVaultName -Name myKey).Key.kid;

Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgName `
    -VMName $vmName `
    -AadClientID $app.ApplicationId `
    -AadClientSecret $securePassword `
    -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
    -DiskEncryptionKeyVaultId $keyVaultResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $keyVaultResourceId
```

VM の暗号化を続行するプロンプトを受け入れます。 この処理中に VM が再起動します。 暗号化処理が完了し、VM が再起動されたら、[Get-AzureRmVmDiskEncryptionStatus](/powershell/module/azurerm.compute/get-azurermvmdiskencryptionstatus) を使用して暗号化の状態を確認します。

```powershell
Get-AzureRmVmDiskEncryptionStatus  -ResourceGroupName $rgName -VMName $vmName
```

出力は次の例のようになります。

```powershell
OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OsVolume: Encrypted, DataVolumes: Encrypted
```

## <a name="next-steps"></a>次のステップ
* Azure Key Vault の管理の詳細については、[仮想マシンの Key Vault の設定](key-vault-setup.md)に関するページを参照してください。
* 暗号化されたカスタム VM を Azure にアップロードするための準備など、ディスクの暗号化について詳しくは、「[Azure Disk Encryption](../../security/azure-security-disk-encryption.md)」をご覧ください。

