---
title: Azure で使用する監視データのソース
description: 現在 Azure で使用できるすべての監視データ ソースについて説明します。
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/12/2018
ms.author: johnkem
ms.subservice: ''
ms.openlocfilehash: c703f735f59a8425c2a14641781f482a6e2d1c78
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58848546"
---
# <a name="consume-monitoring-data-from-azure"></a>Azure の監視データの使用

Azure プラットフォーム全体にわたって、Azure Monitor パイプラインで監査データを 1 か所に集めていますが、現状、このパイプラインですべての監査データを利用できるわけではありません。 この記事では、プログラムで Azure サービスの監査データにアクセスする方法を説明します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="options-for-data-consumption"></a>データの使用のオプション

| データ型 | Category | サポートされているサービス | アクセスの方法 |
| --- | --- | --- | --- |
| Azure Monitor プラットフォーム レベルのメトリック | メトリック | [この一覧をご覧ください](metrics-supported.md) | <ul><li>**REST API:** [Azure Monitor メトリック API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Storage BLOB またはイベント ハブ:** [診断設定](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| ゲスト OS メトリック ( Perf counters など) のコンピューティング | メトリック | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) と Linux の仮想マシン (v2)、[Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**ストレージ テーブルまたは BLOB:** [Windows または Linux の Azure Diagnostics](diagnostics-extension-to-storage.md)</li><li>**イベント ハブ:** [Windows の Azure Diagnostics](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| カスタムまたはアプリケーション メトリック | メトリック | Application Insights を使用してインストルメント化されたすべてのアプリケーション | <ul><li>**REST API:** [Application Insights REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| Storage のメトリック | メトリック | Azure Storage | <ul><li>**ストレージ テーブル:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| 課金データ | メトリック | すべての Azure サービス | <ul><li>**REST API:** [Azure Resource Usage API と RateCard API](../../billing/billing-usage-rate-card-overview.md)</li></ul> |
| アクティビティ ログ | events | すべての Azure サービス | <ul><li>**REST API:** [Azure Monitor イベント API](https://docs.microsoft.com/rest/api/monitor/eventcategories)</li><li>**Storage BLOB またはイベント ハブ:** [ログ プロファイル](activity-logs-overview.md#export-the-activity-log-with-a-log-profile)</li></ul> |
| Azure Monitor 診断ログ | events | [この一覧をご覧ください](diagnostic-logs-schema.md) | <ul><li>**Storage BLOB またはイベント ハブ:** [診断設定](diagnostic-logs-overview.md#diagnostic-settings)</li></ul> |
| ゲスト OS ログ ( IIS、ETW、Syslog など) のコンピューティング | events | [Windows](/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines) と Linux の仮想マシン (v2)、[Cloud Services](../../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**ストレージ テーブルまたは BLOB:** [Windows または Linux の Azure Diagnostics](diagnostics-extension-to-storage.md)</li><li>**イベント ハブ:** [Windows の Azure Diagnostics](diagnostics-extension-stream-event-hubs.md)</li></ul> |
| App Service ログ | events | App Services | <ul><li>**ファイル、テーブル、または Blob Storage:** [Web アプリ診断](../../app-service/troubleshoot-diagnostic-logs.md)</li></ul> |
| ストレージ ログ | events | Azure Storage | <ul><li>**ストレージ テーブル:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/storage-analytics)</li></ul> |
| Security Center のアラート | events | Azure Security Center | <ul><li>**REST API:** [セキュリティのアラート](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory レポート | events | Azure Active Directory | <ul><li>**REST API:** [Azure Active Directory Graph API](../../active-directory/reports-monitoring/concept-reporting-api.md)</li></ul> |
| Security Center リソースのステータス | Status | [サポートされているすべてのリソース](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API:** [セキュリティ ステータス](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| リソース正常性 | Status | サポートされているサービス | <ul><li>**REST API:** [Resource Health REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure Monitor メトリックのアラート | 通知 | [この一覧をご覧ください](metrics-supported.md) | <ul><li>**Webhook:** [Azure メトリックのアラート](alerts-webhooks.md)</li></ul> |
| Azure Monitor アクティビティ ログのアラート | 通知 | すべての Azure サービス | <ul><li>**Webhook:** Azure アクティビティ ログのアラート</li></ul> |
| 自動スケールの通知 | 通知 | [この一覧をご覧ください](autoscale-overview.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [自動スケーリング通知の Webhook ペイロード スキーマ](autoscale-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| ログ検索クエリのアラート | 通知 | Azure Monitor ログ | <ul><li>**Webhook:** [ログ アラート ルールの Webhook アクション](alerts-log-webhook.md)</li></ul> |
| Application Insights メトリックのアラート | 通知 | Application Insights | <ul><li>**Webhook:** [Application Insights のアラート](../../azure-monitor/app/alerts.md)</li></ul> |
| Application Insights の Web テスト | 通知 | Application Insights | <ul><li>**Webhook:** [Application Insights のアラート](../../azure-monitor/app/alerts.md)</li></ul> |

## <a name="next-steps"></a>次の手順

- [Azure Monitor メトリック](data-platform.md)の詳細を確認する
- [Azure アクティビティ ログ](activity-logs-overview.md)の詳細を確認する
- [Azure 診断ログ](diagnostic-logs-overview.md)の詳細を確認する

