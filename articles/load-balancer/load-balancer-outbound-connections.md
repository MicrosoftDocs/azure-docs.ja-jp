---
title: "Azure の送信用接続の詳細 | Microsoft Docs"
description: "この記事では、Azure によって、パブリック インターネット サービスと VM がどのように通信するかを説明します。"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
ms.assetid: 5f666f2a-3a63-405a-abcd-b2e34d40e001
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: f02e17bb413f250fc4d980c62cfb46bc5359f7fb
ms.lasthandoff: 03/29/2017

---

# <a name="understanding-outbound-connections-in-azure"></a>Azure の送信用接続の詳細

Azure の仮想マシン (VM) は、パブリック IP アドレス空間で Azure 外部のエンドポイントと通信できます。 VM がパブリック IP アドレス空間の宛先への送信フローを開始すると、Azure は VM のプライベート IP アドレスをパブリック IP アドレスにマッピングし、戻りトラフィックが VM に届くようにします。

Azure では、送信接続を実現するために 3 つの異なる方法が提供されます。 それぞれの方法には独自の機能と制約があります。 各方法をよく調べて、ニーズに合う方法を選択してください。

| シナリオ | メソッド | 注 |
| --- | --- | --- |
| スタンドアロン VM (ロード バランサーなし、インスタンス レベルのパブリック IP アドレスなし) |既定の SNAT |Azure は SNAT のパブリック IP アドレスを関連付けます。 |
| 負荷分散 VM (VM にインスタンス レベルのパブリック IP アドレスなし) |ロード バランサーを使用する SNAT |Azure は SNAT でロード バランサーのパブリック IP を使用します。 |
| インスタンス レベル パブリック IP アドレスを含む VM (ロード バランサーあり、またはなし) |SNAT は使用されません。 |Azure は VM に割り当てられたパブリック IP を使用します。 |

