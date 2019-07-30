---
title: Azure Monitor を使用した Azure Media Services のメトリックと診断ログの監視 | Microsoft Docs
description: この記事では、Azure Monitor を使用して Azure Media Services のメトリックと診断ログを監視する方法の概要を説明します。
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
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 138b31856e424078ae32ca2f53393f5aa13505b2
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797601"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Media Services のメトリックと診断ログの監視

[Azure Monitor](../../azure-monitor/overview.md) により、アプリケーションの実行状況を理解する上で役立つメトリックと診断ログを監視できます。 Azure Monitor で収集されたすべてのデータは、2 つの基本的な型であるメトリックとログのどちらかに該当します。 Media Services の診断ログを監視し、収集されたメトリックおよびログのアラートと通知を作成できます。 [メトリックス エクスプローラー](../../azure-monitor/platform/metrics-getting-started.md)を使用して、メトリック データを視覚化し、分析できます。 ログを [Azure Storage](https://azure.microsoft.com/services/storage/) に送信し、それらを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にストリーミング配信して、[Log Analytics](https://azure.microsoft.com/services/log-analytics/) にエクスポートできます。またはサード パーティのサービスを使用できます。

詳細については、[Azure Monitor メトリック](../../azure-monitor/platform/data-platform.md)および [Azure Monitor 診断ログ](../../azure-monitor/platform/diagnostic-logs-overview.md)に関する記事をご覧ください。

このトピックでは、サポートされている [Media Services のメトリック](#media-services-metrics)と [Media Services の診断ログ](#media-services-diagnostic-logs)について説明します。

## <a name="media-services-metrics"></a>Media Services メトリック

メトリックは、値の変化とは無関係に、一定の間隔で収集されます。 これらは頻繁にサンプリングでき、比較的単純なロジックですばやく起動できるため、アラートを発行するときに役に立ちます。 メトリック アラートの作成方法の詳細については、「[Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)」を参照してください。

Media Services では、次のリソースの監視メトリックがサポートされています。

* Account
* ストリーミング エンドポイント
 
### <a name="account"></a>Account

次のアカウント メトリックを監視できます。 

|メトリックの名前|Display name|説明|
|---|---|---|
|AssetCount|アセット数|アカウント内のアセットです。|
|AssetQuota|アセットのクォータ|アカウント内のアセットのクォータです。|
|AssetQuotaUsedPercentage|アセットのクォータの使用率|アセットのクォータのうち、既に使用されている割合です。|
|ContentKeyPolicyCount|コンテンツ キー ポリシー数|アカウント内のコンテンツ キー ポリシーの数です。|
|ContentKeyPolicyQuota|コンテンツ キー ポリシーのクォータ|アカウント内のコンテンツ キー ポリシーのクォータです。|
|ContentKeyPolicyQuotaUsedPercentage|コンテンツ キー ポリシーのクォータの使用率|コンテンツ キー ポリシーのクォータのうち、既に使用されている割合です。|
|StreamingPolicyCount|ストリーミング ポリシー数|アカウント内のストリーミング ポリシーの数です。|
|StreamingPolicyQuota|ストリーミング ポリシーのクォータ|アカウント内のストリーミング ポリシーのクォータです。|
|StreamingPolicyQuotaUsedPercentage|ストリーミング ポリシーのクォータの使用率|ストリーミング ポリシーのクォータのうち、既に使用されている割合です。|
 
[アカウントのクォータと制限事項](limits-quotas-constraints.md)もご確認ください。

### <a name="streaming-endpoint"></a>ストリーミング エンドポイント

現在、次の Media Services では、[ストリーミング エンドポイント](https://docs.microsoft.com/rest/api/media/streamingendpoints) メトリックが Azure によって生成されます。

|メトリックの名前|Display name|説明|
|---|---|---|
|Requests|Requests|ストリーミング エンドポイントで処理された HTTP 要求の合計数を提供します。|
|エグレス|エグレス|エグレス バイト数の合計。 ストリーミング エンドポイントによってストリーミングされるバイト数など。|
|SuccessE2ELatency|成功したエンド ツー エンドの待機時間|ストリーミング エンドポイントが要求を受信してから応答の最後のバイトが送信されるまでの期間。|

### <a name="why-would-i-want-to-use-metrics"></a>メトリックを使用する理由 

ここでは、Media Services のメトリックを監視すると、アプリケーションのパフォーマンスの把握にどのように役立つかについて、いくつかの例を示します。 Media Services のメトリックを使用して対処できる内容は次のとおりです。

* 制限値を超過したタイミングを把握するために、Standard ストリーミング エンドポイントを監視する方法。
* 十分な Premium ストリーミング エンドポイント スケール ユニットがあるかどうかを把握する方法。 
* アラートを設定して、ストリーミング エンドポイントをスケールアップするタイミングを把握する方法。
* アラートを設定して、アカウントで構成されている最大エグレスに達したタイミングを把握する方法。
* 失敗した要求の内訳を表示し、エラーの原因を確認する方法。
* パッケージャーからプルされている DASH または HLS 要求の数を確認する方法。
* アラートを設定して、失敗した要求の数がしきい値に達したタイミングを把握する方法。 

### <a name="example"></a>例

[Media Services のメトリックを監視する方法](media-services-metrics-howto.md)に関するページをご覧ください

## <a name="media-services-diagnostic-logs"></a>Media Services の診断ログ

診断ログからは、Azure リソースの操作で頻繁に見られるデータが豊富に提供されます。 詳細については、[Azure リソースからのログ データを収集して使用する方法](../../azure-monitor/platform/diagnostic-logs-overview.md)に関するページをご覧ください。

Media Services では、次の診断ログがサポートされています。

* キー配信

### <a name="key-delivery"></a>キー配信

|Name|説明|
|---|---|
|キー配信サービス要求|キー配信サービス要求の情報を表示するログ。 詳細については、[スキーマ](media-services-diagnostic-logs-schema.md)に関するページを参照してください。|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>診断ログを使用する理由 

キー配信の診断ログで調べることができる内容は、次のとおりです。

* DRM の種類ごとに提供されるライセンス数
* ポリシーごとに提供されるライセンス数 
* DRM またはポリシーの種類ごとのエラー数
* クライアントからの未承認のライセンス要求数

### <a name="example"></a>例

[Media Services の診断ログを監視する方法](media-services-diagnostic-logs-howto.md)に関するページをご覧ください

## <a name="next-steps"></a>次の手順 

* [Azure リソースからログ データを収集して使用する方法](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)
* [Media Services のメトリックを監視する方法](media-services-metrics-howto.md)
* [Media Services の診断ログを監視する方法](media-services-diagnostic-logs-howto.md)
