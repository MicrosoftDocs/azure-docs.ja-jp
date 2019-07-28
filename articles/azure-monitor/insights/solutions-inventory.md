---
title: Azure での監視ソリューションのインベントリ | Microsoft Docs
description: Azure Monitor の監視ソリューションには、特定の問題点に関するメトリックを提供するロジックや視覚化、データ取得規則が集約されています。  この記事では、Microsoft から利用できる管理ソリューションの一覧を示し、データ収集の手法および頻度に関する詳細について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: f029dd6d-58ae-42c5-ad27-e6cc92352b3b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/26/2018
ms.author: bwren
ms.openlocfilehash: 9398815ea75c0eacd99a6e40c569254fac671cbb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66234031"
---
# <a name="inventory-and-data-collection-details-for-monitoring-solutions-in-azure"></a>Azure における監視ソリューションのインベントリとデータ収集の詳細
[監視ソリューション](solutions.md)では、Azure のサービスを活用して、特定のアプリケーションまたはサービスの操作に対する詳細な分析情報を提供します。 通常、監視ソリューションではログ データが収集されます。また、収集されたデータを分析するクエリとビューが用意されています。 使用する任意のアプリケーションおよびサービスの Azure Monitor に監視ソリューションを追加できます。 管理ソリューションは通常、無料で使用できますが、データの収集に対して利用料金が発生する場合があります。

この記事では、Microsoft で利用可能な[監視ソリューション](solutions.md)の一覧を、詳細なドキュメントへのリンクと共に示します。  また、Azure Monitor へのデータ収集の手法および頻度に関する情報についても提供します。  この記事の情報を利用して、使用可能な各種のソリューションを識別し、さまざまな監視ソリューションのデータ フローと接続要件を理解することができます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="list-of-monitoring-solutions"></a>監視ソリューションの一覧

次の表に、Microsoft が提供する Azure の[監視ソリューション](solutions.md)を示します。 列のエントリは、該当のソリューションではその手法を使用して Azure Monitor にデータを収集することを意味します。  ソリューションのどの列にも印がない場合は、別の Azure サービスから直接 Azure Monitor に書き込みが行われます。 詳細については、各ソリューションの詳しいドキュメントへのリンクを確認してください。

列の説明を次に示します。

- **Microsoft Monitoring Agent** - Windows および Linux 上で使用され、SCOM からは管理パックを、また、Azure からは監視ソリューションを実行するエージェント。 この構成では、エージェントは Operations Manager 管理グループに接続されずに、直接 Azure Monitor に接続されます。 
- **Operations Manager** - Microsoft Monitoring Agent と同一のエージェント。 この構成では、Azure Monitor に接続されている [Operations Manager 管理グループに接続](../platform/om-agents.md)されます。 
-  **Azure Storage** - ソリューションは、Azure ストレージ アカウントからデータを収集します。 
- **Operations Manager が必要か** - 監視ソリューションによるデータ収集には、接続された Operations Manager 管理グループが必要です。 
- **管理グループ経由で送信される Operations Manager エージェント データ** - エージェントが[ SCOM 管理グループに接続されている](../platform/om-agents.md)場合、データは管理サーバーから Azure Monitor に送信されます。 この場合、エージェントが直接 Azure Monitor に接続される必要はありません。 このボックスにチェックマークがない場合、エージェントが SCOM 管理グループに接続されている場合でも、データはエージェントから直接 Azure Monitor に送信されます。 また、エージェントは、[Log Analytics ゲートウェイ](../platform/gateway.md)経由で Azure Monitor と通信できる必要があります。
- **収集の頻度** - 監視ソリューションによってデータが収集される頻度を指定します。 



