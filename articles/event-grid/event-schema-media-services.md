---
title: Event Grid ソースとしての Azure Media Services
description: Azure Event Grid で Media Services イベント用に用意されているプロパティについて説明します
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 1f2f62f0a5ceed0e000c8bb7690fff009593bf82
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104591930"
---
# <a name="azure-media-services-as-an-event-grid-source"></a>Event Grid ソースとしての Azure Media Services

この記事では、Media Services イベント用のスキーマとプロパティについて説明します。

## <a name="job-related-event-types"></a>ジョブ関連イベントの種類

Media Services では、下で説明する **ジョブ関連** イベントの種類が出力されます。 **ジョブ関連** イベントには 2 つのカテゴリがあります。"ジョブの状態変更の監視" と "ジョブ出力の状態変更の監視" です。 

JobStateChange イベントをサブスクライブすると、すべてのイベントを登録できます。 また、特定のイベントのみ (たとえば、JobErrored、JobFinished、JobCanceled などの最終状態) をサブスクライブすることもできます。   

### <a name="monitoring-job-state-changes"></a>ジョブの状態変更の監視

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| ジョブの状態変更のイベントをすべて取得します。 |
| Microsoft.Media.JobScheduled| ジョブがスケジュール済みの状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobProcessing| ジョブが処理中の状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobCanceling| ジョブが取り消し中の状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobCanceled| ジョブが取り消し済みの状態に遷移したときにイベントを取得します。 これは、ジョブの出力を含む最終状態です。|
| Microsoft.Media.JobErrored | ジョブがエラー状態に遷移したときにイベントを取得します。 これは、ジョブの出力を含む最終状態です。|

