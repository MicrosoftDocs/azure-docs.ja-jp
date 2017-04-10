---
title: "URL ルーティング ルールを使用してApplication Gateway を作成する | Microsoft Docs"
description: "このページでは、URL ルーティング ルールを使用して Azure Application Gateway を作成し、構成する方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: d141cfbb-320a-4fc9-9125-10001c6fa4cf
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: aaf13418331f29287399621cb911e4b9f5b33dc0
ms.openlocfilehash: 54ec0b039b14246e3c64d1721b4562035e39efa5


---
# <a name="create-an-application-gateway-using-path-based-routing"></a>パスベースのルーティングを使用して Application Gateway を作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-url-route-arm-ps.md)

URL パスベースのルーティングを使用すると、HTTP 要求の URL パスに基づいてルートの関連付けを行うことができます。 Application Gateway の URL 一覧に対して構成されたバックエンド プールへのルートがあるかどうかを調べ、定義されたバックエンド プールにネットワーク トラフィックを送信します。 URL ベースのルーティングの一般的な用途は、さまざまな種類のコンテンツに対する要求をさまざまなバックエンド サーバー プールに負荷分散することです。

URL ベースのルーティングでは、新しいルールの種類が Application Gateway に導入されています。 Application Gateway には、basic と PathBasedRouting という 2 つのルールの種類があります。 basic は、バックエンド プールに対してラウンドロビン サービスを提供します。一方、PathBasedRouting はラウンドロビン サービスに加えて、バックエンド プールを選択しながら要求 URL のパス パターンも考慮に入れます。

