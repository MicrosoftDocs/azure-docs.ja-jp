---
title: Azure Application Gateway でのエンド ツー エンド TLS の構成
description: この記事では、PowerShell を使用して Azure Application Gateway でエンド ツー エンド TLS を構成する方法を説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 06/09/2020
ms.author: victorh
ms.openlocfilehash: 47891dfa7fc0c9b30ccdbf2ed7710125eb36e4a3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397809"
---
# <a name="configure-end-to-end-tls-by-using-application-gateway-with-powershell"></a>Application Gateway での PowerShell を使用したエンド ツー エンド TLS の構成

## <a name="overview"></a>概要

Azure Application Gateway は、トラフィックのエンド ツー エンド暗号化をサポートしています。 Application Gateway では、アプリケーション ゲートウェイで TLS または SSL 接続が終端処理されます。 ゲートウェイでは、その後、トラフィックへのルーティング規則の適用、パケットの再暗号化、定義済みのルーティング規則に基づいた適切なバックエンド サーバーへのパケットの転送が実行されます。 Web サーバーからの応答は、同じ手順でエンドユーザーに移動します。

Application Gateway は、カスタム TLS オプションの定義をサポートしています。 また、**TLSv1.0**、**TLSv1.1**、および **TLSv1.2** のバージョンのプロトコルの無効化と、使用する暗号スイートとその優先順位の定義もサポートしています。 構成できる TLS オプションの詳細については、[TLS ポリシーの概要](application-gateway-SSL-policy-overview.md)に関するページを参照してください。

> [!NOTE]
> SSL 2.0 と SSL 3.0 は既定で無効になっており、有効にすることはできません。 これらはセキュリティで保護されておらず、Application Gateway では使用できません。

![シナリオのイメージ][scenario]

## <a name="scenario"></a>シナリオ

このシナリオでは、PowerShell で、エンド ツー エンド TLS を使用してアプリケーション ゲートウェイを作成する方法について説明します。

このシナリオで、以下の作業を行います。

* **appgw-rg** という名前のリソース グループを作成します。
* アドレス スペースが **10.0.0.0/16** の **appgwvnet** という名前の仮想ネットワークを作成します。
* **appgwsubnet** と **appsubnet** という名前の 2 つのサブネットを作成します。
* TLS プロトコル バージョンと暗号化スイートを制限するエンド ツー エンド TLS 暗号化をサポートする、小さなアプリケーション ゲートウェイを作成します。

## <a name="before-you-begin"></a>開始する前に

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

アプリケーション ゲートウェイでエンド ツー エンド TLS を構成するには、ゲートウェイ用とバックエンド サーバー用の証明書が必要です。 ゲートウェイ証明書は、TLS プロトコルの仕様に従って対称キーを得る目的で使用されます。 対称キーは、ゲートウェイに送信されたトラフィックの暗号化と暗号化の解除に使用されます。 ゲートウェイ証明書は、Personal Information Exchange (PFX) 形式である必要があります。 このファイル形式により、アプリケーション ゲートウェイがトラフィックの暗号化および暗号化解除を実行する際に必要な秘密キーをエクスポートできます。

エンド ツー エンド TLS 暗号化では、アプリケーション ゲートウェイにバックエンドを明示的に許可する必要があります。 バックエンド サーバーの公開証明書をアプリケーション ゲートウェイにアップロードしてください。 証明書を追加することにより、アプリケーション ゲートウェイは、既知のバックエンド インスタンスのみと通信するため、 エンド ツー エンド通信のセキュリティが強化されます。

この構成プロセスについては以降のセクションで説明します。

## <a name="create-the-resource-group"></a>リソース グループの作成

このセクションでは、アプリケーション ゲートウェイを含むリソース グループの作成手順について説明します。

1. Azure アカウントにサインインします。

   ```powershell
   Connect-AzAccount
   ```

2. このシナリオで使用するサブスクリプションを選択します。

   ```powershell
   Select-Azsubscription -SubscriptionName "<Subscription name>"
   ```

3. リソース グループを作成します。 (既存のリソース グループを使用する場合は、この手順をスキップしてください。)

   ```powershell
   New-AzResourceGroup -Name appgw-rg -Location "West US"
   ```

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>アプリケーション ゲートウェイの仮想ネットワークとサブネットを作成します。

次の例では、仮想ネットワークと 2 つのサブネットを作成します。 1 つはアプリケーション ゲートウェイを保持するために使用します。 もう 1 つのサブネットは、Web アプリケーションをホストするバックエンドに使用します。

