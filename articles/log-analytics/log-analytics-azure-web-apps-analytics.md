---
title: "Azure Web Apps 分析データの表示 | Microsoft Docs"
description: "Azure Web Apps Analytics ソリューションを使用して、すべての Azure Web App リソースのさまざまなメトリックを収集することで、Azure Web Apps について把握することができます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: 20ff337f-b1a3-4696-9b5a-d39727a94220
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/11/2017
ms.author: banders
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 90d0b7e3f49daa33ab8c617d07ba9098cdda751f
ms.contentlocale: ja-jp
ms.lasthandoff: 05/12/2017

---

# <a name="view-analytic-data-for-metrics-across-all-your-azure-web-app-resources"></a>すべての Azure Web App リソースのメトリック分析データの表示

![Web Apps のシンボル](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-symbol.png)  
Azure Web Apps Analytics (プレビュー) ソリューションでは、すべての Azure Web App リソースのさまざまなメトリックを収集することで、[Azure Web Apps](../app-service-web/app-service-web-overview.md) を把握することができます。 ソリューションを使用することで、Web アプリ リソースのメトリック データの分析および検索が可能になります。

ソリューションを使用すれば、以下の情報を表示することができます。

- 最大応答時間の上位の Web Apps
- 成功した要求と失敗した要求を含む、Web Apps 全体の要求数
- 最大送受信トラフィックの上位の Web Apps
- CPU およびメモリ使用率の高い上位のサービス プラン
- Azure Web Apps アクティビティ ログ操作

## <a name="connected-sources"></a>接続先ソース

他のほとんどの Log Analytics ソリューションとは異なり、Azure Web Apps のデータはエージェントによって収集されません。 ソリューションで使用されるデータはすべて、Azure から直接収集されます。

| 接続されているソース | サポートの有無 | 説明 |
| --- | --- | --- |
| [Windows エージェント](log-analytics-windows-agents.md) | いいえ | ソリューションでは、Windows エージェントの情報は収集しません。 |
| [Linux エージェント](log-analytics-linux-agents.md) | なし | ソリューションでは、Linux エージェントの情報は収集しません。 |
| [SCOM 管理グループ](log-analytics-om-agents.md) | いいえ | ソリューションでは、接続された SCOM 管理グループ内のエージェントの情報は収集しません。 |
| [Azure Storage アカウント](log-analytics-azure-storage.md) | いいえ | ソリューションでは、Azure Storage の情報は収集しません。 |

## <a name="prerequisites"></a>前提条件

- Azure Web App メトリック情報にアクセスするには、Azure サブスクリプションが必要です。

## <a name="configuration"></a>構成

ワークスペースの Azure Web Apps Analytics ソリューションを構成するには、次の手順を実行します。

1. Azure Web Apps Analytics ソリューションを有効にします。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureWebAppsAnalyticsOMS?tab=Overview) から有効にするか、[ソリューション ギャラリーからの Log Analytics ソリューションの追加](log-analytics-add-solutions.md)に関するページで説明されているプロセスを使用して有効にしてください。
2. [PowerShell を使用して OMS への Azure リソース メトリックのログ記録を有効にします](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell)。

Azure Web Apps Analytics ソリューションでは Azure から以下の 2 つのメトリック セットを収集します。

- Azure Web Apps のメトリック
  - 平均メモリ ワーキング セット
  - 平均応答時間
  - 受信/送信バイト数
  - CPU 時間
  - 要求
  - メモリ ワーキング セット
  - Httpxxx
- App Service プランのメトリック
  - 受信/送信バイト数
  - CPU の割合
  - ディスク キューの長さ
  - HTTP キューの長さ
  - メモリの割合

App Service プランのメトリックは、専用のサービス プランを使用している場合にのみ収集されます。 これは、無料または共有の App Service プランには適用されません。

