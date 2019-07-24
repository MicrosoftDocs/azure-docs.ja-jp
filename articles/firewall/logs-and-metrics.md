---
title: Azure Firewall ログの概要
description: この記事では、Azure Firewall の診断ログの概要を示します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 9/24/2018
ms.author: victorh
ms.openlocfilehash: c129c394f3d694b832722287027c1f9e58028a33
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56957693"
---
# <a name="azure-firewall-logs"></a>Azure Firewall ログ

Azure Firewall を監視するには、ファイアウォール ログを使用できます。 また、アクティビティ ログを使用して、Azure Firewall リソースに対する操作を監査することもできます。

一部のログにはポータルからアクセスできます。 ログを [Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)、Storage、および Event Hubs に送信し、Azure Monitor ログや他のツール (Excel、Power BI など) で分析することができます。

## <a name="diagnostic-logs"></a>診断ログ

 Azure Firewall では、次の診断ログを利用できます。

* **アプリケーション ルール ログ**

   アプリケーション ルール ログは、Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信され、Azure Monitor ログに送信されます。 構成されているアプリケーション ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます。 次の例に示すように、データは JSON 形式でログに記録されます。

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

* **ネットワーク ルール ログ**

   ネットワーク ルール ログは、Azure Firewall ごとに有効にしている場合にのみ、ストレージ アカウントに保存され、イベント ハブにストリーム配信され、Azure Monitor ログに送信されます。 構成されているネットワーク ルールのいずれかと一致する新しい接続ごとに、許可/拒否された接続のログが生成されます。 次の例に示すように、データは JSON 形式でログに記録されます。

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

ログを保存するための 3 つのオプションがあります。

* **ストレージ アカウント**:ストレージ アカウントは、ログを長期間保存し、必要に応じて参照する場合に最適です。
* **イベント ハブ**:イベント ハブは、他のセキュリティ情報/イベント管理 (SEIM) ツールと統合してリソースに関するアラートを取得する場合に便利なオプションです。
* **Azure Monitor ログ**: Azure Monitor ログは、アプリケーションをリアルタイムに監視したり、傾向を見たりする一般的な用途に最適です。

## <a name="activity-logs"></a>アクティビティ ログ

   アクティビティ ログ エントリは既定で収集され、Azure Portal で表示できます。

   [Azure アクティビティ ログ](../azure-resource-manager/resource-group-audit.md) (以前の操作ログと監査ログ) を使用して、Azure サブスクリプションに送信されるすべての操作を表示できます。


## <a name="next-steps"></a>次の手順

Azure Firewall のログとメトリックを監視する方法については、[Azure Firewall のログを監視する方法に関するチュートリアル](tutorial-diagnostics.md)を参照してください。