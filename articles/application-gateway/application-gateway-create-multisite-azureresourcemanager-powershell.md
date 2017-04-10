---
title: "複数のサイトをホストするアプリケーション ゲートウェイを作成する | Microsoft Docs"
description: "このページでは、同じゲートウェイで複数の Web アプリケーションをホストするための Azure アプリケーション ゲートウェイを作成して構成する手順について説明します。"
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: b107d647-c9be-499f-8b55-809c4310c783
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: e20f7349f30c309059c2867d7473fa6fdefa9b61
ms.openlocfilehash: d46c87480fd198bf4f09e48f4d2ea838a350190c


---
# <a name="create-an-application-gateway-for-hosting-multiple-web-applications"></a>複数の Web アプリケーションをホストするためのアプリケーション ゲートウェイを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-multisite-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-multisite-azureresourcemanager-powershell.md)

複数サイトのホストにより、複数の Web アプリケーションを同じアプリケーション ゲートウェイにデプロイすることができます。 どのリスナーがトラフィックを受信するかを決定するには、受信 HTTP 要求にホスト ヘッダーがあるかどうかに依存します。 リスナーは、ゲートウェイのルール定義の構成に従って、適切なバックエンド プールにトラフィックを送ります。 SSL 対応の Web アプリケーションの場合、アプリケーション ゲートウェイは Server Name Indication (SNI) 拡張機能を使用して Web トラフィックに適切なリスナーを選択します。 複数サイトのホストの一般的な用途は、さまざまな Web ドメインに対する要求をさまざまなバックエンド サーバー プールに負荷分散することです。 同様に、同じルート ドメインの複数のサブドメインも、同じアプリケーション ゲートウェイでホストすることができます。

## <a name="scenario"></a>シナリオ

次の例では、アプリケーション ゲートウェイは、2 つのバックエンド サーバー プール (contoso サーバー プールと fabrikam サーバー プール) を使用して contoso.com および fabrikam.com のトラフィックを処理します。 同様の設定は、app.contoso.com や blog.contoso.com などのサブドメインのホストにも使用できます。

![imageURLroute](./media/application-gateway-create-multisite-azureresourcemanager-powershell/multisite.png)

## <a name="before-you-begin"></a>開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
2. アプリケーション ゲートウェイを使用するためにバックエンド プールに追加されたサーバーが存在している必要があります。つまり、仮想ネットワーク内の別のサブネット、または割り当てられたパブリック IP/VIP を使用してエンドポイントが作成されている必要があります。

## <a name="requirements"></a>必要条件

* **バックエンド サーバー プール:** バックエンド サーバーの IP アドレスの一覧。 一覧の IP アドレスは、仮想ネットワークのサブネットに属しているか、パブリック IP/VIP である必要があります。 FQDN を使用することもできます。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定はプールに関連付けられ、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** このポートは、Application Gateway で開かれたパブリック ポートです。 このポートにトラフィックがヒットすると、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。 複数サイト対応のアプリケーション ゲートウェイでは、ホスト名と SNI インジケーターも追加されます。
* **ルール:** ルールはリスナーとバックエンド サーバー プールを結び付け、トラフィックが特定のリスナーにヒットした際に送られるバックエンド サーバー プールを定義します。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

アプリケーション ゲートウェイを作成するために必要な手順を次に示します。

1. リソース マネージャーのリソース グループを作成します。
2. アプリケーション ゲートウェイの仮想ネットワーク、サブネット、およびパブリック IP を作成します。
3. Application Gateway 構成オブジェクトを作成します。
4. Application Gateway のリソースを作成します。

