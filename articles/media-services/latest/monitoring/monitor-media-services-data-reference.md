---
title: Media Services データ参照の監視
description: Media Services を監視する際に必要となる重要なリファレンス資料
author: IngridAtMicrosoft
ms.author: inhenkel
manager: femila
ms.topic: reference
ms.service: media-services
ms.date: 03/17/2021
ms.openlocfilehash: 866b2faf473f06fc3f85cdb434d6555504a7f6a8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598186"
---
# <a name="monitoring-media-services-data-reference"></a>Media Services データ参照の監視

この記事では、Media Services の監視に役立つデータについて説明します。 Azure Monitor でサポートされているすべてのプラットフォーム メトリックの詳細については、「[Azure Monitor のサポートされるメトリック](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported)」を参照してください。

## <a name="media-services-metrics"></a>Media Services メトリック

メトリックは、値の変化とは無関係に、一定の間隔で収集されます。 これらは頻繁にサンプリングでき、比較的単純なロジックですばやく起動できるため、アラートを発行するときに役に立ちます。

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

[アカウントのクォータと制限事項](../limits-quotas-constraints.md)もご確認ください。

### <a name="streaming-endpoint"></a>ストリーミング エンドポイント

次の Media Services [ストリーミング エンドポイント](/rest/api/media/streamingendpoints) メトリックがサポートされています。

|メトリックの名前|Display name|説明|
|---|---|---|
|Requests|Requests|ストリーミング エンドポイントで処理された HTTP 要求の合計数を提供します。|
|エグレス|エグレス|ストリーミング エンドポイントあたりの合計エグレス バイト/分。|
|SuccessE2ELatency|成功したエンド ツー エンドの待機時間|ストリーミング エンドポイントが要求を受信してから応答の最後のバイトが送信されるまでの期間。|
|CPU 使用率| | premium ストリーミング エンドポイントの CPU 使用率。 このデータは、standard ストリーミング エンドポイントでは使用できません。 |
|エグレス帯域幅 | | エグレス帯域幅 (ビット/秒)。|

## <a name="metric-dimensions"></a>メトリック ディメンション

メトリック ディメンションの詳細については、「[多次元メトリック](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics#multi-dimensional-metrics)」を参照してください。

<!--**PLACEHOLDER** for dimensions table.-->

## <a name="resource-logs"></a>リソース ログ

## <a name="media-services-diagnostic-logs"></a>Media Services の診断ログ

診断ログからは、Azure リソースの操作に関するデータが豊富かつ頻繁に提供されます。 詳細については、[Azure リソースからのログ データを収集して使用する方法](https://docs.microsoft.com/azure/azure-monitor/essentials/platform-logs-overview.md)に関するページをご覧ください。

Media Services では、次の診断ログがサポートされています。

* キー配信

### <a name="key-delivery"></a>キー配信

|名前|説明|
|---|---|
|キー配信サービス要求|キー配信サービス要求の情報を表示するログ。 詳細については、[スキーマ](monitor-media-services-data-reference.md)に関するページを参照してください。|

## <a name="schemas"></a>スキーマ

診断ログの上位スキーマについて詳しくは、「[Azure 診断ログでサポートされているサービス、スキーマ、カテゴリ](https://docs.microsoft.com/azure/azure-monitor/essentials/resource-logs-schema.md)」をご覧ください。

## <a name="key-delivery-log-schema-properties"></a>キー配信ログのスキーマのプロパティ

これらのプロパティは、キー配信ログのスキーマに固有です。

|名前|説明|
|---|---|
|keyId|要求されたキーの ID。|
|keyType|次のいずれかの値を指定できます:"Clear" (暗号化なし)、"FairPlay"、"PlayReady"、または"Widevine"。|
|policyName|ポリシーの Azure Resource Manager の名前。|
|tokenType|トークンの型。|
|statusMessage|状態メッセージ。|

### <a name="example"></a>例

キー配信の要求スキーマのプロパティ。

```json
{
    "time": "2019-01-11T17:59:10.4908614Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Succeeded",
    "resultSignature": "OK",
    "durationMs": 315,
    "identity": {
        "authorization": {
            "issuer": "http://testacs",
            "audience": "urn:test"
        },
        "claims": {
            "urn:microsoft:azure:mediaservices:contentkeyidentifier": "3321e646-78d0-4896-84ec-c7b98eddfca5",
            "iss": "http://testacs",
            "aud": "urn:test",
            "exp": "1547233138"
        }
    },
    "level": "Informational",
    "location": "uswestcentral",
    "properties": {
        "requestId": "b0243468-d8e5-4edf-a48b-d408e1661050",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "JWT",
        "statusMessage": "OK"
    }
} 
```

```json
 {
    "time": "2019-01-11T17:59:33.4676382Z",
    "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-0000000000/RESOURCEGROUPS/SBKEY/PROVIDERS/MICROSOFT.MEDIA/MEDIASERVICES/SBDNSTEST",
    "operationName": "MICROSOFT.MEDIA/MEDIASERVICES/CONTENTKEYS/READ",
    "operationVersion": "1.0",
    "category": "KeyDeliveryRequests",
    "resultType": "Failed",
    "resultSignature": "Unauthorized",
    "durationMs": 2,
    "level": "Error",
    "location": "uswestcentral",
    "properties": {
        "requestId": "875af030-b77c-416b-b7e1-58f23ebec182",
        "keyType": "Clear",
        "keyId": "3321e646-78d0-4896-84ec-c7b98eddfca5",
        "policyName": "56a70229-82d0-4174-82bc-e9d3b14e5dbf",
        "tokenType": "None",
        "statusMessage": "No token present in authorization header or URL."
    }
} 
```

>[!NOTE]
> Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [monitoring-next-steps](../includes/monitoring-next-steps.md)]