次の[スキーマの例](#event-schema-examples)を参照してください。

### <a name="monitoring-job-output-state-changes"></a>ジョブの出力の状態変更の監視

1 つのジョブに複数のジョブ出力が含まれている場合があります (複数のジョブ出力を含めるように変換を構成した場合)。個々のジョブ出力の詳細を追跡する場合は、ジョブ出力変更イベントをリッスンします。

各 **ジョブ** は **JobOutput** よりも上位レベルになるため、ジョブ出力イベントは対応するジョブの内部で発生します。 

`JobFinished`、`JobCanceled`、`JobError` の各エラー メッセージには、(すべてのジョブ出力が完了したときに) ジョブ出力ごとの集計結果が出力されます。 一方、ジョブ出力イベントは、各タスクが完了したときに発生します。 たとえば、エンコードの出力とそれに続く Video Analytics の出力が行われる場合は、2 つのイベントがジョブ出力イベントとして発生してから、最後の JobFinished イベントが集計データと共に発生します。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.JobOutputStateChange| ジョブ出力の状態変更のイベントをすべて取得します。 |
| Microsoft.Media.JobOutputScheduled| ジョブ出力がスケジュール済みの状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobOutputProcessing| ジョブ出力が処理中の状態に遷移したときにイベントを取得します。 |
| Microsoft.Media.JobOutputCanceling| ジョブ出力が取り消し中の状態に遷移したときにイベントを取得します。|
| Microsoft.Media.JobOutputFinished| ジョブ出力が終了済みの状態に遷移したときにイベントを取得します。|
| Microsoft.Media.JobOutputCanceled| ジョブ出力が取り消し済みの状態に遷移したときにイベントを取得します。|
| Microsoft.Media.JobOutputErrored| ジョブ出力がエラー状態に遷移したときにイベントを取得します。|

次の[スキーマの例](#event-schema-examples)を参照してください。

### <a name="monitoring-job-output-progress"></a>ジョブ出力の進行状況の監視

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.JobOutputProgress| このイベントは、ジョブ処理の進行状況を反映します (0 % から 100%)。 進行状況の値が 5% 以上増加した場合、または前回のイベントから 30 秒以上経過した (ハートビート) 場合に、サービスによるイベントの送信が試されます。 進行状況の値は、0% で始まることも 100% に達することも保証されていません。さらに、時間の経過とともに一定の率で増加することも保証されていません。 このイベントは、処理が完了していることを判断するために使用すべきではありません。代わりに、状態変更イベントを使用する必要があります。|

次の[スキーマの例](#event-schema-examples)を参照してください。

## <a name="live-event-types"></a>ライブ イベントの種類

Media Services では、以下の種類の **ライブ** イベントも出力されます。 **Live** イベントには、ストリーム レベル イベントとトラック レベル イベントの 2 つのカテゴリがあります。 

### <a name="stream-level-events"></a>ストリーム レベル イベント

ストリーム レベル イベントは、ストリームまたは接続ごとに発生します。 各イベントには、接続またはストリームを識別する `StreamId` パラメーターがあります。 各ストリームまたは接続には、さまざまな種類のトラックが少なくとも 1 つ存在します。 たとえば、エンコーダーからの接続 1 つにつき、1 つのオーディオ トラックと 4 つのビデオ トラックが存在する場合もあります。 ストリーム イベントには、次の種類があります。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventConnectionRejected | エンコーダーの接続試行が拒否されました。 |
| Microsoft.Media.LiveEventEncoderConnected | エンコーダーがライブ イベントとの接続を確立しました。 |
| Microsoft.Media.LiveEventEncoderDisconnected | エンコーダーが切断されました。 |

次の[スキーマの例](#event-schema-examples)を参照してください。

### <a name="track-level-events"></a>トラック レベル イベント

トラック レベル イベントは、トラックごとに発生します。 

> [!NOTE]
> すべてのトラックレベル イベントは、ライブ エンコーダーが接続された後に発生します。

トラックレベル イベントには、次の種類があります。

| イベントの種類 | 説明 |
| ---------- | ----------- |
| Microsoft.Media.LiveEventIncomingDataChunkDropped | データ チャンクが遅すぎるか、タイムスタンプが重複している (新しいデータ チャンクのタイムスタンプが前のデータ チャンクの終了時間よりも古い) ため、メディア サーバーがデータ チャンクをドロップしました。 |
| Microsoft.Media.LiveEventIncomingStreamReceived | メディア サーバーが、ストリームまたは接続において、各トラックの最初のデータ チャンクを受信しました。 |
| Microsoft.Media.LiveEventIncomingStreamsOutOfSync | オーディオ ストリームとビデオ ストリームの同期がずれていることをメディア サーバーが検出しました。ユーザー エクスペリエンスへの影響はないと考えられるため、警告として使用してください。 |
| Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync | 外部エンコーダーから受信している 2 つのビデオ ストリームの同期がずれていることをメディア サーバーが検出しました。ユーザー エクスペリエンスへの影響はないと考えられるため、警告として使用してください。 |
| Microsoft.Media.LiveEventIngestHeartbeat | ライブ イベントの実行中、各トラックについて 20 秒ごとに発行されます。 取り込みの正常性についての概要が得られます。<br/><br/>エンコーダーが最初に接続された後、エンコーダーがまだ接続されているかどうかにかかわらず、ハートビート イベントは 20 秒ごとに出力され続けます。 |
| Microsoft.Media.LiveEventTrackDiscontinuityDetected | メディア サーバーが受信中のトラックの途切れを検出しました。 |

次の[スキーマの例](#event-schema-examples)を参照してください。

## <a name="event-schema-examples"></a>イベント スキーマの例

### <a name="jobstatechange"></a>JobStateChange

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**JobStateChange** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**JobStateChange** イベントのスキーマを示しています。 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
    "type": "Microsoft.Media.JobStateChange",
    "time": "2018-04-20T21:26:13.8978772",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "specversion": "1.0"
  }
]
```

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `previousState` | string | イベントの前のジョブの状態。 |
| `state` | string | このイベントで通知されるジョブの新しい状態。 例: "Scheduled:The job is ready to start" または "Finished:The job is finished"。|

ジョブの状態。値は次のいずれかです。*Queued*、*Scheduled*、*Processing*、*Finished*、*Error*、*Canceled*、*Canceling*

> [!NOTE]
> *Queued* は **previousState** プロパティでのみ示され、**state** プロパティでは示されません。

### <a name="jobscheduled-jobprocessing-jobcanceling"></a>JobScheduled、JobProcessing、JobCanceling

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

非最終ジョブの状態変更 (JobScheduled、JobProcessing、JobCanceling など) については、 サンプル スキーマは次のようになります。

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobProcessing",
  "eventTime": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished、JobCanceled、JobErrored

最終ジョブの状態変更 (JobFinished、JobCanceled、JobErrored など) については、 サンプル スキーマは次のようになります。

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobFinished",
  "eventTime": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

非最終ジョブの状態変更 (JobScheduled、JobProcessing、JobCanceling など) については、 サンプル スキーマは次のようになります。

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobProcessing",
  "time": "2018-10-12T16:12:18.0839935",
  "id": "a0a6efc8-f647-4fc2-be73-861fa25ba2db",
  "data": {
    "previousState": "Scheduled",
    "state": "Processing",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="jobfinished-jobcanceled-joberrored"></a>JobFinished、JobCanceled、JobErrored

最終ジョブの状態変更 (JobFinished、JobCanceled、JobErrored など) については、 サンプル スキーマは次のようになります。

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobFinished",
  "time": "2018-10-12T16:25:56.4115495",
  "id": "9e07e83a-dd6e-466b-a62f-27521b216f2a",
  "data": {
    "outputs": [
      {
        "@odata.type": "#Microsoft.Media.JobOutputAsset",
        "assetName": "output-7640689F",
        "error": null,
        "label": "VideoAnalyzerPreset_0",
        "progress": 100,
        "state": "Finished"
      }
    ],
    "previousState": "Processing",
    "state": "Finished",
    "correlationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```

---


データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | [説明] |
| -------- | ---- | ----------- |
| `outputs` | Array | ジョブ出力を取得します。|

### <a name="joboutputstatechange"></a>JobOutputStateChange

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**JobOutputStateChange** イベントのスキーマを示しています。

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputStateChange",
  "eventTime": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled、JobOutputProcessing、JobOutputFinished、JobOutputCanceling、JobOutputCanceled、JobOutputErrored

各 JobOutput の状態変更については、サンプル スキーマは次のようになります。

```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "eventType": "Microsoft.Media.JobOutputProcessing",
  "eventTime": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

スキーマの例は次のようになります。

 ```json
[{
  "topic": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "eventType": "Microsoft.Media.JobOutputProgress",
  "eventTime": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

次の例は、**LiveEventConnectionRejected** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventConnectionRejected",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**JobOutputStateChange** イベントのスキーマを示しています。

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobOutputStateChange",
  "time": "2018-10-12T16:25:56.0242854",
  "id": "dde85f46-b459-4775-b5c7-befe8e32cf90",
  "data": {
    "previousState": "Processing",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 100,
      "state": "Finished"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```

### <a name="joboutputscheduled-joboutputprocessing-joboutputfinished-joboutputcanceling-joboutputcanceled-joboutputerrored"></a>JobOutputScheduled、JobOutputProcessing、JobOutputFinished、JobOutputCanceling、JobOutputCanceled、JobOutputErrored

各 JobOutput の状態変更については、サンプル スキーマは次のようになります。

```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/<job-id>",
  "type": "Microsoft.Media.JobOutputProcessing",
  "time": "2018-10-12T16:12:18.0061141",
  "id": "f1fd5338-1b6c-4e31-83c9-cd7c88d2aedb",
  "data": {
    "previousState": "Scheduled",
    "output": {
      "@odata.type": "#Microsoft.Media.JobOutputAsset",
      "assetName": "output-7640689F",
      "error": null,
      "label": "VideoAnalyzerPreset_0",
      "progress": 0,
      "state": "Processing"
    },
    "jobCorrelationData": {
      "testKey1": "testValue1",
      "testKey2": "testValue2"
    }
  },
  "specversion": "1.0"
}]
```
### <a name="joboutputprogress"></a>JobOutputProgress

スキーマの例は次のようになります。

 ```json
[{
  "source": "/subscriptions/<subscription-id>/resourceGroups/belohGroup/providers/Microsoft.Media/mediaservices/<account-name>",
  "subject": "transforms/VideoAnalyzerTransform/jobs/job-5AB6DE32",
  "type": "Microsoft.Media.JobOutputProgress",
  "time": "2018-12-10T18:20:12.1514867",
  "id": "00000000-0000-0000-0000-000000000000",
  "data": {
    "jobCorrelationData": {
      "TestKey1": "TestValue1",
      "testKey2": "testValue2"
    },
    "label": "VideoAnalyzerPreset_0",
    "progress": 86
  },
  "specversion": "1.0"
}]
```

### <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

次の例は、**LiveEventConnectionRejected** イベントのスキーマを示しています。 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "type": "Microsoft.Media.LiveEventConnectionRejected",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "b303db59-d5c1-47eb-927a-3650875fded1",
    "data": { 
      "streamId":"Mystream1",
      "ingestUrl": "http://abc.ingest.isml",
      "encoderIp": "118.238.251.xxx",
      "encoderPort": 52859,
      "resultCode": "MPE_INGEST_CODEC_NOT_SUPPORTED"
    },
    "specversion": "1.0"
  }
]
```

---


データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `streamId` | string | ストリームまたは接続の識別子。 この ID は、エンコーダーまたはカスタマーが取り込み URL に追加します。 |  
| `ingestUrl` | string | ライブ イベントから提供される取り込み URL。 |  
| `encoderIp` | string | エンコーダーの IP。 |
| `encoderPort` | string | このストリームの送信元であるエンコーダーのポート。 |
| `resultCode` | string | 接続が拒否された理由。 結果コードについては、以下の表をご覧ください。 |

エラーの結果コードは、[ライブ イベントのエラー コード](../media-services/latest/live-event-error-codes.md)で確認できます。

### <a name="liveeventencoderconnected"></a>LiveEventEncoderConnected

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**LiveEventEncoderConnected** イベントのスキーマを示しています。 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderConnected",
    "eventTime": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**LiveEventEncoderConnected** イベントのスキーマを示しています。 

```json
[
  { 
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventEncoderConnected",
    "time": "2018-08-07T23:08:09.1710643",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485"
    },
    "specversion": "1.0"
  }
]
```

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `streamId` | string | ストリームまたは接続の識別子。 この ID は、エンコーダーまたはカスタマーが取り込み URL に指定します。 |
| `ingestUrl` | string | ライブ イベントから提供される取り込み URL。 |
| `encoderIp` | string | エンコーダーの IP。 |
| `encoderPort` | string | このストリームの送信元であるエンコーダーのポート。 |

### <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**LiveEventEncoderDisconnected** イベントのスキーマを示しています。 

```json
[
  { 
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventEncoderDisconnected",
    "eventTime": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**LiveEventEncoderDisconnected** イベントのスキーマを示しています。 

```json
[
  { 
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventEncoderDisconnected",
    "time": "2018-08-07T23:08:09.1710872",
    "id": "<id>",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml",
      "streamId": "15864-stream0",
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "resultCode": "S_OK"
    },
    "specversion": "1.0"
  }
]
```

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `streamId` | string | ストリームまたは接続の識別子。 この ID は、エンコーダーまたはカスタマーが取り込み URL に追加します。 |  
| `ingestUrl` | string | ライブ イベントから提供される取り込み URL。 |  
| `encoderIp` | string | エンコーダーの IP。 |
| `encoderPort` | string | このストリームの送信元であるエンコーダーのポート。 |
| `resultCode` | string | エンコーダーの切断の理由。 正常な切断とエラーによる切断とがあります。 結果コードについては、以下の表をご覧ください。 |

エラーの結果コードは、[ライブ イベントのエラー コード](../media-services/latest/live-event-error-codes.md)で確認できます。

正常な切断の結果コードは次のとおりです。

| 結果コード | 説明 |
| ----------- | ----------- |
| S_OK | エンコーダーが正常に切断されました。 |
| MPE_CLIENT_TERMINATED_SESSION | エンコーダーが切断されました (RTMP)。 |
| MPE_CLIENT_DISCONNECTED | エンコーダーが切断されました (FMP4)。 |
| MPI_REST_API_CHANNEL_RESET | チャンネルのリセット コマンドを受信しました。 |
| MPI_REST_API_CHANNEL_STOP | チャンネルの停止コマンドを受信しました。 |
| MPI_REST_API_CHANNEL_STOP | チャンネルはメンテナンス中です。 |
| MPI_STREAM_HIT_EOF | エンコーダーから EOF ストリームが送信されました。 |

### <a name="liveeventincomingdatachunkdropped"></a>LiveEventIncomingDataChunkDropped

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**LiveEventIncomingDataChunkDropped** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**LiveEventIncomingDataChunkDropped** イベントのスキーマを示しています。 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/MyLiveEvent1",
    "type": "Microsoft.Media.LiveEventIncomingDataChunkDropped",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "03da9c10-fde7-48e1-80d8-49936f2c3e7d",
    "data": { 
      "trackType": "Video",
      "trackName": "Video",
      "bitrate": 300000,
      "timestamp": 36656620000,
      "timescale": 10000000,
      "resultCode": "FragmentDrop_OverlapTimestamp"
    },
    "specversion": "1.0"
  }
]
```

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `trackType` | string | 追跡のタイプ (オーディオ/ビデオ)。 |
| `trackName` | string | トラックの名前。 |
| `bitrate` | 整数 (integer) | トラックのビットレート。 |
| `timestamp` | string | ドロップされたデータ チャンクのタイムスタンプ。 |
| `timescale` | string | タイムスタンプのタイムスケール。 |
| `resultCode` | string | データ チャンクがドロップされた理由。 **FragmentDrop_OverlapTimestamp** または **FragmentDrop_NonIncreasingTimestamp**。 |

### <a name="liveeventincomingstreamreceived"></a>LiveEventIncomingStreamReceived

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**LiveEventIncomingStreamReceived** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "eventTime": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**LiveEventIncomingStreamReceived** イベントのスキーマを示しています。 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIncomingStreamReceived",
    "time": "2018-08-07T23:08:10.5069288Z",
    "id": "7f939a08-320c-47e7-8250-43dcfc04ab4d",
    "data": {
      "ingestUrl": "http://mle1-amsts03mediaacctgndos-ts031.channel.media.azure-test.net:80/ingest.isml/Streams(15864-stream0)15864-stream0",
      "trackType": "video",
      "trackName": "video",
      "bitrate": 2962000,
      "encoderIp": "131.107.147.xxx",
      "encoderPort": "27485",
      "timestamp": "15336831655032322",
      "duration": "20000000",
      "timescale": "10000000"
    },
    "specversion": "1.0"
  }
]
```

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `trackType` | string | 追跡のタイプ (オーディオ/ビデオ)。 |
| `trackName` | string | トラックの名前。エンコーダーによって指定されるか、または RTMP の場合は、*TrackType_Bitrate* 形式でサーバーによって生成されます。 |
| `bitrate` | 整数 (integer) | トラックのビットレート。 |
| `ingestUrl` | string | ライブ イベントから提供される取り込み URL。 |
| `encoderIp` | string  | エンコーダーの IP。 |
| `encoderPort` | string | このストリームの送信元であるエンコーダーのポート。 |
| `timestamp` | string | 受信したデータ チャンクの最初のタイムスタンプ。 |
| `timescale` | string | タイムスタンプの表示に使用されるタイムスケール。 |

### <a name="liveeventincomingstreamsoutofsync"></a>LiveEventIncomingStreamsOutOfSync

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**LiveEventIncomingStreamsOutOfSync** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "eventTime": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**LiveEventIncomingStreamsOutOfSync** イベントのスキーマを示しています。 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIncomingStreamsOutOfSync",
    "time": "2018-08-10T02:26:20.6269183Z",
    "id": "b9d38923-9210-4c2b-958f-0054467d4dd7",
    "data": {
      "minLastTimestamp": "319996",
      "typeOfStreamWithMinLastTimestamp": "Audio",
      "maxLastTimestamp": "366000",
      "typeOfStreamWithMaxLastTimestamp": "Video",
      "timescaleOfMinLastTimestamp": "10000000", 
      "timescaleOfMaxLastTimestamp": "10000000"       
    },
    "specversion": "1.0"
  }
]
```

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `minLastTimestamp` | string | 全トラック (オーディオまたはビデオ) における最後のタイムスタンプの最小値。 |
| `typeOfTrackWithMinLastTimestamp` | string | 最後のタイムスタンプが最も小さいトラックの種類 (オーディオまたはビデオ)。 |
| `maxLastTimestamp` | string | 全トラック (オーディオまたはビデオ) におけるすべてのタイムスタンプの最大値。 |
| `typeOfTrackWithMaxLastTimestamp` | string | 最後のタイムスタンプが最も大きいトラックの種類 (オーディオまたはビデオ)。 |
| `timescaleOfMinLastTimestamp`| string | "MinLastTimestamp" の表示に使用されるタイムスケールを取得します。|
| `timescaleOfMaxLastTimestamp`| string | "MaxLastTimestamp" の表示に使用されるタイムスケールを取得します。|

### <a name="liveeventincomingvideostreamsoutofsync"></a>LiveEventIncomingVideoStreamsOutOfSync

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**LiveEventIncomingVideoStreamsOutOfSync** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "eventType": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "eventTime": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**LiveEventIncomingVideoStreamsOutOfSync** イベントのスキーマを示しています。 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaServices/<account-name>",
    "subject": "/LiveEvents/LiveEvent1",
    "type": "Microsoft.Media.LiveEventIncomingVideoStreamsOutOfSync",
    "time": "2018-01-16T01:57:26.005121Z",
    "id": "6dd4d862-d442-40a0-b9f3-fc14bcf6d750",
    "data": {
      "firstTimestamp": "2162058216",
      "firstDuration": "2000",
      "secondTimestamp": "2162057216",
      "secondDuration": "2000",
      "timescale": "10000000"      
    },
    "specversion": "1.0"
  }
]
```

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `firstTimestamp` | string | 種類がビデオであるいずれかのトラック/品質レベルについて受信したタイムスタンプ。 |
| `firstDuration` | string | 1 つ目のタイムスタンプを持つデータ チャンクの期間。 |
| `secondTimestamp` | string  | 種類がビデオである他の何らかのトラック/品質レベルについて受信したタイムスタンプ。 |
| `secondDuration` | string | 2 つ目のタイムスタンプを持つデータ チャンクの期間。 |
| `timescale` | string | タイムスタンプと時間のタイムスケール。|

### <a name="liveeventingestheartbeat"></a>LiveEventIngestHeartbeat

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**LiveEventIngestHeartbeat** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventIngestHeartbeat",
    "eventTime": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)


次の例は、**LiveEventIngestHeartbeat** イベントのスキーマを示しています。 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventIngestHeartbeat",
    "time": "2018-08-07T23:17:57.4610506",
    "id": "7f450938-491f-41e1-b06f-c6cd3965d786",
    "data": {
      "trackType": "audio",
      "trackName": "audio",
      "bitrate": 160000,
      "incomingBitrate": 155903,
      "lastTimestamp": "15336837535253637",
      "timescale": "10000000",
      "overlapCount": 0,
      "discontinuityCount": 0,
      "nonincreasingCount": 0,
      "unexpectedBitrate": false,
      "state": "Running",
      "healthy": true
    },
    "specversion": "1.0"
  }
]
```

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `trackType` | string | 追跡のタイプ (オーディオ/ビデオ)。 |
| `trackName` | string | トラックの名前。エンコーダーによって指定されるか、または RTMP の場合は、*TrackType_Bitrate* 形式でサーバーによって生成されます。 |
| `bitrate` | 整数 (integer) | トラックのビットレート。 |
| `incomingBitrate` | 整数 (integer) | エンコーダーから送信されるデータ チャンクに基づいて計算されたビットレート。 |
| `lastTimestamp` | string | トラックに関して直近 20 秒に受信した最新のタイムスタンプ。 |
| `timescale` | string | タイムスタンプの表示に使用されるタイムスケール。 |
| `overlapCount` | 整数 (integer) | 直近 20 秒にタイムスタンプが重複したデータ チャンクの数。 |
| `discontinuityCount` | 整数 (integer) | 直近 20 秒に観察された途切れの数。 |
| `nonIncreasingCount` | 整数 (integer) | 直近 20 秒に過去のタイムスタンプを受信したデータ チャンクの数。 |
| `unexpectedBitrate` | [bool] | 直近 20 秒における予想ビットレートと実ビットレートの差が、許容されている上限を超えているかどうか。 incomingBitrate がビットレートの 2 倍以上または incomingBitrate がビットレートの 1/2 以下または incomingBitrate が 0 のとき、かつそのときに限り true。 |
| `state` | string | ライブ イベントの状態。 |
| `healthy` | [bool] | カウントとフラグに基づき、取り込みが正常であるかどうかを示します。 overlapCount = 0 && discontinuityCount = 0 && nonIncreasingCount = 0 && unexpectedBitrate = false の場合、Healthy は true になります。 |