> [!IMPORTANT]
> PathPattern: これは照合するパス パターンの一覧です。 それぞれ "/" で始まる必要があり、"\*" を配置できるのは末尾だけです。 有効な例としては、/xyz、/xyz*、または /xyz/* があります。 パス照合に提供する文字列では、最初の "?" または "#" の後にテキストを含めません (これらの文字は許可されません)。 

## <a name="scenario"></a>シナリオ

次の例で、Application Gateway は、2 つのバックエンド サーバー プール (ビデオ サーバー プールとイメージ サーバー プール) を使用して contoso.com のトラフィックを処理します。

http://contoso.com/image* に対する要求はイメージ サーバー プール (pool1) に、http://contoso.com/video* に対する要求はビデオ サーバー プール (pool2) にそれぞれルーティングされます。 一致するパス パターンがない場合は、既定のサーバー プール (pool1) が選択されます。

![url ルート](./media/application-gateway-create-url-route-arm-ps/figure1.png)

## <a name="before-you-begin"></a>開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
2. Application Gateway の仮想ネットワークとサブネットを作成します。 仮想マシンまたはクラウドのデプロイメントでサブネットを使用していないことを確認します。 Application Gateway そのものが、仮想ネットワーク サブネットに含まれている必要があります。
3. Application Gateway を使用するためにバックエンド プールに追加されたサーバーが存在している必要があります。つまり、仮想ネットワーク内、または割り当てられたパブリック IP/VIP を使用してエンドポイントが作成されている必要があります。

## <a name="what-is-required-to-create-an-application-gateway"></a>Application Gateway の作成に必要な構成

* **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。 一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。 このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。
* **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

Azure クラシックと Azure Resource Manager の使用方法の違いは、設定が必要なアプリケーション ゲートウェイと項目を作成する順番にあります。

Resource Manager を使用すると、アプリケーション ゲートウェイを作成するすべての項目は個別に構成され、その後結合されてアプリケーション ゲートウェイのリソースが作成されます。

Application Gateway を作成するために必要な手順を次に示します。

1. リソース マネージャーのリソース グループを作成します。
2. Application Gateway の仮想ネットワーク、サブネット、およびパブリック IP を作成します。
3. Application Gateway 構成オブジェクトを作成します。
4. Application Gateway のリソースを作成します。

## <a name="create-a-resource-group-for-resource-manager"></a>リソース マネージャーのリソース グループの作成

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

### <a name="step-1"></a>手順 1

Azure へのログイン

```powershell
Login-AzureRmAccount
```

資格情報を使用して認証を行うように求めるメッセージが表示されます。<BR>

### <a name="step-2"></a>手順 2.

アカウントのサブスクリプションを確認します。

```powershell
Get-AzureRmSubscription
```

### <a name="step-3"></a>手順 3.

使用する Azure サブスクリプションを選択します。 <BR>

```powershell
Select-AzureRmSubscription -Subscriptionid "GUID of subscription"
```

### <a name="step-4"></a>手順 4.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
New-AzureRmResourceGroup -Name appgw-RG -Location "West US"
```

あるいは、Application Gateway のリソース グループに対してタグを作成することもできます。

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway URL routing"} 
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、"appgw-RG" という名前のリソース グループと "West US" という名前の場所を作成しました。

> [!NOTE]
> アプリケーション ゲートウェイのカスタム プローブを構成する必要がある場合は、 [PowerShell を使用したカスタム プローブとアプリケーション ゲートウェイの作成](application-gateway-create-probe-ps.md)に関するページを参照してください。 詳細については、 [カスタム プローブと正常性監視](application-gateway-probe-overview.md) に関するページを参照してください。
> 
> 

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。

### <a name="step-1"></a>手順 1

アドレス範囲 10.0.0.0/24 を仮想ネットワークの作成に使用するサブネットの変数に割り当てます。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24
```

### <a name="step-2"></a>手順 2.

サブネット 10.0.0.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ **appgw-rg** に、**appgwvnet** という名前の仮想ネットワークを作成します。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet
```

### <a name="step-3"></a>手順 3.

Application Gateway を作成する次の手順のために、サブネット変数を割り当てます。

```powershell
$subnet=$vnet.Subnets[0]
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

米国西部リージョンのリソース グループ **appgw-rg** に、パブリック IP リソース **publicIP01** を作成します。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

サービスが開始すると、Application Gateway に IP アドレスが割り当てられます。

## <a name="create-application-gateway-configuration"></a>Application Gateway 構成の作成

Application Gateway を作成するには、すべての構成項目を設定する必要があります。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### <a name="step-1"></a>手順 1

**gatewayIP01** という名前のアプリケーション ゲートウェイの IP 構成を作成します。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet
```

### <a name="step-2"></a>手順 2.

**pool01** および **pool2** という名前のバックエンド IP アドレス プールを構成します。**pool1** には IP アドレス **134.170.185.46**、**134.170.188.221**、**134.170.185.50** を使用し、**pool2** には IP アドレス **134.170.186.46**、**134.170.189.221**、**134.170.186.50** を使用します。

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 134.170.186.46, 134.170.189.221,134.170.186.50
```

この例では、URL パスに基づいてネットワーク トラフィックをルーティングするためのバックエンド プールが 2 つあります。 1 つのプールは、URL パス "/video" からトラフィックを受信し、もう 1 つのプールはパス "/image" からトラフィックを受信します。 独自のアプリケーションの IP アドレス エンドポイントを追加するために、上記の IP アドレスを置き換えます。 

### <a name="step-3"></a>手順 3.

バックエンド プール内の負荷を分散したネットワーク トラフィックに対して、Application Gateway の設定 **poolsetting01** と **poolsetting02** を構成します。 この例では、各バックエンド プールに対してそれぞれ異なるバックエンド プール設定を構成します。 各バックエンド プールは、独自のバックエンド プール設定を持つことができます。

```powershell
$poolSetting01 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting01" -Port 80 -Protocol Http -CookieBasedAffinity Disabled -RequestTimeout 120

$poolSetting02 = New-AzureRmApplicationGatewayBackendHttpSettings -Name "besetting02" -Port 80 -Protocol Http -CookieBasedAffinity Enabled -RequestTimeout 240
```

### <a name="step-4"></a>手順 4.

パブリック IP エンドポイントでフロント エンド IP を構成します。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-5"></a>手順 5.

Application Gateway のフロント エンド ポートを構成します。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 80
```

### <a name="step-6"></a>手順 6.

リスナーを構成します。 この手順では、着信ネットワーク トラフィックを受信するために使用するポートとパブリック IP アドレスについて、リスナーを構成します。 

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Http -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01
```

### <a name="step-7"></a>手順 7.

バックエンド プールの URL ルール パスを構成します。 この手順では、Application Gateway が使用する相対パスを構成します。これにより、URL パスと、着信トラフィックを処理するために割り当てられるバックエンド プールとの間のマッピングが定義されます。

次の例では 2 つのルールを作成します。1 つは、トラフィックをバックエンド "pool1" にルーティングする "/image/" パス用で、もう 1 つは、トラフィックをバックエンド "pool2" にルーティングする "/video/" パス用です。

```powershell
$imagePathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule1" -Paths "/image/*" -BackendAddressPool $pool1 -BackendHttpSettings $poolSetting01

$videoPathRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "pathrule2" -Paths "/video/*" -BackendAddressPool $pool2 -BackendHttpSettings $poolSetting02
```

パスがあらかじめ定義されているパス ルールのいずれとも一致しない場合、ルール パス マップ構成では、既定のバックエンド アドレス プールも構成します。 

```powershell
$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $videoPathRule, $imagePathRule -DefaultBackendAddressPool $pool1 -DefaultBackendHttpSettings $poolSetting02
```

### <a name="step-8"></a>手順 8.

ルール設定を作成します。 この手順では、URL パスベースのルーティングを使用する Application Gateway を構成します。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-9"></a>手順 9.

Application Gateway のインスタンス数とサイズを構成します。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Small" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>Application Gateway の作成

前述の手順の構成オブジェクトをすべて使用して、Application Gateway を作成します。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku
```

## <a name="get-application-gateway-dns-name"></a>アプリケーション ゲートウェイの DNS 名の取得

ゲートウェイを作成したら、次は通信用にフロントエンドを構成します。 パブリック IP を使用する場合、アプリケーション ゲートウェイには、動的に割り当てられたフレンドリではない DNS 名が必要です。 エンド ユーザーがアプリケーション ゲートウェイを確実に見つけられるように、CNAME レコードを使用して、アプリケーション ゲートウェイのパブリック エンドポイントを参照できます。 次に、[Azure でのカスタム ドメイン名を構成します](../cloud-services/cloud-services-custom-domain-name-portal.md)。 フロントエンド IP CNAME レコードを構成するには、アプリケーション ゲートウェイに接続されている PublicIPAddress 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。 アプリケーション ゲートウェイの DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成する必要があります。 アプリケーション ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

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

Secure Sockets Layer (SSL) オフロードの詳細については、 [SSL オフロード用のアプリケーション ゲートウェイの構成](application-gateway-ssl-arm.md)に関するページを参照してください。




<!--HONumber=Feb17_HO3-->


