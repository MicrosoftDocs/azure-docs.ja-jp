---
title: 'チュートリアル: Web アプリケーション アクセスを改善する - Azure Application Gateway'
description: このチュートリアルでは、Azure PowerShell を使用して、自動スケールの予約済み IP アドレスを持つゾーン冗長アプリケーション ゲートウェイを作成する方法について学習します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 2a756313a4659dfc531289c2c86890371f700367
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102452290"
---
# <a name="tutorial-create-an-application-gateway-that-improves-web-application-access"></a>チュートリアル: Web アプリケーションのアクセスを改善するアプリケーション ゲートウェイを作成する

Web アプリケーションのアクセスを改善しようと考えている IT 管理者なら、顧客の需要に応じてスケーリングし、複数の可用性ゾーンを利用するように、アプリケーション ゲートウェイを最適化できます。 このチュートリアルは、自動スケーリング、ゾーン冗長性、予約済み VIP (静的 IP) といった Azure Application Gateway の機能を構成するときに役立ちます。 Azure PowerShell コマンドレットと Azure Resource Manager デプロイ モデルを使用して、問題を解決します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * 自己署名証明書の作成
> * 自動スケーリングする仮想ネットワークを作成する
> * 予約済みパブリック IP を作成する
> * アプリケーション ゲートウェイのインフラストラクチャをセットアップする
> * 自動スケールを指定する
> * アプリケーション ゲートウェイの作成
> * アプリケーション ゲートウェイのテスト

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルでは、管理用の Azure PowerShell セッションをローカルで実行する必要があります。 Azure PowerShell モジュール バージョン 1.0.0 以降がインストールされている必要があります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell のバージョンを確認した後、`Connect-AzAccount` を実行して Azure との接続を作成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>リソース グループを作成する
利用可能ないずれかの場所で、リソース グループを作成します。

```azurepowershell
$location = "East US 2"
$rg = "AppGW-rg"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
```

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

実際の運用では、信頼できるプロバイダーによって署名された有効な証明書をインポートする必要があります。 このチュートリアルでは、[New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) を使用して、自己署名証明書を作成します。 [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) と返されたサムプリントを使用して、pfx ファイルを証明書からエクスポートできます。

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

次のような結果が表示されます。

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

サムプリントを使用して、pfx ファイルを作成します。 *\<password>* を任意のパスワードに置き換えます。

```powershell
$pwd = ConvertTo-SecureString -String "<password>" -Force -AsPlainText

Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```


## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

自動スケーリングするアプリケーション ゲートウェイ用の 1 つの専用サブネットを持つ仮想ネットワークを作成します。 現在は、専用サブネットごとに 1 つの自動スケール アプリケーション ゲートウェイしかデプロイできません。

```azurepowershell
#Create VNet with two subnets
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "BackendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg `
       -Location $location -AddressPrefix "10.0.0.0/16" -Subnet $sub1, $sub2
```

## <a name="create-a-reserved-public-ip"></a>予約済みパブリック IP を作成する

PublicIPAddress の割り当て方法を **Static** として指定します。 自動スケール アプリケーション ゲートウェイ VIP は、静的のみです。 動的 IP はサポートされていません。 標準の PublicIpAddress SKU のみがサポートされています。

```azurepowershell
#Create static public IP
$pip = New-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP" `
       -location $location -AllocationMethod Static -Sku Standard -Zone 1,2,3
```

## <a name="retrieve-details"></a>詳細を取得する

ローカル オブジェクトのリソース グループ、サブネット、および IP の詳細を取得して、アプリケーション ゲートウェイの IP 構成の詳細を作成します。

```azurepowershell
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="create-web-apps"></a>Web アプリを作成する

バックエンド プール用に 2 つの Web アプリを構成します。 *\<site1-name>* と *\<site-2-name>* を `azurewebsites.net` ドメインの一意の名前に置き換えます。

```azurepowershell
New-AzAppServicePlan -ResourceGroupName $rg -Name "ASP-01"  -Location $location -Tier Basic `
   -NumberofWorkers 2 -WorkerSize Small
New-AzWebApp -ResourceGroupName $rg -Name <site1-name> -Location $location -AppServicePlan ASP-01
New-AzWebApp -ResourceGroupName $rg -Name <site2-name> -Location $location -AppServicePlan ASP-01
```

## <a name="configure-the-infrastructure"></a>インフラストラクチャを構成する

既存の Standard アプリケーション ゲートウェイと同じ形式で、IP 構成、フロントエンド IP 構成、バックエンド プール、HTTP 設定、証明書、ポート、リスナー、およびルールを構成します。 新しい SKU は、Standard SKU と同じオブジェクト モデルに従います。

$pool 変数の定義内の 2 つの Web アプリの FQDN (例: `mywebapp.azurewebsites.net`) を置き換えます。

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses <your first web app FQDN>, <your second web app FQDN>
$fp01 = New-AzApplicationGatewayFrontendPort -Name "SSLPort" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$securepfxpwd = ConvertTo-SecureString -String "Azure123456!" -AsPlainText -Force
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert" -Password $securepfxpwd `
            -CertificateFile "c:\appgwcert.pfx"
$listener01 = New-AzApplicationGatewayHttpListener -Name "SSLListener" `
             -Protocol Https -FrontendIPConfiguration $fip -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp02

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled -PickHostNameFromBackendAddress
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule2" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener02 -BackendAddressPool $pool
```

## <a name="specify-autoscale"></a>自動スケールを指定する

これで、アプリケーション ゲートウェイに自動スケーリングの構成を指定できます。 

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```
このモードでは、アプリケーション ゲートウェイは、アプリケーションのトラフィック パターンに基づいて、自動スケールします。

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

アプリケーション ゲートウェイを作成し、冗長性ゾーンと自動スケーリングの構成を含めます。

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 `
  -ResourceGroupName $rg -Location $location -BackendAddressPools $pool `
  -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig `
  -FrontendIpConfigurations $fip -FrontendPorts $fp01, $fp02 `
  -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 `
  -Sku $sku -sslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

アプリケーション ゲートウェイのパブリック IP アドレスは、Get-AzPublicIPAddress を使用して取得します。 パブリック IP アドレスまたは DNS 名をコピーして、それをお使いのブラウザーのアドレス バーに貼り付けます。

```azurepowershell
$pip = Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
$pip.IpAddress
```


## <a name="clean-up-resources"></a>リソースをクリーンアップする

最初に、アプリケーション ゲートウェイで作成されたリソースを調べます。 次に、必要がないときは、`Remove-AzResourceGroup` コマンドを使用して、リソース グループ、アプリケーション ゲートウェイ、およびすべての関連リソースを削除できます。

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [URL パスベースのルーティング規則のあるアプリケーション ゲートウェイを作成する](./tutorial-url-route-powershell.md)