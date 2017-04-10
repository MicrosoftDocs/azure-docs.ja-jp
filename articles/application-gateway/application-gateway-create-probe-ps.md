---
title: "カスタム プローブを作成する - Azure Application Gateway - PowerShell | Microsoft Docs"
description: "リソース マネージャーで PowerShell を使用して、Application Gateway のカスタム プローブを作成する方法の説明"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 68feb660-7fa4-4f69-a7e4-bdd7bdc474db
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: fd5960a4488f2ecd93ba117a7d775e78272cbffd
ms.openlocfilehash: 794797d9c42ec7f2fc351bab109147e45ce06070


---
# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Azure リソース マネージャーで PowerShell を使用して Azure Application Gateway のカスタム プローブを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-probe-ps.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-probe-classic-ps.md)

[!INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

> [!NOTE]
> Azure には、リソースの作成と操作に関して、[Resource Manager とクラシックの](../azure-resource-manager/resource-manager-deployment-model.md)&2; 種類のデプロイメント モデルがあります。  この記事では、Resource Manager デプロイメント モデルの使用方法について取り上げていますが、最新のデプロイでは、[クラシック デプロイメント モデル](application-gateway-create-probe-classic-ps.md)ではなくこのモデルをお勧めします。

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>手順 1

`Login-AzureRmAccount` を使用して認証を行います。

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>手順 2.

アカウントのサブスクリプションを確認します。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>手順 3.

使用する Azure サブスクリプションを選択します。

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>手順 4.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、**appgw-RG** という名前のリソース グループと **West US** という名前の場所を作成しました。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

次の手順では、アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

### <a name="step-1"></a>手順 1

アドレス範囲 10.0.0.0/24 を仮想ネットワークの作成に使用するサブネットの変数に割り当てます。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>手順 2.

サブネット 10.0.0.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ **appgw-rg** に、**appgwvnet** という名前の仮想ネットワークを作成します。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>手順 3.

Application Gateway を作成する次の手順のために、サブネット変数を割り当てます。

```powershell
$subnet = $vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

米国西部リージョンのリソース グループ **appgw-rg** に、パブリック IP リソース **publicIP01** を作成します。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic
```

## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>カスタム プローブを設定したアプリケーション ゲートウェイ構成オブジェクトの作成

アプリケーション ゲートウェイを作成する前に、すべての構成項目を設定します。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### <a name="step-1"></a>手順 1

**gatewayIP01** という名前のアプリケーション ゲートウェイの IP 構成を作成します。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを&1; つ取得することに注意してください。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>手順 2.

IP アドレス **134.170.185.46、134.170.188.221、134.170.185.50** を使用して、**pool01** という名前のバックエンド IP アドレス プールを構成します。 これらの値は、フロントエンド IP エンドポイントから送信されるネットワーク トラフィックを受信する IP アドレスです。 独自のアプリケーションの IP アドレス エンドポイントを追加するために、上記の IP アドレスを置き換えます。

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221, 134.170.185.50
```

### <a name="step-3"></a>手順 3.

この手順では、カスタム プローブを作成します。

使用するパラメーターは次のとおりです。

* **Interval** - プローブのチェック間隔を秒単位で指定します。
* **Timeout** - プローブの HTTP 応答チェックのタイムアウト期間を定義します。
* **Hostname と Path** - インスタンスの状態を判断するために Application Gateway により呼び出される完全な URL パス。 たとえば、**http://contoso.com/** という Web サイトがある場合、HTTP 応答が正常かどうかをプローブでチェックするために、**http://contoso.com/path/custompath.htm** に対してカスタム プローブを構成します。
* **UnhealthyThreshold** - バックエンド インスタンスに " **異常**" というフラグを設定するために必要な HTTP 応答の失敗数。

```powershell
$probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-4"></a>手順 4.

バックエンド プール内のトラフィックに対して、アプリケーション ゲートウェイの設定 **poolsetting01** を構成します。 また、この手順では、アプリケーション ゲートウェイの要求に対するバックエンド プールの応答のタイムアウトも構成します。 Application Gateway は、バックエンド応答がタイムアウト制限に達すると要求を取り消します。 この値は、プローブ チェックに対するバックエンド応答についてのみ適用されるプローブ タイムアウトとは異なります。

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80
```

### <a name="step-5"></a>手順 5.

パブリック IP エンドポイントに対して、**frontendport01** という名前のフロントエンド IP ポートを構成します。

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80
```

### <a name="step-6"></a>手順 6.

**fipconfig01** という名前のフロントエンド IP 構成を作成し、このフロントエンド IP 構成にパブリック IP アドレスを関連付けます。

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip
```

### <a name="step-7"></a>手順 7.

**listener01** という名前のリスナーを作成し、フロントエンド IP 構成にフロントエンド ポートを関連付けます。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp
```

### <a name="step-8"></a>手順 8.

ロード バランサーの動作を構成する、**rule01** という名前のロード バランサーのルーティング規則を作成します。

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-9"></a>手順 9.

アプリケーション ゲートウェイのインスタンスのサイズを構成します。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> **InstanceCount** の既定値は 2、最大値は 10 です。 **GatewaySize** の既定値は Medium です。 **Standard_Small**、**Standard_Medium**、**Standard_Large** のいずれかを選択できます。 

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>New-AzureRmApplicationGateway を使用した Application Gateway の作成

前の手順の構成項目をすべて使用して、Application Gateway を作成します。 この例では、アプリケーション ゲートウェイは **appgwtest** という名前です。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku
```

## <a name="add-a-probe-to-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイへのプローブの追加

既存のアプリケーション ゲートウェイにカスタム プローブを追加するには、次の&4; つの手順を実行します。

### <a name="step-1"></a>手順 1

`Get-AzureRmApplicationGateway` を使用して、PowerShell 変数にアプリケーション ゲートウェイ リソースを読み込みます。

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>手順 2.

既存のゲートウェイの構成にプローブを追加します。

```powershell
$getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

この例では、contoso.com/path/custompath.htm という URL パスを 30 秒間隔でチェックするようにカスタム プローブを構成しています。 タイムアウトのしきい値は 120 秒、プローブ要求の最大失敗回数は 8 回です。

### <a name="step-3"></a>手順 3.

`Set-AzureRmApplicationGatewayBackendHttpSettings` を使用して、バック エンド プール設定の構成とタイムアウトにプローブを追加します。

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120
```

### <a name="step-4"></a>手順 4.

`Set-AzureRmApplicationGateway` を使用して、アプリケーション ゲートウェイに構成を保存します。

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイからのプローブの削除

既存のアプリケーション ゲートウェイからカスタム プローブを削除する手順を次に示します。

### <a name="step-1"></a>手順 1

`Get-AzureRmApplicationGateway` を使用して、PowerShell 変数にアプリケーション ゲートウェイ リソースを読み込みます。

```powershell
$getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg
```

### <a name="step-2"></a>手順 2.

`Remove-AzureRmApplicationGatewayProbeConfig` を使用して、アプリケーション ゲートウェイからプローブ構成を削除します。

```powershell
$getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name
```

### <a name="step-3"></a>手順 3.

`Set-AzureRmApplicationGatewayBackendHttpSettings` を使用してバック エンド プールの設定を更新し、プローブとタイムアウトの設定を削除します。

```powershell
    $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled
```

### <a name="step-4"></a>手順 4.

`Set-AzureRmApplicationGateway` を使用して、アプリケーション ゲートウェイに構成を保存します。 

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $getgw
```

## <a name="get-application-gateway-dns-name"></a>アプリケーション ゲートウェイの DNS 名の取得

ゲートウェイを作成したら、次は通信用にフロントエンドを構成します。 パブリック IP を使用する場合、アプリケーション ゲートウェイには、動的に割り当てられたフレンドリではない DNS 名が必要です。 エンド ユーザーがアプリケーション ゲートウェイを確実に見つけられるように、CNAME レコードを使用して、アプリケーション ゲートウェイのパブリック エンドポイントを参照できます。 次に、[Azure でのカスタム ドメイン名を構成します](../cloud-services/cloud-services-custom-domain-name-portal.md)。 それには、アプリケーション ゲートウェイに接続されている PublicIPAddress 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。 アプリケーション ゲートウェイの DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成する必要があります。 アプリケーション ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
```

```
Name                     : publicIP01
ResourceGroupName        : appgw-RG
Location                 : westus
Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
ResourceGuid             : 00000000-0000-0000-0000-000000000000
ProvisioningState        : Succeeded
Tags                     : 
PublicIpAllocationMethod : Dynamic
IpAddress                : xx.xx.xxx.xx
PublicIpAddressVersion   : IPv4
IdleTimeoutInMinutes     : 4
IpConfiguration          : {
                                "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                            Configurations/frontend1"
                            }
DnsSettings              : {
                                "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                            }
```

## <a name="next-steps"></a>次のステップ

[SSL オフロードの構成](application-gateway-ssl-arm.md)




<!--HONumber=Jan17_HO4-->


