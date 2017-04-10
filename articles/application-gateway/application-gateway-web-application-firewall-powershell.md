---
title: "Web アプリケーション ファイアウォールの構成 - Azure Application Gateway | Microsoft Docs"
description: "この記事では、既存または新規のアプリケーション ゲートウェイで Web アプリケーション ファイアウォールの使用を開始する方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 52b7728c3fc702e37f5c5fe3d6544117a11464e8
ms.lasthandoff: 03/30/2017


---
# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>新規または既存の Application Gateway における Web アプリケーション ファイアウォールの構成

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-web-application-firewall-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-web-application-firewall-powershell.md)

Azure Application Gateway の Web アプリケーション ファイアウォール (WAF) は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護します。

Azure Application Gateway はレイヤー 7 のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。 Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどの多くのアプリケーション配信コントローラー (ADC) 機能を備えています。 サポートされている機能の完全な一覧については、「Application Gateway の概要」を参照してください。

以下の記事では、[Web アプリケーション ファイアウォールを既存のアプリケーション ゲートウェイに追加する](#add-web-application-firewall-to-an-existing-application-gateway)方法と [Web アプリケーション ファイアウォールを使用するアプリケーション ゲートウェイを作成する](#create-an-application-gateway-with-web-application-firewall)方法について説明します。

![シナリオのイメージ][scenario]

## <a name="waf-configuration-differences"></a>WAF の構成上の相違点

[PowerShell を使用した Application Gateway の作成](application-gateway-create-gateway-arm.md)に関するページをお読みであれば、アプリケーション ゲートウェイを作成するときに構成する SKU の設定はご存じのとおりです。 WAF には、アプリケーション ゲートウェイで SKU を構成するときに定義する追加の設定があります。 アプリケーション ゲートウェイ自体に加える変更はありません。

**SKU** -WAF が追加されていない通常のアプリケーション ゲートウェイでは、**Standard\_Small**、**Standard\_Medium**、および **Standard\_Large** の各サイズがサポートされています。 WAF を導入すると、2 つの SKU、**WAF\_Medium** と **WAF\_Large** が追加されます。 小規模なアプリケーション ゲートウェイでは、WAF はサポートされていません。

**層** - 使用できる値は **Standard** または **WAF** です。 Web アプリケーション ファイアウォールを使用する場合は、 **WAF** を選択する必要があります。

**モード** - WAF のモードです。 使用できる値は **Detection** と **Prevention**です。 WAF を検出モードで設定すると、すべての脅威がログ ファイルに記録されます。 防止モードでは、検出モードと同様にイベントがログに記録されますが、攻撃者はアプリケーション ゲートウェイから "403 許可されていません" 応答を受信します。

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

### <a name="step-1"></a>手順 1

Azure アカウントにログインします。

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>手順 2.

このシナリオで使用するサブスクリプションを選択します。

```powershell
Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>手順 3.

Web アプリケーション ファイアウォールを追加するゲートウェイを取得します。

```powershell
$gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
```

### <a name="step-4"></a>手順 4.

Web アプリケーション ファイアウォール SKU を構成します。 利用可能なサイズは、**WAF\_Large** と **WAF\_Medium** です。 Web アプリケーション ファイアウォールを使用する場合は、レベルが **WAF** である必要があり、SKU の設定時に容量を確認する必要があります。

```powershell
$gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
```

### <a name="step-5"></a>手順 5.

次の例で定義しているように、WAF の設定を構成します。

**WafMode** の設定に利用できる値は、"Prevention" と "Detection"です。

```powershell
$gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
```

### <a name="step-6"></a>手順 6.

前の手順で定義した設定でアプリケーション ゲートウェイを更新します。

```powershell
Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

このコマンドを実行すると、Web アプリケーション ファイアウォールによってアプリケーション ゲートウェイが更新されます。 [Application Gateway の診断](application-gateway-diagnostics.md) に関する記事を参照して、アプリケーション ゲートウェイのログを表示する方法について理解することをお勧めします。 WAF のセキュリティの性質により、ログを定期的に確認して Web アプリケーションのセキュリティ状況を把握する必要があります。

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Web アプリケーション ファイアウォールのある Application Gateway を作成する

次の手順では、Web アプリケーション ファイアウォールのある Application Gateway を作成するプロセスを最初から最後まで実行します。

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

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
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-4"></a>手順 4.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

前の例では、"appgw-RG" という名前のリソース グループと "West US" という名前の場所を作成しました。

> [!NOTE]
> アプリケーション ゲートウェイのカスタム プローブを構成する必要がある場合は、 [PowerShell を使用したカスタム プローブとアプリケーション ゲートウェイの作成](application-gateway-create-probe-ps.md)に関するページを参照してください。 詳細については、 [カスタム プローブと正常性監視](application-gateway-probe-overview.md) に関するページを参照してください。

### <a name="step-5"></a>手順 5.

Application Gateway 自体に使用するサブネットのアドレス範囲を割り当てます。

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> アプリケーションのサブネットには、少なくとも 28 個のマスク ビットが必要です。 これによって、Application Gateway インスタンスのサブネットに 10 個の使用可能なアドレスが残ります。 これより小さいサブネットには、今後アプリケーション ゲートウェイのインスタンスを追加することができません。


### <a name="step-6"></a>手順 6.

バックエンド アドレス プールに使用するアドレス範囲を割り当てます。

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-7"></a>手順 7.

[リソース グループの作成](#create-the-resource-group)

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-8"></a>手順 8.

次の手順で、使用する仮想ネットワーク リソースとサブネットのリソースを取得します。

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

### <a name="step-9"></a>手順 9.

アプリケーション ゲートウェイに使用するパブリック IP リソースを作成します。 このパブリック IP アドレスは、以降の手順のいずれかで使用します。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway では、ドメイン ラベルを定義して作成したパブリック IP アドレスを使用できません。 動的に作成されるドメイン ラベルを持つパブリック IP アドレスのみがサポートされています。 アプリケーション ゲートウェイにわかりやすい DNS 名を付ける必要がある場合は、CNAME レコードをエイリアスとして使用することをお勧めします。


### <a name="step-10"></a>手順 10.

アプリケーション ゲートウェイを作成する前に、すべての構成項目を設定する必要があります。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

アプリケーション ゲートウェイの IP 構成を作成します。これにより、Application Gateway で使用するサブネットが構成されます。 Application Gateway が起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-11"></a>手順 11.

バックエンド Web サーバーの IP アドレスを使用してバックエンド IP アドレス プールを構成します。 これらの IP アドレスは、フロントエンド IP エンドポイントから送信されるネットワーク トラフィックを受信する IP アドレスです。 独自のアプリケーションの IP アドレス エンドポイントを追加するには、次の IP アドレスを置き換えます。

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

### <a name="step-12"></a>手順 12.

SSL 対応バックエンド プール リソースで使用する証明書をアップロードします。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>
```

### <a name="step-13"></a>手順 13.

アプリケーション ゲートウェイのバックエンドの http 設定を構成します。 前の手順でアップロードした証明書を http 設定に割り当てます。

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-14"></a>手順 14.

パブリック IP エンドポイントのフロントエンド IP ポートを構成します。 このポートは、エンドユーザーが接続するポートです。

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-15"></a>手順 15.

フロントエンドの IP 構成を作成します。この設定によって、アプリケーション ゲートウェイのフロントエンドにプライベート IP アドレスまたはパブリック IP アドレスがマッピングされます。 次の手順で、フロントエンドの IP 構成と前の手順のパブリック IP アドレスを関連付けます。

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-16"></a>手順 16.

アプリケーション ゲートウェイの証明書を構成します。 この証明書は、アプリケーション ゲートウェイでのトラフィックの暗号化解除と再暗号化に使用されます。

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-17"></a>手順 17.

アプリケーション ゲートウェイの HTTP リスナーを作成します。 使用するフロントエンド IP 構成、ポート、および SSL 証明書を割り当てます。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-18"></a>手順 18.

ロード バランサーの動作を構成するロード バランサーのルーティング規則を作成します。 この例では、ラウンド ロビンの基本的な規則を作成します。

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-19"></a>手順 19.

アプリケーション ゲートウェイのインスタンスのサイズを構成します。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2
```

> [!NOTE]
> **WAF\_Medium** と **WAF\_Large** から選択できます。WAF を使用するときの層は常に **WAF** です。 Capacity に指定できるのは、1 から 10 までの任意の数です。

### <a name="step-20"></a>手順 20.

WAF のモードを構成します。指定できる値は **Prevention** と **Detection** です。

```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

### <a name="step-21"></a>手順 21.

前の手順の構成項目をすべて使用して、アプリケーション ゲートウェイを作成します。 この例では、アプリケーション ゲートウェイは "appgwtest" という名前です。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> 基本的な Web アプリケーション ファイアウォールの構成で作成されたアプリケーション ゲートウェイは、CRS 3.0 の保護で構成されています。

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

[Application Gateway の診断](application-gateway-diagnostics.md)に関するページにアクセスして、診断ログを構成する方法と、Web アプリケーション ファイアウォールで検出または防止されたイベントを記録する方法を確認します。

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png

