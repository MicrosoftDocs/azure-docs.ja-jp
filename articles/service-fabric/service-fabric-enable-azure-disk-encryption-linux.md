---
title: Linux クラスターでのディスク暗号化の有効化
description: この記事では、Azure Resource Manager と Azure Key Vault を使用して、Linux で Azure Service Fabric クラスター ノードのディスク暗号化を有効にする方法について説明します。
ms.topic: article
ms.date: 03/22/2019
ms.openlocfilehash: c600d822d20b0e5a0ca613935b1dfa4be838fcec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "78252825"
---
# <a name="enable-disk-encryption-for-azure-service-fabric-cluster-nodes-in-linux"></a>Linux での Azure Service Fabric クラスター ノードのディスク暗号化の有効化 
> [!div class="op_single_selector"]
> * [Linux のディスク暗号化](service-fabric-enable-azure-disk-encryption-linux.md)
> * [Windows のディスク暗号化](service-fabric-enable-azure-disk-encryption-windows.md)
>
>

このチュートリアルでは、Linux で Azure Service Fabric クラスター ノードのディスク暗号化を有効にする方法について説明します。 ノード タイプおよび仮想マシン スケール セットごとに、これらの手順に従う必要があります。 ノードを暗号化するために、ここでは仮想マシン スケール セットで Azure Disk Encryption 機能を利用します。

このガイドでは、次のトピックについて説明します。

* Linux の Service Fabric クラスターの仮想マシン スケール セットでディスク暗号化を有効にする場合に、気を付ける必要がある主要な概念。
* Linux の Service Fabric クラスター ノードでディスク暗号化を有効にする前に実行する必要がある手順。
* Linux の Service Fabric クラスター ノードでディスク暗号化を有効にするために実行する必要がある手順。



[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

 **自己登録**

仮想マシン スケール セットのディスク暗号化プレビューには、自己登録が必要です。 次の手順に従います。

1. 次のコマンドを実行します。 
    ```powershell
    Register-AzProviderFeature -ProviderNamespace Microsoft.Compute -FeatureName "UnifiedDiskEncryption"
    ```
2. 状態が "*登録済み*" になるまで約 10 分待ちます。 状態を確認するには、次のコマンドを実行します。
    ```powershell
    Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
    Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
    ```
**Azure Key Vault**

1. スケール セットと同じサブスクリプションおよびリージョンにキー コンテナーを作成します。 次に、PowerShell コマンドレットを使用して、キー コンテナーに対して **EnabledForDiskEncryption** アクセス ポリシーを選択します。 Azure portal で Key Vault UI を使用して、次のコマンドによってポリシーを設定することもできます。
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
* Linux の仮想マシン スケール セットの OS とデータ ボリュームでは、暗号化と暗号化の無効化の両方がサポートされています。 
* 現在のプレビューでは、仮想マシン スケール セットに対する仮想マシン (VM) の再イメージ操作とアップグレード操作はサポートされていません。


## <a name="create-a-new-cluster-and-enable-disk-encryption"></a>新しいクラスターを作成してディスク暗号化を有効にする

以下のコマンドを使用してクラスターを作成し、Azure Resource Manager テンプレートと自己署名証明書を使って、ディスク暗号化を有効にします。

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

カスタム テンプレートを作成する必要がある場合は、[Azure Service Fabric のクラスター作成テンプレート サンプル](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)のページのいずれかのテンプレートを使用することを強くお勧めします。 

カスタム テンプレートを既に持っている場合は、テンプレート内の証明書に関連した全部で 3 つのパラメーターとパラメーター ファイルの名前が次のようになっていることを再確認します。 また、次に示すように値が null であることも確認してください。

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

Linux の仮想マシン スケール セットではデータ ディスクの暗号化のみがサポートされているため、Resource Manager テンプレートを使用してデータ ディスクを追加する必要があります。 以下のように、ご利用のテンプレートをデータ ディスクのプロビジョニング用に更新します。

```Json
   
   "storageProfile": { 
            "imageReference": { 
              "publisher": "[parameters('vmImagePublisher')]", 
              "offer": "[parameters('vmImageOffer')]", 
              "sku": "[parameters('vmImageSku')]", 
              "version": "[parameters('vmImageVersion')]" 
            }, 
            "osDisk": { 
              "caching": "ReadOnly", 
              "createOption": "FromImage", 
              "managedDisk": { 
                "storageAccountType": "[parameters('storageAccountType')]" 
              } 
           }, 
                "dataDisks": [ 
                { 
                    "diskSizeGB": 1023, 
                    "lun": 0, 
                    "createOption": "Empty" 
   
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

同等の CLI コマンドを次に示します。 宣言ステートメントの値は適切な値に変更してください。 CLI では、上記の PowerShell コマンドでサポートされている他のすべてのパラメーターがサポートされています。

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

### <a name="mount-a-data-disk-to-a-linux-instance"></a>データ ディスクを Linux インスタンスにマウントする
仮想マシン スケール セットでの暗号化に進む前に、追加されたデータ ディスクが正しくマウントされていることを確認してください。 Linux クラスター VM にサインインして、**LSBLK** コマンドを実行します。 出力では、 **[Mount Point]** 列に追加されたデータ ディスクが示されます。


### <a name="deploy-application-to-a-service-fabric-cluster-in-linux"></a>Linux の Service Fabric クラスターにアプリケーションをデプロイする
アプリケーションをクラスターにデプロイするには、「[クイック スタート: Service Fabric への Linux コンテナーのデプロイ](service-fabric-quickstart-containers-linux.md)」の手順とガイダンスに従ってください。


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

### <a name="validate-if-disk-encryption-is-enabled-for-a-virtual-machine-scale-set-in-linux"></a>Linux の仮想マシン スケール セットのディスク暗号化が有効になっているかどうかを確認する
仮想マシン スケール セット全体の状態、またはスケール セット内の任意のインスタンス VM の状態を取得するには、以下のコマンドを実行します。
また、Linux クラスター VM にサインインして、**LSBLK** コマンドを実行することもできます。 出力では、 **[Mount Point]** 列に追加したデータ ディスクが表示され、 **[Type]** 列には *[Crypt]* と表示されるはずです。

```powershell

$VmssName = "nt1vm"
$resourceGroupName = "mycluster"
Get-AzVmssDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName

Get-AzVmssVMDiskEncryption -ResourceGroupName $resourceGroupName -VMScaleSetName $VmssName -InstanceId "0"

```

```azurecli
az vmss encryption show -g <resourceGroupName> -n <VMSS name>

```

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
ここまでで、クラスターがセキュリティで保護され、Service Fabric クラスター ノードと仮想マシン スケール セットでディスク暗号化を有効および無効にする方法を学びました。 Linux での Service Fabric クラスター ノードに関する同様のガイダンスについては、[Windows のディスク暗号化](service-fabric-enable-azure-disk-encryption-windows.md)に関するページを参照してください。 