VM がパブリック IP アドレス空間で Azure の外部のエンドポイントと通信しないようにするには、ネットワーク セキュリティ グループ (NSG) を使用してアクセスをブロックできます。 NSG の使用方法については、「[パブリック接続の防止](#preventing-public-connectivity)」を参照してください。

## <a name="standalone-vm-with-no-instance-level-public-ip-address"></a>インスタンス レベルのパブリック IP アドレスなしのスタンドアロン VM

このシナリオでは、VM は Azure Load Balancer プールに含まれず、インスタンス レベルのパブリック IP (ILPIP) アドレスは割り当てられていません。 VM が送信フローを作成すると、Azure が、送信フローのプライベート ソース IP アドレスをパブリック ソース IP アドレスに変換します。 この送信フローで使用されるパブリック IP アドレスは構成不可能であり、サブスクリプションのパブリック IP リソースの制限に対してカウントされません。 Azure は Source Network Address Translation (SNAT) を使用してこの機能を実行します。 パブリック IP アドレスの一時ポートを使用して、VM から送信される個々のフローが区別されます。 フローが作成されると、SNAT は一時ポートを動的に割り当てます。 ここでは、SNAT で使用される一時ポートを SNAT ポートと呼びます。

SNAT ポートは有限のリソースであり、不足する可能性があります。 使用方法を理解することが大切です。 1 つの SNAT ポートは、1 つの宛先 IP アドレスへの 1 つのフローで消費されます。 同じ宛先 IP アドレスへの複数のフローでは、各フローが 1 つの SNAT ポートを消費します。 これにより、同じパブリック IP アドレスから同じ宛先 IP アドレスに送られる複数のフローが一意であることが保証されます。 別の宛先 IP アドレスへの複数のフローは、宛先ごとに 1 つの SNAT ポートを消費します。 宛先 IP アドレスによってフローが一意になります。

[ロード バランサーの Log Analytics](load-balancer-monitor-log.md) と、[SNAT ポート不足メッセージを監視するためにアラート イベント ログ](load-balancer-monitor-log.md#alert-event-log)を使用できます。 SNAT ポート リソースがなくなると、既存のフローによって SNAT ポートが解放されない限り、送信フローは失敗します。 ロード バランサーは、SNAT ポートを再利用するために 4 分間のアイドル タイムアウトを使用します。

## <a name="load-balanced-vm-with-no-instance-level-public-ip-address"></a>インスタンス レベルのパブリック IP アドレスなしの負荷分散 VM

このシナリオでは、VM は Azure Load Balancer プールに含まれます。 VM にパブリック IP アドレスは割り当てられていません。 負荷分散 VM が送信フローを作成すると、Azure が、送信フローのプライベート ソース IP アドレスをパブリック ロード バランサー フロントエンドのパブリック IP アドレスに変換します。 Azure は Source Network Address Translation (SNAT) を使用してこの機能を実行します。 ロード バランサーのパブリック IP アドレスの一時ポートを使用して、VM から送信される個々のフローが区別されます。 送信フローが作成されると、SNAT は一時ポートを動的に割り当てます。 ここでは、SNAT で使用される一時ポートを SNAT ポートと呼びます。

SNAT ポートは有限のリソースであり、不足する可能性があります。 使用方法を理解することが大切です。 1 つの SNAT ポートは、1 つの宛先 IP アドレスへの 1 つのフローで消費されます。 同じ宛先 IP アドレスへの複数のフローでは、各フローが 1 つの SNAT ポートを消費します。 これにより、同じパブリック IP アドレスから同じ宛先 IP アドレスに送られる複数のフローが一意であることが保証されます。 別の宛先 IP アドレスへの複数のフローは、宛先ごとに 1 つの SNAT ポートを消費します。 宛先 IP アドレスによってフローが一意になります。

[ロード バランサーの Log Analytics](load-balancer-monitor-log.md) と、[SNAT ポート不足メッセージを監視するためにアラート イベント ログ](load-balancer-monitor-log.md#alert-event-log)を使用できます。 SNAT ポート リソースがなくなると、既存のフローによって SNAT ポートが解放されない限り、送信フローは失敗します。 ロード バランサーは、SNAT ポートを再利用するために 4 分間のアイドル タイムアウトを使用します。

## <a name="vm-with-an-instance-level-public-ip-address-with-or-without-load-balancer"></a>インスタンス レベル パブリック IP アドレスを含む VM (ロード バランサーあり、またはなし)

このシナリオでは、VM にはインスタンス レベルのパブリック IP (ILPIP) が割り当てられています。 VM が負荷分散されているかどうかは関係ありません。 ILPIP が使用されるとき、Source Network Address Translation (SNAT) は使用されません。 VM はすべての送信フローで ILPIP を使用します。 アプリケーションが多数の送信フローを開始したために SNAT 不足が発生した場合は、SNAT の制約を回避するために ILPIP を割り当てることを検討してください。

## <a name="discovering-the-public-ip-used-by-a-given-vm"></a>所定の VM で使用されるパブリック IP の検出

送信接続のパブリック ソース IP アドレスを判別する方法は多数あります。 OpenDNS によって提供されるサービスで、VM のパブリック IP アドレスを表示できます。 nslookup コマンドを使用して、名前 myip.opendns.com に関する DNS クエリを OpenDNS Resolver に送信できます。 このサービスは、クエリの送信に使用されたソース IP アドレスを返します。 VM から次のクエリを実行すると、その VM で使用されるパブリック IP が応答として返されます。

    nslookup myip.opendns.com resolver1.opendns.com

## <a name="preventing-public-connectivity"></a>パブリック接続の防止

VM による送信フローの作成が望ましくない状況や、送信フローで接続できる宛先を管理する要件がある場合があります。 このような場合に、[ネットワーク セキュリティ グループ (NSG)](../virtual-network/virtual-networks-nsg.md) を使用して、VM が接続できる宛先を管理できます。 負荷分散された VM に NSG を適用するときは、[既定のタグ](../virtual-network/virtual-networks-nsg.md#default-tags)と[既定のルール](../virtual-network/virtual-networks-nsg.md#default-rules)に注意する必要があります。

VM が Azure Load Balancer からのヘルス プローブ要求を受け取ることができるようにしておいてください。 NSG が AZURE_LOADBALANCER 既定タグからのヘルス プローブ要求をブロックすると、VM のヘルス プローブが失敗して VM が停止しているとマークされます。 ロード バランサーはその VM への新しいフローの送信を停止します。

## <a name="limitations"></a>制限事項

保証されていませんが、現時点で使用可能な SNAT ポートの最大数は 64,511 (65,535 から特権ポート 1024 を差し引いた数) です。  これがそのまま接続数に変換されるわけではありません。SNAT ポートがどのタイミングでいくつ割り当てられるかについて、またこの有限なリソースを管理する方法については、上記を参照してください。

