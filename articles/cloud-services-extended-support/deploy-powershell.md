---
title: クラウド サービス (延長サポート) をデプロイする - PowerShell
description: PowerShell を使用してクラウド サービス (延長サポート) をデプロイする
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 0c1b67e42e7988a836ec58ac022b11d736210bca
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865623"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Azure PowerShell を使用してクラウド サービス (延長サポート) をデプロイする

この記事では、複数のロール (Web ロールおよび worker ロール) を備え、かつ、リモート デスクトップ拡張機能を追加したクラウド サービス (延長サポート) を、`Az.CloudService` PowerShell モジュールを使って Azure にデプロイする方法を説明します。 

> [!IMPORTANT]
> Cloud Services (延長サポート) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="before-you-begin"></a>始める前に

クラウド サービス (延長サポート) の[デプロイの前提条件](deploy-prerequisite.md)を確認し、関連するリソースを作成します。 

## <a name="deploy-a-cloud-services-extended-support"></a>クラウド サービス (延長サポート) をデプロイする
1. Az.CloudService PowerShell モジュールをインストールします。  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. 新しいリソース グループを作成する。 既存のリソース グループを使用する場合は、この手順は省略可能です。   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. クラウド サービス パッケージ (.cspkg) ファイルとサービス構成 (.cscfg) ファイルの格納に使用するストレージ アカウントとコンテナーを作成します。 ストレージ アカウント名には、一意の名前を使用する必要があります。 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. ストレージ アカウントにクラウド サービス パッケージ (cspkg) をアップロードします。

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  ストレージ アカウントにクラウド サービス構成 (cscfg) をアップロードします。 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. 仮想ネットワークとサブネットを作成します。 既存のネットワークとサブネットを使用する場合は、この手順は省略可能です。 この例では、Web ロールと worker ロールのどちらのクラウド サービス ロールにも、単一の仮想ネットワークとサブネットを使用します。 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. パブリック IP アドレスを作成し、(必要に応じて) そのパブリック IP アドレスの DNS ラベル プロパティを設定します。 静的 IP を使用する場合は、サービス構成ファイルで予約済み IP として参照する必要があります。  

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. ネットワーク プロファイル オブジェクトを作成し、プラットフォームで作成されたロード バランサーのフロントエンドにパブリック IP アドレスを関連付けます。  

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. キー コンテナーを作成する このキー コンテナーは、クラウド サービス (延長サポート) の各種ロールに関連付けられている証明書を格納するために使用します。 このキー コンテナーは、一意の名前を使用したうえで、クラウド サービスと同じリージョンおよびサブスクリプションに配置する必要があります。 詳細については、「[Azure Cloud Services (延長サポート) で証明書を使用する](certificates-and-key-vault.md)」を参照してください。

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. キー コンテナーのアクセス ポリシーを更新し、自分のユーザー アカウントに証明書に関するアクセス許可を付与します。 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    または、ObjectId を使ってアクセス ポリシーを設定します (ObjectId は、`Get-AzADUser` を実行すると取得できます)。 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. この例では、キー コンテナーに自己署名証明書を追加します。 クラウド サービスのロールにデプロイするためには、クラウド サービス構成 (.cscfg) ファイルに証明書の拇印を追加する必要があります。 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. OS プロファイルのメモリ内オブジェクトを作成します。 OS プロファイルは、クラウド サービスのロールに関連付ける証明書を指定するものです。 これは、前の手順で作成したのと同じ証明書になります。 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. ロール プロファイルのメモリ内オブジェクトを作成します。 ロール プロファイルは、名前、容量、階層など、ロール SKU 固有のプロパティを定義するものです。 この例では、frontendRole と backendRole の 2 つのロールを定義しています。 ロール プロファイルの情報は、構成 (cscfg) ファイルとサービス定義 (csdef) ファイルの中で定義されているロール構成と一致している必要があります。 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (省略可能) クラウド サービスに追加する拡張機能プロファイルのメモリ内オブジェクトを作成します。 この例では、RDP 拡張機能を追加します。 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    次のように、configFile には PublicConfig タグのみが含まれていること、また名前空間が含まれていることが必要です。
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (省略可能) クラウド サービスに追加するタグを PowerShell ハッシュ テーブルとして定義します。 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. プロファイル オブジェクトと SAS URL を使用してクラウド サービスのデプロイを作成します。

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>次のステップ 
- Cloud Services (延長サポート) に関して[よく寄せられる質問](faq.md)を確認します。
- [Azure portal](deploy-portal.md)、[PowerShell](deploy-powershell.md)、[テンプレート](deploy-template.md)、または [Visual Studio](deploy-visual-studio.md) を使用してクラウド サービス (延長サポート) をデプロイします。
- [Cloud Services (延長サポート) のサンプル リポジトリ](https://github.com/Azure-Samples/cloud-services-extended-support)を確認します。
