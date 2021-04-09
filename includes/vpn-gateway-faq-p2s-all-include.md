---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/19/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: a3c10ca35ee2f085d4ce41e862a895ff17ff63a0
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "84317710"
---
### <a name="how-many-vpn-client-endpoints-can-i-have-in-my-point-to-site-configuration"></a>ポイント対サイト構成で保持できる VPN クライアント エンドポイントの最大数を教えてください。

それは、ゲートウェイ SKU によって異なります。 サポートされる接続の数の詳細については、「[ゲートウェイの SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)」を参照してください。

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a><a name="supportedclientos"></a>ポイント対サイト接続で使用できるクライアント オペレーティング システムを教えてください。

次のクライアント オペレーティング システムがサポートされています。

* Windows 7 (32 ビットと 64 ビット)
* Windows Server 2008 R2 (64 ビットのみ)
* Windows 8.1 (32 ビットと 64 ビット)
* Windows Server 2012 (64 ビットのみ)
* Windows Server 2012 R2 (64 ビットのみ)
* Windows Server 2016 (64 ビットのみ)
* Windows Server 2019 (64 ビットのみ)
* Windows 10
* Mac OS X バージョン 10.11 以降
* Linux (StrongSwan)
* iOS

[!INCLUDE [TLS](vpn-gateway-tls-updates.md)]

### <a name="can-i-traverse-proxies-and-firewalls-using-point-to-site-capability"></a>プロキシやファイアウォールを経由してポイント対サイト接続の機能を使用できますか。

Azure では、次の 3 種類のポイント対サイト VPN オプションをサポートしています。

* Secure Socket Tunneling Protocol (SSTP)。 SSTP は、ほとんどのファイアウォールが 443 SSL で使用される送信 TCP ポートを開いているためにファイアウォールを通過できる、Microsoft 独自の SSL ベースのソリューションです。

* OpenVPN。 OpenVPN は、ほとんどのファイアウォールが 443 SSL で使用される送信 TCP ポートを開いているためにファイアウォールを通過できる、SSL ベースのソリューションです。

* IKEv2 VPN。 IKEv2 VPN は標準ベースの IPsec VPN ソリューションであり、送信 UDP ポート 500 と 4500 のほか、IP プロトコル番号 50 を使用しています。 ファイアウォールでここに挙げたポートが必ずしも開いているとは限りません。このため、IKEv2 VPN ではプロキシとファイアウォールを通過できないことがあります。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>ポイント対サイト接続用に構成されたクライアント コンピューターを再起動した場合、VPN は自動的に再接続されますか。

既定では、クライアント コンピューターの再起動時には VPN の自動再接続は行われません。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>ポイント対サイトの自動再接続と VPN クライアントの DDNS はサポートされていますか。

ポイント対サイト VPN では、自動再接続と DDNS は現時点ではサポートされていません。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-coexist-for-the-same-virtual-network"></a>サイト間接続およびポイント対サイト接続の構成は、同一仮想ネットワークに共存させることはできますか。

はい。 Resource Manager デプロイ モデルの場合には、ゲートウェイにルートベースの VPN が必要になります。 クラシック デプロイ モデルの場合は、動的ゲートウェイが必要です。 静的ルーティング VPN ゲートウェイと、ポリシーベースの VPN ゲートウェイでは、ポイント対サイト接続はサポートされません。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-network-gateways-at-the-same-time"></a>ポイント対サイト接続のクライアントを同時に複数の仮想ネットワーク ゲートウェイに接続するように構成することはできますか。

使用される VPN クライアント ソフトウェアによっては、接続先の仮想ネットワークにおいて、それらの間で、または接続元のクライアントのネットワークとの間で、競合するアドレス空間がない場合、複数の Virtual Network ゲートウェイに接続できることがあります。  Azure VPN クライアントでは多くの VPN 接続がサポートされていますが、一度に接続できる接続は 1 つだけです。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>ポイント対サイト接続のクライアントを同時に複数の仮想ネットワークに接続するように構成することはできますか。

はい。他の VNet とピアリングされている VNet にデプロイされている Virtual Network ゲートウェイへのポイント対サイト接続は、ピアリングされた他の VNet にアクセスできる可能性があります。  ピアリングされた VNet が UseRemoteGateway/AllowGatewayTransit 機能を使用している場合、ポイント対サイトクライアントは、ピアリングされた VNet に接続できます。  詳しくは、[こちら](../articles/vpn-gateway/vpn-gateway-about-point-to-site-routing.md)の記事を参照してください。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>サイト間接続またはポイント対サイト接続ではどの程度のスループットが得られますか。

