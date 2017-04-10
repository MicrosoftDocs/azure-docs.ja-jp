---
title: "Azure Log Analytics 管理ソリューションの追加 | Microsoft Docs"
description: "Operations Management Suite (OMS) / Log Analytics 管理ソリューションには、特定の問題点に関するメトリックを提供するロジックや視覚化、データ取得規則が集約されています。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 4cc1f60820861913f43ae449b20381036fd49bdf
ms.lasthandoff: 03/24/2017


---
# <a name="add-azure-log-analytics-management-solutions-to-your-workspace"></a>Azure Log Analytics 管理ソリューションをワークスペースに追加する

Log Analytics 管理ソリューションには、特定の問題点に関するメトリックを提供する**ロジック**や**視覚化**、**データ取得規則**が集約されています。 この記事では、Log Analytics でサポートされている管理ソリューションの一覧を示し、Azure Portal を使用してワークスペースのソリューションを追加および削除する方法について説明します。 ソリューション ギャラリーを使用して、OMS ポータルでソリューションを追加することもできます。

管理ソリューションを使用すると、次の点についてより深い知識が得られます。

* 運用上の問題を迅速に調査して解決する
* さまざまな種類のマシン データを収集して関連付ける
* ソリューションが公開するアクティビティに積極的に取り組む

> [!NOTE]
> Log Analytics にはログ検索機能が含まれているため、ログ検索を有効にするために管理ソリューションをインストールする必要はありません。 ただし、管理ソリューションをワークスペースに追加することで、データの視覚化、検索候補、洞察を得ることができます。

この記事では、Azure Portal の Marketplace を使用して管理ソリューションをワークスペースに追加します。 ソリューションを追加すると、データはインフラストラクチャのサーバーから収集され、OMS サービスに送信されます。 OMS サービスによる処理には、通常、数分から 1 時間かかります。 サービスによってデータが処理されると、OMS でそのデータを表示できます。

不要になった管理ソリューションは、簡単に削除できます。 管理ソリューションを削除すると、そのデータは OMS に送信されません。 ソリューションを削除するとデータ量を削減できるので、Free 価格レベルの場合は 1 日のデータ使用量をクォータ内に抑えるのに役立ちます。

## <a name="view-available-management-solutions"></a>使用できる管理ソリューションの表示