1. アプリケーション ゲートウェイに使用するサブネットのアドレス範囲を割り当てます。

   ```powershell
   $gwSubnet = New-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24
   ```

   > [!NOTE]
   > アプリケーション ゲートウェイ用に構成されているサブネットのサイズを適切に設定する必要があります。 アプリケーション ゲートウェイは、最大 10 個のインスタンスに対して構成できます。 各インスタンスは、サブネットから IP アドレスを 1 つ取得します。 サブネットのサイズが小さすぎると、アプリケーション ゲートウェイのスケールアウトに悪影響を及ぼす可能性があります。
   >

2. バックエンド アドレス プールに使用するアドレス範囲を割り当てます。

   ```powershell
   $nicSubnet = New-AzVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24
   ```

3. 上記の手順で定義されたサブネットを含む仮想ネットワークを作成します。

   ```powershell
   $vnet = New-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet
   ```

4. 以降の手順で使用する仮想ネットワーク リソースとサブネット リソースを取得します。

   ```powershell
   $vnet = Get-AzvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
   $gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
   $nicSubnet = Get-AzVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet
   ```

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>フロントエンド構成のパブリック IP アドレスの作成

アプリケーション ゲートウェイに使用するパブリック IP リソースを作成します。 このパブリック IP アドレスは、以降の手順のいずれかで使用します。

```powershell
$publicip = New-AzPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic
```

> [!IMPORTANT]
> Application Gateway では、ドメイン ラベルを定義して作成したパブリック IP アドレスを使用できません。 動的に作成されるドメイン ラベルを持つパブリック IP アドレスのみがサポートされています。 アプリケーション ゲートウェイにわかりやすい DNS 名を付ける必要がある場合は、CNAME レコードをエイリアスとして使用することをお勧めします。

## <a name="create-an-application-gateway-configuration-object"></a>Application Gateway 構成オブジェクトの作成

アプリケーション ゲートウェイを作成する前に、すべての構成項目を設定します。 次の手順では、Application Gateway のリソースに必要な構成項目を作成します。

1. アプリケーション ゲートウェイの IP 構成を作成します。 この設定では、アプリケーション ゲートウェイで使用するサブネットを構成します。 アプリケーション ゲートウェイが起動すると、構成されているサブネットから IP アドレスが取得され、ネットワーク トラフィックがバックエンド IP プール内の IP アドレスにルーティングされます。 各インスタンスが IP アドレスを 1 つ取得することに注意してください。

   ```powershell
   $gipconfig = New-AzApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet
   ```

2. フロント エンドの IP 構成を作成します。 この設定によって、アプリケーション ゲートウェイのフロントエンドにプライベート IP アドレスまたはパブリック IP アドレスがマッピングされます。 次の手順で、フロントエンドの IP 構成と前の手順のパブリック IP アドレスを関連付けます。

   ```powershell
   $fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip
   ```

3. バックエンド Web サーバーの IP アドレスを使用してバックエンド IP アドレス プールを構成します。 これらの IP アドレスは、フロントエンド IP エンドポイントから送信されるネットワーク トラフィックを受信する IP アドレスです。 サンプルの IP アドレスを独自のアプリケーションの IP アドレス エンドポイントに置き換えます。

   ```powershell
   $pool = New-AzApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3
   ```

   > [!NOTE]
   > バックエンド サーバーの IP アドレスの代わりに、完全修飾ドメイン名 (FQDN) の値も使用できます。 有効にするには、 **-BackendFqdns** スイッチを使用します。 

4. パブリック IP エンドポイントのフロントエンド IP ポートを構成します。 このポートは、エンドユーザーが接続するポートです。

   ```powershell
   $fp = New-AzApplicationGatewayFrontendPort -Name 'port01'  -Port 443
   ```

5. アプリケーション ゲートウェイの証明書を構成します。 この証明書は、アプリケーション ゲートウェイでのトラフィックの暗号化解除と再暗号化に使用されます。

   ```powershell
   $passwd = ConvertTo-SecureString  <certificate file password> -AsPlainText -Force 
   $cert = New-AzApplicationGatewaySSLCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password $passwd 
   ```

   > [!NOTE]
   > このサンプルでは、TLS 接続に使用する証明書を構成します。 証明書は .pfx 形式であり、4 ～ 12 文字のパスワードを使用する必要があります。

6. アプリケーション ゲートウェイの HTTP リスナーを作成します。 使用するフロントエンド IP 構成、ポート、および TLS または SSL 証明書を割り当てます。

   ```powershell
   $listener = New-AzApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SSLCertificate $cert
   ```

