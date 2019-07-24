---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 0c0ad6ea5a687d066c78533b45a7f531561661bf
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/13/2018
ms.locfileid: "53323746"
---
この FAQ は、クラシック デプロイ モデルを使用した P2S 接続に適用されます。

### <a name="what-client-operating-systems-can-i-use-with-point-to-site"></a>ポイント対サイト接続で使用できるクライアント オペレーティング システムを教えてください。

次のクライアント オペレーティング システムがサポートされています。

* Windows 7 (32 ビットと 64 ビット)
* Windows Server 2008 R2 (64 ビットのみ)
* Windows 8 (32 ビットと 64 ビット)
* Windows 8.1 (32 ビットと 64 ビット)
* Windows Server 2012 (64 ビットのみ)
* Windows Server 2012 R2 (64 ビットのみ)
* Windows 10

### <a name="can-i-use-any-software-vpn-client-that-supports-sstp-for-point-to-site"></a>ポイント対サイト用に SSTP をサポートしているソフトウェア VPN クライアントを使用できますか。

いいえ。 一覧にあるバージョンの Windows オペレーティング システムのみがサポートされています。

### <a name="how-many-vpn-client-endpoints-can-exist-in-my-point-to-site-configuration"></a>ポイント対サイト構成に存在できる VPN クライアント エンドポイントの最大数を教えてください。

仮想ネットワークに同時に接続できる VPN クライアント数は、最大で 128 個です。

### <a name="can-i-use-my-own-internal-pki-root-ca-for-point-to-site-connectivity"></a>ポイント対サイト接続で自社の内部 PKI ルート CA を使用できますか。

はい。 以前は、自己署名ルート証明書のみを使用できました。 引き続き、最大で 20 個のルート証明書をアップロードできます。

### <a name="can-i-traverse-proxies-and-firewalls-by-using-point-to-site"></a>ポイント対サイトを使用して、プロキシやファイアウォールを通過できますか。

はい。 ファイアウォールを越えるトンネリングを行うために、SSTP (Secure Socket トンネリング プロトコル) が使用されます。 このトンネルは HTTPS 接続として表示されます。

### <a name="if-i-restart-a-client-computer-configured-for-point-to-site-will-the-vpn-automatically-reconnect"></a>ポイント対サイト接続用に構成されたクライアント コンピューターを再起動した場合、VPN は自動的に再接続されますか。

既定では、クライアント コンピューターは VPN 接続を自動的に再確立しません。

### <a name="does-point-to-site-support-auto-reconnect-and-ddns-on-the-vpn-clients"></a>ポイント対サイトは、VPN クライアントでの自動再接続と DDNS をサポートしていますか。

いいえ。 ポイント対サイト VPN では、自動再接続と DDNS は現時点ではサポートされていません。

### <a name="can-i-have-site-to-site-and-point-to-site-configurations-for-the-same-virtual-network"></a>サイト間接続とポイント対サイト接続の構成を同一仮想ネットワークに共存させることはできますか。

はい。 ゲートウェイの VPN の種類が RouteBased の場合は、どちらのソリューションも機能します。 クラシック デプロイ モデルの場合は、動的ゲートウェイが必要です。 静的ルーティング VPN ゲートウェイと、**-VpnType PolicyBased** コマンドレットを使用するゲートウェイでは、ポイント対サイト接続はサポートされません。

### <a name="can-i-configure-a-point-to-site-client-to-connect-to-multiple-virtual-networks-at-the-same-time"></a>ポイント対サイト接続のクライアントを同時に複数の仮想ネットワークに接続するように構成することはできますか。

はい。 ただし、仮想ネットワーク内で重複する IP プレフィックスを使用することはできません。また、ポイント対サイト接続のアドレス空間は仮想ネットワーク間で重複しないようにする必要があります。

### <a name="how-much-throughput-can-i-expect-through-site-to-site-or-point-to-site-connections"></a>サイト間接続またはポイント対サイト接続ではどの程度のスループットが得られますか。

VPN トンネルのスループットを正確に一定レベルに維持することは困難です。 IPsec と SSTP は、VPN プロトコルの中でも暗号化処理の負荷が高いものです。 また、スループットはオンプレミスとインターネットの間の待ち時間や帯域幅によっても制限されます。
