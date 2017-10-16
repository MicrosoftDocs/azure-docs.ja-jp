---
title: "Web アプリケーション ファイアウォールの構成: Azure Application Gateway | Microsoft Docs"
description: "この記事では、既存または新規のアプリケーション ゲートウェイで Web アプリケーション ファイアウォールの使用を開始する方法について説明します。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.assetid: 670b9732-874b-43e6-843b-d2585c160982
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/03/2017
ms.author: davidmu
ms.openlocfilehash: e8106805d21b325e33fb3ab376db75cd783b9042
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-web-application-firewall-on-a-new-or-existing-application-gateway"></a>新規または既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを構成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-web-application-firewall-portal.md)
> * [PowerShell](application-gateway-web-application-firewall-powershell.md)
> * [Azure CLI](application-gateway-web-application-firewall-cli.md)

Web アプリケーション ファイアウォール (WAF) 対応のアプリケーション ゲートウェイを作成する方法について説明します。 既存のアプリケーション ゲートウェイに WAF を追加する方法についても説明します。

Azure Application Gateway の WAF は、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの一般的な Web ベースの攻撃から Web アプリケーションを保護します。

 Application Gateway はレイヤー 7 のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。 Application Gateway は、多数のアプリケーション配信コントローラー (ADC) 機能を備えています。

 * HTTP の負荷分散
 * Cookie ベースのセッション アフィニティ
 * Secure Sockets Layer (SSL) のオフロード
 * カスタム正常性プローブ
 * マルチサイト機能のサポート
 
 サポートされている機能の完全な一覧については、「[Application Gateway の概要](application-gateway-introduction.md)」を参照してください。

