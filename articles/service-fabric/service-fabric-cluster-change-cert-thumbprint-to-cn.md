---
title: 証明書共通名を使用するように Azure Service Fabric クラスターを更新する | Microsoft Docs
description: 証明書の拇印の使用から証明書共通名の使用へと Service Fabric クラスターを切り替える方法について説明します。
services: service-fabric
documentationcenter: .net
author: athinanthny
manager: chackdan
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/06/2019
ms.author: atsenthi
ms.openlocfilehash: 3618339349d618b371a40d3b37ebc30192c067ca
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70764820"
---
# <a name="change-cluster-from-certificate-thumbprint-to-common-name"></a>証明書の拇印から共通名へとクラスターを変更する
2 つの証明書が同じ拇印を持つことはできず、そのことがクラスター証明書のロール オーバーや管理を困難にしています。 ただし、複数の証明書で同じ共通名や件名を持つことはできます。  デプロイされたクラスターで使用するのを、証明書の拇印から証明書共通名に切り替えることで、証明書の管理が大幅に単純化します。 この記事では、実行中の Service Fabric クラスターを、証明書の拇印ではなく証明書共通名を使用するように更新する方法について説明します。

>[!NOTE]
> テンプレートで 2 つの拇印が宣言されている場合は、デプロイを 2 回実行する必要があります。  1 回目のデプロイは、この記事の手順を行う前に実行します。  1 回目のデプロイでは、テンプレートの **thumbprint** プロパティが使用される証明書に設定されて、**thumbprintSecondary** プロパティが削除されます。  2 回目のデプロイで、この記事の手順が行われます。
 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="get-a-certificate"></a>証明書を取得する
