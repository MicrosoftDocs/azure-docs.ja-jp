---
title: Azure Sentinel と Azure Web Application Firewall の併用
description: この記事では、Azure Sentinel と Azure Web Application Firewall (WAF) を併用する方法について説明します。
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 3d905dd1e6acab8f9f6d3885c882dd9c32133cb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100596426"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Azure Sentinel と Azure Web Application Firewall の併用

Azure Sentinel と Azure Web Application Firewall (WAF) を組み合わせると、WAF リソースのセキュリティ情報イベントを管理できます。 Azure Sentinel には、Log Analytics を使用したセキュリティ分析機能があります。これを使用すると、WAF データを簡単に分類して表示することができます。 Azure Sentinel を使用すると、事前に作成されたブックにアクセスして、組織のニーズに合わせて変更することができます。 このブックには、Azure Content Delivery Network (CDN) 上の WAF、Azure Front Door 上の WAF、および複数のサブスクリプションとワークスペースにまたがる Application Gateway 上の WAF の分析を表示できます。

## <a name="waf-log-analytics-categories"></a>WAF ログ分析のカテゴリ

WAF ログ分析は次のカテゴリに分類されます。  

- 実行されたすべての WAF アクション 
- ブロックされている上位 40 個の要求 URI アドレス 
- 上位 50 個のイベント トリガー、  
- 時間の経過に伴うメッセージ 
- 完全なメッセージの詳細 
- メッセージ別の攻撃イベント  
- 時間の経過に伴う攻撃イベント 
- 追跡 ID フィルター 
- 追跡 ID メッセージ 
- 上位 10 個の攻撃元 IP アドレス 
- IP アドレスの攻撃メッセージ 

## <a name="waf-workbook-examples"></a>WAF ブックの例

次の WAF ブックの例は、サンプル データを示しています。

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="WAF アクション フィルター":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="上位 50 個のイベント":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="攻撃イベント":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="上位 10 個の攻撃元 IP アドレス":::

## <a name="launch-a-waf-workbook"></a>WAF ブックを起動する

WAF ブックは、すべての Azure Front Door、Application Gateway、および CDN WAF に対して機能します。 これらのリソースからデータを接続する前に、リソースに対して Log Analytics を有効にする必要があります。 

各リソースに対して Log Analytics を有効にするには、個々の Azure Front Door、Application Gateway、または CDN リソースに移動します。

1. **[診断設定]** を選択します。
2. **[+ 診断設定の追加]** を選択します。 
3. [診断設定] ページで、次の手順を実行します。
   1. 名前を入力します。 
   1. **[Log Analytics への送信]** を選択します。 
   1. ログの保存先ワークスペースを選択します。 
   1. 分析するログの種類を選択します。
      1. Application Gateway:'ApplicationGatewayAccessLog' と 'ApplicationGatewayFirewallLog'
      1. Azure Front Door:'FrontDoorAccessLog' と 'FrontDoorFirewallLog'
      1. CDN:'AzureCdnAccessLog'
   1. **[保存]** を選択します。

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="診断設定":::

4. Azure ホーム ページで、検索バーに「**Azure Sentinel**」と入力し、**Azure Sentinel** リソースを選択します。 
2. 既にアクティブなワークスペースを選択するか、新しいワークスペースを作成します。 
3. 左側のパネルの **[構成]** の下にある **[Data Connectors]\(データ コネクタ\)** を選択します。
4. "**Microsoft Web アプリケーション ファイアウォール**" を探し、 **[Microsoft web application firewall (WAF)]\(Microsoft Web アプリケーション ファイアウォール (WAF)\)** を選択します。 右下にある **[Open connector]\(コネクタを開く\)** ページを選択します。

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="データ コネクタ":::

8. **[構成]** の手順をまだ実行していない場合は、ログ分析データを取得する WAF リソースごとに実行します。
6. 個々の WAF リソースの構成が完了したら、 **[次のステップ]** タブを選択します。推奨されるブックのいずれかを選択します。 このブックには、以前に有効にしたすべてのログ分析データが使用されます。 これで、WAF リソースに対して機能する WAF ブックが存在するようになりました。

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF ブック":::


## <a name="next-steps"></a>次のステップ

- [Azure Sentinel に関する詳細](../sentinel/overview.md)
- [Azure Monitor ブックに関する詳細](../azure-monitor/visualize/workbooks-overview.md)