OMS ポータルを使用してソリューションを追加する場合は、次のタイルが表示されます。 [PowerShell を使用して OMS への Azure リソース メトリックのログ記録を有効にする](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell)必要があります。

![評価の実行の通知](./media/log-analytics-azure-web-apps-analytics/performing-assessment.png)

ソリューションを構成した後、ワークスペースへのデータ フローが 15 分以内に始まるはずです。

## <a name="using-the-solution"></a>ソリューションの使用

Azure Web Apps Analytics ソリューションをワークスペースに追加すると、**[Azure Web Apps Analytics]** タイルが [概要] ダッシュボードに追加されます。 このタイルには、ソリューションからアクセスできる、Azure サブスクリプションの Azure Web Apps のカウント数が表示されます。

![[Azure Web Apps Analytics] タイル](./media/log-analytics-azure-web-apps-analytics/azure-web-apps-analytics-tile.png)

### <a name="view-azure-web-apps-analytics-information"></a>Azure Web Apps Analytics 情報の表示

**[Azure Web Apps Analytics]** タイルをクリックして、**[Azure Web Apps Analytics]** ダッシュボードを開きます。 ダッシュボードには、次の表に示したブレードが存在します。 それぞれのブレードには、特定のスコープと時間範囲について、そのブレードの基準に該当する項目が最大 10 個表示されます。 すべてのレコードを返すログ検索を実行するには、ブレードの一番下にある **[すべて表示]** をクリックするか、ブレード ヘッダーをクリックします。

[!include[log-analytics-log-search-nextgeneration](../../includes/log-analytics-log-search-nextgeneration.md)]

| 分割 | Description |
| --- | --- |
| Azure Webapps |   |
| Web Apps の要求傾向 | 選択した日付範囲の Web Apps の要求傾向の折れ線グラフが表示され、上位 10 個の Web 要求のリストが表示されます。 折れ線グラフをクリックすると、<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* (MetricName=Requests OR MetricName=Http*) &#124; measure avg(Average) by MetricName interval 1HOUR</code> のログ検索が実行されます。 <br>Web 要求の項目をクリックすると、要求する Web 要求メトリック傾向のログ検索が実行されます。 |
| Web Apps の応答時間 | 選択した日付範囲の Web Apps の応答時間の折れ線グラフが表示されます。 上位 10 個の Web Apps 応答時間のリストも表示されます。 グラフをクリックすると、<code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"* MetricName="AverageResponseTime" &#124; measure avg(Average) by Resource interval 1HOUR</code> のログ検索が実行されます。<br> Web App をクリックすると、ログ検索が実行され、Web App の応答時間が返されます。 |
| Web Apps のトラフィック | Web Apps のトラフィックの折れ線グラフが MB 単位で表示され、上位の Web Apps のトラフィックがリストされます。 グラフをクリックすると、<code>Type:AzureMetrics ResourceId=*"/MICROSOFT.WEB/SITES/"*  MetricName=BytesSent OR BytesReceived &#124; measure sum(Average) by Resource interval 1HOUR</code> のログ検索が実行されます。<br> 直前の 1 分間にトラフィックが発生した Web Apps がすべて表示されます。 Web App をクリックすると、ログ検索が実行され、Web App の送受信バイト数が表示されます。 |
| Azure App Service プラン |   |
| CPU 使用率 &gt; 80% の App Service プラン | CPU 使用率が 80% を超える App Service プランの合計数が表示され、CPU 使用率別に上位 10 個の App Service プランがリストされます。 合計領域をクリックすると、<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=CpuPercentage &#124; measure Avg(Average) by Resource</code> のログ検索が実行されます。<br> App Service プランとそれらの平均 CPU 使用率のリストが表示されます。 App Service プランをクリックすると、ログ検索が実行され、その平均 CPU 使用率が表示されます。 |
| メモリ使用率 &gt; 80% の App Service プラン | メモリ使用率が 80% を超える App Service プランの合計数が表示され、メモリ使用率別に上位 10 個の App Service プランがリストされます。 合計領域をクリックすると、<code>Type=AzureMetrics ResourceId=*"/MICROSOFT.WEB/SERVERFARMS/"* MetricName=MemoryPercentage &#124; measure Avg(Average) by Resource</code> のログ検索が実行されます。<br> App Service プランとそれらの平均メモリ使用率のリストが表示されます。 App Service プランをクリックすると、ログ検索が実行され、その平均メモリ使用率が表示されます。 |
| Azure Web Apps のアクティビティ ログ |   |
| Azure Web Apps のアクティビティ監査 | Web Apps の合計数と[アクティビティ ログ](log-analytics-activity.md)が表示され、上位 10 個のアクティビティ ログ操作がリストされます。 合計領域をクリックすると、<code>Type=AzureActivity ResourceProvider= "Azure Web Sites" &#124; measure count() by OperationName</code> のログ検索が実行されます。<br> アクティビティ ログ操作のリストが表示されます。 アクティビティ ログ操作をクリックすると、操作のレコードをリストするログ検索が実行されます。 |



