---
title: "Application Gateway の SSL ポリシーとエンド ツー エンド SSL の構成 | Microsoft Docs"
description: "この記事では、Azure Resource Manager PowerShell を使用して Application Gateway でエンド ツー エンド SSL を構成する方法を説明します。"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: tysonn
ms.assetid: e6d80a33-4047-4538-8c83-e88876c8834e
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/14/2016
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 09aeb63d4c2e68f22ec02f8c08f5a30c32d879dc
ms.openlocfilehash: c76dc14998ebf01a938c67d6c78384e169f83266


---
# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>PowerShell を使用した Application Gateway の SSL ポリシーとエンド ツー エンド SSL の構成

## <a name="overview"></a>Overview

Application Gateway は、トラフィックのエンド ツー エンド暗号化をサポートしています。 これは、Application Gateway で SSL 接続を終了することによってサポートされます。 ゲートウェイでは、その後、トラフィックへのルーティング規則の適用、パケットの再暗号化、定義済みのルーティング規則に基づいた適切なバックエンドへのパケットの転送が実行されます。 Web サーバーからの応答は、同じ手順でエンドユーザーに移動します。

アプリケーション ゲートウェイがサポートする他の機能として、特定の SSL プロトコル バージョンの無効化があります。 Application Gateway で無効化がサポートされているプロトコル バージョンは、**TLSv1.0**、**TLSv1.1**、および **TLSv1.2** です。

> [!NOTE]
> SSL 2.0 と SSL 3.0 は既定で無効になっており、有効にすることはできません。 これらはセキュリティで保護されておらず、Application Gateway では使用できないと見なされています。

![シナリオのイメージ][scenario]

## <a name="scenario"></a>シナリオ

このシナリオでは、PowerShell で、エンド ツー エンド SSL を使用してアプリケーション ゲートウェイを作成する方法について説明します。

このシナリオで、以下の作業を行います。

* **appgw-rg** という名前のリソース グループを作成します。
* 予約済み CIDR ブロック 10.0.0.0/16 を持つ、**appgwvnet** という名前の仮想ネットワークを作成します。
* **appgwsubnet** と **appsubnet** という名前の 2 つのサブネットを作成します。
* エンド ツー エンド SSL 暗号化をサポートし、特定の SSL プロトコルを無効に小さなアプリケーション ゲートウェイを作成します。

## <a name="before-you-begin"></a>開始する前に

アプリケーション ゲートウェイでエンド ツー エンド SSL を構成するには、ゲートウェイ用とバックエンド サーバー用の証明書が必要です。 ゲートウェイ証明書は、SSL を使用してゲートウェイに送信されるトラフィックの暗号化と暗号化解除に使用されます。 ゲートウェイ証明書は、Personal Information Exchange (pfx) 形式である必要があります。 このファイル形式により、秘密キーをエクスポートすることができます。このキーは、アプリケーション ゲートウェイがトラフィックの暗号化および暗号化解除を実行する際に必要です。

エンド ツー エンド SSL 暗号化では、アプリケーション ゲートウェイにバックエンドをホワイトリスト登録する必要があります。 許可リストに登録するには、バックエンドの公開証明書をアプリケーション ゲートウェイにアップロードします。 これにより、アプリケーション ゲートウェイは、既知のバックエンド インスタンスのみと通信するため、 エンド ツー エンド通信のセキュリティが強化されます。

このプロセスについては以降の手順で説明します。

## <a name="create-the-resource-group"></a>リソース グループの作成

このセクションでは、アプリケーション ゲートウェイを含むリソース グループの作成手順について説明します。

### <a name="step-1"></a>手順 1

Azure アカウントにログインします。

```powershell
Login-AzureRmAccount
```

### <a name="step-2"></a>手順 2.

このシナリオで使用するサブスクリプションを選択します。

```powershell
Select-AzureRmsubscription -SubscriptionName "<Subscription name>"
```

### <a name="step-3"></a>手順 3.

リソース グループを作成します (既存のリソース グループを使用する場合は、この手順をスキップしてください)。

```powershell
New-AzureRmResourceGroup -Name appgw-rg -Location "West US"
```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

