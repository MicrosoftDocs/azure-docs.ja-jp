---
title: ルーター構成サンプル - Azure ExpressRoute | Microsoft Docs
description: このページでは、Cisco と Juniper のルーター構成のサンプルを示します。
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 12/06/2018
ms.author: cherylmc
ms.custom: seodec18
ms.openlocfilehash: 2d7fb060896de8df266489451a11ba343760c747
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2018
ms.locfileid: "53079961"
---
# <a name="router-configuration-samples-to-set-up-and-manage-routing"></a>ルーティングをセットアップして管理するためのルーター構成のサンプル
このページでは、ExpressRoute を使用する場合の、Cisco IOS-XE と Juniper MX シリーズ ルーターのインターフェイスとルーティング構成のサンプルを示します。 これらはガイダンスとしてのみ使用することを目的としたサンプルであり、現状のまま使用することはできません。 ベンダーと協力して、ネットワークに適した構成を考えてください。 

> [!IMPORTANT]
> このページのサンプルは、ガイダンスとしてのみ使用することを目的としています。 ベンダーの販売/技術チームおよび自社のネットワーク チームと協力して、ニーズに対応する適切な構成を考える必要があります。 Microsoft では、このページに示す構成に関連する問題には対応できません。 サポートの問題については、デバイス ベンダーに問い合わせる必要があります。
> 
> 

## <a name="mtu-and-tcp-mss-settings-on-router-interfaces"></a>ルーターのインターフェイスの MTU および TCP MSS 設定
* ExpressRoute インターフェイスの MTU は 1500 で、これは、ルーターでの Ethernet インターフェイスの一般的な既定の MTU です。 これとは異なる MTU がお使いのルーターに設定されていない限り、ルーター インターフェイスで値を指定する必要はありません。
* Azure VPN Gateway とは異なり、ExpressRoute 回線の TCP MSS を指定する必要はありません。

以下のルーター構成サンプルは、すべてのピアリングを対象としています。 詳細については、「[ExpressRoute のピアリング](expressroute-circuit-peerings.md)」および「[ExpressRoute のルーティングの要件](expressroute-routing.md)」をご覧ください。


## <a name="cisco-ios-xe-based-routers"></a>Cisco IOS-XE ベースのルーター
このセクションのサンプルは、IOS-XE OS ファミリを実行するルーターを対象としています。

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1.インターフェイスとサブインターフェイスの構成
マイクロソフトに接続するすべてのルーターには、ピアリングごとにサブインターフェイスが必要となります。 サブインターフェイスの識別には、VLAN ID を使用するか、または VLAN ID と IP アドレスの組み合わせを使用できます。

**Dot1Q インターフェイスの定義**

これはサブインターフェイスの定義サンプルです。単一の VLAN ID を持つサブインターフェイスを想定しています。 VLAN ID はピアリングごとに一意となります。 IPv4 アドレスの最終オクテットは常に奇数です。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <VLAN_ID>
     ip address <IPv4_Address><Subnet_Mask>

**QinQ インターフェイスの定義**

これはサブインターフェイスの定義サンプルです。2 つの VLAN ID を持つサブインターフェイスを想定しています。 外側 VLAN ID (s-tag) が使用されている場合、すべてのピアリングでそのまま維持されます。 内側 VLAN ID (c-tag) はピアリングごとに一意となります。 IPv4 アドレスの最終オクテットは常に奇数です。

    interface GigabitEthernet<Interface_Number>.<Number>
     encapsulation dot1Q <s-tag> seconddot1Q <c-tag>
     ip address <IPv4_Address><Subnet_Mask>

