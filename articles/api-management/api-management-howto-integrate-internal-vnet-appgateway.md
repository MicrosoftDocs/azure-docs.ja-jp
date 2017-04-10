---
title: "仮想ネットワークで Application Gateway と Azure API Management を使用する方法 | Microsoft Docs"
description: "内部仮想ネットワークで Application Gateway (WAF) をフロントエンドとして使用して Azure API Management をセットアップおよび構成する方法について説明します"
services: api-management
documentationcenter: 
author: solankisamir
manager: kjoshi
editor: antonba
ms.assetid: a8c982b2-bca5-4312-9367-4a0bbc1082b1
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2017
ms.author: sasolank
translationtype: Human Translation
ms.sourcegitcommit: 503f5151047870aaf87e9bb7ebf2c7e4afa27b83
ms.openlocfilehash: 46210c7bc3158c27cda40fb85ffef16820dcbdef
ms.lasthandoff: 03/29/2017


---
# <a name="integrate-api-management-in-an-internal-vnet-with-application-gateway"></a>内部 VNET 内の API Management と Application Gateway の統合 

##<a name="overview"> </a> 概要
 
仮想ネットワークでは、API Management サービスを仮想ネットワーク内からのみアクセスできるようにする内部モードに構成できます。 Azure Application Gateway は、レイヤー 7 のロード バランサーを提供する PaaS サービスです。 リバースプロキシ サービスとしての役目を果たすものであり、その機能の 1 つとして Web アプリケーション ファイアウォール (WAF) を備えています。

内部 VNET にプロビジョニング済みの API Management と Application Gateway フロントエンドを組み合わせることにより、次のシナリオが実現されます。

* 内部コンシューマーと外部コンシューマーの両方における消費用に同一の API Management リソースを使用する。
* 単一の API Management リソースを使用しながら、API Management 内で定義した API の一部を外部コンシューマーが利用できるようにする。
* ターンキーを使用して、パブリック インターネットから API Management へのアクセスのオン/オフを切り替える。 

##<a name="scenario"> </a> シナリオ
この記事では、内部コンシューマーと外部コンシューマーの両方で単一の API Management サービスを使用し、オンプレミスとクラウド双方の API の単一フロントエンドとして機能させる方法について説明します。 さらに、Application Gateway の PathBasedRouting 機能を使用して、これらの API のうち外部消費用に公開するものを一部のみ (例の中で緑色で記載しています) に制限する方法について説明します。

最初のセットアップ例では、すべての API の管理が仮想ネットワーク内から行われます。 内部コンシューマー (オレンジ色で記載しています) は、内部 API と外部 API のすべてにアクセスできます。 トラフィックがインターネット上に出ていくことはなく、ExpressRoute 回線を介して高いパフォーマンスが実現します。

![url ルート](./media/api-management-howto-integrate-internal-vnet-appgateway/api-management-howto-integrate-internal-vnet-appgateway.png)

## <a name="before-you-begin"> </a> 開始する前に

