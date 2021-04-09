---
title: Azure VMware Solution by CloudSimple - VPN ゲートウェイ
description: CloudSimple のサイト間 VPN ゲートウェイとポイント対サイト VPN ゲートウェイについて説明します。これらは、CloudSimple リージョンとその他のリソースとの間で、暗号化されたトラフィックを送信するために使用されます。
author: sharaths-cs
ms.author: dikamath
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 6e013bb96990a8f3a0ef7d3a58529b200919e276
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88140634"
---
# <a name="vpn-gateways-overview"></a>VPN ゲートウェイの概要

VPN ゲートウェイは、オンプレミスの場所の CloudSimple リージョン ネットワーク、またはパブリック インターネット経由のコンピューター間で、暗号化されたトラフィックを送信するために使用されます。  各リージョンには、複数の接続をサポートできる 1 つの VPN ゲートウェイを含めることができます。 同一の VPN ゲートウェイへの複数の接続を作成する場合、利用できるゲートウェイ帯域幅はすべての VPN トンネルによって共有されます。

CloudSimple には、2 種類の VPN ゲートウェイが用意されています。

* サイト間 VPN ゲートウェイ
* ポイント対サイト VPN ゲートウェイ

## <a name="site-to-site-vpn-gateway"></a>サイト間 VPN ゲートウェイ

サイト間 VPN ゲートウェイは、CloudSimple リージョン ネットワークとオンプレミスのデータ センターの間で、暗号化されたトラフィックの送信に使用されます。 この接続を使用して、オンプレミス ネットワークと CloudSimple リージョン ネットワークの間のネットワーク トラフィックのための、サブネット/CIDR の範囲を定義します。

VPN ゲートウェイでは、プライベート クラウド上でオンプレミスのサービスを利用したり、プライベート クラウド上のサービスをオンプレミス ネットワークから利用したりすることができます。  CloudSimple は、オンプレミス ネットワークからの接続を確立するための、ポリシーベースの VPN サーバーを提供します。

サイト間 VPN のユース ケースは次のとおりです。

* オンプレミス ネットワーク内の任意のワークステーションからの、プライベート クラウド vCenter へのアクセシビリティ。
* VCenter ID ソースとして、オンプレミスの Active Directory Domain Services を使用します。
* オンプレミス リソースからプライベート クラウド vCenter への、VM テンプレート、ISO、その他のファイルの簡易な転送。
* オンプレミス ネットワークからの、プライベート クラウド上で実行されているワークロードへのアクセシビリティ。

![サイト間 VPN 接続の図](media/cloudsimple-site-to-site-vpn-connection.png)

### <a name="cryptographic-parameters"></a>暗号化パラメーター

サイト間 VPN 接続では、セキュリティで保護された接続を確立するために、以下の既定の暗号化パラメーターを使用します。  オンプレミス VPN デバイスからの接続を作成する場合は、オンプレミス VPN ゲートウェイでサポートされている以下のいずれかのパラメーターを使用します。

#### <a name="phase-1-proposals"></a>フェーズ 1 の提案

| パラメーター | 提案 1 | 提案 2 | 提案 3 |
|-----------|------------|------------|------------|
| IKE のバージョン | IKEv1 | IKEv1 | IKEv1 |
| 暗号化 | AES 128 | AES 256 | AES 256 |
| ハッシュ アルゴリズム| SHA 256 | SHA 256 | SHA 1 |
| Diffie Hellman グループ (DH グループ) | 2 | 2 | 2 |
| 有効期間 | 28,800 秒 | 28,800 秒 | 28,800 秒 |
| データ サイズ | 4 GB | 4 GB | 4 GB |

#### <a name="phase-2-proposals"></a>フェーズ 2 の提案

| パラメーター | 提案 1 | 提案 2 | 提案 3 |
|-----------|------------|------------|------------|
| 暗号化 | AES 128 | AES 256 | AES 256 |
| ハッシュ アルゴリズム| SHA 256 | SHA 256 | SHA 1 |
| Perfect Forward Secrecy グループ (PFS グループ) | なし | なし | なし |
| 有効期間 | 1,800 秒 | 1,800 秒 | 1,800 秒 |
| データ サイズ | 4 GB | 4 GB | 4 GB |


> [!IMPORTANT]
> VPN デバイスでの TCP MSS クランプは 1,200 で設定します。 お使いの VPN デバイスで MSS クランプがサポートされていない場合は、別の方法として、トンネル インターフェイスの MTU を 1,240 バイトに設定できます。

## <a name="point-to-site-vpn-gateway"></a>ポイント対サイト VPN ゲートウェイ

ポイント対サイト VPN は、CloudSimple リージョン ネットワークとクライアント コンピューターの間で暗号化されたトラフィックの送信に使用されます。  ポイント対サイト VPN は、プライベート クラウド vCenter およびワークロード VM を含む、プライベート クラウド ネットワークにアクセスする最も簡単な方法です。  プライベート クラウドにリモートで接続している場合は、ポイント対サイト VPN 接続を使用します。

## <a name="next-steps"></a>次のステップ

* [VPN ゲートウェイの設定](vpn-gateway.md)
