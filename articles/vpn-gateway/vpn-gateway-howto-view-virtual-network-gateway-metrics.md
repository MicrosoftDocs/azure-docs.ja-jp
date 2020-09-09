---
title: Azure VPN Gateway メトリックを表示する
description: VPN Gateway メトリックを表示する手順
services: vpn-gateway
author: kumudD
ms.service: vpn-gateway
ms.topic: how-to
ms.date: 07/12/2020
ms.author: alzam
ms.openlocfilehash: b3a79b8101a55eaf401c20cb118be3b0796b7aca
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/20/2020
ms.locfileid: "86528252"
---
# <a name="view-vpn-gateway-metrics"></a>VPN Gateway メトリックを表示する

Azure Monitor を使用して、Azure VPN ゲートウェイを監視できます。 この記事では、ポータル経由で利用できるメトリックについて説明します。 メトリックは軽量で、ほぼリアルタイムのシナリオをサポートできるため、アラートや迅速な問題の検出に役立ちます。


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

## <a name="the-following-steps-help-you-locate-and-view-metrics"></a>次の手順に従い、メトリックを検索して表示できます。

1. ポータルで仮想ネットワーク ゲートウェイ リソースに移動します
2. **[概要]** を選択して、トンネル イングレスおよびエグレス総数の各メトリックを表示します。

   ![アラート ルールを作成するための選択](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/overview.png "表示")

3. 上記の一覧に示されている他のメトリックのいずれかを表示するには、 仮想ネットワーク ゲートウェイ リソースの下の **[メトリック]** セクションをクリックし、ドロップダウン リストからメトリックを選択します。

   ![[選択] ボタンとリソースの一覧内の VPN ゲートウェイ](./media/vpn-gateway-howto-view-virtual-network-gateway-metrics/metrics.png "Select")

## <a name="next-steps"></a>次のステップ

トンネル メトリックにアラートを構成するには、「[Set up alerts on VPN Gateway metrics](vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric.md)」(VPN Gateway メトリックにアラートを設定する) を参照してください。

トンネル リソース ログにアラートを構成するには、[VPN Gateway リソース ログのアラートの設定](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)に関するページを参照してください。
