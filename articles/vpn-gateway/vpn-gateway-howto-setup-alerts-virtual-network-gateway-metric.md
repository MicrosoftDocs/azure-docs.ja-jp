---
title: Azure VPN Gateway メトリックにアラートを設定する
description: Azure portal を使用し、仮想ネットワーク VPN ゲートウェイのメトリックに基づいて Azure Monitor のアラートを設定する方法について説明します。
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 09/03/2020
ms.author: alzam
ms.openlocfilehash: 05fbc5675d6ee3b6720d9db9e07e7010cf1d9172
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89435659"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>VPN Gateway メトリックにアラートを設定する

この記事では、Azure VPN Gateway メトリックにアラートを設定する方法について説明します。 Azure Monitor では、Azure リソースのアラートを設定するための機能を提供しています。 "VPN" 型の仮想ネットワーク ゲートウェイにアラートを設定できます。


|**メトリック**   | **単位** | **細分性** | **説明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| バイト/秒  | 5 分| ゲートウェイ上のすべてのサイト対サイト接続の帯域幅合計使用率の平均。     |
|**P2SBandwidth**| バイト/秒  | 1 分  | ゲートウェイ上のすべてのポイント対サイト接続の帯域幅合計使用率の平均。    |
|**P2SConnectionCount**| Count  | 1 分  | ゲートウェイ上のポイント対サイト接続の数。   |
|**TunnelAverageBandwidth** | バイト/秒    | 5 分  | ゲートウェイに作成されたトンネルの帯域幅使用率の平均。 |
|**TunnelEgressBytes** | バイト | 5 分 | ゲートウェイに作成されたトンネルの発信トラフィック。   |
|**TunnelEgressPackets** | Count | 5 分 | ゲートウェイに作成されたトンネルの発信パケット数。   |
|**TunnelEgressPacketDropTSMismatch** | Count | 5 分 | トラフィック セレクターの不一致に原因があるトンネルでドロップされた発信パケットの数。 |
|**TunnelIngressBytes** | バイト | 5 分 | ゲートウェイに作成されたトンネルの着信トラフィック。   |
|**TunnelIngressPackets** | Count | 5 分 | ゲートウェイに作成されたトンネルの着信パケットの数。   |
|**TunnelIngressPacketDropTSMismatch** | Count | 5 分 | トラフィック セレクターの不一致に原因があるトンネルでドロップされた着信パケットの数。 |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>Azure portal を使用して、メトリックに基づいた Azure Monitor のアラートを設定します

次の手順の例では、ゲートウェイに次のアラートを作成します。

- **メトリック:** TunnelAverageBandwidth
- **条件:** 帯域幅 > 10 バイト/秒
- **ウィンドウ:** 5 分
- **アラート アクション:** Email



1. 仮想ネットワーク ゲートウェイのリソースに移動し、 **[監視]** タブから **[アラート]** を選択します。次に新しいアラート ルールを作成するか、または既存のアラート ルールを編集します。

   ![アラート ルールを作成するための選択](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "作成")

2. リソースとして VPN ゲートウェイを選択します。

   ![[選択] ボタンとリソースの一覧内の VPN ゲートウェイ](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "Select")

3. アラート用に構成するメトリックを選択します。

   ![メトリックの一覧内の選択されたメトリック](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "Select")
4. シグナル ロジックを構成します。 これには 3 つのコンポーネントがあります。

    a. **ディメンション**: メトリックにディメンションがある場合、特定のディメンション値を選択して、アラートがそのディメンションのデータのみを評価するようにできます。 これらは省略可能です。

    b. **条件**: これは、メトリック値を評価する操作です。

    c. **Time**: メトリック データの細分性と、アラートを評価する期間を指定します。

   ![シグナル ロジックの構成の詳細](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. 構成済みのルールを表示するには、 **[アラート ルールの管理]** を選択します。

   ![アラート ルールを管理するためのボタン](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Select")

## <a name="next-steps"></a>次のステップ

トンネル リソース ログにアラートを構成するには、[VPN Gateway リソース ログのアラートの設定](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)に関するページを参照してください。
