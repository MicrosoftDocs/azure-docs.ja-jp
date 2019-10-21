---
title: Azure プラットフォーム ログの概要 | Microsoft Docs
description: Azure リソースの操作に関する高頻度の豊富なデータを提供する、Azure の診断ログの概要。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/20/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: c1602b9beb5e667caf1688901c7ae4e67e5f5839
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71263042"
---
# <a name="overview-of-azure-platform-logs"></a>Azure プラットフォーム ログの概要
プラットフォーム ログでは、Azure リソースとそれらが依存している Azure プラットフォームの詳細な診断情報と監査情報が提供されます。 これらは自動的に生成されますが、特定のプラットフォーム ログを保持するための 1 つ以上の送信先に転送するように構成する必要があります。 この記事では、提供される情報の内容や収集と分析のための構成方法など、プラットフォーム ログの概要について説明します。

## <a name="types-of-platform-logs"></a>プラットフォーム ログの種類
次の表に、Azure のさまざまなレイヤーで使用できる特定のプラットフォーム ログを示します。

| レイヤー | ログ | 説明 |
|:---|:---|:---|
| Azure リソース | [リソース ログ](resource-logs-overview.md) | Azure リソース ("*データ プレーン*") 内で実行された操作に関する分析情報を提供します。たとえば、Key Vault からのシークレットの取得や、データベースに対する要求などです。 リソース ログの内容は、Azure サービスとリソースの種類によって異なります。<br>*リソース ログは、以前は診断ログと呼ばれていました。*  |
| Azure サブスクリプション | [アクティビティ ログ](activity-logs-overview.md) | Service Health イベントの更新に加えて、外部 ("*管理プレーン*") からサブスクリプションの各 Azure リソースに対する操作についての分析情報を提供します。 Azure サブスクリプションごとに 1 つのアクティビティ ログがあります。   |
| Azure テナント | [Azure Active Directory ログ](../../active-directory/reports-monitoring/overview-reports.md)  | サインイン アクティビティの履歴と、特定のテナントに対して Azure Active Directory で行われた変更の監査証跡が含まれます。   |


![プラットフォーム ログの概要](media/platform-logs-overview/logs-overview.png)

## <a name="viewing-platform-logs"></a>プラットフォーム ログの表示
Azure portal で、[アクティビティ ログ](activity-log-view.md)と [Azure Active Directory ログ](../../active-directory/reports-monitoring/overview-reports.md)を表示できます。 リソース ログを表示するには、[送信先](#destinations)に送信する必要があります。


## <a name="destinations"></a>Destinations
監視要件に応じて、次の表に示す 1 つ以上の送信先にプラットフォーム ログを送信できます。 

| Destination | シナリオ | 参照 |
|:---|:---|:---|:---|
| Log Analytics ワークスペース | 他の監視データと共にログを分析し、ログ クエリやアラートなどの Azure Monitor 機能を活用します。 | [リソース ログ](resource-logs-collect-storage.md)<br>[アクティビティ ログ](activity-log-collect.md)<br>[Azure Active Directory ログ](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md) |
| Azure Storage | 監査、静的分析、バックアップのためにログをアーカイブします。 |[リソース ログ](archive-diagnostic-logs.md)<br>[アクティビティ ログ](activity-log-export.md)<br>[Azure Active Directory ログ](../../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md) |
| イベント ハブ | サード パーティのログ システムやテレメトリ システムにログをストリーミングします。  |[リソース ログ](resource-logs-stream-event-hubs.md)<br>[アクティビティ ログ](activity-log-export.md)<br>[Azure Active Directory ログ](../../active-directory/reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md) |


## <a name="diagnostic-settings-and-log-profiles"></a>診断設定とログ プロファイル
[診断設定を作成](diagnostic-settings.md)して、リソース ログと Azure Active Directory ログの送信先を構成します。 アクティビティ ログの送信先を構成するには、[ログ プロファイルを作成する](activity-log-export.md)か、[Log Analytics ワークスペースにそのログを接続します](activity-log-collect.md)。

診断設定とログ プロファイルでは、次の内容が定義されます。

- 選択したログとメトリックを送信する 1 つ以上の送信先。
- リソースから送信先に送信するログ カテゴリとメトリック。
- ストレージ アカウントを送信先として選択した場合に、ログの各カテゴリを保持する期間。



## <a name="next-steps"></a>次の手順

* [アクティビティ ログの詳細を確認する](activity-logs-overview.md)
* [リソース ログの詳細を確認する](resource-logs-overview.md)
* [さまざまな Azure サービスのリソース ログ スキーマを確認する](diagnostic-logs-schema.md)
