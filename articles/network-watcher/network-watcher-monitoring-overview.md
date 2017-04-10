---
title: "Azure Network Watcher の概要 | Microsoft Docs"
description: "このページでは、Azure のネットワークに接続されたリソースを監視および視覚化する Network Watcher サービスの概要を示します。"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 14bc2266-99e3-42a2-8d19-bd7257fec35e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: f9e4fededc2bbb069cc653791137f248a7dc3fe8
ms.lasthandoff: 03/04/2017

---

# <a name="azure-network-monitoring-overview"></a>Azure のネットワーク監視の概要

Azure では、VNet、ExpressRoute、Application Gateway、ロード バランサーなどのさまざまなネットワーク リソースを調整および作成することで、エンドツーエンド ネットワークを作成できます。 各ネットワーク リソースは監視することができます。 こうした監視は、リソース レベルの監視と呼ばれます。

エンドツーエンド ネットワークの構成やリソース間の相互作用は複雑になることがあり、Network Watcher を使用してシナリオベースの監視を行う必要のある複雑なシナリオが生じる場合があります。

この記事では、シナリオ レベルおよびリソース レベルの監視について説明します。 Azure でのネットワーク監視が指す意味は広く、2 つの大まかなカテゴリを対象としています。

* [**Network Watcher**](#network-watcher) - シナリオベースの監視は、Network Watcher の機能を使用して実現できます。 このサービスには、パケット キャプチャ、次のホップ、IP フロー検証、セキュリティ グループ ビュー、NSG フロー ログなどが搭載されています。 シナリオ レベルの監視では、個別のネットワーク リソースの監視とは対照的に、ネットワーク リソースを隅から隅まで確認できます。
* [**リソース監視**](#network-resource-level-monitoring) - リソース レベルの監視は、診断ログ、メトリック、トラブルシューティング、Resource Health の&4; つの機能で構成されています。 これらの機能はすべて、ネットワーク リソース レベルで構築されています。

## <a name="network-watcher"></a>Network Watcher

Network Watcher は地域サービスであり、ネットワーク シナリオ レベルで Azure 内と Azure 間の状態を監視して診断できます。 Network Watcher に搭載されているネットワークの診断および監視ツールを使用して、Azure 内のネットワークを把握および診断し、洞察を得ることができます。

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

現在、Network Watcher が備える機能は次のとおりです。

* **[トポロジ](network-watcher-topology-overview.md)** - リソース グループ内のネットワーク リソース間のさまざまな相互接続および関係をネットワーク レベルで確認できます。
* **[可変パケット キャプチャ](network-watcher-packet-capture-overview.md)** - 仮想マシンで送受信されるパケット データをキャプチャします。 時間やサイズの制限などを設定できる詳細なフィルター オプションときめ細やかなコントロールにより、多様なキャプチャを行うことができます。 パケット データは、.cap 形式で BLOB ストアまたはローカル ディスクに保管できます。
* **[IP フロー検証](network-watcher-ip-flow-verify-overview.md)** - フロー情報の 5 タプル パケット パラメーター (宛先 IP、発信元 IP、宛先ポート、発信元ポート、プロトコル) に基づいてパケットが許可されたか拒否されたかを確認します。 パケットがセキュリティ グループにより拒否された場合、そのパケットを拒否した規則とグループが返されます。
* **[次のホップ](network-watcher-next-hop-overview.md)** - Azure Network Fabric におけるルーティング対象パケットの次のホップを特定します。これにより、誤って構成されたユーザー定義のルーティングがあるかどうかを診断できます。
* **[セキュリティ グループ ビュー](network-watcher-security-group-view-overview.md)** - VM に適用されている有効な適用セキュリティ規則を確認できます。
* **[NSG フロー ログ](network-watcher-nsg-flow-logging-overview.md)** - ネットワーク セキュリティ グループのフロー ログにより、そのグループのセキュリティ規則で許可または拒否されるトラフィックに関係するログを記録できます。 フローは 5 タプル情報 (発信元 IP、宛先 IP、発信元ポート、宛先ポート、プロトコル) で定義されます。
* **[仮想ネットワーク ゲートウェイと接続のトラブルシューティング](network-watcher-troubleshoot-manage-rest.md)** - 仮想ネットワーク ゲートウェイと接続に関する問題をトラブルシューティングできます。
* **[ネットワーク サブスクリプションの制限](#network-subscription-limits)** - ネットワーク リソースの使用状況を制限と照らし合わせて確認できます。
* **[診断ログの構成](#diagnostic-logs)** –&1; つのペインで、リソース グループ内のネットワーク リソースの診断ログを有効化または無効化することができます。

### <a name="role-based-access-control-rbac-in-network-watcher"></a>Network Watcher におけるロールベースのアクセス制御 (RBAC)

Network Watcher では、[Azure のロールベースのアクセス制御 (RBAC) モデル](../active-directory/role-based-access-control-what-is.md)を使用しています。 プレビュー段階の Network Watcher には次のアクセス許可が必要です。 Network Watcher API を開始またはポータルから Network Watcher を使用するために用いるロールには、必ず必要なアクセスを設定してください。

|リソース| アクセス許可|
|---|---|
|Microsoft.Storage/ |読み取り|
|Microsoft.Authorization/| 読み取り|
|Microsoft.Resources/subscriptions/resourceGroups/| 読み取り|
|Microsoft.Storage/storageAccounts/listServiceSas/ | アクション|
|Microsoft.Storage/storageAccounts/listAccountSas/ |アクション|
|Microsoft.Storage/storageAccounts/listKeys/ | アクション|
|Microsoft.Compute/virtualMachines/ |読み取り|
|Microsoft.Compute/virtualMachines/ |書き込み|
|Microsoft.Compute/virtualMachineScaleSets/ |読み取り|
|Microsoft.Compute/virtualMachineScaleSets/ |書き込み|
|Microsoft.Network/networkWatchers/packetCaptures/| 読み取り|
|Microsoft.Network/networkWatchers/packetCaptures/| 書き込み|
|Microsoft.Network/networkWatchers/packetCaptures/| 削除|
|Microsoft.Network/networkWatchers/ |書き込み|
|Microsoft.Network/networkWatchers/| 読み取り|
|Microsoft.Insights/alertRules/ |*|
|Microsoft.Support/| *|

### <a name="network-subscription-limits"></a>ネットワーク サブスクリプションの制限

ネットワーク サブスクリプションの制限では、リージョン内のサブスクリプションに含まれる各ネットワーク リソースの使用状況の詳細を、利用可能なリソースの最大数と照らし合わせて確認できます。

![ネットワーク サブスクリプションの制限][nsl]

## <a name="network-resource-level-monitoring"></a>ネットワーク リソース レベルの監視

リソース レベルの監視では次の機能を使用できます。

### <a name="audit-log"></a>監査ログ

ネットワークの構成の一環として実行された操作が記録されます。 これらのログは、Azure Portal で確認することも、Power BI などの Microsoft ツールやサードパーティ ツールを使用して取得することもできます。 監査ログは、ポータル、PowerShell、CLI、Rest API で利用できます。 監査ログの詳細については、[Resource Manager の監査操作](../resource-group-audit.md)に関するページを参照してください。

監査ログは、すべてのネットワーク リソースに対して実行された操作について用意されています。

### <a name="metrics"></a>メトリック

メトリックとは、一定期間にわたり収集されたパフォーマンスの測定値とカウンターのことです。 現時点では、メトリックは Application Gateway で利用できます。 メトリックを使用すると、しきい値に基づいてアラートをトリガーすることができます。 メトリックを使用してアラートを作成する方法については、[Application Gateway の診断機能](../application-gateway/application-gateway-diagnostics.md)に関するページを参照してください。

![メトリック ビュー][metrics]

### <a name="diagnostic-logs"></a>診断ログ

定期イベントおよび自然発生イベントがネットワーク リソースにより作成され、ストレージ アカウント内に記録されるか、イベント ハブまたは Log Analytics へ送信されます。 こうしたログから、リソースの正常性に関する詳細が得られます。 これらのログは、Power BI および Log Analytics などのツールで確認できます。 診断ログを確認する方法については、[Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) に関するページを参照してください。

診断ログは、[ロード バランサー](../load-balancer/load-balancer-monitor-log.md)、[ネットワーク セキュリティ グループ](../virtual-network/virtual-network-nsg-manage-log.md)、ルーティング、および [Application Gateway](../application-gateway/application-gateway-diagnostics.md) で利用できます。

Network Watcher には診断ログ ビューが用意されています。 このビューには、診断ログをサポートするすべてのネットワーク リソースが表示されます。 このビューから、ネットワーク リソースの有効化および無効化を手軽に素早く行うことができます。

![ログ][logs]

### <a name="troubleshooting"></a>トラブルシューティング

ポータルの&1; 機能であるトラブルシューティング ブレードは、現時点ではネットワーク リソースで提供されており、個々のリソースに関連する一般的な問題を診断できます。 この機能は、ExpressRoute、VPN Gateway、Application Gateway、ネットワーク セキュリティ ログ、ルーティング、DNS、ロード バランサー、Traffic Manager の各ネットワーク リソースで利用できます。 リソース レベルのトラブルシューティングの詳細については、「[Diagnose and resolve issues with Azure Troubleshooting](https://azure.microsoft.com/blog/azure-troubleshoot-diagonse-resolve-issues/)」(Azure のトラブルシューティング機能を使用した問題の診断と解決) を参照してください。

![トラブルシューティングの情報][TS]

### <a name="resource-health"></a>リソース ヘルス

ネットワーク リソースの正常性に関する情報は定期的に提供されています。 対象のリソースは、VPN Gateway や VPN トンネルなどです。 Resource Health には、Azure Portal からアクセスできます。 Resource Health の詳細については、[Resource Health の概要](../resource-health/resource-health-overview.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

Network Watcher について学習したので、次のことが可能になりました。

[Azure Portal で可変パケット キャプチャ](network-watcher-packet-capture-manage-portal.md) にアクセスして、VM でパケット キャプチャを実行する。

[パケット キャプチャによりトリガーされるアラート](network-watcher-alert-triggered-packet-capture.md)を使用して、事前対応型の監視と診断を実行する。

オープン ソース ツールを使用して、[Wireshark によるパケット キャプチャの分析](network-watcher-deep-packet-inspection.md)でセキュリティの脆弱性を検出する。

<!--Image references-->
[TS]: ./media/network-watcher-monitoring-overview/troubleshooting.png
[logs]: ./media/network-watcher-monitoring-overview/logs.png
[metrics]: ./media/network-watcher-monitoring-overview/metrics.png
[nsl]: ./media/network-watcher-monitoring-overview/nsl.png












