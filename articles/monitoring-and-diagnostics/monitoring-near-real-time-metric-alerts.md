---
title: "Azure Monitor のほぼリアルタイムのメトリック アラート| Microsoft Docs"
description: "ほぼリアルタイムのメトリック アラートによって、1 分間隔で Azure リソースのメトリックを監視できます。"
author: snehithm
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/16/2017
ms.author: snmuvva
ms.custom: 
ms.openlocfilehash: aeeb6c2fb87e6c19991ef243ee7230f4e8f4e251
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="near-real-time-metric-alerts-preview"></a>ほぼリアルタイムのメトリック アラート (プレビュー)
Azure Monitor は、ほぼリアルタイムのメトリック アラート (プレビュー) という名前の新しい種類のメトリック アラートをサポートするようになりました。 現在、この機能はパブリック プレビュー段階にあります。
このアラートは、いくつかの点で、通常のメトリック アラートとは違っています。

- **待ち時間の短縮**: ほぼリアルタイムのメトリック アラートでは、メトリック値の変化を 1 分間隔で監視できます。
- **メトリックの条件に対する詳細な制御**: ほぼリアルタイムのメトリック アラートでは、詳細なアラート ルールを定義できます。 このアラートでは、メトリックの最大値、最小値、平均値、および合計値の監視をサポートします。
- **複数のメトリックの監視の組み合わせ**: ほぼリアルタイムのメトリック アラートでは、複数のメトリック (現時点では 2 つ) を 1 つのルールで監視できます。 両方のメトリックが指定された期間のしきい値を超えた場合、アラートがトリガーされます。
- **モジュール式の通知システム**: ほぼリアルタイムのメトリック アラートでは、[アクション グループ](monitoring-action-groups.md)を使用します。 この機能を使用して、モジュール方式でアクションを作成し、多数のアラート ルールで再利用できます。

> [!NOTE]
> ほぼリアルタイムのメトリック アラート機能は、現在パブリック プレビュー段階です。 機能やユーザー エクスペリエンスは変更されることがあります。
>

## <a name="what-resources-can-i-create-near-real-time-metric-alerts-for"></a>ほぼリアルタイムのメトリック アラートを作成できるリソース
ほぼリアルタイムのメトリック アラートがサポートされるリソースの種類の完全な一覧を次に示します。

* Microsoft.ApiManagement/service
* Microsoft.Batch/batchAccounts
* Microsoft.Cache/Redis
* Microsoft.Compute/virtualMachines
* Microsoft.Compute/virtualMachineScaleSets
* Microsoft.DataFactory/factories
* Microsoft.DBforMySQL/servers
* Microsoft.DBforPostgreSQL/servers
* Microsoft.EventHub/namespaces
* Microsoft.Logic/workflows
* Microsoft.Network/applicationGateways
* Microsoft.Network/publicipaddresses
* Microsoft.Search/searchServices
* Microsoft.ServiceBus/namespaces
* Microsoft.Sql/servers/elasticpools
* Microsoft.StreamAnalytics/streamingjobs
* Microsoft.Timeseriesinsights
* Microsoft.CognitiveServices/accounts


## <a name="create-a-near-real-time-metric-alert"></a>ほぼリアルタイムのメトリック アラートを作成する
現時点では、ほぼリアルタイムのメトリック アラートは、Azure ポータルでのみ作成できます。 PowerShell、コマンド ライン インターフェイス (CLI)、および Azure Monitor REST API によるほぼリアルタイムのメトリック アラートの作成は、近日対応予定です。

1. [ポータル](https://portal.azure.com/)で、監視するリソースを見つけて選択します。 リソースは、[前のセクション](#what-resources-can-i-create-near-real-time-metric-alerts-for)に示されているリソースの種類のいずれかである必要があります。 サポートされているすべてのリソースの種類に対して、[モニター] > [アラート] から同じことを行うこともできます。

2. [監視] セクションで、**[アラート]** または **[アラート ルール]** を選択します。 テキストとアイコンは、リソースごとに多少異なる場合があります。
   ![監視](./media/insights-alerts-portal/AlertRulesButton.png)

3. **[Add near real time metrics alert (preview)]\(ほぼリアルタイムのメトリック アラートの追加\)** コマンドをクリックします。 コマンドが淡色表示されている場合は、フィルターでリソースが選択されていることを確認します。

    ![[Add near real time metrics alert (preview)]\(ほぼリアルタイムのメトリック アラートの追加 (プレビュー)\) ボタン](./media/monitoring-near-real-time-metric-alerts/AddNRTAlertButton.png)

4. アラート ルールに**名前**を付けて、**説明**を選択します。この説明は通知電子メールにも表示されます。
5. 監視する**メトリック**を選択し、メトリックの **[条件]**、**[時間の集計]**、および **[しきい値]** の値を選択します。 必要に応じて、監視する別の**メトリック**を選択し、この 2 つ目のメトリックの **[条件]**、**[時間の集計]**、および **[しきい値]** の値を選択します。 

    ![ほぼリアルタイムのメトリックの Alert1 の追加](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert1.png) ![ほぼリアルタイムのメトリックの Alert2 の追加](./media/monitoring-near-real-time-metric-alerts/AddNRTAlert2.png)
6. アラートをトリガーする前にメトリック ルールが満たす必要があるメトリックルールの **[期間]** を選択します。 たとえば、期間として [直近 5 分] を使用すると、アラートは、80% を超える CPU (および 500 MB を超える NetworkIn) を探し、その CPU が 5 分間継続して 80% を超えた時点でトリガーされます。 最初のトリガーが発生したら、次のアラートは、CPU が 5 分間継続して 80% を下回ったときにトリガーされます。 アラートは、**[評価の頻度]** に従って評価されます。


6. **[重大度]** ドロップダウンから適切な値を選択します。

7. [新規] と [既存] の**アクション グループ**のどちらを使用するかを指定します。

8. **[新規]** のアクション グループを作成することを選択した場合は、アクション グループの名前、短い名前、アクション (電子メール、SMS、Webhook) を指定し、該当する詳細を入力します。


8. 完了したら **[OK]** を選択して、アラートを作成します。   

数分後にアラートがアクティブになり、前述のようにトリガーされます。

## <a name="managing-near-real-time-metric-alerts"></a>ほぼリアルタイムのメトリック アラートの管理
アラートを作成して選択したら、次の操作を行うことができます。

* メトリックのしきい値と、前日の実際の値を示すグラフを表示する。
* 編集または削除する。
* そのアラートの受信を一時的に停止または再開する必要がある場合に、そのアラートを**無効**または**有効**にする。



