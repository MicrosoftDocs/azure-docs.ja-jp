---
title: Key Vault 証明書を使用して TLS 終端を構成する - PowerShell
titleSuffix: Azure Application Gateway
description: Azure PowerShell スクリプトを使用して、TLS または SSL 終端の証明書のためにキー コンテナーとアプリケーション ゲートウェイを統合する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 05/26/2020
ms.author: victorh
ms.openlocfilehash: aaaeed9d8d6a2d84fa13f495f581dc1f5fdc19e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323426"
---
# <a name="configure-tls-termination-with-key-vault-certificates-using-azure-powershell"></a>Azure PowerShell で Key Vault 証明書を使用して TLS 終端を構成する

[Azure Key Vault](../key-vault/general/overview.md) はプラットフォームマネージド シークレット ストアです。シークレット、キー、TLS または SSL 証明書を保護するために使用できます。 Azure Application Gateway では、HTTPS 対応リスナーにアタッチされているサーバー証明書用の Key Vault との統合をサポートします。 このサポートは、Application Gateway v2 SKU に制限されます。

詳細については、「[Key Vault 証明書での SSL 終了](key-vault-certs.md)」を参照してください。

この記事では、Azure PowerShell スクリプトを使用して TLS または SSL 終端の証明書のためにキー コンテナーとアプリケーション ゲートウェイを統合する方法を示します。

この記事では、Azure PowerShell モジュール バージョン 1.0.0 以降が必要です。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 この記事でコマンドを実行するには、`Connect-AzAccount` を実行して Azure との接続を作成することも必要です。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

開始する前に、ManagedServiceIdentity モジュールがインストールされている必要があります。

```azurepowershell
Install-Module -Name Az.ManagedServiceIdentity
Connect-AzAccount
Select-AzSubscription -Subscription <your subscription>
```

## <a name="example-script"></a>サンプル スクリプト

### <a name="set-up-variables"></a>変数を設定する

```azurepowershell
$rgname = "KeyVaultTest"
$location = "East US"
$kv = "<your key vault name>"
$appgwName = "AppGwKVIntegration"
```
> [!IMPORTANT]
> キー コンテナーの名前は普遍的に一意である必要があります。

### <a name="create-a-resource-group-and-a-user-managed-identity"></a>リソース グループとユーザーマネージド ID を作成する

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname
```

### <a name="create-a-key-vault-policy-and-certificate-to-be-used-by-the-application-gateway"></a>アプリケーション ゲートウェイに使用されるキー コンテナー、ポリシー、証明書を作成する

```azurepowershell
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
Set-AzKeyVaultAccessPolicy -VaultName $kv -EmailAddress <your email address> -PermissionsToCertificates create,get,list
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")
```
> [!NOTE]
> TLS 終端が正しく機能するには、-EnableSoftDelete フラグを使用する必要があります。 [ポータルからの Key Vault の論理的な削除](../key-vault/general/soft-delete-overview.md#soft-delete-behavior)を構成している場合、保持期間は 90 日 (既定値) で維持する必要があります。 Application Gateway では、まだ異なる保有期間をサポートしていません。 

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-virtual-ip-vip-address"></a>静的パブリック仮想 IP (VIP) アドレスを作成する

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-front-end-ports"></a>プールとフロントエンドのポートを作成する

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80
```

### <a name="point-the-tlsssl-certificate-to-your-key-vault"></a>TLS または SSL 証明書をキー コンテナーに向ける

```azurepowershell
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId
```

### <a name="create-listeners-rules-and-autoscale"></a>リスナー、規則、自動スケーリングを作成する

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSetting -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
```

### <a name="assign-the-user-managed-identity-to-the-application-gateway"></a>ユーザーマネージド ID をアプリケーション ゲートウェイに割り当てる

```azurepowershell
$appgwIdentity = New-AzApplicationGatewayIdentity -UserAssignedIdentityId $identity.Id
```

### <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -Identity $appgwIdentity -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts @($fp01, $fp02) -HttpListeners @($listener01, $listener02) `
  -RequestRoutingRules @($rule01, $rule02) -Sku $sku `
  -SslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="next-steps"></a>次のステップ

[TLS 終端に関する詳細情報](ssl-overview.md)
