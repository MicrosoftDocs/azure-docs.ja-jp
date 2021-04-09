---
title: Azure Application Gateway を作成して HTTP ヘッダーを書き換える
description: この記事では、Azure PowerShell を使用して、Azure Application Gateway を作成し、HTTP ヘッダーを書き換える方法に関する情報を提供します
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/19/2019
ms.author: absha
ms.openlocfilehash: 4a1a122eb7b5b0abcc47cd321c74267a1a4aecda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93396857"
---
# <a name="create-an-application-gateway-and-rewrite-http-headers"></a>アプリケーション ゲートウェイを作成して HTTP ヘッダーを書き換える

[自動スケールおよびゾーン冗長アプリケーション ゲートウェイの SKU](./application-gateway-autoscaling-zone-redundant.md) を新規作成するときには、Azure PowerShell を使用して、[HTTP 要求および応答ヘッダーを書き換えるルール](rewrite-http-headers.md)を構成できます

この記事では、次のことについて説明します。

* 自動スケーリングする仮想ネットワークを作成する
* 予約済みパブリック IP を作成する
* アプリケーション ゲートウェイのインフラストラクチャをセットアップする
* http ヘッダーの書き換えルールの構成を指定する
* 自動スケールを指定する
* アプリケーション ゲートウェイの作成
* アプリケーション ゲートウェイのテスト

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

この記事では、Azure PowerShell をローカルで実行する必要があります。 Az モジュール バージョン 1.0.0 以降がインストールされている必要があります。 バージョンを確認するには、`Import-Module Az`、`Get-Module Az` の順に実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 PowerShell のバージョンを確認した後、`Login-AzAccount` を実行して Azure との接続を作成します。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="create-a-resource-group"></a>リソース グループを作成する
利用可能ないずれかの場所で、リソース グループを作成します。

```azurepowershell
$location = "East US 2"
$rg = "<rg name>"

#Create a new Resource Group
New-AzResourceGroup -Name $rg -Location $location
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
       -location $location -AllocationMethod Static -Sku Standard
```

## <a name="retrieve-details"></a>詳細を取得する

ローカル オブジェクトのリソース グループ、サブネット、および IP の詳細を取得して、アプリケーション ゲートウェイの IP 構成の詳細を作成します。

```azurepowershell
$resourceGroup = Get-AzResourceGroup -Name $rg
$publicip = Get-AzPublicIpAddress -ResourceGroupName $rg -name "AppGwVIP"
$vnet = Get-AzvirtualNetwork -Name "AutoscaleVNet" -ResourceGroupName $rg
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "AppGwSubnet" -VirtualNetwork $vnet
```

## <a name="configure-the-infrastructure"></a>インフラストラクチャを構成する

既存の Standard アプリケーション ゲートウェイと同じ形式で、IP 構成、フロントエンド IP 構成、バックエンド プール、HTTP 設定、証明書、ポート、およびリスナーを構成します。 新しい SKU は、Standard SKU と同じオブジェクト モデルに従います。

```azurepowershell
$ipconfig = New-AzApplicationGatewayIPConfiguration -Name "IPConfig" -Subnet $gwSubnet
$fip = New-AzApplicationGatewayFrontendIPConfig -Name "FrontendIPCOnfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "Pool1" `
       -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "HTTPPort" -Port 80

$listener01 = New-AzApplicationGatewayHttpListener -Name "HTTPListener" `
             -Protocol Http -FrontendIPConfiguration $fip -FrontendPort $fp01

$setting = New-AzApplicationGatewayBackendHttpSettings -Name "BackendHttpSetting1" `
          -Port 80 -Protocol Http -CookieBasedAffinity Disabled
