---
title: Azure Service Fabric Windows クラスターのディスク暗号化を有効にする | Microsoft Docs
description: この記事では、Azure Resource Manager、Azure Key Vault を使用して、Azure で Service Fabric クラスター ノードのディスク暗号化を有効にする方法について説明します。
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: navya
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/22/2019
ms.author: aljo
ms.openlocfilehash: 2e9c41409c1f528947e3bef281e9a3c34da39e9b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050444"
---
# <a name="enable-disk-encryption-for-service-fabric-windows-cluster-nodes"></a>Service Fabric Windows クラスター ノードのディスク暗号化を有効にする 
> [!div class="op_single_selector"]
> * [Windows のディスク暗号化](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Linux のディスク暗号化](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

以下の手順に従って、Service Fabric Windows クラスター ノードでディスク暗号化を有効にします。 ノード タイプ/仮想マシン スケール セットごとにこれらの手順を実行する必要があります。 ノードを暗号化するために、ここでは仮想マシン スケール セットで Azure Disk Encryption 機能を利用します。

このガイドでは次の手順について説明します。

* Service Fabric Windows クラスターの仮想マシン スケール セットでディスク暗号化を有効にする場合に、気を付ける必要がある主要な概念。
* Service Fabric Windows クラスターの仮想マシン スケール セットでディスク暗号化を有効にする前に、従う必要がある前提条件の手順。
* Service Fabric Windows クラスターの仮想マシン スケール セットでディスク暗号化を有効にするために、従う必要がある手順。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件
* **自己登録** - 使用するには、仮想マシン スケール セットのディスク暗号化プレビューで自己登録が必要です。
* 次の手順を実行して、サブスクリプションを自己登録することができます。 
```powershell
Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
```
* 状態が "登録済み" になるまで約 10 分待ちます。 次のコマンドを実行して、状態を確認することができます。 
```powershell
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
```
* **Azure Key Vault** - スケール セットと同じサブスクリプションおよびリージョンで KeyVault を作成し、PS コマンドレットを使用して KeyVault に "EnabledForDiskEncryption" というアクセス ポリシーを設定します。 Azure portal で KeyVault UI を使用して、ポリシーを設定することもできます。 
```powershell
Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
```
* 新しい暗号化コマンドを含む、最新の [Azure CLI](/cli/azure/install-azure-cli) をインストールします。
* [Azure PowerShell の Azure SDK](https://github.com/Azure/azure-powershell/releases) リリースの最新バージョンをインストールします。 暗号化の有効化 ([Set](/powershell/module/az.compute/set-azvmssdiskencryptionextension))、暗号化状態の取得 ([Get](/powershell/module/az.compute/get-azvmssvmdiskencryption)) および暗号化の削除 ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) をスケール セット インスタンスで行うための仮想マシン スケール セット ADE コマンドレットを以下に示します。

| command | バージョン |  ソース  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 以降 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 以降 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 以降 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 以降 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 以降 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 以降 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>ディスク暗号化のサポートされるシナリオ
* 仮想マシン スケール セットの暗号化はマネージド ディスクで作成されたスケール セットの場合にのみサポートされます。ネイティブ (またはアンマネージド) ディスク スケール セットの場合はサポートされません。
* Windows 仮想マシン スケール セットの OS とデータ ボリュームでは、仮想マシン スケール セットの暗号化がサポートされています。 Windows スケール セットの OS とデータ ボリュームでは、暗号化の無効化がサポートされています。
* 現在のプレビューでは、仮想マシン スケール セット VM の再イメージ操作とアップグレード操作はサポートされていません。


### <a name="create-new-cluster-and-enable-disk-encryption"></a>新しいクラスターを作成してディスク暗号化を有効にする

次のコマンドを使用してクラスターを作成し、Azure Resource Manager テンプレートと自己署名証明書を使って、ディスク暗号化を有効にします。

### <a name="sign-in-to-azure"></a>Azure へのサインイン 

```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

#### <a name="use-the-custom-template-that-you-already-have"></a>既存のカスタム テンプレートを使用する 

ニーズに合わせてカスタム テンプレートを作成する必要がある場合は、[Azure Service Fabric テンプレート サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)で入手できるテンプレートのいずれかから始めることを強くお勧めします。 ガイダンスと説明については、後述する[クラスター テンプレートのカスタマイズ][customize-your-cluster-template]に関するセクションを参照してください。

カスタム テンプレートを既に持っている場合は、3 つの証明書すべてがテンプレートのパラメーターに関連付けられ、パラメーターのファイル名がテンプレートで指定され、値がテンプレートで null であることを慎重に確認してください。

```Json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```


```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\templates\templateparam.json"
$templateFilePath="c:\templates\template.json"


New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 

```


```azurecli

declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"


az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath

```

#### <a name="deploy-application-to-windows-service-fabric-cluster"></a>Windows Service Fabric クラスターにアプリケーションをデプロイする
[クラスターにアプリケーションをデプロイする](service-fabric-deploy-remove-applications.md)ための手順とガイダンスに従ってください。


#### <a name="enable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set-created-above"></a>先ほど作成した Service Fabric クラスターの仮想マシン スケール セットでディスク暗号化を有効にする
 
```powershell

$VmssName = "nt1vm"
$vaultName = "mykeyvault"
$resourceGroupName = "mycluster"
$KeyVault = Get-AzKeyVault -VaultName $vaultName -ResourceGroupName $rgName
$DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
$KeyVaultResourceId = $KeyVault.ResourceId

Set-AzVmssDiskEncryptionExtension -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType All

```

```azurecli

az vmss encryption enable -g <resourceGroupName> -n <VMSS name> --disk-encryption-keyvault <KeyVaultResourceId>

```


#### <a name="validate-if-disk-encryption-enabled-for-windows-virtual-machine-scale-set"></a>Windows 仮想マシン スケール セットでディスク暗号化が有効になっていることを確認します。
仮想マシン スケール セット全体またはスケール セットの任意のインスタンスの状態を取得します。 以下のコマンドを参照してください。
さらに、スケール セットの VM にサインインしてドライブが暗号化されていることを確認できます

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


#### <a name="disable-disk-encryption-for-service-fabric-cluster-virtual-machine-scale-set"></a>Service Fabric クラスターの仮想マシン スケール セットでディスク暗号化を無効にする 
ディスク暗号化の無効化は、インスタンス単位ではなく、仮想マシン スケール セット全体に適用されます。 

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```CLI

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>次の手順
この時点で、Service Fabric クラスターの仮想マシン スケール セットでディスク暗号化を有効/無効にする方法により、クラスターはセキュリティ保護されています。 次は [Linux のディスク暗号化](service-fabric-enable-azure-disk-encryption-linux.md)です 

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
