---
title: "Azure の監視データの使用 | Microsoft Docs"
description: "現在 Azure で使用できるすべての監視データ ソースについて説明します。"
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/27/2017
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 6e0ad6b5bec11c5197dd7bded64168a1b8cc2fdd
ms.openlocfilehash: b58bc0455c634631a732e28fb46191949dbc2717
ms.lasthandoff: 03/28/2017


---
# <a name="consume-monitoring-data-from-azure"></a>Azure の監視データの使用

Azure プラットフォーム全体にわたって、Azure Monitor パイプラインで監査データを 1 か所に集めていますが、現状、このパイプラインですべての監査データを利用できるわけではありません。 この記事では、プログラムで Azure サービスの監査データにアクセスする方法を説明します。

## <a name="options-for-data-consumption"></a>データの使用のオプション

| データ型 | カテゴリ | サポートされているサービス | アクセスの方法 |
| --- | --- | --- | --- |
| Azure Monitor プラットフォーム レベルのメトリック | メトリック | [この一覧をご覧ください](monitoring-supported-metrics.md) | <ul><li>**REST API:** [Azure Monitor メトリック API](https://docs.microsoft.com/rest/api/monitor/metrics)</li><li>**Storage BLOB またはイベント ハブ:** [診断設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| ゲスト OS メトリック ( Perf counters など) のコンピューティング | メトリック | [Windows](../virtual-machines-dotnet-diagnostics.md) と Linux の仮想マシン (v2)、[Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**ストレージ テーブルまたは Storage BLOB:** [Windows または Linux の Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**イベント ハブ:** [Windows Azure 診断](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| カスタムまたはアプリケーション メトリック | メトリック | Application Insights を使用してインストルメント化されたすべてのアプリケーション | <ul><li>**REST API:** [Application Insights REST API](https://dev.applicationinsights.io/reference)</li></ul> |
| Storage のメトリック | メトリック | Azure Storage (Azure Storage) | <ul><li>**ストレージ テーブル:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)</li></ul> |
| 課金データ | メトリック | すべての Azure サービス | <ul><li>**REST API:** [Azure Resource Usage API と RateCard API](../billing/billing-usage-rate-card-overview.md)</li></ul> |
| アクティビティ ログ | イベント | すべての Azure サービス | <ul><li>**REST API:** [Azure Monitor イベント API](https://docs.microsoft.com/rest/api/monitor/events)</li><li>**Storage BLOB またはイベント ハブ:** [ログ プロファイル](monitoring-overview-activity-logs.md#export-the-activity-log-with-log-profiles)</li></ul> |
| Azure Monitor 診断ログ | イベント | [この一覧をご覧ください](monitoring-overview-of-diagnostic-logs.md#supported-services-and-schema-for-diagnostic-logs) | <ul><li>**Storage BLOB またはイベント ハブ:** [診断設定](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings)</li></ul> |
| ゲスト OS ログ ( IIS、ETW、Syslog など) のコンピューティング | イベント | [Windows](../virtual-machines-dotnet-diagnostics.md) と Linux の仮想マシン (v2)、[Cloud Services](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)、[Service Fabric](../service-fabric/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md) | <ul><li>**ストレージ テーブルまたは Storage BLOB:** [Windows または Linux の Azure 診断](../cloud-services/cloud-services-dotnet-diagnostics-storage.md)</li><li>**イベント ハブ:** [Windows Azure 診断](../event-hubs/event-hubs-streaming-azure-diags-data.md)</li></ul> |
| App Service ログ | イベント | App Services | <ul><li>**ファイル、テーブル、または BLOB Storage:** [Web アプリ診断](../app-service-web/web-sites-enable-diagnostic-log.md)</li></ul> |
| ストレージ ログ | イベント | Azure Storage (Azure Storage) | <ul><li>**ストレージ テーブル:** [Storage Analytics](https://docs.microsoft.com/rest/api/storageservices/fileservices/storage-analytics)</li></ul> |
| Security Center のアラート | イベント | [Azure Security Center] (Azure Security Center) | <ul><li>**REST API:** [セキュリティ アラート](https://msdn.microsoft.com/library/mt704050.aspx)</li></ul> |
| Active Directory レポート | イベント | Azure Active Directory | <ul><li>**REST API:** [Azure Active Directory Graph API](../active-directory/active-directory-reporting-api-getting-started.md)</li></ul> |
| Security Center リソースのステータス | [Status] | [サポートされているすべてのリソース](https://msdn.microsoft.com/library/mt704041.aspx#Anchor_1) | <ul><li>**REST API:** [セキュリティ ステータス](https://msdn.microsoft.com/library/mt704041.aspx)</li></ul> |
| リソース正常性 | [Status] | サポートされているサービス | <ul><li>**REST API:** [Resource Health REST API](https://azure.microsoft.com/blog/reduce-troubleshooting-time-with-azure-resource-health/)</li></ul> |
| Azure Monitor メトリックのアラート | 通知 | [この一覧をご覧ください](monitoring-supported-metrics.md) | <ul><li>**Webhook:** [Azure メトリックのアラート](insights-webhooks-alerts.md)</li></ul> |
| Azure Monitor アクティビティ ログのアラート | 通知 | すべての Azure サービス | <ul><li>**Webhook:** Azure アクティビティ ログのアラート</li></ul> |
| 自動スケールの通知 | 通知 | [この一覧をご覧ください](monitoring-overview-autoscale.md#supported-services-for-autoscale) | <ul><li>**Webhook:** [自動スケール通知の Webhook ペイロード スキーマ](insights-autoscale-to-webhook-email.md#autoscale-notification-webhook-payload-schema)</li></ul> |
| OMS ログ検索クエリのアラート | 通知 | OMS Log Analytics | <ul><li>**Webhook:** [Log Analytics のアラート](../log-analytics/log-analytics-alerts-actions.md#webhook-actions)</li></ul> |
| Application Insights メトリックのアラート | 通知 | Application Insights | <ul><li>**Webhook:** [Application Insights のアラート](../application-insights/app-insights-alerts.md)</li></ul> |
| Application Insights の Web テスト | 通知 | Application Insights | <ul><li>**Webhook:** [Application Insights のアラート](../application-insights/app-insights-alerts.md)</li></ul> |

## <a name="next-steps"></a>次のステップ

- [Azure Monitor メトリック](monitoring-overview-metrics.md)の詳細を確認する
- [Azure アクティビティ ログ](monitoring-overview-activity-logs.md)の詳細を確認する
- [Azure 診断ログ](monitoring-overview-of-diagnostic-logs.md)の詳細を確認する