VPN トンネルのスループットを正確に一定レベルに維持することは困難です。 IPsec と SSTP は、VPN プロトコルの中でも暗号化処理の負荷が高いものです。 また、スループットはオンプレミスとインターネットの間の待機時間や帯域幅によっても制限されます。 IKEv2 ポイント対サイト VPN 接続のみを使用する VPN ゲートウェイでは、期待できるスループットの総量がゲートウェイの SKU に応じて変わります。 スループットの詳細については、「[ゲートウェイの SKU](../articles/vpn-gateway/vpn-gateway-about-vpngateways.md#gwsku)」を参照してください。

### <a name="can-i-use-any-software-vpn-client-for-point-to-site-that-supports-sstp-andor-ikev2"></a>SSTP や IKEv2 をサポートしているポイント対サイト接続では、ソフトウェア VPN クライアントを使用できますか。

いいえ。 SSTP については Windows のネイティブ VPN クライアント、IKEv2 については Mac のネイティブ VPN クライアントのみ使用できます。 ただし、すべてのプラットフォーム上で OpenVPN クライアントを使用して、OpenVPN プロトコル経由で接続できます。 サポートされているクライアント オペレーティング システムの一覧を参照してください。

### <a name="does-azure-support-ikev2-vpn-with-windows"></a>Azure は、Windows で IKEv2 VPN をサポートしていますか。

IKEv2 は、Windows 10 および Windows Server 2016 でサポートされています。 ただし、IKEv2 を使用するには、更新プログラムをインストールして、ローカルでレジストリ キーの値を設定する必要があります。 Windows 10 以前の OS バージョンはサポートされておらず、それらのバージョンで使用できるのは SSTP または **OpenVPN® プロトコル** のみです。

Windows 10 または Windows Server 2016 を IKEv2 用に準備するには:

1. 更新プログラムをインストールします。

   | OS バージョン | Date | 数/リンク |
   |---|---|---|
   | Windows Server 2016<br>Windows 10 バージョン 1607 | 2018 年 1 月 17 日 | [KB4057142](https://support.microsoft.com/help/4057142/windows-10-update-kb4057142) |
   | Windows 10 バージョン 1703 | 2018 年 1 月 17 日 | [KB4057144](https://support.microsoft.com/help/4057144/windows-10-update-kb4057144) |
   | Windows 10 バージョン 1709 | 2018 年 3 月 22 日 | [KB4089848](https://www.catalog.update.microsoft.com/search.aspx?q=kb4089848) |
   |  |  |  |

2. レジストリ キーの値を設定します。 レジストリの "HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\RasMan\ IKEv2\DisableCertReqPayload" REG_DWORD キーを作成するか、または 1 に設定します。

### <a name="what-happens-when-i-configure-both-sstp-and-ikev2-for-p2s-vpn-connections"></a>P2S VPN 接続に対して SSTP と IKEv2 の両方を構成した場合、何が起こりますか。

Windows デバイスと Mac デバイスが混在する環境で SSTP と IKEv2 の両方を構成すると、Windows VPN クライアントは必ず最初に IKEv2 トンネルを試行します。ただし、IKEv2 接続が成功しなかった場合 SSTP にフォールバックします。 MacOSX は IKEv2 経由のみで接続します。

### <a name="other-than-windows-and-mac-which-other-platforms-does-azure-support-for-p2s-vpn"></a>Windows と Mac 以外に、Azure が P2S VPN 向けにサポートしている他のプラットフォームはありますか。

Azure では、P2S VPN 向けに Windows、Mac、および Linux をサポートしています。

### <a name="i-already-have-an-azure-vpn-gateway-deployed-can-i-enable-radius-andor-ikev2-vpn-on-it"></a>Azure VPN Gateway を既にデプロイしています。 ここで RADIUS または IKEv2 VPN または両方を有効にできますか。

はい。Powershell または Azure Portal を使用して、既にデプロイされているゲートウェイでこれらの新機能を有効にできます。ただし、ご利用のゲートウェイ SKU が、RADIUS と IKEv2 のいずれかまたは両方をサポートしている必要があります。 たとえば VPN Gateway Basic SKU は、RADIUS と IKEv2 のどちらもサポートしていません。

### <a name="how-do-i-remove-the-configuration-of-a-p2s-connection"></a><a name="removeconfig"></a>P2S 接続の構成を削除するにはどうすればよいですか。

P2S 構成は、次のコマンドを使って Azure CLI と PowerShell を使用して削除できます。

#### <a name="azure-powershell"></a>Azure PowerShell

```azurepowershell-interactive
$gw=Get-AzVirtualNetworkGateway -name <gateway-name>`  
$gw.VPNClientConfiguration = $null`  
Set-AzVirtualNetworkGateway -VirtualNetworkGateway $gw`
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network vnet-gateway update --name <gateway-name> --resource-group <resource-group name> --remove "vpnClientConfiguration"
```
