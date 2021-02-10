---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: dbb32aa0cb61024c3d59879775fe73801d2b9668
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2021
ms.locfileid: "99214710"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x 以降

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTimeUtc": ""
            }
        }
    }
}  
```

|プロパティ  |Default | 説明 |
|---------|---------|---------|
|batchCheckpointFrequency|1|EventHub カーソル チェックポイントを作成する前に処理するイベント バッチ数。|
|eventProcessorOptions/maxBatchSize|10|受信ループあたりで受信される最大イベント数。|
|eventProcessorOptions/prefetchCount|300|基となる `EventProcessorHost` によって使用される既定のプリフェッチ カウント。 許容される最小値は 10 です。|
|initialOffsetOptions/type|fromStart|チェックポイントがストレージに存在しない場合に処理を開始するイベント ストリーム内の位置。 `fromStart`、`fromEnd`、`fromEnqueuedTime` のいずれかを選択できます。 `fromEnd` は、関数アプリが実行を開始した後にエンキューされた新しいイベントを処理します。 すべてのパーティションに適用されます。  詳細については、[EventProcessorOptions のドキュメント](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet)を参照してください。|
|initialOffsetOptions/enqueuedTimeUtc|該当なし| ストリームにエンキューされたイベントの処理を開始する時刻を指定します。 `initialOffsetOptions/type` が `fromEnqueuedTime` として構成されている場合、この設定は必須です。 [DateTime.Parse()](/dotnet/standard/base-types/parsing-datetime) でサポートされている形式の時刻がサポートされます (`2020-10-26T20:31Z` など)。 明確にするためには、タイムゾーンも指定する必要があります。 タイムゾーンが指定されなかった場合は、関数アプリを実行しているマシンのローカル タイムゾーン (Azure で実行されている場合は UTC) が使用されます。 詳細については、[EventProcessorOptions のドキュメント](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider?view=azure-dotnet)を参照してください。|
> [!NOTE]
> Azure Functions 2.x 以降の host.json のリファレンスについては、[Azure Functions の host.json のリファレンス](../articles/azure-functions/functions-host-json.md)に関する記事を参照してください。

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|プロパティ  |Default | 説明 |
|---------|---------|---------| 
|maxBatchSize|64|受信ループあたりで受信される最大イベント数。|
|prefetchCount|該当なし|基となる `EventProcessorHost` によって使用される既定のプリフェッチ。| 
|batchCheckpointFrequency|1|EventHub カーソル チェックポイントを作成する前に処理するイベント バッチ数。| 

> [!NOTE]
> Functions 1.x の host.json のリファレンスについては、「[Azure Functions 1.x の host.json のリファレンス](../articles/azure-functions/functions-host-json-v1.md)」を参照してください。