1. Web Platform Installer を使用して、Azure PowerShell コマンドレットの最新バージョンをインストールします。 **ダウンロード ページ** の [Windows PowerShell](https://azure.microsoft.com/downloads/)セクションから最新バージョンをダウンロードしてインストールできます。
2. 仮想ネットワークを作成し、API Management と Application Gateway 用に別々のサブネットを作成します。 
3. 仮想ネットワーク用にカスタム DNS サーバーを作成する場合は、デプロイを開始する前にその作業を行ってください。 仮想ネットワーク内の新しいサブネットに作成された仮想マシンによってすべての Azure サービス エンドポイントを解決してアクセスできることを確認し、それが動作することを再確認してください。

## <a name="what-is-required-to-create-an-integration-between-api-management-and-application-gateway"></a>API Management と Application Gateway の統合作成に必要なもの

* **バックエンド サーバー プール:** これは、API Management サービスの内部仮想 IP アドレスです。
* **バックエンド サーバー プールの設定:** すべてのプールには、ポート、プロトコル、Cookie ベースのアフィニティなどの設定があります。 これらの設定は、プール内のすべてのサーバーに適用されます。
* **フロントエンド ポート:** これは、Application Gateway で開かれたパブリック ポートです。 このポートにヒットしたトラフィックは、バックエンド サーバーのいずれかにリダイレクトされます。
* **リスナー:** リスナーには、フロントエンド ポート、プロトコル (Http または Https で、値には大文字小文字の区別あり)、SSL 証明書名 (オフロードの SSL を構成する場合) があります。
* **ルール:** このルールによって、リスナーがバックエンド サーバー プールにバインドされます。
* **カスタムの正常性プローブ:** 既定では、Application Gateway は、IP アドレス ベースのプローブを使用して、BackendAddressPool 内でアクティブなサーバーを見つけます。 API Management サービスは適切なホスト ヘッダーを持つ要求だけに応答するため、既定のプローブでは失敗します。 サービスが有効であり要求を転送する必要があることを Application Gateway が判定できるようにするには、カスタムの正常性プローブを定義する必要があります。
* **カスタムのドメイン証明書:** インターネットから API Management にアクセスするには、ホスト名と Application Gateway のフロントエンド DNS 名との間の CNAME マッピングを作成する必要があります。 これにより、API Management に転送される、Application Gateway に送信されたホスト名のヘッダーと証明書を、APIM が有効であると識別できるようになります。

## <a name="overview-steps"> </a> API Management と Application Gateway を統合するために必要な手順 

1. リソース マネージャーのリソース グループを作成します。
2. Application Gateway の仮想ネットワーク、サブネット、およびパブリック IP を作成します。 API Management 用のサブネットを別途作成します。
3. 上記で作成した VNET サブネットの内部に API Management サービスを作成し、内部モードになっていることを確認します。
4. API Management サービスのカスタム ドメイン名を設定します。
5. Application Gateway 構成オブジェクトを作成します。
6. Application Gateway のリソースを作成します。
7. Application Gateway のパブリック DNS 名から API Management プロキシのホスト名への CNAME を作成します。

## <a name="create-a-resource-group-for-resource-manager"></a>リソース マネージャーのリソース グループの作成

Azure PowerShell の最新バージョンを使用していることを確認します。 詳細については、 [Resource Manager での Windows PowerShell の使用](../powershell-azure-resource-manager.md)に関するページを参照してください。

### <a name="step-1"></a>手順 1

Azure へのログイン

```powershell
Login-AzureRmAccount
```

資格情報を使用して認証を行います。<BR>

### <a name="step-2"></a>手順 2.

アカウントのサブスクリプションを確認して選択します。

```powershell
Get-AzureRmSubscription -Subscriptionid "GUID of subscription" | Select-AzureRmSubscription
```

### <a name="step-3"></a>手順 3.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
New-AzureRmResourceGroup -Name "apim-appGw-RG" -Location "West US"
```
Azure リソース マネージャーでは、すべてのリソース グループの場所を指定する必要があります。 指定した場所は、そのリソース グループ内のリソースの既定の場所として使用されます。 アプリケーション ゲートウェイを作成するためのすべてのコマンドで、同じリソース グループが使用されていることを確認します。

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Application Gateway の仮想ネットワークとサブネットを作成する

次の例では、リソース マネージャーを使用して仮想ネットワークを作成する方法を示します。

### <a name="step-1"></a>手順 1

仮想ネットワークの作成時に Application Gateway 用に使用するサブネット変数に、アドレス範囲 10.0.0.0/24 を割り当てます。

```powershell
$appgatewaysubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim01" -AddressPrefix "10.0.0.0/24"
```

### <a name="step-2"></a>手順 2.

仮想ネットワークの作成時に API Management 用に使用するサブネット変数に、アドレス範囲 10.0.1.0/24 を割り当てます。

```powershell
$apimsubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "apim02" -AddressPrefix "10.0.1.0/24"
```

### <a name="step-3"></a>手順 3.

サブネット 10.0.0.0/24 および 10.0.1.0/24 とプレフィックス 10.0.0.0/16 を使用して、米国西部リージョンのリソース グループ **apim-appGw-RG** に **appgwvnet** という名前の仮想ネットワークを作成します。

```powershell
$vnet = New-AzureRmVirtualNetwork -Name "appgwvnet" -ResourceGroupName "apim-appGw-RG" -Location "West US" -AddressPrefix "10.0.0.0/16" -Subnet $appgatewaysubnet,$apimsubnet
```

### <a name="step-4"></a>手順 4.

次の手順のためにサブネット変数を割り当てます。

```powershell
$appgatewaysubnetdata=$vnet.Subnets[0]
$apimsubnetdata=$vnet.Subnets[1]
```
## <a name="create-an-api-management-service-inside-a-vnet-configured-in-internal-mode"></a>内部モードで構成された VNET 内に API Management サービスを作成する

次の例は、内部アクセス専用に構成された VNET に API Management サービスを作成する方法を示しています。

### <a name="step-1"></a>手順 1
上記の手順で作成したサブネット $apimsubnetdata を使用して、API Management の仮想ネットワーク オブジェクトを作成します。

```powershell
$apimVirtualNetwork = New-AzureRmApiManagementVirtualNetwork -Location "West US" -SubnetResourceId $apimsubnetdata.Id
```
### <a name="step-2"></a>手順 2.
仮想ネットワーク内に API Management サービスを作成します。

```powershell
$apimService = New-AzureRmApiManagement -ResourceGroupName "apim-appGw-RG" -Location "West US" -Name "ContosoApi" -Organization "Contoso" -AdminEmail "admin@contoso.com" -VirtualNetwork $apimVirtualNetwork -VpnType "Internal" -Sku "Premium"
```
上記のコマンドが成功したら、[内部 VNET の API Management サービスへのアクセスに必要な DNS 構成](api-management-using-with-internal-vnet.md#apim-dns-configuration)に関するセクションを参照してアクセスします。

## <a name="set-up-a-custom-domain-name-in-api-management"></a>API Management でカスタム ドメイン名をセットアップする

### <a name="step-1"></a>手順 1
ドメインの秘密キーを使用して証明書をアップロードします。 この例では、`*.contoso.net` となります。 

```powershell
$certUploadResult = Import-AzureRmApiManagementHostnameCertificate -ResourceGroupName "apim-appGw-RG" -Name "ContosoApi" -HostnameType "Proxy" -PfxPath <full path to .pfx file> -PfxPassword <password for certificate file> -PassThru
```

### <a name="step-2"></a>手順 2.
この例の証明書では、`*.contoso.net` ドメインの権限が提供されるため、証明書がアップロードされたら、ホスト名 `api.contoso.net` を指定してプロキシのホスト名構成オブジェクトを作成します。 

```powershell
$proxyHostnameConfig = New-AzureRmApiManagementHostnameConfiguration -CertificateThumbprint $certUploadResult.Thumbprint -Hostname "api.contoso.net"
$result = Set-AzureRmApiManagementHostnames -Name "ContosoApi" -ResourceGroupName "apim-appGw-RG" -ProxyHostnameConfiguration $proxyHostnameConfig
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

米国西部リージョンのリソース グループ **apim-appGw-RGrg** に、パブリック IP リソース **publicIP01** を作成します。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -name "publicIP01" -location "West US" -AllocationMethod Dynamic
```

サービスが開始すると、Application Gateway に IP アドレスが割り当てられます。

## <a name="create-application-gateway-configuration"></a>Application Gateway 構成の作成

Application Gateway を作成するには、すべての構成項目を設定する必要があります。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### <a name="step-1"></a>手順 1

**gatewayIP01** という名前のアプリケーション ゲートウェイの IP 構成を作成します。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name "gatewayIP01" -Subnet $appgatewaysubnetdata
```

### <a name="step-2"></a>手順 2.

パブリック IP エンドポイントのフロントエンド IP ポートを構成します。 このポートは、エンドユーザーが接続するポートです。

```powershell
$fp01 = New-AzureRmApplicationGatewayFrontendPort -Name "port01"  -Port 443
```
### <a name="step-3"></a>手順 3.

パブリック IP エンドポイントでフロント エンド IP を構成します。

```powershell
$fipconfig01 = New-AzureRmApplicationGatewayFrontendIPConfig -Name "frontend1" -PublicIPAddress $publicip
```

### <a name="step-4"></a>手順 4.

通過するトラフィックの暗号化解除と再暗号化に使用される、Application Gateway の証明書を構成します。

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name "cert01" -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

### <a name="step-5"></a>手順 5.

Application Gateway の HTTP リスナーを作成します。 そのリスナーにフロントエンド IP 構成、ポート、および SSL 証明書を割り当てます。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name "listener01" -Protocol "Https" -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $cert
```

### <a name="step-6"></a>手順 6.

API Management サービスの `ContosoApi` プロキシのドメイン エンドポイントに対するカスタム プローブを作成します。 パス `/status-0123456789abcdef` は、すべての API Management サービスでホストされている既定の正常性エンドポイントです。 `api.contoso.net` をカスタム プローブのホスト名として設定し、SSL 証明書でセキュリティ保護します。

> [!NOTE]
> ホスト名 `contosoapi.azure-api.net` は、`contosoapi` という名前のサービスがパブリック Azure に作成されたときに構成された既定のプロキシ ホスト名です。 
> 

```powershell
$apimprobe = New-AzureRmApplicationGatewayProbeConfig -Name "apimproxyprobe" -Protocol "Https" -HostName "api.contoso.net" -Path "/status-0123456789abcdef" -Interval 30 -Timeout 120 -UnhealthyThreshold 8
```

### <a name="step-7"></a>手順 7.

SSL 対応バックエンド プール リソースで使用する証明書をアップロードします。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name "whitelistcert1" -CertificateFile <full path to .cer file>
```

### <a name="step-8"></a>手順 8.

Application Gateway の HTTP バックエンド設定を構成します。 これには、バックエンド要求が取り消されるまでのタイムアウト制限の設定が含まれます。 この値はプローブのタイムアウトとは異なります。

```powershell
$apimPoolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name "apimPoolSetting" -Port 443 -Protocol "Https" -CookieBasedAffinity "Disabled" -Probe $apimprobe -AuthenticationCertificates $authcert -RequestTimeout 180
```

### <a name="step-9"></a>手順 9.

**apimbackend** という名前のバックエンド IP アドレス プールを、上記で作成した API Management サービスの内部仮想 IP アドレスを指定して構成します。

```powershell
$apimProxyBackendPool = New-AzureRmApplicationGatewayBackendAddressPool -Name "apimbackend" -BackendIPAddresses $apimService.StaticIPs[0]
```

### <a name="step-10"></a>手順 10.
バックエンド プールの URL ルール パスを構成します。 これにより、API Management の一部の API のみを公開対象として選択することができます  (たとえば、`Echo API` (/echo/)、`Calculator API` (/calc/) などがある場合に `Echo API` のみをインターネットからアクセスできるようにします)。 

次の例では、"/echo/" パスに対し、トラフィックを "apimProxyBackendPool" バックエンドにルーティングする単純なルールを作成します。

```powershell
$echoapiRule = New-AzureRmApplicationGatewayPathRuleConfig -Name "externalapis" -Paths "/echo/*" -BackendAddressPool $apimProxyBackendPool -BackendHttpSettings $apimPoolSetting

$urlPathMap = New-AzureRmApplicationGatewayUrlPathMapConfig -Name "urlpathmap" -PathRules $echoapiRule -DefaultBackendAddressPool $apimProxyBackendPool -DefaultBackendHttpSettings $apimPoolSetting
```

上記の手順により、Application Gateway によりパス "/echo" に対する要求のみが許可されるようになります。 インターネットからのアクセス時には、API Management で構成済みの別の API に要求を送信すると Application Gateway から 404 エラーが返されます。 

### <a name="step-11"></a>手順 11.

URL パスベースのルーティングを使用するように Application Gateway のルール設定を作成します。

```powershell
$rule01 = New-AzureRmApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType PathBasedRouting -HttpListener $listener -UrlPathMap $urlPathMap
```

### <a name="step-12"></a>手順 12.

Application Gateway のインスタンス数とサイズを構成します。 ここでは、API Management リソースのセキュリティを強化するために、[WAF SKU](../application-gateway/application-gateway-webapplicationfirewall-overview.md) を使用しています。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name "WAF_Medium" -Tier "WAF" -Capacity 2
```

### <a name="step-13"></a>手順 13.

WAF を "Prevention" モードに構成します。
```powershell
$config = New-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode "Prevention"
```

## <a name="create-application-gateway"></a>Application Gateway の作成

前述の手順の構成オブジェクトをすべて使用して、Application Gateway を作成します。

```powershell
$appgw = New-AzureRmApplicationGateway -Name "appgwtest" -ResourceGroupName "apim-appGw-RG" -Location "West US" -BackendAddressPools $apimProxyBackendPool -BackendHttpSettingsCollection $apimPoolSetting -FrontendIpConfigurations $fipconfig01 -GatewayIpConfigurations $gipconfig -FrontendPorts $fp01 -HttpListeners $listener -UrlPathMaps $urlPathMap -RequestRoutingRules $rule01 -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert -Probes $apimprobe
```

## <a name="cname-the-api-management-proxy-hostname-to-the-public-dns-name-of-the-application-gateway-resource"></a>API Management プロキシのホスト名から Application Gateway リソースのパブリック DNS 名への CNAME を作成する

ゲートウェイを作成したら、次は通信用にフロントエンドを構成します。 パブリック IP を使用する場合、Application Gateway は動的に割り当てられる DNS 名を必要としますが、これを使用するのは必ずしも簡単ではありません。 

Application Gateway の DNS 名を使用して、この DNS 名に対して構成した APIM プロキシ ホスト名 (上の例の `api.contoso.net`) を指す CNAME レコードを作成する必要があります。 フロントエンド IP CNAME レコードを構成するには、PublicIPAddress 要素を使用して、Application Gateway の詳細とそれに関連付けられている IP/DNS 名を取得します。 ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName "apim-appGw-RG" -Name "publicIP01"
```

##<a name="summary"> </a> まとめ
VNET で構成された Azure API Management は、ホスト先がオンプレミスかクラウドかにかかわらず、すべての構成済みの API に単一のゲートウェイ インターフェイスを提供します。 Application Gateway を API Management と統合すると、インターネット上で特定の API に選択的にアクセスできる柔軟性が提供されるほか、API Management インスタンスのフロントエンドとして Web アプリケーション ファイアウォールを利用できるようになります。

##<a name="next-steps"> </a> 次のステップ
* Azure Application Gateway の詳細を確認する
  * [Application Gateway の概要](../application-gateway/application-gateway-introduction.md)
  * [Application Gateway の Web アプリケーション ファイアウォール](../application-gateway/application-gateway-webapplicationfirewall-overview.md)
* API Management と VNET の詳細を確認する
  * [VNET での API Management の使用](api-management-using-with-vnet.md)



