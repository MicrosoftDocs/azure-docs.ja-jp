---
title: Cisco ASA デバイスを Azure VPN ゲートウェイに接続するためのサンプル構成
description: この記事では、Cisco ASA デバイスを Azure VPN ゲートウェイに接続するためのサンプル構成を紹介します。
services: vpn-gateway
author: yushwang
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: yushwang
ms.openlocfilehash: 82e00e0b519040c57fdfd798d8992086c61e5481
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89426177"
---
# <a name="sample-configuration-cisco-asa-device-ikev2no-bgp"></a>サンプル構成: Cisco ASA デバイス (IKEv2/BGP なし)
この記事では、Cisco Adaptive Security Appliance (ASA) デバイスを Azure VPN ゲートウェイに接続するためのサンプル構成を紹介します。 この例は、ボーダー ゲートウェイ プロトコル (BGP) を使用せずに IKEv2 を実行する Cisco ASA デバイスに適用されます。 

## <a name="device-at-a-glance"></a>デバイスの概略

* デバイスの製造元:**Cisco**
* デバイスのモデル:**ASA**           
* ターゲット バージョン:**8.4 以降**
* テスト済みのモデル:**ASA 5505**
* テスト済みのバージョン:**9.2**             
* IKE のバージョン:**IKEv2**                  
* BGP:**いいえ**      
* Azure VPN ゲートウェイの種類:**ルートベースの VPN ゲートウェイ**

> [!NOTE]
> このサンプル構成では、Cisco ASA デバイスが Azure の **ルートベース** の VPN ゲートウェイに接続されます。 この接続では、[こちらの記事](vpn-gateway-connect-multiple-policybased-rm-ps.md)で説明したカスタム IPsec/IKE ポリシーと **UsePolicyBasedTrafficSelectors** オプションが使用されます。
>
> このサンプルで使用する ASA デバイスの **IKEv2** ポリシーは、VTI ベースではなくアクセスリストベースの構成であることが必要です。 オンプレミスの VPN デバイスで IKEv2 ポリシーがサポートされることを、VPN デバイス ベンダーの仕様で確認してください。


## <a name="vpn-device-requirements"></a>VPN デバイスの要件
Azure VPN ゲートウェイでは、標準の IPsec/IKE プロトコル スイートを使用してサイト間 (S2S) VPN トンネルが確立されます。 Azure VPN ゲートウェイで使用する IPsec/IKE プロトコル パラメーターと既定の暗号アルゴリズムについて詳しくは、[VPN デバイス](vpn-gateway-about-vpn-devices.md)に関する記事を参照してください。

> [!NOTE]
> [暗号化の要件](vpn-gateway-about-compliance-crypto.md)に関する記事の説明に従って、特定の接続を対象に、暗号アルゴリズムとキーの強度の組み合わせを具体的に指定することもできます。 アルゴリズムとキーの強度の組み合わせを具体的に指定する場合は必ず、対応する仕様をご利用の VPN デバイスで使用してください。

## <a name="single-vpn-tunnel"></a>単一の VPN トンネル
この構成は、Azure VPN ゲートウェイとオンプレミスの VPN デバイスの間にある単一の S2S VPN トンネルで構成されます。 VPN トンネル上に BGP を構成することもできます。

![単一の S2S VPN トンネル](./media/vpn-gateway-3rdparty-device-config-cisco-asa/singletunnel.png)

