---
title: Azure Service Fabric クラスターの証明書をロールオーバーする
description: 証明書共通名によって識別される Service Fabric クラスターの証明書をロールオーバーする方法について説明します。
ms.topic: conceptual
ms.date: 09/06/2019
ms.openlocfilehash: 65ea4f463073c472ac6a31e62dcfdfd11cb28cc5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88853347"
---
# <a name="manually-roll-over-a-service-fabric-cluster-certificate"></a>Service Fabric クラスターの証明書を手動でロールオーバーする
Service Fabric クラスター証明書の期限切れが近い場合は、証明書を更新する必要があります。  証明書のロール オーバーは、クラスターが (拇印ではなく) [共通名に基づく証明書を使用するように設定](service-fabric-cluster-change-cert-thumbprint-to-cn.md)されている場合は単純です。  証明機関から、新しい有効期限日の新しい証明書を取得します。  自己署名証明書は、Azure portal のクラスター作成ワークフロー中に生成された証明書を含めるため、運用環境の Service Fabric クラスターではサポートされません。 新しい証明書は、古い証明書と同じ共通名を持っている必要があります。 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Service Fabric クラスターは、ホストに複数の有効な証明書がインストールされている場合、さらに将来の有効期限日を持つ宣言された証明書を自動的に使用します。 ベスト プラクティスは、Azure Resource Manager テンプレートを使用して Azure リソースをプロビジョニングすることです。 運用環境以外の環境では、次のスクリプトを使用して、キー コンテナーに新しい証明書をアップロードしてから、仮想マシン スケール セットに証明書をインストールすることができます。 

```powershell
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser -Force

$SubscriptionId  =  <subscription ID>

# Sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId $SubscriptionId

$region = "southcentralus"
$KeyVaultResourceGroupName  = "keyvaultgroup"
$VaultName = "cntestvault2"
$certFilename = "C:\users\sfuser\sftutorialcluster20180419110824.pfx"
$certname = "cntestcert"
$Password  = "!P@ssw0rd321"
$VmssResourceGroupName     = "sfclustertutorialgroup"
$VmssName                  = "prnninnxj"

# Create new Resource Group 
New-AzResourceGroup -Name $KeyVaultResourceGroupName -Location $region

# Get the key vault.  The key vault must be enabled for deployment.
$keyVault = Get-AzKeyVault -VaultName $VaultName -ResourceGroupName $KeyVaultResourceGroupName 
$resourceId = $keyVault.ResourceId  

# Add the certificate to the key vault.
$PasswordSec = ConvertTo-SecureString -String $Password -AsPlainText -Force
$KVSecret = Import-AzKeyVaultCertificate -VaultName $vaultName -Name $certName  -FilePath $certFilename -Password $PasswordSec

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
$vmss.VirtualMachineProfile.OsProfile.Secrets[0].VaultCertificates.Add($certConfig)

# Update the VM scale set 
Update-AzVmss -ResourceGroupName $VmssResourceGroupName -Name $VmssName -VirtualMachineScaleSet $vmss  -Verbose
```

>[!NOTE]
> Compute の仮想マシン スケール セットのシークレットでは、各シークレットはバージョン管理された一意のリソースであるため、2 つの個別のシークレットに対して同じリソース ID はサポートしていません。 

## <a name="next-steps"></a>次の手順

* [クラスター セキュリティ](service-fabric-cluster-security.md)について学習します。
* [クラスター証明書の更新と管理を行います](service-fabric-cluster-security-update-certs-azure.md)
