---
title: Azure Firewall のログとメトリックの概要
description: Azure Firewall を監視するには、ファイアウォール ログを使用できます。 また、アクティビティ ログを使用して、Azure Firewall リソースに対する操作を監査することもできます。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 02/16/2021
ms.author: victorh
ms.openlocfilehash: 8922e6e0d5137a3a900e0f57f685d449c08b3f47
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100596961"
---
# <a name="azure-firewall-logs-and-metrics"></a>Azure Firewall のログとメトリック

Azure Firewall を監視するには、ファイアウォール ログを使用できます。 また、アクティビティ ログを使用して、Azure Firewall リソースに対する操作を監査することもできます。

一部のログにはポータルからアクセスできます。 ログを [Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)、Storage、および Event Hubs に送信し、Azure Monitor ログや他のツール (Excel、Power BI など) で分析することができます。

メトリックは軽量で、ほぼリアルタイムのシナリオをサポートできるため、アラートや迅速な問題の検出に役立ちます。

## <a name="diagnostic-logs"></a>診断ログ

 Azure Firewall では、次の診断ログを利用できます。

* **アプリケーション ルール ログ**

   アプリケーション ルール ログは、Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信されて、Azure Monitor ログに送信されます。 構成されているアプリケーション ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます。 次の例に示すように、データは JSON 形式でログに記録されます。

   ```
   Category: application rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
   {
    "category": "AzureFirewallApplicationRule",
    "time": "2018-04-16T23:45:04.8295030Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallApplicationRuleLog",
    "properties": {
        "msg": "HTTPS request from 10.1.0.5:55640 to mydestination.com:443. Action: Allow. Rule Collection: collection1000. Rule: rule1002"
    }
   }
   ```

   ```json
   {
     "category": "AzureFirewallApplicationRule",
     "time": "2018-04-16T23:45:04.8295030Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallApplicationRuleLog",
     "properties": {
         "msg": "HTTPS request from 10.11.2.4:53344 to www.bing.com:443. Action: Allow. Rule Collection: ExampleRuleCollection. Rule: ExampleRule. Web Category: SearchEnginesAndPortals"
     }
   }
   ```

* **ネットワーク ルール ログ**

   ネットワーク ルール ログは、Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信されて、Azure Monitor ログに送信されます。 構成されているネットワーク ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます。 次の例に示すように、データは JSON 形式でログに記録されます。

   ```
   Category: network rule logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   ```json
  {
    "category": "AzureFirewallNetworkRule",
    "time": "2018-06-14T23:44:11.0590400Z",
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
    "operationName": "AzureFirewallNetworkRuleLog",
    "properties": {
        "msg": "TCP request from 111.35.136.173:12518 to 13.78.143.217:2323. Action: Deny"
    }
   }

   ```

* **DNS プロキシ ログ**

   DNS プロキシ ログは、Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信されて、Azure Monitor ログに送信されます。 このログは、DNS プロキシを使用して構成された DNS サーバーへの DNS メッセージを追跡します。 次の例に示すように、データは JSON 形式でログに記録されます。


   ```
   Category: DNS proxy logs.
   Time: log timestamp.
   Properties: currently contains the full message.
   note: this field will be parsed to specific fields in the future, while maintaining backward compatibility with the existing properties field.
   ```

   成功: 
   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": "DNS Request: 11.5.0.7:48197 – 15676 AAA IN md-l1l1pg5lcmkq.blob.core.windows.net. udp 55 false 512 NOERROR - 0 2.000301956s"
     }
   }
   ```

   ［失敗］:

   ```json
   {
     "category": "AzureFirewallDnsProxy",
     "time": "2020-09-02T19:12:33.751Z",
     "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/AZUREFIREWALLS/{resourceName}",
     "operationName": "AzureFirewallDnsProxyLog",
     "properties": {
         "msg": " Error: 2 time.windows.com.reddog.microsoft.com. A: read udp 10.0.1.5:49126->168.63.129.160:53: i/o timeout”
     }
   }
   ```

   メッセージの形式:

   `[client’s IP address]:[client’s port] – [query ID] [type of the request] [class of the request] [name of the request] [protocol used] [request size in bytes] [EDNS0 DO (DNSSEC OK) bit set in the query] [EDNS0 buffer size advertised in the query] [response CODE] [response flags] [response size] [response duration]`