Azure の構成を作成する具体的な手順については、[単一の VPN トンネルの設定](vpn-gateway-3rdparty-device-config-overview.md#singletunnel)に関する記事を参照してください。

### <a name="virtual-network-and-vpn-gateway-information"></a>仮想ネットワークと VPN ゲートウェイの情報
このセクションでは、サンプルのパラメーターの一覧を示します。

| **パラメーター**                | **Value**                    |
| ---                          | ---                          |
| 仮想ネットワーク アドレス プレフィックス        | 10.11.0.0/16<br>10.12.0.0/16 |
| Azure VPN Gateway IP         | Azure_Gateway_Public_IP      |
| オンプレミス アドレス プレフィックス | 10.51.0.0/16<br>10.52.0.0/16 |
| オンプレミス VPN デバイス IP    | OnPrem_Device_Public_IP     |
| * 仮想ネットワーク BGP ASN                | 65010                        |
| * Azure BGP ピア IP           | 10.12.255.30                 |
| * オンプレミス BGP ASN         | 65050                        |
| * オンプレミス BGP ピア IP     | 10.52.255.254                |
|                              |                              |

\* BGP 専用のオプション パラメーター。

### <a name="ipsecike-policy-and-parameters"></a>IPsec/IKE のポリシーとパラメーター
以下の表に、サンプルで使用されている IPsec/IKE のアルゴリズムとパラメーターの一覧を示します。 これらのアルゴリズムが VPN デバイスのモデルとファームウェア バージョンでサポートされていることを、ご使用の VPN デバイスの仕様で確認してください。

| **IPsec/IKEv2**  | **Value**                            |
| ---              | ---                                  |
| IKEv2 暗号化 | AES256                               |
| IKEv2 整合性  | SHA384                               |
| DH グループ         | DHGroup24                            |
| * IPsec 暗号化 | AES256                               |
| * IPsec 整合性  | SHA1                                 |
| PFS グループ        | PFS24                                |
| QM SA の有効期間   | 7,200 秒                         |
| トラフィック セレクター | UsePolicyBasedTrafficSelectors $True |
| 事前共有キー   | PreSharedKey                         |
|                  |                                      |

\* 一部のデバイスでは、IPsec 暗号化アルゴリズムが AES-GCM である場合に IPsec 整合性の値を null にする必要があります。

### <a name="asa-device-support"></a>ASA デバイスのサポート

* IKEv2 のサポートには ASA バージョン 8.4 以降が必要です。

* DH グループおよび PFS グループのグループ 5 以降のサポートには ASA バージョン 9.x が必要です。

* AES-GCM による IPsec の暗号化と、SHA-256、SHA-384、SHA-512 との IPsec の整合性のサポートには、ASA パージョン 9.x が必要です。 より新しい ASA デバイスには、このサポート要件が当てはまります。 公開時点では、ASA モデル 5505、5510、5520、5540、5550、および 5580 は、これらのアルゴリズムはサポートされていません。 これらのアルゴリズムが VPN デバイスのモデルとファームウェア バージョンでサポートされていることを、ご使用の VPN デバイスの仕様で確認してください。


### <a name="sample-device-configuration"></a>デバイスのサンプル構成
スクリプトのサンプルは、前出のセクションで説明した構成およびパラメーターに基づいたものです。 S2S VPN トンネルの構成は、次の部分で構成されます。

1. インターフェイスとルート
2. アクセス リスト
3. IKE ポリシーとパラメーター (フェーズ 1 またはメイン モード)
4. IPsec ポリシーとパラメーター (フェーズ 2 またはクイック モード)
5. その他のパラメーター (TCP MSS クランプなど)

> [!IMPORTANT]
> サンプル スクリプトを使用する前に、次の手順を完了する必要があります。 スクリプト内のプレースホルダーの値は、実際の構成に使用するデバイスの設定に置き換えてください。

* インターフェイス内外のインターフェイスの構成を指定します。
* 内部/プライベートおよび外部/パブリック ネットワークのルートを特定します。
* すべての名前とポリシー番号がデバイスで一意であることを確認します。
* 暗号アルゴリズムがデバイスでサポートされていることを確認します。
* 次の **プレースホルダーの値** を実際の構成の値に置き換えます。
  - 外部インターフェイスの名前: **outside**
  - **Azure_Gateway_Public_IP**
  - **OnPrem_Device_Public_IP**
  - IKE:**Pre_Shared_Key**
  - 仮想ネットワークおよびローカル ネットワーク ゲートウェイの名前:**VNetName** と **LNGName**
  - 仮想ネットワークおよびオンプレミスのネットワーク アドレス **プレフィックス**
  - 適切な **ネットマスク**

#### <a name="sample-script"></a>サンプル スクリプト

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
!     > Set access list & traffic selectors, PFS, IPsec proposal, SA lifetime
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

デバッグには、次の ASA コマンドを使用します。

* IPsec または IKE のセキュリティ アソシエーション (SA) を表示します。
    ```
    show crypto ipsec sa
    show crypto ikev2 sa
    ```

* デバッグ モードに切り替えます。
    ```
    debug crypto ikev2 platform <level>
    debug crypto ikev2 protocol <level>
    ```
    `debug` コマンドを使用すると、重要な出力をコンソールで生成することができます。

* デバイスの現在の構成を表示します。
    ```
    show run
    ```
    `show` サブコマンドを使用すると、デバイス構成の特定の部分を一覧表示できます。
    ```
    show run crypto
    show run access-list
    show run tunnel-group
    ```

## <a name="next-steps"></a>次のステップ
アクティブ/アクティブのクロスプレミス接続と VNet 間接続を構成する方法については、[アクティブ/アクティブの VPN ゲートウェイの構成](vpn-gateway-activeactive-rm-powershell.md)に関するページを参照してください。
