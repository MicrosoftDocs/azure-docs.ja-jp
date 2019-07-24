---
title: Azure Monitor を使用した Media Services のメトリックと診断ログの監視 | Microsoft Docs
description: この記事では、Azure Monitor を使用して Media Services のメトリックと診断ログを監視する方法の概要を説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: 23c87ae92a0f22b4a1a31c054df730af2efc07d1
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785087"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Media Services のメトリックと診断ログの監視

[Azure Monitor](../../azure-monitor/overview.md) により、アプリケーションの実行状況を理解する上で役立つメトリックと診断ログを監視できます。 Azure Monitor が収集したすべてのデータは、2 つの基本的な型であるメトリックとログのどちらかに該当します。 Media Services の診断ログを監視し、収集されたメトリックおよびログのアラートと通知を作成できます。 [メトリックス エクスプローラー](../../azure-monitor/platform/metrics-getting-started.md)を使用して、メトリック データを視覚化し、分析できます。 ログを [Azure Storage](https://azure.microsoft.com/services/storage/) に送信し、それらを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にストリーミング配信し、[Log Analytics](https://azure.microsoft.com/services/log-analytics/) にエクスポートできます。またはサード パーティのサービスを使用できます。

概要については、[Azure Monitor メトリック](../../azure-monitor/platform/data-platform.md)および [Azure Monitor 診断ログ](../../azure-monitor/platform/diagnostic-logs-overview.md)に関する記事をご覧ください。

このトピックでは、現在利用できる [Media Services のメトリック](#media-services-metrics)と [Media Services の診断ログ](#media-services-diagnostic-logs)について説明します。

## <a name="media-services-metrics"></a>Media Services メトリック

メトリックは、値の変化とは無関係に、一定の間隔で収集されます。 これらは頻繁にサンプリングでき、比較的単純なロジックですばやく起動できるため、アラートを発行するときに役に立ちます。

現在、次の Media Services では、[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints) メトリックが Azure によって生成されます。

|Name|説明|
|---|---|
|Requests|ストリーミング エンドポイントによって処理された要求の合計数に関する詳細情報を提供します。|
|エグレス|エグレス バイト数の合計。 ストリーミング エンドポイントによってストリーミングされるバイト数など。|
|成功したエンド ツー エンドの待機時間| 成功した要求のエンド ツー エンドの待機時間について情報を提供します。|

たとえば、CLI を使用した "Egress" メトリックを取得するには、次の `az monitor metrics` CLI コマンドを実行します。

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

メトリック アラートの作成方法の詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)」を参照してください。

## <a name="media-services-diagnostic-logs"></a>Media Services の診断ログ

現時点では、次の診断ログを取得できます。

|Name|説明|
|---|---|
|キー配信サービス要求|キー配信サービス要求の情報を表示するログ。 詳細については、[スキーマ](media-services-diagnostic-logs-schema.md)に関するページを参照してください。|

ストレージ アカウントでの診断ログの保存を有効にするには、次の `az monitor diagnostic-settings` CLI コマンドを実行します。 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

例: 

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>次の手順 

[Azure リソースからのログ データの収集と使用の方法](../../azure-monitor/platform/diagnostic-logs-overview.md)。