```

## <a name="specify-your-http-header-rewrite-rule-configuration"></a>HTTP ヘッダーの書き換えルールの構成を指定する

http ヘッダーの書き換えに必要な新しいオブジェクトを構成します。

- **RequestHeaderConfiguration**: このオブジェクトは、書き換えようとしている要求ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。
- **ResponseHeaderConfiguration**: このオブジェクトは、書き換えようとしている応答ヘッダー フィールドと、元のヘッダーを書き換える必要がある新しい値を指定するために使用されます。
- **ActionSet**: このオブジェクトには、上で指定した要求ヘッダーと応答ヘッダーの構成が格納されます。 
- **RewriteRule**: このオブジェクトには、上で指定したすべての *ActionSet* が格納されます。 
- **RewriteRuleSet**: このオブジェクトは、すべての *RewriteRule* を含み、基本またはパス ベースの要求ルーティング規則にアタッチする必要があります。

   ```azurepowershell
   $requestHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "X-isThroughProxy" -HeaderValue "True"
   $responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Strict-Transport-Security" -HeaderValue "max-age=31536000"
   $actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration    
   $rewriteRule = New-AzApplicationGatewayRewriteRule -Name rewriteRule1 -ActionSet $actionSet    
   $rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name rewriteRuleSet1 -RewriteRule $rewriteRule
   ```

## <a name="specify-the-routing-rule"></a>ルーティング規則を指定する

要求のルーティング規則を作成します。 この書き換えの構成は、作成されると、ルーティング規則によってソース リスナーにアタッチされます。 基本ルーティング規則の使用時には、書き換えの構成は、ソース リスナーに関連付けられている、グローバルなヘッダーの書き換えとなります。 パスベースのルーティング規則を使用する場合、ヘッダーの書き換えの構成は、URL パス マップで定義されています。 そのため、サイトの特定のパス領域にのみ適用されます。 下では、基本ルーティング規則が作成され、書き換えルール セットがアタッチされます。

```azurepowershell
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "Rule1" -RuleType basic `
         -BackendHttpSettings $setting -HttpListener $listener01 -BackendAddressPool $pool -RewriteRuleSet $rewriteRuleSet
```

## <a name="specify-autoscale"></a>自動スケールを指定する

これで、アプリケーション ゲートウェイに自動スケーリングの構成を指定できます。 次の 2 種類の自動スケール構成がサポートされています。

* **固定容量モード**。 このモードでは、アプリケーション ゲートウェイは自動スケールせず、固定されたスケール ユニットの容量で動作します。

   ```azurepowershell
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2 -Capacity 2
   ```

* **自動スケール モード**。 このモードでは、アプリケーション ゲートウェイは、アプリケーションのトラフィック パターンに基づいて、自動スケールします。

   ```azurepowershell
   $autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 2
   $sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
   ```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

アプリケーション ゲートウェイを作成し、冗長性ゾーンと自動スケーリングの構成を含めます。

```azurepowershell
$appgw = New-AzApplicationGateway -Name "AutoscalingAppGw" -Zone 1,2,3 -ResourceGroupName $rg -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $setting -GatewayIpConfigurations $ipconfig -FrontendIpConfigurations $fip -FrontendPorts $fp01 -HttpListeners $listener01 -RequestRoutingRules $rule01 -Sku $sku -AutoscaleConfiguration $autoscaleConfig -RewriteRuleSet $rewriteRuleSet
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

アプリケーション ゲートウェイのパブリック IP アドレスは、Get-AzPublicIPAddress を使用して取得します。 パブリック IP アドレスまたは DNS 名をコピーして、それをお使いのブラウザーのアドレス バーに貼り付けます。

```azurepowershell
Get-AzPublicIPAddress -ResourceGroupName $rg -Name AppGwVIP
```



## <a name="clean-up-resources"></a>リソースをクリーンアップする

最初に、アプリケーション ゲートウェイで作成されたリソースを調べます。 次に、必要がないときは、`Remove-AzResourceGroup` コマンドを使用して、リソース グループ、アプリケーション ゲートウェイ、およびすべての関連リソースを削除できます。

`Remove-AzResourceGroup -Name $rg`

## <a name="next-steps"></a>次のステップ

- [URL パスベースのルーティング規則のあるアプリケーション ゲートウェイを作成する](./tutorial-url-route-powershell.md)