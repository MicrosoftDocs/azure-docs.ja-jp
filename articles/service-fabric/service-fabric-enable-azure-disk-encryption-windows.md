---
title: Windows クラスターでのディスク暗号化の有効化
description: この記事では、Azure Resource Manager 内の Azure Key Vault を使用して、Azure Service Fabric クラスター ノードのディスク暗号化を有効にする方法について説明します。
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: b08cdb63aa6f334c5a6f7c230b1624d232206c3b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78251812"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-windows"></a>Windows での Azure Service Fabric クラスター ノードのディスク暗号化の有効化 
> [!div class="op_single_selector"]
> * [Windows のディスク暗号化](service-fabric-enable-azure-disk-encryption-windows.md)
> * [Linux のディスク暗号化](service-fabric-enable-azure-disk-encryption-linux.md)
>
>

このチュートリアルでは、Windows で Service Fabric クラスター ノードのディスク暗号化を有効にする方法について説明します。 ノード タイプおよび仮想マシン スケール セットごとに、これらの手順に従う必要があります。 ノードを暗号化するために、ここでは仮想マシン スケール セットで Azure Disk Encryption 機能を利用します。

このガイドでは、次のトピックについて説明します。

* Windows の Service Fabric クラスターの仮想マシン スケール セットでディスク暗号化を有効にする場合に、気を付ける必要がある主要な概念。
* Windows の Service Fabric クラスター ノードでディスク暗号化を有効にする前に実行する必要がある手順。
* Windows の Service Fabric クラスター ノードでディスク暗号化を有効にするために実行する必要がある手順。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

**自己登録** 

仮想マシン スケール セットのディスク暗号化プレビューには、自己登録が必要です。 次の手順に従います。 

1. まず、次のコマンドを実行します。
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 状態が "*登録済み*" になるまで約 10 分待ちます。 状態を確認するには、次のコマンドを実行します。 
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault** 

1. スケール セットと同じサブスクリプションおよびリージョンでキー コンテナーを作成し、PowerShell コマンドレットを使用して、キー コンテナーに **EnabledForDiskEncryption** アクセス ポリシーを選択します。 Azure portal で Key Vault UI を使用して、次のコマンドによってポリシーを設定することもできます。
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -EnabledForDiskEncryption
    ```
2. 新しい暗号化コマンドが含まれている、最新バージョンの [Azure CLI](/cli/azure/install-azure-cli) をインストールします。
3. [Azure PowerShell の Azure SDK](https://github.com/Azure/azure-powershell/releases) リリースの最新バージョンをインストールします。 暗号化の有効化 ([set](/powershell/module/az.compute/set-azvmssdiskencryptionextension))、暗号化状態の取得 ([get](/powershell/module/az.compute/get-azvmssvmdiskencryption))、および暗号化の削除 ([disable](/powershell/module/az.compute/disable-azvmssdiskencryption)) をスケール セット インスタンスに対して行うための仮想マシン スケール セット Azure Disk Encryption コマンドレットを以下に示します。

| command | Version |  source  |
| ------------- |-------------| ------------|
| Get-AzVmssDiskEncryptionStatus   | 1.0.0 以降 | Az.Compute |
| Get-AzVmssVMDiskEncryptionStatus   | 1.0.0 以降 | Az.Compute |
| Disable-AzVmssDiskEncryption   | 1.0.0 以降 | Az.Compute |
| Get-AzVmssDiskEncryption   | 1.0.0 以降 | Az.Compute |
| Get-AzVmssVMDiskEncryption   | 1.0.0 以降 | Az.Compute |
| Set-AzVmssDiskEncryptionExtension   | 1.0.0 以降 | Az.Compute |


## <a name="supported-scenarios-for-disk-encryption"></a>ディスク暗号化のサポートされるシナリオ
* 仮想マシン スケール セットの暗号化はマネージド ディスクで作成されたスケール セットの場合にのみサポートされます。 ネイティブ (アンマネージド) ディスク スケール セットの場合はサポートされません。
* Windows では、仮想マシン スケール セットの OS とデータ ボリュームに対する暗号化がサポートされています。 Windows では、仮想マシン スケール セットの OS とデータ ボリュームに対する暗号化の無効化もサポートされています。
* 現在のプレビューでは、仮想マシン スケール セットに対する仮想マシンの再イメージ操作とアップグレード操作はサポートされていません。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>新しいクラスターを作成してディスク暗号化を有効にする

次のコマンドを使用してクラスターを作成し、Azure Resource Manager テンプレートと自己署名証明書を使って、ディスク暗号化を有効にします。

### <a name="sign-in-to-azure"></a>Azure へのサインイン 
以下のコマンドでサインインします。
```powershell
Login-AzAccount
Set-AzContext -SubscriptionId <guid>

```

```azurecli

azure login
az account set --subscription $subscriptionId

```

### <a name="use-the-custom-template-that-you-already-have"></a>既存のカスタム テンプレートを使用する 

ニーズに合わせてカスタム テンプレートを作成する必要がある場合は、[Azure Service Fabric クラスター作成テンプレート サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master) ページで入手できるテンプレートのいずれかから始めることを強くお勧めします。 [クラスター テンプレートをカスタマイズする][customize-your-cluster-template]には、次のガイダンスを参照してください。

カスタム テンプレートが既にある場合は、テンプレートとパラメーター ファイルで、3 つの証明書関連パラメーターの名前が次のように指定されていて、値が次のように null であることを確認します。

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

### <a name="deploy-an-application-to-a-service-fabric-cluster-in-windows"></a>Windows で Service Fabric クラスターにアプリケーションをデプロイする
アプリケーションをクラスターにデプロイするには、「[PowerShell を使用してアプリケーションのデプロイと削除を実行する](service-fabric-deploy-remove-applications.md)」の手順とガイダンスに従ってください。


### <a name="enable-disk-encryption-for-the-virtual-machine-scale-sets-created-previously"></a>以前に作成した仮想マシン スケール セットのディスク暗号化を有効にする

前の手順で作成した仮想マシン スケール セットのディスク暗号化を有効にするには、次のコマンドを実行します。
 
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


### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-windows"></a>Windows で仮想マシン スケール セットのディスク暗号化が有効になっているかどうかを確認する
以下のコマンドを実行して、仮想マシン スケール セット全体の状態、またはスケール セット内の任意のインスタンスの状態を取得します。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli

az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```


さらに、仮想マシン スケール セットにサインインしてドライブが暗号化されていることを確認できます。

### <a name="disable-disk-encryption-for-a-virtual-machine-scale-set-in-a-service-fabric-cluster"></a>Service Fabric クラスターの仮想マシン スケール セットのディスク暗号化を無効にする 
以下のコマンドを実行して、仮想マシン スケール セットのディスク暗号化を無効にします。 ディスク暗号化の無効化は、個別のインスタンスではなく、仮想マシン スケール セット全体に適用されることに注意してください。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Disable-AzVmssDiskEncryption -ResourceGroupName $rgName -VMScaleSetName $VmssName

```

```azurecli

az vmss encryption disable -g <resourceGroupName> -n <VMSS name>

```


## <a name="next-steps"></a>次のステップ
ここまでで、クラスターがセキュリティで保護され、Service Fabric クラスター ノードと仮想マシン スケール セットでディスク暗号化を有効および無効にする方法を学びました。 Linux での Service Fabric クラスター ノードに関する同様のガイダンスについては、[Linux でのディスクの暗号化](service-fabric-enable-azure-disk-encryption-linux.md)に関するページを参照してください。

[customize-your-cluster-template]: https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure#creating-a-custom-arm-template
