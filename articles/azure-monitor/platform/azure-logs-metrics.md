---
title: Azure でのログとメトリック | Microsoft Docs
description: Azure リソースの操作で頻繁に見られるデータを豊富に提供する、Azure の診断ログの概要。
author: bwren
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: bwren
ms.subservice: logs
ms.openlocfilehash: 3fe220506e074f881a16c1805d25fb4b39927488
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262978"
---
# <a name="logs-and-metrics-in-azure"></a>Azure でのログとメトリック
さまざまな[ログ](data-platform-logs.md)と[メトリック](data-platform-metrics.md)があります

Azure でのアプリケーションとサービスの監視方法は、[Azure データ プラットフォーム](data-platform.md)に格納される、に分離できます。 

ログとメトリックは 2 つのカテゴリに分けることができます

- プラットフォーム - 単純なもの以外に構成を必要としないで自動的に生成されるログとメトリック 



| レイヤー | プラットフォーム ログ | プラットフォーム メトリック | カスタム ログ | カスタム メトリック |
|:---|:---|:---|:---|:---|
| Application  | | | | |
| ゲスト OS     | Heartbeat |  | 診断拡張機能<br>Log Analytics エージェント | 診断拡張機能 |
| リソース     | [リソース ログ](resource-logs-overview.md)<br>(各サービスに固有) | [リソース メトリック](metrics-supported.md)<br>(各サービスに固有) | | [カスタム メトリック](metrics-custom-overview.md) |
| Subscription | [アクティビティ ログ](activity-logs-overview.md) | | | |
| Tenant       | 

## <a name="next-steps"></a>次の手順

* [リソース診断ログを **Event Hubs** にストリーミングする](resource-logs-stream-event-hubs.md)
* [Azure Monitor REST API を使用してリソース診断設定を変更する](https://docs.microsoft.com/rest/api/monitor/)
* [Azure Monitor を使用した、Azure ストレージからのログの分析](collect-azure-metrics-logs.md)
