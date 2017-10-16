---
title: "Azure に Windows Service Fabric クラスターを作成する | Microsoft Docs"
description: "PowerShell を使用して Windows Service Fabric クラスターを既存の Azure 仮想ネットワークにデプロイする方法を学習します。"
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/26/2017
ms.author: ryanwi
ms.openlocfilehash: 7cee4f8d68062dcfd2b6f61d55319160a2a80a98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Azure 仮想ネットワークに Service Fabric Windows クラスターをデプロイする
このチュートリアルは、シリーズの第 1 部です。 PowerShell を使用して Windows Service Fabric クラスターを既存の Azure 仮想ネットワーク (VNET) とサブネットにデプロイする方法を学習します。 完了すると、クラウドで実行されているクラスターにアプリケーションをデプロイできるようになります。  Azure CLI を使用して Linux クラスターを作成する場合は、[Azure でのセキュリティで保護された Linux クラスターの作成](service-fabric-tutorial-create-vnet-and-linux-cluster.md)に関するページを参照してください。

このチュートリアルで学習する内容は次のとおりです。

> [!div class="checklist"]
> * PowerShell を使用して Azure に VNET を作成する
> * Key Vault を作成して証明書をアップロードする
> * セキュリティで保護された Service Fabric クラスターを Azure PowerShell に作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * Azure にセキュリティで保護されたクラスターを作成する
> * [Service Fabric を使用して API Management をデプロイする](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>前提条件
このチュートリアルを開始する前に
- Azure サブスクリプションを持っていない場合は[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成する
- [Service Fabric SDK と PowerShell モジュール](service-fabric-get-started.md)をインストールする
- [Azure PowerShell モジュール Version 4.1 以上](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)をインストールする

次の手順で、5 ノードの Service Fabric クラスターを作成します。 Azure で Service Fabric クラスターを実行することによって発生するコストを計算するには、[Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を使用します。

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Azure にサインインしてサブスクリプションを選択する
このガイドでは Azure PowerShell を使用します。 新しい PowerShell セッションを開始するときに、Azure アカウントにサインインし、Azure のコマンドを実行する前にサブスクリプションを選択します。
 
Azure アカウントにサインインしてサブスクリプションを選択するには、次のスクリプトを実行します。

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>リソース グループの作成
デプロイ用の新しいリソース グループを作成し、リソース グループに名前を付けて、場所を指定します。

```powershell
$ResourceGroupName = "sfclustertutorialgroup"
New-AzureRmResourceGroup -Name $ResourceGroupName -Location centralus
```

## <a name="deploy-the-network-topology"></a>ネットワーク トポロジをデプロイする
次に、API Management と Service Fabric クラスターのデプロイ先となるネットワーク トポロジを設定します。 [network.json][network-arm] Resource Manager テンプレートは、仮想ネットワーク (VNET) を作成するよう構成されています。また、Service Fabric のサブネットおよびネットワーク セキュリティ グループ (NSG)、API Management のサブネットおよび NSG を作成するようにも構成されています。 VNET、サブネット、NSG の詳細については、[こちら](../virtual-network/virtual-networks-overview.md)を参照してください。

[network.parameters.json][network-parameters-arm] パラメーター ファイルには、Service Fabric と API Management のデプロイ先となるサブネットと NSG の名前が含まれています。  API Management は、[次のチュートリアル](service-fabric-tutorial-deploy-api-management.md)でデプロイされます。 このガイドでは、パラメーターの値を変更する必要はありません。 Service Fabric Resource Manager テンプレートでは、これらのパラメーターの値を使用します。  ここで値を変更する場合は、このチュートリアルおよび [API Management のデプロイのチュートリアル](service-fabric-tutorial-deploy-api-management.md)で使用されている、他の Resource Manager テンプレート内の値も変更する必要があります。 

次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。
- [network.json][network-arm]
- [network.parameters.json][network-parameters-arm]

次の PowerShell コマンドを使用して、ネットワーク設定用の Resource Manager テンプレートとパラメーター ファイルをデプロイします。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\network.json -TemplateParameterFile .\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="create-a-key-vault-and-upload-a-certificate"></a>Key Vault を作成して証明書をアップロードする
次の手順の Service Fabric クラスターの Resource Manager テンプレートは、証明書セキュリティで保護されたクラスターを作成するよう構成されています。 この証明書は、クラスターのノード間通信を保護し、Service Fabric クラスターへのユーザー アクセスを管理するために使用されます。 また、API Management がサービスを検出するために Service Fabric Naming Service にアクセスする場合にも、この証明書が使用されます。 それには、クラスターのセキュリティのため、証明書を Key Vault で管理する必要があります。

次のスクリプトは、Azure に Key Vault を作成します。また、自己署名証明書を作成し、作成した自己署名証明書を Key Vault にアップロードします。  既存の証明書を使用する場合は、**$CreateSelfSignedCertificate** を "$false" に設定し、場所を **$ExistingPfxFilePath** に指定します。

```powershell
$VaultResourceGroupName = 'ryanwikeyvaultgroup'
$VaultName= 'ryanwikeyvault'
$Location = "westus"
$CertificateName = "ryanwicertificate1"
$Password = 'mypa$$word!'
$DnsName = "www.mycluster.westus.mydomain.com" #The certificate's subject name must match the domain used to access the Service Fabric cluster.
$OutputPath = "C:\MyCertificates" # location where you want the .PFX to be stored
$CreateSelfSignedCertificate = $true
$ExistingPfxFilePath = 'C:\MyCertificates\ryanwicertificate1.pfx'

$ErrorActionPreference = 'Stop'

Write-Host "Switching context to SubscriptionId $SubscriptionId"
Set-AzureRmContext -SubscriptionId $SubscriptionId | Out-Null

# New-AzureRmResourceGroup is idempotent as long as the location matches
Write-Host "Ensuring ResourceGroup $VaultResourceGroupName in $Location"
New-AzureRmResourceGroup -Name $VaultResourceGroupName -Location $Location -Force | Out-Null
$resourceId = $null

try
{
    $existingKeyVault = Get-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName
    $resourceId = $existingKeyVault.ResourceId

    Write-Host "Using existing vault $VaultName in $($existingKeyVault.Location)"
}
catch
{
}

if(!$existingKeyVault)
{
    Write-Host "Creating new vault $VaultName in $location"
    $newKeyVault = New-AzureRmKeyVault -VaultName $VaultName -ResourceGroupName $VaultResourceGroupName -Location $Location -EnabledForDeployment
    $resourceId = $newKeyVault.ResourceId
}

if($CreateSelfSignedCertificate)
{
    $securePassword = ConvertTo-SecureString -String $password -AsPlainText -Force

    $NewPfxFilePath = Join-Path $OutputPath $($CertificateName+".pfx")

    Write-Host "Creating new self signed certificate at $NewPfxFilePath"
    
    ## Changes to PSPKI version 3.5.2 New-SelfSignedCertificate replaced by New-SelfSignedCertificateEx
    $PspkiVersion = (Get-Module PSPKI).Version
    if($PSPKIVersion.Major -ieq 3 -And $PspkiVersion.Minor -ieq 2 -And $PspkiVersion.Build -ieq 5) {
        New-SelfsignedCertificateEx -Subject "CN=$DnsName" -EKU "Server Authentication", "Client authentication" -KeyUsage "KeyEncipherment, DigitalSignature" -Path $NewPfxFilePath -Password $securePassword -Exportable
    }
    else {
        New-SelfSignedCertificate -CertStoreLocation Cert:\CurrentUser\My -DnsName $DnsName | Export-PfxCertificate -FilePath $NewPfxFilePath -Password $securePassword | Out-Null
    }

    $ExistingPfxFilePath = $NewPfxFilePath
}

Write-Host "Reading pfx file from $ExistingPfxFilePath"
$cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2 $ExistingPfxFilePath, $Password

$bytes = [System.IO.File]::ReadAllBytes($ExistingPfxFilePath)
$base64 = [System.Convert]::ToBase64String($bytes)

$jsonBlob = @{
   data = $base64
   dataType = 'pfx'
   password = $Password
   } | ConvertTo-Json

    $contentbytes = [System.Text.Encoding]::UTF8.GetBytes($jsonBlob)
    $content = [System.Convert]::ToBase64String($contentbytes)

    $secretValue = ConvertTo-SecureString -String $content -AsPlainText -Force

Write-Host "Writing secret to $CertificateName in vault $VaultName"
$secret = Set-AzureKeyVaultSecret -VaultName $VaultName -Name $CertificateName -SecretValue $secretValue

$output = @{};
$output.SourceVault = $resourceId;
$output.CertificateURL = $secret.Id;
$output.CertificateThumbprint = $cert.Thumbprint;

Write-Host "Source vault: " $output.SourceVault
Write-Host "Certificate URL: " $output.CertificateURL
Write-Host "Certificate Thumbprint: " $output.CertificateThumbprint
```

## <a name="deploy-the-service-fabric-cluster"></a>Service Fabric クラスターをデプロイする
ネットワーク リソースのデプロイが完了したら、次は Service Fabric クラスターを VNET の所定のサブネットと NSG にデプロイします。 既存の VNET とサブネットにクラスターをデプロイするには (この記事では既にデプロイ済み)、Resource Manager テンプレートが必要です。  詳細については、「[Azure Resource Manager を使用して Service Fabric クラスターを作成する](service-fabric-cluster-creation-via-arm.md)」を参照してください。 このチュートリアル シリーズで使用するテンプレートは、前の手順で設定した VNET、サブネット、NSG の名前を使用するようあらかじめ構成されています。  次の Resource Manager テンプレートとパラメーター ファイルをダウンロードします。
- [cluster.json][cluster-arm]
- [cluster.parameters.json][cluster-parameters-arm]

デプロイ用の *cluster.parameters.json* ファイルにある、空の **clusterName**、**adminUserName**、**adminPassword**、**certificateThumbprint**、**certificateUrlValue**、および **sourceVaultValue** の各パラメーターに入力します。  以前に既存の証明書を Key Vault にアップロードした場合は、その証明書の **certificateThumbprint**、**certificateUrlValue**、および **sourceVaultValue** の各値を入力します。

次の PowerShell コマンドを使用して、Service Fabric クラスターを作成するための Resource Manager テンプレートとパラメーター ファイルをデプロイします。

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -TemplateFile .\cluster.json -TemplateParameterFile .\cluster.parameters.json -Verbose
```

## <a name="connect-to-the-secure-cluster"></a>セキュリティで保護されたクラスターに接続する
Service Fabric SDK とともにインストールされた Service Fabric PowerShell モジュールを使用してクラスターに接続します。  まず、この証明書を現在のユーザーのコンピューターの個人用 (マイ) ストアにインストールする必要があります。  次の PowerShell コマンドを実行します。

```powershell
$certpwd="Password#1234" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

これで、セキュリティで保護されたクラスターに接続する準備ができました。

**Service Fabric** PowerShell モジュールには、Service Fabric のクラスター、アプリケーション、およびサービスを管理するための多くのコマンドレットが用意されています。  セキュリティで保護されたクラスターに接続するには、[Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) コマンドレットを使用します。 証明書の拇印と接続エンドポイントの詳細は、前の手順の出力で確認できます。

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

接続とクラスターの正常性を確認するには、[Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) コマンドレットを使用します。

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ
このチュートリアル シリーズの他の記事では、ここで作成したクラスターを使用します。 次の記事にすぐに進まない場合は、料金の発生を避けるため、クラスターを削除することができます。 クラスターと、そのクラスターによって使用されるすべてのリソースを削除するための最も簡単な方法は、リソース グループを削除することです。

Azure にログインして、クラスターを削除するサブスクリプション ID を選択します。  サブスクリプション ID は、[Azure Portal](http://portal.azure.com) にログインして確認できます。 リソース グループとそのグループのクラスター リソースすべてを削除するには、[Remove-AzureRMResourceGroup cmdlet](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup) コマンドレットを使用します。

```powershell
Login-AzureRmAccount
Select-AzureRmSubscription -SubscriptionId "Subcription ID"

$ResourceGroupName = "sfclustertutorialgroup"
Remove-AzureRmResourceGroup -Name $ResourceGroupName -Force
```

## <a name="next-steps"></a>次のステップ
このチュートリアルで学習した内容は次のとおりです。

> [!div class="checklist"]
> * PowerShell を使用して Azure に VNET を作成する
> * Key Vault を作成して証明書をアップロードする
> * PowerShell を使用してセキュリティで保護された Service Fabric クラスターを Azure に作成する
> * X.509 証明書を持つクラスターを保護する
> * PowerShell を使用してクラスターに接続する
> * クラスターの削除

次のチュートリアルでは、Service Fabric を使用して API Management をデプロイする方法について説明します。
> [!div class="nextstepaction"]
> [API Management をデプロイする](service-fabric-tutorial-deploy-api-management.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
