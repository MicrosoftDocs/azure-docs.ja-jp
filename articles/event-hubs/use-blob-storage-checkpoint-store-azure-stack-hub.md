---
title: Azure Stack Hub でチェックポイント ストアとして Blob Storage を使用する
description: この記事では Azure Stack Hub 上の Event Hubs でチェックポイント ストアとして Blob Storage を使用する方法について説明します。
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 07d7cf844480a9a88468c17cecc7ca38cca5d176
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97007825"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub"></a>チェックポイント ストアとして Blob Storage を使用する - Azure Stack Hub 上の Event Hubs
Azure で一般公開されているものとは異なるバージョンの Storage Blob SDK をサポートする環境で、チェックポイント ストアとして Azure Blob Storage を使用している場合は、コードを使用して、Storage Service API バージョンをその環境でサポートされている特定のバージョンに変更する必要があります。 たとえば、[Azure Stack Hub バージョン 2002 上で Event Hubs](/azure-stack/user/event-hubs-overview) を実行している場合、Storage Service で利用可能な最も高いバージョンは 2017-11-09 です。 この場合は、コードを使用して、対象にする Storage Service API のバージョンを 2017-11-09 にする必要があります。 特定の Storage API バージョンを対象にする方法の例については、GitHub の次のサンプルを参照してください。 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) または [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python - [同期](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py)、[非同期](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

Azure Stack Hub でサポートされているバージョンを対象にせずに、Blob Storage をチェックポイント ストアとして使用する Event Hubs レシーバーを実行すると、次のエラー メッセージが表示されます。

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Python におけるサンプルのエラー メッセージ
Python の場合、`azure.core.exceptions.HttpResponseError` のエラーは `EventHubConsumerClient.receive()` の `on_error(partition_context, error)` エラー ハンドラーに渡されます。 ただし、メソッド `receive()` は例外を発生させません。 `print(error)` は次の例外情報を出力します。

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

ロガーは、次のような 2 つの警告をログに記録します。

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>次のステップ

パーティション分割およびチェックポイント処理については、次の記事を参照してください。[アプリケーションの複数のインスタンス間でパーティション負荷のバランスを取る](event-processor-balance-partition-load.md)