### <a name="2-setting-up-ebgp-sessions"></a>2.eBGP セッションの設定
すべてのピアリングについて、マイクロソフトとの BGP セッションを設定する必要があります。 マイクロソフトとの BGP セッションは、以下のサンプルで設定できます。 サブインターフェイスに使用されている IPv4 アドレスが a.b.c.d である場合、BGP 近隣ノード (マイクロソフト) の IP アドレスは a.b.c.d+1 になります。 BGP 近隣ノードの IPv4 アドレスの最終オクテットは常に偶数です。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
     neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>手順 3.BGP セッションでアドバタイズするプレフィックスの設定
特定のプレフィックスをマイクロソフトにアドバタイズするようにルーターを構成することができます。 その方法を紹介したのが以下のサンプルです。

    router bgp <Customer_ASN>
     bgp log-neighbor-changes
     neighbor <IP#2_used_by_Azure> remote-as 12076
     !        
     address-family ipv4
      network <Prefix_to_be_advertised> mask <Subnet_mask>
      neighbor <IP#2_used_by_Azure> activate
     exit-address-family
    !

### <a name="4-route-maps"></a>4.ルート マップ
ネットワークに伝播されるプレフィックスは、ルート マップとプレフィックス リストを使用してフィルタリングできます。 このタスクは、以下のサンプルで実現できます。 適切なプレフィックス リストが設定されていることを確認してください。

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


## <a name="juniper-mx-series-routers"></a>Juniper MX シリーズ ルーター
このセクションのサンプルは、Juniper MX シリーズのルーターを対象としています。

### <a name="1-configuring-interfaces-and-sub-interfaces"></a>1.インターフェイスとサブインターフェイスの構成

**Dot1Q インターフェイスの定義**

これはサブインターフェイスの定義サンプルです。単一の VLAN ID を持つサブインターフェイスを想定しています。 VLAN ID はピアリングごとに一意となります。 IPv4 アドレスの最終オクテットは常に奇数です。

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


**QinQ インターフェイスの定義**

これはサブインターフェイスの定義サンプルです。2 つの VLAN ID を持つサブインターフェイスを想定しています。 外側 VLAN ID (s-tag) が使用されている場合、すべてのピアリングでそのまま維持されます。 内側 VLAN ID (c-tag) はピアリングごとに一意となります。 IPv4 アドレスの最終オクテットは常に奇数です。

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

### <a name="2-setting-up-ebgp-sessions"></a>2.eBGP セッションの設定
すべてのピアリングについて、マイクロソフトとの BGP セッションを設定する必要があります。 マイクロソフトとの BGP セッションは、以下のサンプルで設定できます。 サブインターフェイスに使用されている IPv4 アドレスが a.b.c.d である場合、BGP 近隣ノード (マイクロソフト) の IP アドレスは a.b.c.d+1 になります。 BGP 近隣ノードの IPv4 アドレスの最終オクテットは常に偶数です。

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

### <a name="3-setting-up-prefixes-to-be-advertised-over-the-bgp-session"></a>手順 3.BGP セッションでアドバタイズするプレフィックスの設定
特定のプレフィックスをマイクロソフトにアドバタイズするようにルーターを構成することができます。 その方法を紹介したのが以下のサンプルです。

    policy-options {
        policy-statement <Policy_Name> {
            term 1 {
                from protocol OSPF;
        route-filter <Prefix_to_be_advertised/Subnet_Mask> exact;
                then {
                    accept;
                }
            }
        }
    }
    protocols {
        bgp { 
            group <Group_Name> { 
                export <Policy_Name>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }


### <a name="4-route-maps"></a>4.ルート マップ
ネットワークに伝播されるプレフィックスは、ルート マップとプレフィックス リストを使用してフィルタリングできます。 このタスクは、以下のサンプルで実現できます。 適切なプレフィックス リストが設定されていることを確認してください。

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
                export <Policy_Name>
                import <MS_Prefixes_Inbound>
                peer-as 12076;              
                neighbor <IP#2_used_by_Azure>;
            }                               
        }                                   
    }

## <a name="next-steps"></a>次の手順
詳細については、 [ExpressRoute の FAQ](expressroute-faqs.md) を参照してください。

