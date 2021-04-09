---
title: 'Azure ExpressRoute: ルーター構成サンプル'
description: Cisco IOS-XE と Juniper MX シリーズ ルーターのこれらのインターフェイスとルーティング構成のサンプルは、Azure ExpressRoute を使用する場合の例として使用します。
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: article
ms.date: 03/26/2020
ms.author: duau
ms.openlocfilehash: 3bc850f02884ae0547c2ecf56a46a57a4e66a752
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89397408"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>ルーティングをセットアップして管理するためのルーター構成のサンプル
このページでは、Azure ExpressRoute を使用する場合の、Cisco IOS-XE と Juniper MX シリーズ ルーターのインターフェイスとルーティング構成のサンプルを示します。

> [!IMPORTANT]
> このページのサンプルは、ガイダンスとしてのみ使用することを目的としています。 ベンダーの販売 (または技術) チームおよび自社のネットワーク チームと協力して、ニーズに対応する適切な構成を見つける必要があります。 Microsoft では、このページに示す構成に関連する問題には対応できません。 サポートの問題については、デバイス ベンダーに問い合わせる必要があります。
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>ルーターのインターフェイスの MTU および TCP MSS 設定
ExpressRoute インターフェイスの最大転送単位 (MTU) は 1500 で、これは、ルーターでの Ethernet インターフェイスの一般的な既定の MTU です。 これとは異なる MTU がお使いのルーターに設定されていない限り、ルーター インターフェイスで値を指定する必要はありません。

Azure VPN ゲートウェイとは異なり、ExpressRoute 回線の TCP 最大セグメント サイズ (MSS) を指定する必要はありません。

この記事のルーター構成サンプルは、すべてのピアリングを対象としています。 詳細については、「[ExpressRoute のピアリング](expressroute-circuit-peerings.md)」および「[ExpressRoute のルーティングの要件](expressroute-routing.md)」をご覧ください。


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE ベースのルーター
このセクションのサンプルは、IOS-XE OS ファミリを実行するルーターを対象としています。

### <a name="configure-interfaces-and-subinterfaces"></a>インターフェイスとサブインターフェイスの構成
Microsoft に接続するすべてのルーターには、ピアリングごとにサブインターフェイスが必要となります。 サブインターフェイスの識別には、VLAN ID を使用するか、または VLAN ID と IP アドレスの組み合わせを使用できます。

**Dot1Q インターフェイスの定義**

これはサブインターフェイスの定義サンプルです。単一の VLAN ID を持つサブインターフェイスを想定しています。 VLAN ID はピアリングごとに一意となります。 IPv4 アドレスの最終オクテットは常に奇数です。

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <VLAN_ID>
 ip address <IPv4_Address><Subnet_Mask>
```

**QinQ インターフェイスの定義**

これはサブインターフェイスの定義サンプルです。2 つの VLAN ID を持つサブインターフェイスを想定しています。 外側 VLAN ID (s-tag) が使用されている場合、すべてのピアリングでそのまま維持されます。 内側 VLAN ID (c-tag) はピアリングごとに一意となります。 IPv4 アドレスの最終オクテットは常に奇数です。

```console
interface GigabitEthernet<Interface_Number>.<Number>
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>
```

### <a name="set-up-ebgp-sessions"></a>eBGP セッションの設定
すべてのピアリングについて、Microsoft との BGP セッションを設定する必要があります。 以下のサンプルを使用して BGP セッションを設定してください。 サブインターフェイスに使用されている IPv4 アドレスが a.b.c.d である場合、BGP 近隣ノード (Microsoft) の IP アドレスは a.b.c.d+1 になります。 BGP 近隣ノードの IPv4 アドレスの最終オクテットは常に偶数です。

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
 neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>BGP セッションでアドバタイズするプレフィックスの設定
次のサンプルを使用して、特定のプレフィックスを Microsoft にアドバタイズするようにルーターを構成します。

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
 exit-address-family
!
```

### <a name="route-maps"></a>ルート マップ
ネットワークに伝播されるプレフィックスは、ルート マップとプレフィックス リストを使用してフィルタリングします。 次のサンプルを参照して、適切なプレフィックス リストが設定されていることを確認してください。

```console
router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  network <Prefix_to_be_advertised> mask <Subnet_mask>
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> route-map <MS_Prefixes_Inbound> in
 exit-address-family
!
route-map <MS_Prefixes_Inbound> permit 10
 match ip address prefix-list <MS_Prefixes>
!
```

### <a name="configure-bfd"></a>BFD の構成

BFD の構成は 2 か所で行います。1 つはインターフェイス レベルで、もう 1 つは BGP レベルです。 次の例は、QinQ インターフェイス用です。 