### <a name="liveeventtrackdiscontinuitydetected"></a>LiveEventTrackDiscontinuityDetected

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

次の例は、**LiveEventTrackDiscontinuityDetected** イベントのスキーマを示しています。 

```json
[
  {
    "topic": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "eventType": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "eventTime": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

次の例は、**LiveEventTrackDiscontinuityDetected** イベントのスキーマを示しています。 

```json
[
  {
    "source": "/subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Media/mediaservices/<account-name>",
    "subject": "liveEvent/mle1",
    "type": "Microsoft.Media.LiveEventTrackDiscontinuityDetected",
    "time": "2018-08-07T23:18:06.1270405Z",
    "id": "5f4c510d-5be7-4bef-baf0-64b828be9c9b",
    "data": {
      "trackName": "video",
      "previousTimestamp": "15336837615032322",
      "trackType": "video",
      "bitrate": 2962000,
      "newTimestamp": "15336837619774273",
      "discontinuityGap": "575284",
      "timescale": "10000000"
    },
    "specversion": "1.0"
  }
]
```

---

データ オブジェクトには、次のプロパティがあります。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `trackType` | string | 追跡のタイプ (オーディオ/ビデオ)。 |
| `trackName` | string | トラックの名前。エンコーダーによって指定されるか、または RTMP の場合は、*TrackType_Bitrate* 形式でサーバーによって生成されます。 |
| `bitrate` | 整数 (integer) | トラックのビットレート。 |
| `previousTimestamp` | string | 前のフラグメントのタイムスタンプ。 |
| `newTimestamp` | string | 現在のフラグメントのタイムスタンプ。 |
| `discontinuityGap` | string | 2 つのタイムスタンプの差。 |
| `timescale` | string | タイムスタンプと不連続性の両方について、差を表すときに使用されるタイムスケール。 |

### <a name="common-event-properties"></a>共通のイベント プロパティ

# <a name="event-grid-event-schema"></a>[Event Grid イベント スキーマ](#tab/event-grid-event-schema)

イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `topic` | string | Event Grid トピック。 このプロパティは、Media Services アカウントのリソース ID を保持します。 |
| `subject` | string | Media Services アカウント下の Media Services チャンネルのリソース パス。 トピックとサブジェクトを連結することで、ジョブのリソース ID が得られます。 |
| `eventType` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 例: "Microsoft.Media.JobStateChange" |
| `eventTime` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | Media Services イベント データ。 |
| `dataVersion` | string | データ オブジェクトのスキーマ バージョン。 スキーマ バージョンは発行元によって定義されます。 |
| `metadataVersion` | string | イベント メタデータのスキーマ バージョン。 最上位プロパティのスキーマは Event Grid によって定義されます。 この値は Event Grid によって指定されます。 |

# <a name="cloud-event-schema"></a>[クラウド イベント スキーマ](#tab/cloud-event-schema)

イベントのトップレベルのデータを次に示します。

| プロパティ | Type | 説明 |
| -------- | ---- | ----------- |
| `source` | string | Event Grid トピック。 このプロパティは、Media Services アカウントのリソース ID を保持します。 |
| `subject` | string | Media Services アカウント下の Media Services チャンネルのリソース パス。 トピックとサブジェクトを連結することで、ジョブのリソース ID が得られます。 |
| `type` | string | このイベント ソース用に登録されたイベントの種類のいずれか。 例: "Microsoft.Media.JobStateChange" |
| `time` | string | プロバイダーの UTC 時刻に基づくイベントの生成時刻。 |
| `id` | string | イベントの一意識別子。 |
| `data` | object | Media Services イベント データ。 |
| `specversion` | string | CloudEvents スキーマ仕様バージョン。 |


---

## <a name="next-steps"></a>次のステップ

[ジョブ状態変更イベントを登録する](../media-services/latest/monitoring/job-state-events-cli-how-to.md)

## <a name="see-also"></a>関連項目

- [メディア サービス イベントを含む EventGrid .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.EventGrid/)
- [Media Services イベントの定義](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/eventgrid/data-plane/Microsoft.Media/stable/2018-01-01/MediaServices.json)
- [ライブ イベントのエラー コード](../media-services/latest/live-event-error-codes.md)