最初に、[証明機関 (CA)](https://wikipedia.org/wiki/Certificate_authority) から証明書を取得します。  証明書共通名は、クラスターのホスト名にする必要があります。  たとえば、"myclustername.southcentralus.cloudapp.azure.com" とします。  

テスト目的の場合は、無料またはオープンな証明機関から CA の署名証明書を取得できます。

> [!NOTE]
> Azure Portal で、Service Fabric クラスターをデプロイするときに生成されるものを含め、自己署名証明書はサポートされません。

## <a name="upload-the-certificate-and-install-it-in-the-scale-set"></a>証明書をアップロードしてそれをスケールセットにインストールする
Azure 内で、Service Fabric クラスターは仮想マシン スケール セットにデプロイされます。  キー コンテナーに証明書をアップロードしてから、それをクラスターが実行されている仮想マシン スケール セットにインストールします。

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  "<subscription ID>"

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "mykeyvaultgroup"
$VaultName = "mykeyvault"
$certFilename = "C:\users\sfuser\myclustercert.pfx"
$certname = "myclustercert"
$Password  = "P@ssw0rd!123"
$VmssResourceGroupName     = "myclustergroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Create the new key vault
$newKeyVault = New-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName `
    -Location $region -EnabledForDeployment 
$resourceId = $newKeyVault.ResourceId 

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName `
    -FilePath $certFilename -Password $PasswordSec

$CertificateThumbprint = $KVSecret.Thumbprint
$CertificateURL = $KVSecret.SecretId
$SourceVault = $resourceId
$CommName    = $KVSecret.Certificate.SubjectName.Name

Write-Host "CertificateThumbprint    :"  $CertificateThumbprint
Write-Host "CertificateURL           :"  $CertificateURL
Write-Host "SourceVault              :"  $SourceVault
Write-Host "Common Name              :"  $CommName    

Set-StrictMode -Version 3
$ErrorActionPreference = "Stop"

$certConfig = New-AzVmssVaultCertificateConfig -CertificateUrl $CertificateURL -CertificateStore "My"

# Get current VM scale set 
$vmss = Get-AzVmss -ResourceGroupName $VmssResourceGroupName -VMScaleSetName $VmssName

# Add new secret to the VM scale set.
$vmss = Add-AzVmssSecret -VirtualMachineScaleSet $vmss -SourceVaultId $SourceVault `
    -VaultCertificate $certConfig

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Verbose `
    -Name $VmssName -VirtualMachineScaleSet $vmss 
```

>[!NOTE]
> スケール セットのシークレットでは、各シークレットはバージョン管理された一意のリソースであるため、2 つの個別のシークレットに対して同じリソース ID はサポートされません。 

## <a name="download-and-update-the-template-from-the-portal"></a>ポータルからテンプレートをダウンロードして更新する
基になるスケール セットに証明書がインストールされましたが、その証明書と共通名を使用するように Service Fabric クラスターを更新する必要もあります。  ここで、クラスター デプロイ用のテンプレートをダウンロードします。  [Azure portal](https://portal.azure.com) にサインインし、クラスターをホストしているリソース グループに移動します。  **[設定]** で、 **[デプロイ]** を選択します。  最新のデプロイを選択し、 **[テンプレートの表示]** をクリックします。

![テンプレートの表示][image1]

テンプレートおよびパラメーターの JSON ファイルをローカル コンピューターにダウンロードします。

最初に、パラメーター ファイルをテキスト エディターで開き、次のパラメーター値を追加します。
```json
"certificateCommonName": {
    "value": "myclustername.southcentralus.cloudapp.azure.com"
},
```

次に、テキスト エディターでテンプレート ファイルを開き、証明書共通名をサポートするための 3 つの更新を行います。

1. **parameters** セクションに *certificateCommonName* パラメーターを追加します。
    ```json
    "certificateCommonName": {
        "type": "string",
        "metadata": {
            "description": "Certificate Commonname"
        }
    },
    ```

    また、*certificateThumbprint* を削除することを検討します。これは Resource Manager テンプレートでもう参照されていない可能性があります。

2. **Microsoft.Compute/virtualMachineScaleSets** リソースで、拇印ではなく証明書の設定の共通名を使用するように仮想マシン拡張機能を更新します。  **virtualMachineProfile**->**extensionProfile**->**extensions**->**properties**->**settings**->**certificate** に `"commonNames": ["[parameters('certificateCommonName')]"],` を追加し、`"thumbprint": "[parameters('certificateThumbprint')]",` を削除します。
    ```json
        "virtualMachineProfile": {
        "extensionProfile": {
            "extensions": [
                {
                    "name": "[concat('ServiceFabricNodeVmExt','_vmNodeType0Name')]",
                    "properties": {
                        "type": "ServiceFabricNode",
                        "autoUpgradeMinorVersion": true,
                        "protectedSettings": {
                            "StorageAccountKey1": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key1]",
                            "StorageAccountKey2": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('supportLogStorageAccountName')),'2015-05-01-preview').key2]"
                        },
                        "publisher": "Microsoft.Azure.ServiceFabric",
                        "settings": {
                            "clusterEndpoint": "[reference(parameters('clusterName')).clusterEndpoint]",
                            "nodeTypeRef": "[variables('vmNodeType0Name')]",
                            "dataPath": "D:\\SvcFab",
                            "durabilityLevel": "Bronze",
                            "enableParallelJobs": true,
                            "nicPrefixOverride": "[variables('subnet0Prefix')]",
                            "certificate": {
                                "commonNames": [
                                    "[parameters('certificateCommonName')]"
                                ],
                                "x509StoreName": "[parameters('certificateStoreValue')]"
                            }
                        },
                        "typeHandlerVersion": "1.0"
                    }
                },
    ```

3.  **Microsoft.ServiceFabric/clusters** リソースで、API バージョンを "2018-02-01" に更新します。  次の例のように、**certificateCommonNames** の設定 (**commonNames** プロパティを含む) の追加と、**certificate** の設定 (thumbprint プロパティを含む) の削除も行います。
    ```json
    {
        "apiVersion": "2018-02-01",
        "type": "Microsoft.ServiceFabric/clusters",
        "name": "[parameters('clusterName')]",
        "location": "[parameters('clusterLocation')]",
        "dependsOn": [
            "[concat('Microsoft.Storage/storageAccounts/', variables('supportLogStorageAccountName'))]"
        ],
        "properties": {
            "addonFeatures": [
                "DnsService",
                "RepairManager"
            ],
            "certificateCommonNames": {
                "commonNames": [
                    {
                        "certificateCommonName": "[parameters('certificateCommonName')]",
                        "certificateIssuerThumbprint": ""
                    }
                ],
                "x509StoreName": "[parameters('certificateStoreValue')]"
            },
        ...
    ```

## <a name="deploy-the-updated-template"></a>更新したテンプレートをデプロイする
変更を加えた後、更新したテンプレートを再度デプロイします。

```powershell
$groupname = "sfclustertutorialgroup"

New-AzResourceGroupDeployment -ResourceGroupName $groupname -Verbose `
    -TemplateParameterFile "C:\temp\cluster\parameters.json" -TemplateFile "C:\temp\cluster\template.json" 
```

## <a name="next-steps"></a>次の手順
* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* [クラスター証明書のロールオーバー](service-fabric-cluster-rollover-cert-cn.md)について学習します
* [クラスター証明書の更新と管理を行います](service-fabric-cluster-security-update-certs-azure.md)

[image1]: ./media/service-fabric-cluster-change-cert-thumbprint-to-cn/PortalViewTemplates.png