```console
interface GigabitEthernet<Interface_Number>.<Number>
 bfd interval 300 min_rx 300 multiplier 3
 encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
 ip address <IPv4_Address><Subnet_Mask>

router bgp <Customer_ASN>
 bgp log-neighbor-changes
 neighbor <IP#2_used_by_Azure> remote-as 12076
 !
 address-family ipv4
  neighbor <IP#2_used_by_Azure> activate
  neighbor <IP#2_used_by_Azure> fall-over bfd
 exit-address-family
!
```


## <a name="juniper-mx-series-routers"></a>Juniper MX シリーズ ルーター
このセクションのサンプルは、Juniper MX シリーズのルーターを対象としています。

### <a name="configure-interfaces-and-subinterfaces"></a>インターフェイスとサブインターフェイスの構成

**Dot1Q インターフェイスの定義**

これはサブインターフェイスの定義サンプルです。単一の VLAN ID を持つサブインターフェイスを想定しています。 VLAN ID はピアリングごとに一意となります。 IPv4 アドレスの最終オクテットは常に奇数です。

```console
    interfaces {
        vlan-tagging;
        <Interface_Number> {
            unit <Number> {
                vlan-id <VLAN_ID>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }
            }
        }
    }
```


**QinQ インターフェイスの定義**

これはサブインターフェイスの定義サンプルです。2 つの VLAN ID を持つサブインターフェイスを想定しています。 外側 VLAN ID (s-tag) が使用されている場合、すべてのピアリングでそのまま維持されます。 内側 VLAN ID (c-tag) はピアリングごとに一意となります。 IPv4 アドレスの最終オクテットは常に奇数です。

```console
    interfaces {
        <Interface_Number> {
            flexible-vlan-tagging;
            unit <Number> {
                vlan-tags outer <S-tag> inner <C-tag>;
                family inet {
                    address <IPv4_Address/Subnet_Mask>;
                }                           
            }                               
        }                                   
    }                           
```

### <a name="set-up-ebgp-sessions"></a>eBGP セッションの設定
すべてのピアリングについて、Microsoft との BGP セッションを設定する必要があります。 以下のサンプルを使用して BGP セッションを設定してください。 サブインターフェイスに使用されている IPv4 アドレスが a.b.c.d である場合、BGP 近隣ノード (Microsoft) の IP アドレスは a.b.c.d+1 になります。 BGP 近隣ノードの IPv4 アドレスの最終オクテットは常に偶数です。

```console
    routing-options {
        autonomous-system <Customer_ASN>;
    }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="set-up-prefixes-to-be-advertised-over-the-bgp-session"></a>BGP セッションでアドバタイズするプレフィックスの設定
次のサンプルを使用して、特定のプレフィックスを Microsoft にアドバタイズするようにルーターを構成します。

```console
    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
                route-filter; 
                <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="route-policies"></a>ルーティング ポリシー
ネットワークに伝播されるプレフィックスは、ルート マップとプレフィックス リストを使用してフィルタリングできます。 次のサンプルを参照して、適切なプレフィックス リストが設定されていることを確認してください。

```console
    policy-options {
        prefix-list MS_Prefixes {
            <IP_Prefix_1/Subnet_Mask>;
            <IP_Prefix_2/Subnet_Mask>;
        }
        policy-statement <MS_Prefixes_Inbound> {
            term 1 {
                from {
                    prefix-list MS_Prefixes;
                }
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>;
                import <MS_Prefixes_Inbound>;
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }
```

### <a name="configure-bfd"></a>BFD の構成
BFD は、プロトコル BGP のセクションでのみ構成します。

```console
    protocols {
        bgp { 
            group <Group_Name> { 
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
                bfd-liveness-detection {
                       minimum-interval 3000;
                       multiplier 3;
                }
            }                               
        }                                   
    }
```

### <a name="configure-macsec"></a>MACsec の構成
MACSec の構成では、CAK (接続関連付けキー) と CKN (接続関連付けキー名) は、PowerShell コマンドを使用して構成された値と一致する必要があります。

```console
    security {
        macsec {
            connectivity-association <Connectivity_Association_Name> {
                cipher-suite gcm-aes-xpn-128;
                security-mode static-cak;
                pre-shared-key {
                    ckn <Connectivity_Association_Key_Name>;
                    cak <Connectivity_Association_Key>; ## SECRET-DATA
                }
            }
            interfaces {
                <Interface_Number> {
                    connectivity-association <Connectivity_Association_Name>;
                }
            }
        }
    }
```

## <a name="next-steps"></a>次のステップ
詳細については、 [ExpressRoute の FAQ](expressroute-faqs.md) を参照してください。



