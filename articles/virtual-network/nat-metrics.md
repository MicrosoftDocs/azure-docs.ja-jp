---
title: Azure Virtual Network NAT のメトリックとアラート
titleSuffix: Azure Virtual Network
description: Virtual Network NAT で使用できる Azure Monitor のメトリックとアラートについて説明します。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: nat
Customer intent: As an IT administrator, I want to understand available Azure Monitor metrics and alerts for Virtual Network NAT.
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2020
ms.author: allensu
ms.openlocfilehash: e3c47a60a6cda074eba7b5c3292577c29f50c2ab
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "87424053"
---
# <a name="azure-virtual-network-nat-metrics"></a>Azure Virtual Network NAT のメトリック

Azure Virtual Network NAT ゲートウェイ リソースは、多次元メトリックを提供します。 これらのメトリックを使用して処理状況を監視したり、[トラブルシューティング](troubleshoot-nat.md)を行ったりすることができます。  アラートは、SNAT の枯渇などの重大な問題に対して構成できます。

<p align="center">
  <img src="media/nat-overview/flow-direction1.svg" alt="Figure depicts a NAT gateway resource that consumes all IP addresses for a public IP prefix and directs that traffic to and from two subnets of virtual machines and a virtual machine scale set." width="256" title="インターネットへのアウトバウンド接続のための Virtual Network NAT">
</p>

*図:インターネットへのアウトバウンド接続のための Virtual Network NAT*

## <a name="metrics"></a>メトリック

NAT ゲートウェイ リソースは、Azure Monitor の次の多次元メトリックを提供します。

| メトリック | 説明 | 推奨される集計 | Dimensions |
|---|---|---|---|
| バイト | 受信および送信で処理されたバイト数 | SUM | 方向 (In、Out)、プロトコル (6 TCP、17 UDP) |
| パケット | 受信および送信で処理されたパケット数 | SUM | 方向 (In、Out)、プロトコル (6 TCP、17 UDP) |
| Dropped packets (ドロップされたパケット数) | NAT ゲートウェイによってドロップされたパケット数 | SUM | / |
| SNAT Connection Count (SNAT 接続数) | 間隔ごとの状態遷移 | SUM | 接続状態、プロトコル (6 TCP、17 UDP) |
| Total SNAT connection count (SNAT 接続数の合計) | 現在アクティブな SNAT 接続数 (使用中の SNAT ポート数) | SUM | プロトコル (6 TCP、17 UDP) |


## <a name="alerts"></a>警告

メトリックのアラートは、前述の[メトリック](#metrics)ごとに Azure Monitor で構成できます。

## <a name="limitations"></a>制限事項

Resource Health はサポートされていません。

## <a name="next-steps"></a>次のステップ

* [Virtual Network NAT](nat-overview.md) について学習する。
* [NAT ゲートウェイ リソース](nat-gateway-resource.md)について学習する
* [Azure Monitor](../azure-monitor/overview.md) について学習する
* [NAT ゲートウェイ リソースのトラブルシューティング](troubleshoot-nat.md)について学習する。
* [UserVoice で Virtual Network NAT の新機能の構築を提案する](https://aka.ms/natuservoice)。