### <a name="azure-web-apps"></a>Azure Web Apps 

ダッシュボードで、ドリルダウンして Web Apps メトリックの詳細を把握することができます。 この最初のブレード セットには、Web Apps 要求の傾向、エラー (HTTP404 など) の数、トラフィック、および平均応答時間が経時的に表示されます。 また、異なる Web Apps のメトリックの詳細も表示されます。

![Azure Webapps ブレード](./media/log-analytics-azure-web-apps-analytics/web-apps-dash01.png)

データを表示する主な理由は、応答時間の長い Web App を特定し、調査して根本原因を見つけられるようにするためです。 しきい値の上限も適用されます。これは、問題のあるものをより簡単に特定するのに役立ちます。

- 赤で示されている Web Apps の応答時間は 1 秒を超えています。
- オレンジで示されている Web Apps の応答時間は 0.7 秒より長く、1 秒未満です。
- 緑で示されている Web Apps の応答時間は 0.7 秒未満です。

次のログ検索例の図で、*anugup3* Web App の応答時間が他の Web App よりもはるかに長いことがわかります。

![ログ検索例](./media/log-analytics-azure-web-apps-analytics/web-app-search-example.png)

### <a name="app-service-plans"></a>App Service プラン

専用のサービス プランを使用している場合は、App Service プランのメトリックを収集することもできます。 このビューには、CPU またはメモリ使用率が高い (&gt; 80%) App Service プランが表示されます。 メモリまたは CPU 使用率が高い上位の App Service も表示されます。 同様に、しきい値の上限が適用されます。これは、問題のあるものをより簡単に特定するのに役立ちます。

- 赤で示されている App Service プランの CPU/メモリ使用率は 80% を超えています。
- オレンジで示されている App Service プランの CPU/メモリ使用率は、60% より高く、80% より低くなっています。
- 緑で示されている App Service プランの CPU/メモリ使用率は 60% より低くなっています。

![Azure App Service プランのブレード](./media/log-analytics-azure-web-apps-analytics/web-apps-dash02.png)

## <a name="azure-web-apps-log-searches"></a>Azure Web Apps のログ検索

**一般的な Azure Web Apps 検索クエリのリスト**には、Web Apps の関連するすべてのアクティビティ ログが表示され、Web Apps リソースで実行された操作を把握することができます。 関連するすべての操作と、それらの発生回数もリストされます。

ログ検索クエリのいずれかを開始点として使用することで、簡単にアラートを作成できます。 たとえば、メトリックの平均応答時間が 1 秒ごとより長い場合にアラートを作成することができます。

## <a name="next-steps"></a>次のステップ

- 特定のメトリックの[アラート](log-analytics-alerts-creating.md)を作成します。
- [ログ検索](log-analytics-log-searches.md)を使用して、アクティビティ ログの詳細情報を表示します。