ログを保存するための 3 つのオプションがあります。

* **ストレージ アカウント**: ストレージ アカウントは、ログを長期間保存し、必要に応じて参照する場合に最適です。
* **イベント ハブ**:イベント ハブは、他のセキュリティ情報/イベント管理 (SEIM) ツールと統合してリソースに関するアラートを取得する場合に便利なオプションです。
* **Azure Monitor ログ**: Azure Monitor ログは、アプリケーションをリアルタイムに監視したり、傾向を見たりする一般的な用途に最適です。

## <a name="activity-logs"></a>アクティビティ ログ

   アクティビティ ログ エントリは既定で収集され、Azure Portal で表示できます。

   [Azure アクティビティ ログ](../azure-resource-manager/management/view-activity-logs.md) (以前の操作ログと監査ログ) を使用して、Azure サブスクリプションに送信されるすべての操作を表示できます。

## <a name="metrics"></a>メトリック

Azure Monitor において、メトリックは特定の時点におけるシステムの何らかの側面を表す数値です。 メトリックは 1 分ごとに収集され、頻繁にサンプリングできるためアラート発信に役立ちます。 アラートは比較的シンプルなロジックで迅速に発生させることができます。

Azure Firewall では、次のメトリックを利用できます。

- **[Application rules hit count]\(アプリケーション規則のヒット数\)** - アプリケーション規則がヒットした回数。

    単位: カウント

- **[Network rules hit count]\(ネットワーク規則のヒット数\)** - ネットワーク規則がヒットした回数。

    単位: カウント

- **[処理済みデータ]** - 所与の時間枠内でファイアウォールを通過したデータの合計。

    単位: バイト

- **[スループット]** - 1 秒間にファイアウォールを通過したデータの率。

    [単位]: 1 秒あたりのビット数

- **[Firewall health state]\(ファイアウォール正常性状態\)** - SNAT ポートの可用性に基づいて、ファイアウォールの正常性を示します。

    単位: パーセント

   このメトリックには次の 2 つのディメンションがあります。
  - 状態:値は *[Healthy]* \(正常\)、 *[Degraded]* \(低下\)、 *[Unhealthy]* \(異常\) のいずれかになります。
  - 理由:ファイアウォールの対応する状態の理由を示します。 

     SNAT ポートの使用率が 95% を超える場合、使い果たされたと見なされ、正常性は 50% で、状態 = **低下** および理由 = **SNAT ポート** になります。 ファイアウォールはトラフィックの処理を継続し、既存の接続に影響はありません。 ただし、新しい接続は断続的に確立されない可能性があります。

     SNAT ポートの使用率が 95% 未満の場合、ファイアウォールは正常と見なされ、正常性は 100% として表示されます。

     SNAT ポートの使用が報告されない場合、正常性は 0% として表示されます。 

- **[SNAT port utilization]\(SNAT ポート使用率\)** - ファイアウォールによって使用されている SNAT ポートの割合。

    単位: パーセント

   ファイアウォールにパブリック IP アドレスを追加すると、より多くの SNAT ポートが使用可能になり、SNAT ポートの使用率が低下します。 さらに、さまざまな理由 (CPU やスループットなど) に応じてファイアウォールをスケールアウトすると、追加の SNAT ポートも使用できるようになります。 実際には、サービスがスケールアウトされると、パブリック IP アドレスを追加しなくても、SNAT ポートの使用率の割合が低下する可能性があります。使用可能なパブリック IP アドレスの数を直接制御して、ファイアウォールで使用可能なポートを増やすことができます。 ただし、ファイアウォールのスケーリングを直接制御することはできません。


## <a name="next-steps"></a>次のステップ

- Azure Firewall のログとメトリックを監視する方法については、[Azure Firewall のログを監視する方法に関するチュートリアル](./firewall-diagnostics.md)を参照してください。

- Azure Monitor のメトリックの詳細については、「[Azure Monitor のメトリック](../azure-monitor/essentials/data-platform-metrics.md)」を参照してください。