次の例では、仮想ネットワークと 2 つのサブネットを作成します。 1 つはアプリケーション ゲートウェイを保持するために使用します。 もう 1 つのサブネットは、Web アプリケーションをホストするバックエンドに使用します。

### <a name="step-1"></a>手順 1

Application Gateway 自体に使用するサブネットのアドレス範囲を割り当てます。

```powershell
$gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
```

> [!NOTE]
> アプリケーション ゲートウェイ用に構成されているサブネットのサイズを適切に設定する必要があります。 アプリケーション ゲートウェイは、最大 10 個のインスタンスに対して構成できます。 各インスタンスは、サブネットから IP アドレスを 1 つ取得します。 サブネットのサイズが小さすぎると、アプリケーション ゲートウェイのスケールアウトに悪影響を及ぼす可能性があります。
> 
> 

### <a name="step-2"></a>手順 2.

バックエンド アドレス プールに使用するアドレス範囲を割り当てます。

```powershell
$nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
```

### <a name="step-3"></a>手順 3.

上記の手順で定義されたサブネットを含む仮想ネットワークを作成します。

```powershell
$vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
```

### <a name="step-4"></a>手順 4.

次の手順で使用する仮想ネットワーク リソースとサブネット リソースを取得します。

```powershell
$vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
$gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
$nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

アプリケーション ゲートウェイに使用するパブリック IP リソースを作成します。 このパブリック IP アドレスは、次の手順で使用します。

```powershell
$publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway では、ドメイン ラベルを定義して作成したパブリック IP アドレスを使用できません。 動的に作成されるドメイン ラベルを持つパブリック IP アドレスのみがサポートされています。 アプリケーション ゲートウェイにわかりやすい DNS 名を付ける必要がある場合は、CNAME レコードをエイリアスとして使用することをお勧めします。

## <a name="create-an-application-gateway-configuration-object"></a>Application Gateway 構成オブジェクトの作成

アプリケーション ゲートウェイを作成する前に、すべての構成項目を設定する必要があります。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

### <a name="step-1"></a>手順 1

アプリケーション ゲートウェイの IP 構成を作成します。この設定によって、アプリケーション ゲートウェイで使用されるサブネットが構成されます。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得されて、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

```powershell
$gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
```

### <a name="step-2"></a>手順 2.

フロントエンドの IP 構成を作成します。この設定によって、アプリケーション ゲートウェイのフロントエンドにプライベート IP アドレスまたはパブリック IP アドレスがマッピングされます。 次の手順で、フロントエンドの IP 構成と前の手順のパブリック IP アドレスを関連付けます。

```powershell
$fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
```

### <a name="step-3"></a>手順 3.

バックエンド Web サーバーの IP アドレスを使用してバックエンド IP アドレス プールを構成します。 これらの IP アドレスは、フロントエンド IP エンドポイントから送信されるネットワーク トラフィックを受信する IP アドレスです。 独自のアプリケーションの IP アドレス エンドポイントを追加するには、次の IP アドレスを置き換えます。

```powershell
$pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
```

> [!NOTE]
> バックエンド サーバーの IP アドレスの代わりに、-BackendFqdns スイッチを使用した完全修飾ドメイン名 (FQDN) も有効な値です。 

### <a name="step-4"></a>手順 4.

パブリック IP エンドポイントのフロントエンド IP ポートを構成します。 このポートは、エンドユーザーが接続するポートです。

```powershell
$fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443
```

### <a name="step-5"></a>手順 5.

アプリケーション ゲートウェイの証明書を構成します。 この証明書は、アプリケーション ゲートウェイでのトラフィックの暗号化解除と再暗号化に使用されます。

```powershell
$cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>
```

> [!NOTE]
> このサンプルでは、SSL 接続に使用する証明書を構成します。 証明書は .pfx 形式であり、4 ～ 12 文字のパスワードを使用する必要があります。

### <a name="step-6"></a>手順 6.

アプリケーション ゲートウェイの HTTP リスナーを作成します。 使用するフロントエンド IP 構成、ポート、および SSL 証明書を割り当てます。

```powershell
$listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert
```

### <a name="step-7"></a>手順 7.

