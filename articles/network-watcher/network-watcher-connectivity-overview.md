---
title: Azure Network Watcher との接続のトラブルシューティングの概要 | Microsoft Docs
description: このページでは、Network Watcher の接続のトラブルシューティング機能の概要を説明します。
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/11/2017
ms.author: damendo
ms.openlocfilehash: 9c855fff9e5791b9c0cf870acfc6de53e7a700b2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97653998"
---
# <a name="introduction-to-connection-troubleshoot-in-azure-network-watcher"></a>Azure Network Watcher の接続のトラブルシューティングの概要

Network Watcher の接続のトラブルシューティング機能を使用すると、仮想マシンから仮想マシン (VM)、完全修飾ドメイン名 (FQDN)、URI、または IPv4 アドレスへの直接 TCP 接続を確認できます。 ネットワークのシナリオは複雑であり、ネットワーク セキュリティ グループ、ファイアウォール、ユーザー定義ルート、および Azure が提供するリソースを使用して実装されます。 構成が複雑であるため、接続に関する問題のトラブルシューティングは困難です。 Network Watcher を使用すると、接続に関する問題を検出するまでの時間を減らすのに役立ちます。 返される結果は、接続に関する問題がプラットフォームによるものか、ユーザーの構成が問題であるかの洞察を提供します。 接続は、[PowerShell](network-watcher-connectivity-powershell.md)、[Azure CLI](network-watcher-connectivity-cli.md)、および [REST API](network-watcher-connectivity-rest.md) を使用してチェックできます。

> [!IMPORTANT]
> 接続のトラブルシューティングを行うには、トラブルシューティングの実行に使用する VM に `AzureNetworkWatcherExtension` VM 拡張機能がインストールされている必要があります。 Windows VM への拡張機能のインストールについては、[Windows 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/extensions/network-watcher-windows.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関する記事をご覧ください。Linux VM の場合は、[Linux 用 Azure Network Watcher Agent 仮想マシン拡張機能](../virtual-machines/extensions/network-watcher-linux.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)に関する記事をご覧ください。 接続先のエンドポイントでは、拡張機能は必要ありません。

## <a name="response"></a>Response

次の表には、接続のトラブルシューティングの実行が完了したときに返されるプロパティを示します。

|プロパティ  |説明  |
|---------|---------|
|ConnectionStatus     | 接続チェックの状態。 結果は **Reachable** か **Unreachable** のいずれかです。        |
|AvgLatencyInMs     | 接続チェック中の平均待機時間 (ミリ秒)。 (チェックの状態が Reachable の場合にのみ表示されます)        |
|MinLatencyInMs     | 接続チェック中の最小待機時間 (ミリ秒)。 (チェックの状態が Reachable の場合にのみ表示されます)        |
|MaxLatencyInMs     | 接続チェック中の最大待機時間 (ミリ秒)。 (チェックの状態が Reachable の場合にのみ表示されます)        |
|ProbesSent     | チェック中に送信されたプローブの数。 最大値は 100 です。        |
|ProbesFailed     | チェック中に失敗したプローブの数。 最大値は 100 です。        |
|Hops     | 送信元から送信先へのホップ バイ ホップのパス。        |
|Hops[].Type     | リソースの種類。 値は、**Source**、**VirtualAppliance**、**VnetLocal**、**Internet** のいずれかです。        |
|Hops[].Id | ホップの一意識別子。|
|Hops[].Address | ホップの IP アドレス。|
|Hops[].ResourceId | ホップが Azure リソースの場合、そのホップのリソース ID。 インターネット リソースの場合、リソース ID は **Internet** です。 |
|Hops[].NextHopIds | 次に取得されるホップの一意識別子。|
|Hops[].Issues | そのホップのチェック中に発生した問題のコレクション。 問題がない場合、値は空になります。|
|Hops[].Issues[].Origin | 現在のホップで、問題が発生した場所。 次のいずれかの値になります。<br/> **Inbound** - 問題が前のホップから現在のホップへのリンク上にある<br/>**Outbound** - 問題が現在のホップから次のホップへのリンク上にある<br/>**Local** - 問題が現在のホップにある|
|Hops[].Issues[].Severity | 検出された問題の重大度。 値は、**Error** か **Warning** のいずれかです。 |
|Hops[].Issues[].Type |見つかった問題の種類。 次のいずれかの値になります。 <br/>**CPU**<br/>**メモリ**<br/>**GuestFirewall**<br/>**DnsResolution**<br/>**NetworkSecurityRule**<br/>**UserDefinedRoute** |
|Hops[].Issues[].Context |見つかった問題に関する詳細。|
|Hops[].Issues[].Context[].key |返されたキーと値のペアのキー。|
|Hops[].Issues[].Context[].value |返されたキーと値のペアの値。|

ホップに見つかった問題の例を次に示します。

```json
"Issues": [
    {
        "Origin": "Outbound",
        "Severity": "Error",
        "Type": "NetworkSecurityRule",
        "Context": [
            {
                "key": "RuleName",
                "value": "UserRule_Port80"
            }
        ]
    }
]
```
## <a name="fault-types"></a>障害の種類

接続のトラブルシューティングでは、接続に関する障害の種類が返されます。 次の表は、返された現在の障害の種類の一覧を示します。

|Type  |説明  |
|---------|---------|
|CPU     | CPU の使用率が高くなっています。       |
|メモリ     | メモリの使用率が高くなっています。       |
|GuestFirewall     | 仮想マシンのファイアウォールの構成によりトラフィックがブロックされています。        |
|DNSResolution     | 送信先アドレスの DNS 解決が失敗しました。        |
|NetworkSecurityRule    | トラフィックが NSG ルールによりブロックされています (ルールが返されている)        |
|UserDefinedRoute|ユーザー定義またはシステム ルートによりトラフィックがドロップしました。 |

### <a name="next-steps"></a>次のステップ

[Azure Portal](network-watcher-connectivity-portal.md)、[PowerShell](network-watcher-connectivity-powershell.md)、[Azure CLI](network-watcher-connectivity-cli.md)、または [REST API](network-watcher-connectivity-rest.md) を使用して接続をトラブルシューティングする方法を説明します。