| **監視ソリューション** | **プラットフォーム** | **Microsoft Monitoring Agent** | **Operations Manager エージェント** | **Azure Storage** | **Operations Manager が必要か** | **管理グループ経由で送信される Operations Manager エージェント データ** | **収集の頻度** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| [アクティビティ ログ分析](../platform/activity-log-collect.md) | Azure | | | | | | 通知時 |
| [AD 評価](ad-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 日 |
| [AD レプリケーションの状態](ad-replication-status.md) |Windows |&#8226; |&#8226; | | |&#8226; |5 日 |
| [Agent Health](solution-agenthealth.md) | Windows および Linux | &#8226; | &#8226; | | | &#8226; | 1 分 |
| [アラート管理](../platform/alert-management-solution.md) (Nagios) |Linux |&#8226; | | | | |着信時 |
| [アラート管理](../platform/alert-management-solution.md) (Zabbix) |Linux |&#8226; | | | | |1 分 |
| [アラート管理](../platform/alert-management-solution.md) (Operations Manager) |Windows | |&#8226; | |&#8226; |&#8226; |3 分 |
| [Azure Site Recovery](../../site-recovery/site-recovery-overview.md) | Azure | | | | | | 該当なし |
| [Application Insights Connector (非推奨)](../platform/app-insights-connector.md) | Azure | | | |  |  | 通知時 |
| [Automation Hybrid Worker](../../automation/automation-hybrid-runbook-worker.md) | Windows | &#8226; | &#8226; |  |  |  | 該当なし |
| [Azure Application Gateway 分析](azure-networking-analytics.md) | Azure |  |  |  |  |  | 通知時 |
| **監視ソリューション** | **プラットフォーム** | **Microsoft Monitoring Agent** | **Operations Manager エージェント** | **Azure Storage** | **Operations Manager が必要か** | **管理グループ経由で送信される Operations Manager エージェント データ** | **収集の頻度** |
| [Azure ネットワーク セキュリティ グループ分析 (非推奨)](azure-networking-analytics.md) | Azure |  |  |  |  |  | 通知時 |
| [Azure SQL Analytics (プレビュー)](azure-sql.md) | Windows | | | | | | 1 分 |
| [Backup](https://azure.microsoft.com/resources/templates/101-backup-oms-monitoring/) | Azure |  |  |  |  |  | 通知時 |
| [Capacity and Performance (プレビュー)](capacity-performance.md) |Windows |&#8226; |&#8226; | | |&#8226; |着信時 |
| [変更の追跡](../../automation/change-tracking.md) |Windows |&#8226; |&#8226; | | |&#8226; |[状況に応じて異なる](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [変更の追跡](../../automation/change-tracking.md) |Linux |&#8226; | | | | |[状況に応じて異なる](../../automation/change-tracking.md#change-tracking-data-collection-details) |
| [Containers](containers.md) | Windows および Linux | &#8226; | &#8226; |  |  |  | 3 分 |
| [Key Vault Analytics](azure-key-vault.md) |Windows | | | | | |通知時 |
| [マルウェアの評価](../../security-center/security-center-install-endpoint-protection.md) |Windows |&#8226; |&#8226; | | |&#8226; |1 時間に 1 回 |
| [ネットワーク パフォーマンス監視](network-performance-monitor.md) | Windows | &#8226; | &#8226; |  |  |  | TCP ハンドシェイク (5 秒ごと)、データ送信 (3 分ごと) |
| [Office 365 Analytics (プレビュー)](solution-office-365.md) |Windows | | | | | |通知時 |
| **監視ソリューション** | **プラットフォーム** | **Microsoft Monitoring Agent** | **Operations Manager エージェント** | **Azure Storage** | **Operations Manager が必要か** | **管理グループ経由で送信される Operations Manager エージェント データ** | **収集の頻度** |
| [Service Fabric 分析](../../service-fabric/service-fabric-diagnostics-oms-setup.md) |Windows | | |&#8226; | | |5 分 |
| [サービス マップ](service-map.md) | Windows および Linux | &#8226; | &#8226; |  |  |  | 15 秒 |
| [SQL の評価](sql-assessment.md) |Windows |&#8226; |&#8226; | | |&#8226; |7 日 |
| [SurfaceHub](surface-hubs.md) |Windows |&#8226; | | | | |着信時 |
| [System Center Operations Manager Assessment (プレビュー)](scom-assessment.md) | Windows | &#8226; | &#8226; |  |  | &#8226; | 7 日 |
| [更新管理](../../automation/automation-update-management.md) | Windows |&#8226; |&#8226; | | |&#8226; |1 日に少なくとも 2 回、更新プログラムのインストールの 15 分後 |
| [Upgrade Readiness](https://docs.microsoft.com/windows/deployment/upgrade/upgrade-readiness-get-started) | Windows | &#8226; |  |  |  |  | 2 日 |
| [VMware Monitoring (非推奨)](vmware.md) | Linux | &#8226; |  |  |  |  | 3 分 |
| [Wire Data 2.0 (プレビュー)](wire-data.md) |Windows (2012 R2/8.1 以降) |&#8226; |&#8226; | | | | 1 分 |




## <a name="next-steps"></a>次の手順
* [監視ソリューションをインストールして使用する](solutions.md)方法を確認してください。
* 監視ソリューションで収集されたデータを分析するための[クエリを作成する](../log-query/log-query-overview.md)方法を確認します。