SSL 対応バックエンド プール リソースで使用する証明書をアップロードします。

> [!NOTE]
> 既定のプローブは、バックエンドの IP アドレスでの**既定の** SSL バインドから公開キーを取得し、取得した公開キー値をここで指定した公開キー値と比較します。 バックエンドでホスト ヘッダーと SNI を使用している**場合**、取得した公開キーがトラフィックの送信先となる目的のサイトであるとは限りません。 確かでない場合は、バックエンドで https://127.0.0.1/ にアクセスして、**既定の** SSL バインドにどの証明書が使用されているかを確認します。 このセクションでその要求の公開キーを使用します。 HTTPS バインドでホスト ヘッダーと SNI を使用しており、バックエンドでの https://127.0.0.1/ に対する手動のブラウザー要求から応答と証明書を受信していない場合は、バックエンドで既定の SSL バインドを設定する必要があります。 これを行わないと、プローブは失敗し、バックエンドは許可リストに登録されなくなります。

```powershell
$authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer
```

> [!NOTE]
> この手順で指定する証明書は、バックエンドに存在する pfx 証明書の公開キーである必要があります。 バックエンド サーバーにインストールされている (ルート証明書ではない) 証明書を .CER 形式でエクスポートし、この手順で使用します。 この手順によって、アプリケーション ゲートウェイにバックエンドがホワイトリスト登録されます。

### <a name="step-8"></a>手順 8.

アプリケーション ゲートウェイのバックエンドの http 設定を構成します。 前の手順でアップロードした証明書を http 設定に割り当てます。

```powershell
$poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
```

### <a name="step-9"></a>手順 9.

ロード バランサーの動作を構成するロード バランサーのルーティング規則を作成します。 この例では、ラウンド ロビンの基本的な規則を作成します。

```powershell
$rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

### <a name="step-10"></a>手順 10.

アプリケーション ゲートウェイのインスタンスのサイズを構成します。  利用可能なサイズは、**Standard\_Small**、**Standard\_Medium**、および **Standard\_Large** です。  容量に使用可能な値は 1 ～ 10 です。

```powershell
$sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
```

> [!NOTE]
> テスト目的では、インスタンス数として 1 を選択できます。 重要なのは、2 より小さいインスタンス数は SLA の対象外のため、推奨されていないことを把握しておくことです。 小規模のゲートウェイは開発テスト用であり、運用目的ではありません。

### <a name="step-11"></a>手順 11.

Application Gateway で使用する SSL ポリシーを構成します。 Application Gateway では、SSL プロトコルの特定バージョンを無効にする機能がサポートされています。

次の値が、無効にできるプロトコル バージョンの一覧です。

* **TLSv1_0**
* **TLSv1_1**
* **TLSv1_2**

次の例では、**TLSv1\_0** を無効にします。

```powershell
$sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

これまでのすべての手順に従って、Application Gateway を作成します。 ゲートウェイの作成には、長い時間がかかります。

```powershell
$appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose
```

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>既存の Application Gateway で SSL プロトコル バージョンを無効する

これまでの手順で、エンド ツー エンド SSL を使用したアプリケーションの作成と、SSL プロトコルの特定バージョンの無効化について説明しました。 次の例では、既存のアプリケーション ゲートウェイの特定の SSL ポリシーを無効にします。

### <a name="step-1"></a>手順 1

更新するアプリケーション ゲートウェイを取得します。

```powershell
$gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
```

### <a name="step-2"></a>手順 2.

SSL ポリシーを定義します。 次の例では、TLSv1.0 と TLSv1.1 が無効になります。

```powershell
Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw
```

### <a name="step-3"></a>手順 3.

最後に、ゲートウェイを更新します。 この最後の手順は実行時間が長くかかるため、注意してください。 終了すると、エンド ツー エンド SSL がアプリケーション ゲートウェイに構成されます。

```powershell
$gw | Set-AzureRmApplicationGateway
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

Application Gateway を使用して Web アプリケーション ファイアウォールで Web アプリケーションのセキュリティを強化する詳細については、 [Web アプリケーション ファイアウォールの概要](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png



<!--HONumber=Feb17_HO3-->