## <a name="create-a-resource-group-for-resource-manager"></a>リソース マネージャーのリソース グループの作成

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、[リソース マネージャーでの Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

### <a name="step-1"></a>手順 1

Azure へのログイン

```powershell
Login-AzureRmAccount
```
資格情報を使用して認証を行うように求めるメッセージが表示されます。

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
New-AzureRmResourceGroup -Name appgw-RG -location "West US"
```

あるいは、Application Gateway のリソース グループに対してタグを作成することもできます。

```powershell
$resourceGroup = New-AzureRmResourceGroup -Name appgw-RG -Location "West US" -Tags @{Name = "testtag"; Value = "Application Gateway multiple site"}
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

上記の例では、**appgw-RG** という名前のリソース グループと**米国西部**という名前の場所を作成しました。

> [!NOTE]
> アプリケーション ゲートウェイのカスタム プローブを構成する必要がある場合は、 [PowerShell を使用したカスタム プローブとアプリケーション ゲートウェイの作成](application-gateway-create-probe-ps.md)に関するページを参照してください。 詳細については、[カスタム プローブと正常性監視](application-gateway-probe-overview.md)に関するページを参照してください。

## <a name="create-a-virtual-network-and-subnets"></a>仮想ネットワークとサブネットの作成

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。 この手順では、2 つのサブネットを作成します。 最初のサブネットは、アプリケーション ゲートウェイ自体で使用します。 アプリケーション ゲートウェイには、インスタンスを保持するための専用のサブネットが必要です。 このサブネットにデプロイできるのは、他のアプリケーション ゲートウェイのみです。 2 番目のサブネットは、アプリケーションのバックエンド サーバーを保持するために使用されます。

### <a name="step-1"></a>手順 1

アドレス範囲 10.0.0.0/24 を、アプリケーション ゲートウェイを保持するために使用するサブネットの変数に割り当てます。

```powershell
$subnet = New-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -AddressPrefix 10.0.0.0/24
```
### <a name="step-2"></a>手順 2.

アドレス範囲 10.0.1.0/24 を、バックエンド プールに使用する 2 番目のサブネットの変数に割り当てます。

```powershell
$subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -AddressPrefix 10.0.1.0/24
```

### <a name="step-3"></a>手順 3.

サブネット 10.0.0.0/24 および 10.0.1.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ **appgw-rg** に、**appgwvnet** という名前の仮想ネットワークを作成します。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet,$subnet2
```

### <a name="step-4"></a>手順 4.

Application Gateway を作成する次の手順のために、サブネット変数を割り当てます。

```powershell
$appgatewaysubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name appgatewaysubnet -VirtualNetwork $vnet
$backendsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name backendsubnet -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

米国西部リージョンのリソース グループ **appgw-rg** に、パブリック IP リソース **publicIP01** を作成します。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -name publicIP01 -location "West US" -AllocationMethod Dynamic
```

サービスが開始すると、Application Gateway に IP アドレスが割り当てられます。

## <a name="create-application-gateway-configuration"></a>Application Gateway 構成の作成

アプリケーション ゲートウェイを作成する前に、すべての構成項目を設定する必要があります。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### <a name="step-1"></a>手順 1

**gatewayIP01** という名前のアプリケーション ゲートウェイの IP 構成を作成します。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを&1; つ取得することに注意してください。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $appgatewaysubnet
```

### <a name="step-2"></a>手順 2.

**pool01** および **pool2** という名前のバックエンド IP アドレス プールを構成します。**pool1** には IP アドレス **134.170.185.46**、**134.170.188.221**、**134.170.185.50** を使用し、**pool2** には IP アドレス **134.170.186.46**、**134.170.189.221**、**134.170.186.50** を使用します。

```powershell
$pool1 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 10.0.1.100, 10.0.1.101, 10.0.1.102
$pool2 = New-AzureRmApplicationGatewayBackendAddressPool -Name pool02 -BackendIPAddresses 10.0.1.103, 10.0.1.104, 10.0.1.105
```

この例では、要求されたサイトに基づいてネットワーク トラフィックをルーティングするためのバックエンド プールが&2; つあります。 1 つのプールは、サイト "contoso.com" からトラフィックを受信し、もう&1; つのプールはサイト "fabrikam.com" からトラフィックを受信します。 独自のアプリケーションの IP アドレス エンドポイントを追加するには、上記の IP アドレスを置き換える必要があります。 内部 IP アドレスの代わりに、バックエンド インスタンスにパブリック IP アドレス、FQDN、または VM の NIC も使用できます。 PowerShell で IP の代わりに FQDN を指定する場合は、"-BackendFQDNs" パラメーターを使用します。

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
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "fep01" -Port 443
```

### <a name="step-6"></a>手順 6.

この例でサポートする&2; つの Web サイトのために、2 つの SSL 証明書を構成します。 1 つの証明書が contoso.com トラフィック用で、もう&1; つが fabrikam.com トラフィック用です。 これらの証明書は、Web サイトに対して証明機関によって発行された証明書である必要があります。 自己署名証明書もサポートされていますが、運用環境のトラフィックにはお勧めしません。

```powershell
$cert01 = New-AzureRmApplicationGatewaySslCertificate -Name contosocert -CertificateFile <file path> -Password <password>
$cert02 = New-AzureRmApplicationGatewaySslCertificate -Name fabrikamcert -CertificateFile <file path> -Password <password>
```

### <a name="step-7"></a>手順 7.

この例では、2 つの Web サイトのために&2; つのリスナーを構成します。 この手順では、着信トラフィックを受信するために使用するパブリック IP アドレス、ポート、およびホストのリスナーを構成します。 複数サイトのサポートには HostName パラメーターが必要であり、トラフィックの受信対象である適切な Web サイトに設定する必要があります。 複数ホストのシナリオで SSL のサポートを必要とする Web サイトでは、RequireServerNameIndication パラメーターを true に設定する必要があります。 SSL のサポートが必要な場合は、その Web アプリケーションへのトラフィックをセキュリティで保護するために使用される SSL 証明書も指定する必要があります。 FrontendIPConfiguration、FrontendPort、HostName の組み合わせは、リスナーに対して一意である必要があります。 リスナーごとに&1; つの証明書をサポートできます。

```powershell
$listener01 = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "contoso11.com" -RequireServerNameIndication true  -SslCertificate $cert01
$listener02 = New-AzureRmApplicationGatewayHttpListener -Name "listener02" -Protocol Https -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -HostName "fabrikam11.com" -RequireServerNameIndication true -SslCertificate $cert02
```

### <a name="step-8"></a>手順 8.

この例では、2 つの Web アプリケーション用に&2; つのルール設定を作成します。 ルールによって、リスナー、バックエンド プール、および http 設定が相互に関連付けられます。 この手順では、Web サイトごとに&1; つ、基本のルーティング ルールを使用するようにアプリケーション ゲートウェイを構成します。 各 Web サイトへのトラフィックは、構成されたリスナーによって受信され、BackendHttpSettings で指定されたプロパティを使用して、構成されたバックエンド プールに送られます。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule01" -RuleType Basic -HttpListener $listener01 -BackendHttpSettings $poolSetting01 -BackendAddressPool $pool1
$rule02 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule02" -RuleType Basic -HttpListener $listener02 -BackendHttpSettings $poolSetting02 -BackendAddressPool $pool2
```

### <a name="step-9"></a>手順 9.

Application Gateway のインスタンス数とサイズを構成します。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "Standard_Medium" -Tier Standard -Capacity 2
```

## <a name="create-application-gateway"></a>アプリケーション ゲートウェイの作成

前述の手順の構成オブジェクトをすべて使用して、Application Gateway を作成します。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-RG -Location "West US" -BackendAddressPools $pool1,$pool2 -BackendHttpSettingsCollection $poolSetting01, $poolSetting02 -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener01, $listener02 -RequestRoutingRules $rule01, $rule02 -Sku $sku -SslCertificates $cert01, $cert02
```

> [!IMPORTANT]
> Application Gateway プロビジョニングは実行時間の長い操作であり、完了までに時間がかかる場合があります。
> 
> 

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

[Application Gateway - Web アプリケーション ファイアウォール](application-gateway-webapplicationfirewall-overview.md)を使用して Web サイトを保護する方法について学びます。




<!--HONumber=Dec16_HO2-->