[Log Analytics 用の管理ソリューション](https://azuremarketplace.microsoft.com/marketplace/apps/category/monitoring-management?page=1&subcategories=management-solutions)は、Azure Marketplace に一覧表示されます。

Azure Marketplace から各ソリューションの下にある **[Get it now (今すぐ入手する)]** リンクをクリックして管理ソリューションをインストールすることができます。

## <a name="add-a-management-solution"></a>管理ソリューションの追加
1. まだサインインしていない場合は、Azure サブスクリプションを使用して [Azure Portal](https://portal.azure.com) にサインインします。
2. **[新規]** ブレードの **[Marketplace]** で、**[監視 + 管理]** を選択します。
3. **[監視 + 管理]** ブレードで、**[すべて表示]** をクリックします。  
    ![[監視 + 管理] ブレード](./media/log-analytics-add-solutions/monitoring-management-blade.png)  
4. **[管理ソリューション]** の右側の **[More (その他)]** をクリックします。
5. **[管理ソリューション]** ブレードで、ワークスペースに追加する管理ソリューションを選択します。  
    ![[監視 + 管理] ブレード](./media/log-analytics-add-solutions/management-solutions.png)  
6. 管理ソリューションのブレードで、管理ソリューションに関する情報を確認し、**[作成]** をクリックします。
7. "*管理ソリューション名*" のブレードで、管理ソリューションに関連付けるワークスペースを選択します。
8. 必要に応じて、Azure サブスクリプション、リソース グループ、および場所に関するワークスペースの設定を変更します。 **[Automation オプション]** を選択することもできます。 **[作成]**をクリックします。  
    ![ソリューションのワークスペース](./media/log-analytics-add-solutions/solution-workspace.png)  
9. ワークスペースに追加した管理ソリューションの使用を開始するには、**[Log Analytics]** > **[サブスクリプション]** > ***[ワークスペース名]*** > **[概要]** の順に選択します。 管理ソリューションの新しいタイルが表示されます。 タイルをクリックしてソリューションを開き、データが収集されたらソリューションの使用を開始します。

## <a name="remove-a-management-solution"></a>管理ソリューションの削除

1. [Azure Portal](https://portal.azure.com) で、**[Log Analytics]** > **[サブスクリプション]** > ***[ワークスペース名]*** の順に選択します。***[ワークスペース名]*** ブレードで、**[ソリューション]** をクリックします。
2. 管理ソリューションの一覧で、削除するソリューションを選択します。
3. ワークスペースのソリューション ブレードで、**[削除]** をクリックします。  
    ![ソリューションの削除](./media/log-analytics-add-solutions/solution-delete.png)  
4. 確認のダイアログ ボックスで **[はい]** をクリックします。

## <a name="offers-and-pricing-tiers"></a>プランと価格レベル

次の表に、運用管理とセキュリティの各プランに属する管理ソリューションと、
各管理ソリューションで利用可能な価格レベルを示します。
次の表のソリューションはすべて、Azure Portal 内からと、Log Analytics ポータルのソリューション ギャラリーから利用できます。

| 管理ソリューション                                                                        | プラン                                                                     | 価格レベル<sup>1</sup>                                                    | メモ |
| ---                                                                                        | ---                                                                       | ---                                                                                                       | ---   |
| アクティビティ ログ分析                                                                     | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | 90 日間のデータを無料で使用できます。<br>Free レベル キャップの対象とならないデータ |
| [AD 評価](log-analytics-ad-assessment.md)                                              | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| [AD レプリケーションの状態](log-analytics-ad-replication-status.md)                              | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| エージェントの正常性                                                                                  | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | Free レベル キャップの対象とならないデータ |
| [アラートの管理](log-analytics-solution-alert-management.md)                              | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| Application Insights コネクタ <br>(プレビュー)                                                 | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>    | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| Automation ハイブリッド worker                                                                     | <ul><li>Automation and Control</li></ul>                                    | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                            | Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。 |
| [Azure Application Gateway 分析](log-analytics-azure-networking-analytics.md)      | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| [Azure ネットワーク セキュリティ グループ分析](log-analytics-azure-networking-analytics.md)      | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| [Azure SQL Analytics (プレビュー)](log-analytics-azure-sql.md)                                                          | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br>&nbsp;ノード&nbsp;単位 (OMS)                                                                             | Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。|
| バックアップ                                                                                    | <ul><li>洞察と分析</li></ul>                                     | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                            | 従来のバックアップ資格情報コンテナーが必要です。 |
| 容量とパフォーマンス <br>(プレビュー)                                                     | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| [変更の追跡](log-analytics-change-tracking.md)                                         | <ul><li>Automation and Control</li></ul>                                    | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                            | Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。 |
| [コンテナー](log-analytics-containers.md)                                                  | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| HDInsight HBase の監視 <br>(プレビュー)                                                     | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| [Key Vault Analytics](log-analytics-azure-key-vault.md)                      | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| Logic Apps B2B                      | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| [マルウェアの評価](log-analytics-malware.md)                                             | <ul><li>セキュリティとコンプライアンス</li></ul>                                    | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                               | |
| [ネットワーク パフォーマンス監視](log-analytics-network-performance-monitor.md) <br>(プレビュー)    | <ul><li>洞察と分析</li></ul>                                     | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                         | |
| Office 365 分析 <br>(プレビュー)                                                        | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| [セキュリティと監査](../operations-management-suite/oms-security-getting-started.md)        | <ul><li>Security&nbsp;and&nbsp;Compliance</li></ul>                        | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                               | セキュリティ イベント ログの収集にはこのソリューションが必要です。 |
| [Service Fabric 分析](log-analytics-service-fabric.md) <br>(プレビュー)                    | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| [サービス マップ](../operations-management-suite/operations-management-suite-service-map.md) <br>(プレビュー)| <ul><li>洞察と分析</li></ul>                         | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                         | 米国東部と西ヨーロッパで使用できます。     |
| Site Recovery                                                                                | <ul><li>洞察と分析</li></ul>                                     | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                            | 従来の Site Recovery 資格情報コンテナーが必要です。 |
| [SQL の評価](log-analytics-sql-assessment.md)                                            | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| 勤務時間外に VM を起動/停止する<br>(プレビュー)                                                 | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                         | Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。 |
| [SurfaceHub](log-analytics-surface-hubs.md)                                                    | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| [System Center Operations Manager Assessment](log-analytics-scom-assessment.md) <br>(プレビュー) | <ul><li>洞察と分析</li><li>Log Analytics</li></ul>         | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| 更新管理                                                                               | <ul><li>Automation and Control</li></ul>                                    | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                            | Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。 |
| [更新プログラムのコンプライアンス](https://technet.microsoft.com/en-us/itpro/windows/manage/update-compliance-get-started) <br>(プレビュー)                                                             | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | データまたはノードに対する課金はありません。<br>Free レベル キャップの対象とならないデータ |
| [Upgrade Readiness](https://technet.microsoft.com/itpro/windows/deploy/manage-windows-upgrades-with-upgrade-readiness)                                                             | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | データまたはノードに対する課金はありません。<br>Free レベル キャップの対象とならないデータ |
| [VMware の監視](log-analytics-vmware.md) <br>(プレビュー)                                 | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> Standard<br> Premium&nbsp;(OMS)<br> &nbsp;GB&nbsp;単位 (スタンドアロン)<br> &nbsp;ノード&nbsp;単位 (OMS)     | |
| Wire Data 2.0<br>(プレビュー)                                                                   | <ul><li>洞察と分析</li></ul>                                      | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)                                                                         | 米国東部と西ヨーロッパで使用できます。 |

<sup>1</sup> *Standard* と *Premium (OMS)* の価格レベルをご利用いただけるのは、2016 年 9 月 21 日より前に Log Analytics ワークスペースを作成したお客様のみです。

### <a name="community-provided-management-solutions"></a>コミュニティ提供の管理ソリューション

コミュニティ提供の管理ソリューションは、[Azure テンプレート ギャラリー](https://azure.microsoft.com/resources/templates/?term=Per&nbsp;Node&nbsp;(OMS))から、または作成者から直接入手していただけます。

| 管理ソリューション                 | プラン                                                                     | 価格レベル                         | メモ |
| ---                                 | ---                                                                       | ---                                   | ---   |
| すべてのコミュニティ提供ソリューション  | <ul><li>Insight&nbsp;and&nbsp;Analytics</li><li>Log Analytics</li></ul>     | 無料<br> &nbsp;ノード&nbsp;単位 (OMS)     |    Log Analytics ワークスペースを Automation アカウントにリンクする必要があります。 |




## <a name="data-collection-details"></a>データ収集の詳細
次の表は、Log Analytics 管理ソリューションとデータ ソースにおけるデータの収集に関して、その手段と各種情報をまとめたものです。 この表は、[サブスクリプション価格レベル](https://go.microsoft.com/fwlink/?linkid=827926)に対応するソリューションのプラン別に分類されています。 アクティビティ ログ分析ソリューションは、すべての価格レベルで無料で使用できます。

Log Analytics Windows エージェントと System Center Operations Manager エージェントは基本的には同じものですが、 Windows エージェントには別途、OMS ワークスペースに接続し、プロキシ経由でルーティングするための機能が備わっています。 Operations Manager エージェントを使用する場合、OMS とやり取りするためには、その Operations Manager エージェントを OMS エージェントとしてターゲット指定する必要があります。 この表の Operations Manager エージェントは、Operations Manager に接続された OMS エージェントです。 既存の Operations Manager 環境を OMS に接続する方法については、「[Operations Manager を Log Analytics に接続する](log-analytics-om-agents.md)」を参照してください。

> [!NOTE]
> データがどのように OMS に送信されるかは、使用するエージェントの種類によって異なります。また、以下の条件によっても左右されます。
> - 使用するのは、Windows エージェントか、Operations Manager に接続された OMS エージェントのどちらか一方です。
> - Operations Manager が必要なときは、ソリューションの Operations Manager エージェントのデータは常に Operations Manager 管理グループを使用して OMS に送信されます。 またこのとき、ソリューションによって使用されるのは Operations Manager エージェントだけです。
> - Operations Manager が不要であり、なおかつ管理グループを使用して Operations Manager エージェント データが OMS に送信されることが以下の表に示されているときは、Operations Manager エージェント データが常に管理グループを使用して OMS に送信されます。 Windows エージェントは、管理グループをバイパスして、そのデータを直接 OMS に送信します。
> - Operations Manager エージェント データの送信に管理グループが使用されないときは、データが直接 OMS に送信され、管理グループはバイパスされます。

### <a name="insight--analytics--log-analytics"></a>Insight & Analytics / Log Analytics

| 管理ソリューション | プラットフォーム | Microsoft Monitoring Agent | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| アクティビティ ログ分析 | Azure | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 通知時 |
| AD 評価 |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 日 |
| AD レプリケーションの状態 |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![はい](./media/log-analytics-add-solutions/oms-bullet-green.png) |5 日 |
| エージェントの正常性 | Windows および Linux | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | 1 分 |
| アラート管理 (Nagios) |Linux |![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |着信時 |
| アラート管理 (Zabbix) |Linux |![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |1 分 |
| アラート管理 (Operations Manager) |Windows |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![はい](./media/log-analytics-add-solutions/oms-bullet-green.png) |![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) |3 分 |
| Application Insights コネクタ (プレビュー) | Azure | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 通知時 |
| Azure Application Gateway 分析 | Azure | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 分 |
| Azure ネットワーク セキュリティ グループ分析 | Azure | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 分 |
| Azure SQL Analytics (プレビュー) |Windows |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 10 分 |
| 容量管理 |Windows |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![はい](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |1 時間に 1 回 |
| コンテナー | Windows および Linux | ![はい](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 分 |
| Key Vault Analytics |Windows |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 分 |
| ネットワーク パフォーマンス監視 | Windows | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | TCP ハンドシェイク (5 秒ごと)、データ送信 (3 分ごと) |
| Office 365 分析 (プレビュー) |Windows |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |通知時 |
| Service Fabric 分析 |Windows |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 分 |
| サービス マップ | Windows および Linux | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | 15 秒 |
| SQL の評価 |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |7 日 |
| SurfaceHub |Windows |![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |着信時 |
| System Center Operations Manager Assessment (プレビュー) | Windows | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![はい](./media/log-analytics-add-solutions/oms-bullet-green.png) | 7 日 |
| アップグレード分析 (プレビュー) | Windows | ![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 2 日 |
| VMware の監視 (プレビュー) | Linux | ![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | 3 分 |
| 送信データ |Windows (2012 R2/8.1 以降) |![はい](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 1 分 |


### <a name="automation--control"></a>オートメーションと制御

| 管理ソリューション | プラットフォーム | Microsoft Monitoring Agent | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Automation ハイブリッド worker | Windows | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 該当なし |
| 変更の追跡 |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |時間単位 |
| 変更の追跡 |Linux |![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |1 時間に 1 回 |
| 更新管理 | Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) |1 日に少なくとも 2 回、更新プログラムのインストールの 15 分後 |

### <a name="security--compliance"></a>セキュリティとコンプライアンス

| 管理ソリューション | プラットフォーム | Microsoft Monitoring Agent | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| マルウェア対策評価 |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |1 時間に 1 回 |
| セキュリティおよび監査<sup>1</sup> | Windows および Linux | ![一部](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![一部](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![一部](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![一部](./media/log-analytics-add-solutions/oms-bullet-yellow.png) | 各種 |

<sup>1</sup> セキュリティと監査ソリューションは、Windows、Operations Manager、Linux エージェントからログを収集できます。 データ収集方法については、「[データ ソース](#data-sources)」を参照してください。

- syslog
- Windows セキュリティ イベント ログ
- Windows ファイアウォール ログ
- Windows イベント ログ



### <a name="protection--recovery"></a>保護と復旧

| 管理ソリューション | プラットフォーム | Microsoft Monitoring Agent | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| バックアップ | Azure | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 該当なし |
| Azure Site Recovery | Azure | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) | ![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) | 該当なし |


### <a name="data-sources"></a>データ ソース


| データ ソース | プラットフォーム | Microsoft Monitoring Agent | Operations Manager エージェント | Azure Storage | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ETW |Windows |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 分 |
| IIS ログ |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |5 分 |
| ネットワーク アプリケーション ゲートウェイ |Windows |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 分 |
| ネットワーク セキュリティ グループ |Windows |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 分 |
| パフォーマンス カウンター |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| パフォーマンス カウンター |Linux |![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |スケジュールに基づく頻度 (間隔は 10 秒以上) |
| syslog |Linux |![[はい]](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合) |
| Windows セキュリティ イベント ログ |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |10 分 (Azure Storage の場合) または着信時 (エージェントの場合) |
| Windows ファイアウォール ログ |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![いいえ](./media/log-analytics-add-solutions/oms-bullet-red.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |着信時 |
| Windows イベント ログ |Windows |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![あり](./media/log-analytics-add-solutions/oms-bullet-green.png) |![なし](./media/log-analytics-add-solutions/oms-bullet-red.png) |![はい](./media/log-analytics-add-solutions/oms-bullet-green.png) |1 分 (Azure Storage からデータを収集する場合) または着信時 (エージェントからデータを収集する場合) |



## <a name="preview-management-solutions-and-features"></a>プレビュー段階の管理ソリューションと機能
サービスを実行し、DevOps の実践に従うことで、お客様と協力して、機能やソリューションを開発できます。

プライベート プレビューの期間中は、フィードバックをもらって改良するために、少数のお客様に機能やソリューションの初期実装へのアクセスを提供します。 この初期実装には、最低限の機能や運用上の機能が備わっています。

この目的は、効果的な機能と効果的でない機能を見極めることができるように、さまざま操作をすばやく試すことです。 プライベート プレビューを利用するお客様からのフィードバックでパブリック プレビューの準備ができたと判断できるまで、このプロセスを繰り返します。

パブリック プレビューでは、すべてのユーザーが機能またはソリューションを利用できるようにし、フィードバックの数を増やしてスケールと効率性を検証します。 このフェーズでは、次のようになります。

* プレビュー機能は、[設定] タブに表示され、すべてのユーザーが有効にできます。
* プレビュー ソリューションは、ギャラリーまたはスクリプトを使用して追加できます。

### <a name="what-should-i-know-about-preview-features-and-solutions"></a>プレビューの機能とソリューションについて知っておくべきこと
Microsoft では、新しい機能や管理ソリューションに力を入れており、それらをお客様と協力して開発できることを嬉しく思っています。

ただし、プレビューの機能とソリューションはすべてのユーザーに適しているわけではありません。 そのため、プライベート プレビューへの参加を依頼する前やパブリック プレビューを有効にする前に、開発中の機能やソリューションを利用して問題ないかどうかを確認してください。

ポータルからプレビュー機能を有効にする場合は、機能がプレビュー段階であることを示す警告が表示されます。

#### <a name="for-both-private-and-public-preview"></a>"*プライベート*" プレビューと "*パブリック*" プレビューの両方について
パブリックとプライベートの両方のプレビューに当てはまる項目を次に示します。

* 必ずしも適切に動作するとは限りません。
  * 問題は、少し不快に感じる程度ものから、まったく動作しないものまでさまざまです。
* プレビューによってシステムや環境が悪影響を受ける可能性があります。
  * OMS で使用しているシステムに悪影響が出ないように努めていますが、予期しない問題が発生することがあります。
* データの損失や破損が発生する可能性があります。
* 問題のトラブルシューティングに役立つ、診断ログなどのデータの収集を依頼する場合があります。
* 機能やソリューションは (一時的または永続的に) 削除される可能性があります。
  * プレビュー期間中に得た情報に基づいて、機能またはソリューションをリリースしない決定を下す場合があります。
* プレビューが動作しない構成やテストされていない構成もあるため、次のものが制限される可能性があります。
  * 使用できるオペレーティング システム (たとえば、プレビュー段階では、機能が Linux のみに適用される場合があります)。
  * 使用できるエージェントの種類 (MMA、Operations Manager) (たとえば、プレビュー段階では、機能が Operations Manager で動作しない場合があります)。  
* プレビューのソリューションと機能は、サービス レベル アグリーメントの対象外です。
* プレビュー機能を利用すると、利用料金が発生します。
* 機能やソリューションを活用するために必要な機能が存在しないか、不完全な場合があります。
* 機能やソリューションは、リージョンによっては利用できない場合があります。
* 機能やソリューションがローカライズされていない場合があります。
* 機能やソリューションを利用できるお客様やデバイスの数に制限がある場合があります。
* 構成を実行したり、ソリューションや機能を有効にしたりするスクリプトの使用が必要になる場合があります。
* ユーザー インターフェイス (UI) は不完全で、日々変更される可能性があります。
* パブリック プレビューが運用システムや重要なシステムに適していない場合があります。

#### <a name="for-private-preview"></a>*プライベート* プレビューについて
プライベート プレビューには、上記の項目に加え、次の固有の項目も当てはまります。

* 機能やソリューションを改善するために、利用に関するフィードバックをお寄せください。
* アンケート、電話、電子メールを使用して、フィードバックを依頼する場合があります。
* 必ずしも適切に動作するとは限りません。
* 参加するために機密保持契約 (NDA) が必要な場合や機密コンテンツが含まれている場合があります。
  * ブログ、ツイート、サード パーティとのやり取りを行う前に、プレビューを担当しているプログラム マネージャーに確認を取り、開示に関する制限を把握してください。
* 運用システムまたは重要なシステムで実行しないでください。

### <a name="how-do-i-get-access-to-private-preview-features-and-solutions"></a>プライベート プレビューの機能やソリューションにアクセスする方法
Microsoft では、プレビューに応じたさまざまな方法でお客様をプライベート プレビューに招待します。

* 毎月のお客様アンケートに答えたり、追跡調査を許可したりすることで、プライベート プレビューへの招待を受ける機会が増えます。
* Microsoft アカウント チームが推薦する場合があります。
* Twitter の [msopsmgmt](https://twitter.com/msopsmgmt) に投稿された詳細に基づいてサインアップできます。
* コミュニティ イベントで共有された詳細に基づいてサインアップできます。ミートアップ、カンファレンス、オンライン コミュニティで見つけてください。

## <a name="next-steps"></a>次のステップ
* [ログを検索](log-analytics-log-searches.md)して、管理ソリューションによって収集された詳細情報を確認します。

