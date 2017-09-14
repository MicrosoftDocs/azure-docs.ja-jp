---
title: "サンプル構成 - Azure VPN ゲートウェイに接続される Cisco ASA デバイス | Microsoft Docs"
description: "この記事には、Azure VPN ゲートウェイに接続される Cisco ASA デバイスのサンプル構成が用意されています。"
services: vpn-gateway
documentationcenter: na
author: yushwang
manager: rossort
editor: 
tags: 
ms.assetid: a8bfc955-de49-4172-95ac-5257e262d7ea
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/20/2017
ms.author: yushwang
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 10466b8928e2cd687f7961a2956b6d60823b82be
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>サンプル構成: Cisco ASA デバイス (IKEv2/BGP なし)
この記事には、Azure VPN ゲートウェイに接続される Cisco ASA デバイスのサンプル構成が用意されています。

## <a name="device-at-a-glance"></a>デバイスの概略

|                        |                                   |
| ---                    | ---                               |
| デバイスの製造元          | Cisco                             |
| デバイスのモデル           | ASA (Adaptive Security Appliance) |
| ターゲット バージョン         | 8.4+                              |
| テスト済みのモデル           | ASA 5505                          |
| テスト済みのバージョン         | 9.2                               |
| IKE バージョン            | **IKEv2**                         |
| BGP                    | **いいえ**                            |
| Azure VPN ゲートウェイの種類 | **ルート ベース**の VPN ゲートウェイ       |
|                        |                                   |

> [!NOTE]
> 1. 以下の構成は、[この記事](vpn-gateway-connect-multiple-policybased-rm-ps.md)で説明されているように、カスタム IPsec/IKE ポリシーを "UserPolicyBasedTrafficSelectors" オプションで使用して Cisco ASA デバイスを**ルート ベース**の Azure VPN ゲートウェイに接続します。
> 2. ASA デバイスでは、VTI ベースではなくアクセス リスト ベースの構成の **IKEv2** を使用する必要があります。
> 3. オンプレミスの VPN デバイスでポリシーがサポートされることを、VPN デバイス ベンダーの仕様で確認してください。

## <a name="vpn-device-requirements"></a>VPN デバイスの要件
Azure VPN ゲートウェイは、標準の IPsec/IKE プロトコル スイートを使用して S2S VPN トンネルを確立します。 Azure VPN ゲートウェイで使用する IPsec/IKE プロトコル パラメーターと既定の暗号アルゴリズムについて詳しくは、[VPN デバイス](vpn-gateway-about-vpn-devices.md)に関する記事をご覧ください。 [暗号化の要件](vpn-gateway-about-compliance-crypto.md)に関する記事の説明に従って、特定の接続について、暗号アルゴリズムとキーの強度の正確な組み合わせを指定することもできます。 暗号アルゴリズムとキーの強度の具体的な組み合わせを選択した場合は、お使いの VPN デバイスに対応する仕様を使用してください。

## <a name="single-vpn-tunnel"></a>単一の VPN トンネル
このトポロジーは、Azure VPN ゲートウェイとオンプレミスの VPN デバイスの間の単一の S2S VPN トンネルで構成されます。 VPN トンネル間に BGP を構成することもできます。