7. TLS 対応バックエンド プール リソースで使用する証明書をアップロードします。

   > [!NOTE]
   > 既定のプローブは、バックエンドの IP アドレスでの *既定の* TLS バインドから公開キーを取得し、取得した公開キー値をここで指定した公開キー値と比較します。 
   > 
   > バックエンドでホスト ヘッダーと Server Name Indication (SNI) を使用している場合、取得した公開キーがトラフィックの送信先となる目的のサイトであるとは限りません。 確かでない場合は、バックエンド サーバーで https://127.0.0.1/ にアクセスして、*既定* の TLS バインドにどの証明書が使用されているかを確認します。 このセクションでその要求の公開キーを使用します。 HTTPS バインドでホスト ヘッダーと SNI を使用しており、バックエンド サーバーでの https://127.0.0.1/ に対する手動のブラウザー要求から応答と証明書を受信していない場合は、バックエンド サーバーで既定の TLS バインドを設定する必要があります。 これを行わないと、プローブは失敗し、バックエンドは許可されません。
   
   Application Gateway の SNI の詳細については、「[Application Gateway での TLS 終了とエンド ツー エンド TLS の概要](ssl-overview.md)」を参照してください。

   ```powershell
   $authcert = New-AzApplicationGatewayAuthenticationCertificate -Name 'allowlistcert1' -CertificateFile C:\cert.cer
   ```

   > [!NOTE]
   > 前の手順で指定される証明書は、バックエンドに存在する .pfx 証明書の公開キーである必要があります。 バックエンド サーバーにインストールされている (ルート証明書ではない) 証明書を Claim, Evidence, and Reasoning (CER) 形式でエクスポートし、この手順で使用します。 この手順によって、アプリケーション ゲートウェイにバックエンドが許可されます。

   Application Gateway v2 SKU を使用している場合は、認証証明書ではなく、信頼されたルート証明書を作成します。 詳細については、[Application Gateway でのエンド ツー エンド TLS の概要](ssl-overview.md#end-to-end-tls-with-the-v2-sku)に関するページを参照してください。

   ```powershell
   $trustedRootCert01 = New-AzApplicationGatewayTrustedRootCertificate -Name "test1" -CertificateFile  <path to root cert file>
   ```

8. アプリケーション ゲートウェイのバックエンドの HTTP 設定を構成します。 前の手順でアップロードした証明書を HTTP 設定に割り当てます。

   ```powershell
   $poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert
   ```

   Application Gateway v2 SKU では、次のコマンドを使用します。

   ```powershell
   $poolSetting01 = New-AzApplicationGatewayBackendHttpSettings -Name “setting01” -Port 443 -Protocol Https -CookieBasedAffinity Disabled -TrustedRootCertificate $trustedRootCert01 -HostName "test1"
   ```

9. ロード バランサーの動作を構成するロード バランサーのルーティング規則を作成します。 この例では、ラウンド ロビンの基本的な規則を作成します。

   ```powershell
   $rule = New-AzApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   ```

10. Application Gateway のインスタンスのサイズを構成します。 利用可能なサイズは、**Standard\_Small**、**Standard\_Medium**、および **Standard\_Large** です。  容量に使用可能な値は **1** ～ **10** です。

    ```powershell
    $sku = New-AzApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2
    ```

    > [!NOTE]
    > テスト目的では、インスタンス数として 1 を選択できます。 重要なのは、2 より小さいインスタンス数は SLA の対象外のため、推奨されていないことを把握しておくことです。 小規模のゲートウェイは開発テスト用であり、運用目的ではありません。

11. アプリケーション ゲートウェイで使用する TLS ポリシーを構成します。 Application Gateway では、TLS プロトコルの最小バージョンを設定する機能がサポートされています。

    次の値が、定義できるプロトコル バージョンの一覧です。

    - **TLSV1_0**
    - **TLSV1_1**
    - **TLSV1_2**
    
    次の例では、最小のプロトコルのバージョンを **TLSv1_2** に設定し、**TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**、**TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**、および **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** のみを有効にします。

    ```powershell
    $SSLPolicy = New-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -PolicyType Custom
    ```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

これまでのすべての手順に従って、アプリケーション ゲートウェイを作成します。 ゲートウェイの作成は、実行時間が長くかかるプロセスです。

V1 SKU の場合は、以下のコマンドを使用します
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -AuthenticationCertificates $authcert -Verbose
```

V2 SKU の場合は、以下のコマンドを使用します
```powershell
$appgw = New-AzApplicationGateway -Name appgateway -SSLCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SSLPolicy $SSLPolicy -TrustedRootCertificate $trustedRootCert01 -Verbose
```

## <a name="apply-a-new-certificate-if-the-back-end-certificate-is-expired"></a>バックエンド証明書が期限切れになった場合に新しい証明書を適用する

バックエンド証明書が期限切れになった場合に新しい証明書を適用するには、この手順を使用します。

1. 更新するアプリケーション ゲートウェイを取得します。

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. .cer ファイルから新しい証明書リソースを追加します。このファイルには、証明書の公開キーが含まれています。また、アプリケーション ゲートウェイで TLS 終端のためにリスナーに追加されたものと同じ証明書にすることもできます。

   ```powershell
   Add-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name 'NewCert' -CertificateFile "appgw_NewCert.cer" 
   ```
    
3. 新しい認証証明書オブジェクトを変数に取得します (TypeName:Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayAuthenticationCertificate)。

   ```powershell
   $AuthCert = Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name NewCert
   ```
 
 4. 新しい証明書を **BackendHttp** 設定に割り当て、それを $AuthCert 変数を使用して参照します (変更する HTTP 設定名を指定します)。
 
   ```powershell
   $out= Set-AzApplicationGatewayBackendHttpSetting -ApplicationGateway $gw -Name "HTTP1" -Port 443 -Protocol "Https" -CookieBasedAffinity Disabled -AuthenticationCertificates $Authcert
   ```
    
 5. 変更をアプリケーション ゲートウェイにコミットし、含まれる新しい構成を $out 変数に渡します。
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw  
   ```

## <a name="remove-an-unused-expired-certificate-from-http-settings"></a>HTTP 設定から使用されていない期限切れ証明書を削除する

HTTP 設定から使用されていない期限切れ証明書を削除するには、この手順を使用します。

1. 更新するアプリケーション ゲートウェイを取得します。

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```
   
2. 削除する認証証明書の名前を一覧表示します。

   ```powershell
   Get-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw | select name
   ```
    
3. アプリケーション ゲートウェイから認証証明書を削除します。

   ```powershell
   $gw=Remove-AzApplicationGatewayAuthenticationCertificate -ApplicationGateway $gw -Name ExpiredCert
   ```
 
 4. 変更をコミットします。
 
   ```powershell
   Set-AzApplicationGateway -ApplicationGateway $gw
   ```

   
## <a name="limit-tls-protocol-versions-on-an-existing-application-gateway"></a>既存のアプリケーション ゲートウェイで TLS プロトコル バージョンを制限する

これまでの手順で、エンド ツー エンド TLS を使用したアプリケーションの作成と、TLS プロトコルの特定バージョンの無効化について説明しました。 次の例では、既存のアプリケーション ゲートウェイの特定の TLS ポリシーを無効にします。

1. 更新するアプリケーション ゲートウェイを取得します。

   ```powershell
   $gw = Get-AzApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG
   ```

2. TLS ポリシーを定義します。 次の例では、 **TLSv1.0** および **TLSv1.1** は無効で、暗号スイート **TLS\_ECDHE\_ECDSA\_WITH\_AES\_128\_GCM\_SHA256**、**TLS\_ECDHE\_ECDSA\_WITH\_AES\_256\_GCM\_SHA384**、および **TLS\_RSA\_WITH\_AES\_128\_GCM\_SHA256** のみが許可されています。

   ```powershell
   Set-AzApplicationGatewaySSLPolicy -MinProtocolVersion TLSv1_2 -PolicyType Custom -CipherSuite "TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256", "TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384", "TLS_RSA_WITH_AES_128_GCM_SHA256" -ApplicationGateway $gw

   ```

3. 最後に、ゲートウェイを更新します。 この最後の手順は実行時間が長くかかります。 終了すると、エンド ツー エンド TLS がアプリケーション ゲートウェイに構成されます。

   ```powershell
   $gw | Set-AzApplicationGateway
   ```

## <a name="get-an-application-gateway-dns-name"></a>アプリケーション ゲートウェイの DNS 名を取得する

ゲートウェイを作成した後の次の手順は通信用にフロントエンドを構成することです。 パブリック IP を使用する場合、Application Gateway には、動的に割り当てられたフレンドリではない DNS 名が必要です。 エンド ユーザーがアプリケーション ゲートウェイを確実にヒットできるように、CNAME レコードを使用して、アプリケーション ゲートウェイのパブリック エンドポイントを参照できます。 詳細については、[Azure でのカスタム ドメイン名の構成](../cloud-services/cloud-services-custom-domain-name-portal.md)に関する記事を参照してください。 

別名を構成するには、アプリケーション ゲートウェイに接続されている **PublicIPAddress** 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。 アプリケーション ゲートウェイの DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成します。 アプリケーション ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードの使用はお勧めしません。

```powershell
Get-AzPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
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

Application Gateway を使用して Web アプリケーション ファイアウォールで Web アプリケーションのセキュリティを強化する方法の詳細については、[Web アプリケーション ファイアウォールの概要](../web-application-firewall/ag/ag-overview.md)に関するページをご覧ください。

[scenario]: ./media/application-gateway-end-to-end-SSL-powershell/scenario.png