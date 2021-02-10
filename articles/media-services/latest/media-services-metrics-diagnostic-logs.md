---
title: Azure Monitor を使用したメトリックと診断ログ
description: Azure Monitor 経由で Azure Media Services のメトリックと診断ログを監視する方法について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2020
ms.author: inhenkel
ms.openlocfilehash: 35b0d9c2937024341be6183ac1438b7c4500d6ef
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2021
ms.locfileid: "98954988"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-with-azure-monitor"></a>Azure Monitor を使用した Media Services のメトリックと診断ログを監視する

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

[Azure Monitor](../../azure-monitor/overview.md) を使用すると、アプリの実行状況を理解するために役立つメトリックと診断ログを監視できます。 Azure Monitor によって収集されたすべてのデータは、2 つの基本的な種類であるメトリックとログのどちらかに該当します。 Media Services の診断ログを監視し、収集されたメトリックおよびログのアラートと通知を作成できます。 [メトリックス エクスプローラー](../../azure-monitor/platform/metrics-getting-started.md)を使用して、メトリック データを視覚化し、分析できます。 ログを [Azure Storage](https://azure.microsoft.com/services/storage/) に送信したり、[Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) にストリーミングしたり、[Log Analytics](https://azure.microsoft.com/services/log-analytics/) にエクスポートしたり、サードパーティのサービスを使用したりできます。

詳細については、[Azure Monitor メトリック](../../azure-monitor/platform/data-platform.md)および [Azure Monitor 診断ログ](../../azure-monitor/platform/platform-logs-overview.md)に関する記事をご覧ください。

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

次の Media Services [ストリーミング エンドポイント](/rest/api/media/streamingendpoints) メトリックがサポートされています。

|メトリックの名前|Display name|説明|
|---|---|---|
|Requests|Requests|ストリーミング エンドポイントで処理された HTTP 要求の合計数を提供します。|
|エグレス|エグレス|ストリーミング エンドポイントあたりの合計エグレス バイト/分。|
|SuccessE2ELatency|成功したエンド ツー エンドの待機時間|ストリーミング エンドポイントが要求を受信してから応答の最後のバイトが送信されるまでの期間。|
|CPU 使用率| | premium ストリーミング エンドポイントの CPU 使用率。 このデータは、standard ストリーミング エンドポイントでは使用できません。 |
|エグレス帯域幅 | | エグレス帯域幅 (ビット/秒)。|

### <a name="metrics-are-useful"></a>メトリックは役に立つ

次に示すいくつかの例は、Media Services のメトリックの監視がアプリの実行状況を理解するためにどのように役立つかを示しています。 Media Services のメトリックを使用して対処できる内容は次のとおりです。

* 制限値を超過したタイミングを把握するために、Standard ストリーミング エンドポイントを監視する方法。
* 十分な Premium ストリーミング エンドポイント スケール ユニットがあるかどうかを把握する方法。
* アラートを設定して、ストリーミング エンドポイントをスケールアップするタイミングを把握する方法。
* アラートを設定して、アカウントで構成されている最大エグレスに達したタイミングを把握する方法。
* 失敗した要求の内訳を表示し、エラーの原因を確認する方法。
* パッケージャーからプルされている DASH または HLS 要求の数を確認する方法。
* アラートを設定して、失敗した要求の数がしきい値に達したタイミングを把握する方法。

同時実行性は、時間の経過と共に 1 つのアカウントで使用されるストリーミング エンドポイントの数の問題になります。 複数のプロトコルへのダイナミック パッケージ、複数の DRM 暗号化など、複雑な公開パラメーターを持つ複数の同時ストリーム間の関係に留意する必要があります。公開されたライブ ストリームが追加されるたびに、ストリーミング エンドポイントの CPU と出力帯域幅が増えます。 この点を念頭に置き、Azure Monitor を使用してストリーミング エンドポイントの使用率 (CPU とエグレス容量) を注意深く監視し、適切にスケーリングしていることを確認する必要があります (または、同時実行性が非常に高くなっている場合は、トラフィックを複数のストリーミング エンドポイントに分割します)。

### <a name="example"></a>例

[Media Services のメトリックを監視する方法](media-services-metrics-howto.md)に関するページを参照してください。

## <a name="media-services-diagnostic-logs"></a>Media Services の診断ログ

診断ログからは、Azure リソースの操作に関するデータが豊富かつ頻繁に提供されます。 詳細については、[Azure リソースからのログ データを収集して使用する方法](../../azure-monitor/platform/platform-logs-overview.md)に関するページをご覧ください。

Media Services では、次の診断ログがサポートされています。

* キー配信

### <a name="key-delivery"></a>キー配信

|名前|説明|
|---|---|
|キー配信サービス要求|キー配信サービス要求の情報を表示するログ。 詳細については、[スキーマ](media-services-diagnostic-logs-schema.md)に関するページを参照してください。|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>診断ログを使用する理由

キー配信の診断ログで調べることができるいくつかのことを次に示します。

* DRM の種類ごとに配信されるライセンスの数を確認します。
* ポリシーごとに配信されるライセンスの数を確認します。
* DRM またはポリシーの種類ごとのエラーを確認します。
* クライアントからの承認されていないライセンス要求の数を確認します。

### <a name="example"></a>例

[Media Services の診断ログを監視する方法](media-services-diagnostic-logs-howto.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure リソースからログ データを収集して使用する方法](../../azure-monitor/platform/platform-logs-overview.md)
* [Azure Monitor を使用してメトリック アラートを作成、表示、管理する](../../azure-monitor/platform/alerts-metric.md)
* [Media Services のメトリックを監視する方法](media-services-metrics-howto.md)
* [Media Services の診断ログを監視する方法](media-services-diagnostic-logs-howto.md)