![単一のトンネル](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Azure の構成を構築する詳細な手順については、[単一のトンネルの設定](vpn-gateway-3rdparty-device-config-overview.md#singletunnel)に関する記事をご覧ください。

### <a name="network-and-vpn-gateway-information"></a>ネットワークおよび VPN ゲートウェイの情報
このセクションでは、このサンプルのパラメーターの一覧を示します。

| **パラメーター**                | **値**                    |
| ---                          | ---                          |
| VNet アドレス プレフィックス        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN ゲートウェイ IP         | Azure_Gateway_Public_IP      |
| オンプレミス アドレス プレフィックス | 10.51.0.0/16<br>10.52.0.0/16 |
| オンプレミス VPN デバイス IP    | OnPrem_Device_Public_IP     |
| *VNet BGP ASN                | 65010                        |
| *Azure BGP ピア IP           | 10.12.255.30                 |
| *オンプレミス BGP ASN         | 65050                        |
| *オンプレミス BGP ピア IP     | 10.52.255.254                |
|                              |                              |

* (*) BGP 専用のオプション パラメーター。

### <a name="ipsecike-policy--parameters"></a>IPsec/IKE のポリシーとパラメーター

以下の表に、サンプルで使用されている IPsec/IKE のアルゴリズムとパラメーターの一覧を示します。 上記のすべてのアルゴリズムがお使いの VPN デバイス モデルとファームウェア バージョンでサポートされていることを確認するには、VPN デバイスの仕様をご覧ください。

| **IPsec/IKEv2**  | **値**                            |
| ---              | ---                                  |
| IKEv2 暗号化 | AES256                               |
| IKEv2 整合性  | SHA384                               |
| DH グループ         | DHGroup24                            |
| IPsec 暗号化 | AES256                               |
| IPsec 整合性  | SHA1                                 |
| PFS グループ        | PFS24                                |
| QM SA の有効期間   | 7200 秒                         |
| トラフィック セレクター | UsePolicyBasedTrafficSelectors $True |
| 事前共有キー   | PreSharedKey                         |
|                  |                                      |

- (*) 一部のデバイスでは、IPsec の暗号化アルゴリズムとして GCM-AES が使用されている場合、IPSec の整合性が "null" である必要があります。

### <a name="device-notes"></a>デバイス メモ

>[!NOTE]
>
> 1. IKEv2 のサポートには ASA バージョン 8.4 以上が必要です。
> 2. より上位の DH および PFS グループのサポート (グループ 5 以降) には ASA バージョン 9.x が必要です。
> 3. AES-GCM による IPsec の暗号化と、SHA-256、SHA-384、SHA-512 との IPsec の整合性のサポートには、より新しい ASA ハードウェア上に ASA パージョン 9.x 以上が必要です。ASA 5505、5510、5520、5540、5550、5580 は**サポートされていません**。 (製造元の仕様を確認してください。)
>


### <a name="sample-device-configurations"></a>デバイスのサンプル構成
以下のスクリプトは、上記のトポロジやパラメーターに基づくサンプル構成を提供します。 S2S VPN トンネルの構成は、次の部分で構成されます。

1. インターフェイスとルート
2. アクセス リスト
3. IKE ポリシーとパラメーター (フェーズ 1 またはメイン モード)
4. IPsec ポリシーとパラメーター (フェーズ 2 またはクイック モード)
5. その他のパラメーター (TCP MSS クランプ他)

>[!IMPORTANT] 
>以下に列挙する追加構成に入力します。プレースホルダーは実際の値に置き換えてください。
> 
> - インターフェイス内外のインターフェイスの構成
> - 内部/プライベートおよび外部/パブリック ネットワークのルート
> - すべての名前とポリシー番号がデバイスで一意であることを確認する
> - 暗号アルゴリズムがデバイスでサポートされていることを確認する
> - 次のプレースホルダーを実際の値に置き換える
>   - 外部インターフェイスの名前: "outside"
>   - Azure_Gateway_Public_IP
>   - OnPrem_Device_Public_IP
>   - IKE Pre_Shared_Key
>   - VNet およびローカル ネットワーク ゲートウェイの名前 (VNetName、LNGName)
>   - VNet およびオンプレミスのネットワーク アドレス プレフィックス
>   - 適切なネットマスク

#### <a name="sample-configuration"></a>サンプル構成

```
! Sample ASA configuration for connecting to Azure VPN gateway
!
! Tested hardware: ASA 5505
! Tested version:  ASA version 9.2(4)
!
! Replace the following place holders with your actual values:
!   - Interface names - default are "outside" and "inside"
!   - <Azure_Gateway_Public_IP>
!   - <OnPrem_Device_Public_IP>
!   - <Pre_Shared_Key>
!   - <VNetName>*
!   - <LNGName>* ==> LocalNetworkGateway - the Azure resource that represents the
!     on-premises network, specifies network prefixes, device public IP, BGP info, etc.
!   - <PrivateIPAddress> ==> Replace it with a private IP address if applicable
!   - <Netmask> ==> Replace it with appropriate netmasks
!   - <Nexthop> ==> Replace it with the actual nexthop IP address
!
! (*) Must be unique names in the device configuration
!
! ==> Interface & route configurations
!
!     > <OnPrem_Device_Public_IP> address on the outside interface or vlan
!     > <PrivateIPAddress> on the inside interface or vlan; e.g., 10.51.0.1/24
!     > Route to connect to <Azure_Gateway_Public_IP> address
!
!     > Example:
!
!       interface Ethernet0/0
!        switchport access vlan 2
!       exit
!
!       interface vlan 1
!        nameif inside
!        security-level 100
!        ip address <PrivateIPAddress> <Netmask>
!       exit
!
!       interface vlan 2
!        nameif outside
!        security-level 0
!        ip address <OnPrem_Device_Public_IP> <Netmask>
!       exit
!
!       route outside 0.0.0.0 0.0.0.0 <NextHop IP> 1
!
! ==> Access lists
!
!     > Most firewall devices deny all traffic by default. Create access lists to
!       (1) Allow S2S VPN tunnels between the ASA and the Azure gateway public IP address
!       (2) Construct traffic selectors as part of IPsec policy or proposal
!
access-list outside_access_in extended permit ip host <Azure_Gateway_Public_IP> host <OnPrem_Device_Public_IP>
!
!     > Object group that consists of all VNet prefixes (e.g., 10.11.0.0/16 &
!       10.12.0.0/16)
!
object-group network Azure-<VNetName>
 description Azure virtual network <VNetName> prefixes
 network-object 10.11.0.0 255.255.0.0
 network-object 10.12.0.0 255.255.0.0
exit
!
!     > Object group that corresponding to the <LNGName> prefixes.
!       E.g., 10.51.0.0/16 and 10.52.0.0/16. Note that LNG = "local network gateway".
!       In Azure network resource, a local network gateway defines the on-premises
!       network properties (address prefixes, VPN device IP, BGP ASN, etc.)
!
object-group network <LNGName>
 description On-Premises network <LNGName> prefixes
 network-object 10.51.0.0 255.255.0.0
 network-object 10.52.0.0 255.255.0.0
exit
!
!     > Specify the access-list between the Azure VNet and your on-premises network.
!       This access list defines the IPsec SA traffic selectors.
!
access-list Azure-<VNetName>-acl extended permit ip object-group <LNGName> object-group Azure-<VNetName>
!
!     > No NAT required between the on-premises network and Azure VNet
!
nat (inside,outside) source static <LNGName> <LNGName> destination static Azure-<VNetName> Azure-<VNetName>
!
! ==> IKEv2 configuration
!
!     > General IKEv2 configuration - enable IKEv2 for VPN
!
group-policy DfltGrpPolicy attributes
 vpn-tunnel-protocol ikev1 ikev2
exit
!
crypto isakmp identity address
crypto ikev2 enable outside
!
!     > Define IKEv2 Phase 1/Main Mode policy
!       - Make sure the policy number is not used
!       - integrity and prf must be the same
!       - DH group 14 and above require ASA version 9.x.
!
crypto ikev2 policy 1
 encryption       aes-256
 integrity        sha384
 prf              sha384
 group            24
 lifetime seconds 86400
exit
!
!     > Set connection type and pre-shared key
!
tunnel-group <Azure_Gateway_Public_IP> type ipsec-l2l
tunnel-group <Azure_Gateway_Public_IP> ipsec-attributes
 ikev2 remote-authentication pre-shared-key <Pre_Shared_Key> 
 ikev2 local-authentication  pre-shared-key <Pre_Shared_Key> 
exit
!
! ==> IPsec configuration
!
!     > IKEv2 Phase 2/Quick Mode proposal
!       - AES-GCM and SHA-2 requires ASA version 9.x on newer ASA models. ASA
!         5505, 5510, 5520, 5540, 5550, 5580 are not supported.
!       - ESP integrity must be null if AES-GCM is configured as ESP encryption
!
crypto ipsec ikev2 ipsec-proposal AES-256
 protocol esp encryption aes-256
 protocol esp integrity  sha-1
exit
!
!     > Set access list & traffic selectors, PFS, IPsec protposal, SA lifetime
!       - This sample uses "Azure-<VNetName>-map" as the crypto map name
!       - ASA supports only one crypto map per interface, if you already have
!         an existing crypto map assigned to your outside interface, you must use
!         the same crypto map name, but with a different sequence number for
!         this policy
!       - "match address" policy uses the access-list "Azure-<VNetName>-acl" defined 
!         previously
!       - "ipsec-proposal" uses the proposal "AES-256" defined previously 
!       - PFS groups 14 and beyond requires ASA version 9.x.
!
crypto map Azure-<VNetName>-map 1 match address Azure-<VNetName>-acl
crypto map Azure-<VNetName>-map 1 set pfs group24
crypto map Azure-<VNetName>-map 1 set peer <Azure_Gateway_Public_IP>
crypto map Azure-<VNetName>-map 1 set ikev2 ipsec-proposal AES-256
crypto map Azure-<VNetName>-map 1 set security-association lifetime seconds 7200
crypto map Azure-<VNetName>-map interface outside
!
! ==> Set TCP MSS to 1350
!
sysopt connection tcpmss 1350
!
```

## <a name="simple-debugging-commands"></a>シンプルなデバッグ コマンド

デバッグ用途の ASA コマンドをいくつか紹介します。

1. IPsec や IKE SA を表示する
    - "show crypto ipsec sa"
    - "show crypto ikev2 sa"
2. デバッグ モードに入る - コンソールのノイズが非常に多くなる場合があります
    - "debug crypto ikev2 platform <level>"
    - "debug crypto ikev2 protocol <level>"
3. 現在の構成の一覧を表示する
    - "show run" - デバイスの現在の構成を表示します。各種サブ コマンドを使用して構成の特定の部分の一覧を表示できます。 例: "show run crypto"、"show run access-list"、"show run tunnel-group"、他。


## <a name="next-steps"></a>次のステップ
アクティブ/アクティブのクロスプレミス接続と VNet 間接続を構成する手順については、[クロスプレミス接続と VNet 間接続のアクティブ/アクティブ VPN Gateway の構成](vpn-gateway-activeactive-rm-powershell.md)に関するページを参照してください。