この記事では、[既存のアプリケーション ゲートウェイに WAF を追加する](#add-web-application-firewall-to-an-existing-application-gateway)方法について説明します。 [WAF を使用するアプリケーション ゲートウェイを作成する](#create-an-application-gateway-with-web-application-firewall)方法についても示します。

![シナリオのイメージ][scenario]

## <a name="waf-configuration-differences"></a>WAF の構成上の相違点

[PowerShell を使用したアプリケーション ゲートウェイの作成](application-gateway-create-gateway-arm.md)に関する記事をお読みであれば、アプリケーション ゲートウェイを作成するときに構成する SKU の設定はご存じのとおりです。 WAF には、アプリケーション ゲートウェイで SKU を構成するときに定義する追加の設定があります。 アプリケーション ゲートウェイ自体に加える変更はありません。

| **設定** | **詳細**
|---|---|
|**SKU** |WAF が追加されていない通常のアプリケーション ゲートウェイでは、**Standard\_Small**、**Standard\_Medium**、および **Standard\_Large** の各サイズがサポートされています。 WAF を導入すると、2 つの SKU (**WAF\_Medium** と **WAF\_Large**) が追加されます。 小規模なアプリケーション ゲートウェイでは、WAF はサポートされません。|
|**レベル** | 使用できる値は **Standard** または **WAF** です。 WAF を使用するときは、**[WAF]** を選択する必要があります。|
|**モード** | この設定は WAF のモードです。 使用できる値は **[検出]** と **[防止]** です。 WAF を **[検出]** モードで設定すると、すべての脅威がログ ファイルに記録されます。 **[防止]** モードでも、イベントがログに記録されますが、攻撃者はアプリケーション ゲートウェイから "403 許可されていません" 応答を受信します。|

## <a name="add-a-web-application-firewall-to-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイに Web アプリケーション ファイアウォールを追加する

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、[ Windows PowerShell の Resource Manager での使用](../powershell-azure-resource-manager.md)に関する記事を参照してください。

1. Azure アカウントにサインインします。

    ```powershell
    Login-AzureRmAccount
    ```

2. このシナリオで使用するサブスクリプションを選択します。

    ```powershell
    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"
    ```

3. WAF を追加するゲートウェイを取得します。

    ```powershell
    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"
    ```

4. WAF SKU を構成します。 利用可能なサイズは、**WAF\_Large** と **WAF\_Medium** です。 WAF を使用するときは、階層が **WAF** である必要があります。 SKU を設定するときに容量を確認します。

    ```powershell
    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2
    ```

5. 次の例で定義しているように、WAF の設定を構成します。 **FirewallMode** の設定に利用できる値は、**Prevention** と **Detection**です。

    ```powershell
    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention
    ```

6. 前の手順で定義した設定でアプリケーション ゲートウェイを更新します。

    ```powershell
    Set-AzureRmApplicationGateway -ApplicationGateway $gw
    ```

このコマンドは、アプリケーション ゲートウェイを WAF で更新します。 アプリケーション ゲートウェイのログの表示方法を理解するには、[Application Gateway の診断](application-gateway-diagnostics.md)に関する記事を参照してください。 WAF のセキュリティの性質により、ログを定期的に確認して Web アプリケーションのセキュリティ状況を把握してください。

## <a name="create-an-application-gateway-with-a-web-application-firewall"></a>Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する

次の手順では、WAF があるアプリケーション ゲートウェイを作成するプロセスを最初から最後まで実行します。

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、[ Windows PowerShell の Resource Manager での使用](../powershell-azure-resource-manager.md)に関する記事を参照してください。

1. `Login-AzureRmAccount` を実行して Azure にサインインします。 資格情報を使用して認証を行うように求めるメッセージが表示されます。

2. `Get-AzureRmSubscription` を実行して、アカウントのサブスクリプションを確認します。

3. 使用する Azure サブスクリプションを選択します。

    ```powershell
    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
    ```

### <a name="create-a-resource-group"></a>リソース グループの作成

アプリケーション ゲートウェイのリソース グループを作成します。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 この場所は、そのリソース グループ内のリソースの既定の保存先として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

前の例では、"appgw-RG" という名前のリソース グループを "West US" という場所に作成しました。

> [!NOTE]
> アプリケーション ゲートウェイのカスタム プローブを構成する必要がある場合は、 [PowerShell を使用したカスタム プローブとアプリケーション ゲートウェイの作成](application-gateway-create-probe-ps.md)に関するページを参照してください。 詳細については、[カスタム プローブと正常性の監視](application-gateway-probe-overview.md)に関するページを参照してください。

### <a name="configure-a-virtual-network"></a>仮想ネットワークを構成する

アプリケーション ゲートウェイには、専用のサブネットが必要です。 この手順では、10.0.0.0/16 のアドレス空間と 2 つのサブネット (1 つはアプリケーション ゲートウェイ用、1 つはバックエンド プール メンバー用) を持つ仮想ネットワークを作成します。

```powershell
# Create a subnet configuration object for the application gateway subnet. A subnet for an application should have a minimum of 28 mask bits. This value leaves 10 available addresses in the subnet for application gateway instances. With a smaller subnet, you might not be able to add more instances of your application gateway in the future.
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

# Create a subnet configuration object for the back-end pool members subnet.
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

# Create the virtual network with the previously created subnets.
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="configure-the-public-ip-address"></a>パブリック IP アドレスを構成する

外部からの要求を処理するには、アプリケーション ゲートウェイはパブリック IP アドレスが必要です。 このパブリック IP アドレスには、アプリケーション ゲートウェイで使用するように定義された `DomainNameLabel` は必要ありません。

```powershell
# Create a public IP address for use with the application gateway. Defining the `DomainNameLabel` during creation is not supported for use with the application gateway.
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic
```

### <a name="configure-the-application-gateway"></a>Application Gateway の構成

```powershell
# Create an IP configuration to configure which subnet the application gateway uses. When the application gateway starts, it picks up an IP address from the configured subnet and routes network traffic to the IP addresses in the back-end IP pool.
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

# Create a back-end pool to hold the addresses or NIC handles for the application that the application gateway is protecting.
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

# Upload the authentication certificate to be used to communicate with the back-end servers.
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

# Configure the back-end HTTP settings to be used to define how traffic is routed to the back-end pool. The authentication certificate used in the previous step is added to the back-end HTTP settings.
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

# Create a front-end port to be used by the listener.
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

# Create a front-end IP configuration to associate the public IP address with the application gateway.
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

# Configure the certificate for the application gateway. This certificate is used to decrypt and re-encrypt the traffic on the application gateway.
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

# Create the HTTP listener for the application gateway. Assign the front-end IP configuration, port, and SSL certificate to use.
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

# Create a load-balancer routing rule that configures the load balancer behavior. In this example, a basic round-robin rule is created.
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

# Configure the SKU of the application gateway.
$sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

# Define the SSL policy to use.
$policy = New-AzureRmApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName AppGwSslPolicy20170401S

# Configure the WAF configuration settings.
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"

# Create the application gateway by using all the previously created configuration objects.
$appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert
```

> [!NOTE]
> 基本的な WAF の構成で作成されたアプリケーション ゲートウェイは、CRS 3.0 の保護で構成されます。

## <a name="get-an-application-gateway-dns-name"></a>アプリケーション ゲートウェイの DNS 名を取得する

ゲートウェイを作成した後の次の手順は通信用にフロントエンドを構成することです。 パブリック IP を使用する場合、アプリケーション ゲートウェイには、動的に割り当てられたフレンドリではない DNS 名が必要です。 エンド ユーザーがアプリケーション ゲートウェイを確実にヒットできるように、CNAME レコードを使用して、アプリケーション ゲートウェイのパブリック エンドポイントを参照します。 詳細については、「[Azure クラウド サービスのカスタム ドメイン名の構成](../cloud-services/cloud-services-custom-domain-name-portal.md)」を参照してください。 

別名を構成するには、アプリケーション ゲートウェイに接続されている PublicIPAddress 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。 アプリケーション ゲートウェイの DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成します。 VIP はアプリケーション ゲートウェイが再起動するときに変更されることがあるため、A レコードの使用はお勧めしません。

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

WAF で検出または防止されたイベントをログに記録するための診断ログを構成するには、[Application Gateway の診断](application-gateway-diagnostics.md)に関する記事を参照してください。

[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png
