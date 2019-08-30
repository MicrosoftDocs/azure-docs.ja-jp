---
title: Azure IoT Hub メッセージ ルーティングについて | Microsoft Docs
description: 開発者ガイド - メッセージ ルーティングを使用して、device-to-cloud メッセージを送信する方法。 テレメトリ データとそれ以外のデータの両方の送信についても説明します。
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: asrastog
ms.openlocfilehash: 6ee9e334c10bd2d0f291b5fd1bb547ba3ba83ddb
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69877186"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

メッセージ ルーティングを使用すると、自動化された、スケーラブルで信頼性の高い方法で、デバイスからクラウド サービスにメッセージを送信することができます。 メッセージ ルーティングは、次の目的で使用できます。 

* **デバイスのテレメトリ メッセージだけでなくイベント** (デバイス ライフサイクル イベントとデバイス ツイン変更イベント) を組み込みのエンドポイントとカスタム エンドポイントに送信する。 [ルーティング エンドポイント](#routing-endpoints)について確認してください。

* リッチ クエリを適用して、**さまざまなエンドポイントにルーティングする前にデータをフィルター処理**する。 メッセージ ルーティングでは、メッセージ プロパティとメッセージ本文に基づいてクエリを実行できるほか、デバイス ツインのタグとプロパティに基づいてクエリを実行することもできます。 [メッセージ ルーティングでクエリ](iot-hub-devguide-routing-query-syntax.md)を使用する方法の詳細について確認してください。

IoT Hub でメッセージのルーティングを機能させるには、これらのサービス エンドポイントへの書き込みアクセス許可が必要です。 Azure Portal を使用してエンドポイントを構成する場合、必要なアクセス許可は自動的に追加されます。 必要なスループットをサポートするようにサービスを確実に構成してください。 IoT ソリューションを初めて構成する場合は、追加したエンドポイントを監視し、実際の負荷の調整を行う必要があります。

IoT ハブは、各種プロトコルにおける相互運用性を確保するためにすべての device-to-cloud メッセージ用の[共通形式](iot-hub-devguide-messages-construct.md)を定義します。 メッセージが、同じエンドポイントを指している複数のルートと一致する場合、IoT Hub はそのエンドポイントにメッセージを 1 回だけ送信します。 そのため、Service Bus キューまたはトピックで重複除去を構成する必要はありません。 パーティション分割されたキューでは、パーティションのアフィニティによってメッセージの順序が保証されます。 このチュートリアルを使用して、[メッセージ ルーティングを構成する](tutorial-routing.md)方法について学習してください。

## <a name="routing-endpoints"></a>ルーティング エンドポイント

IoT ハブには、Event Hubs との互換性がある、既定の組み込みのエンドポイント (**messages/events**) があります。 サブスクリプション内の他のサービスを IoT ハブにリンクして、メッセージのルーティング先となる[カスタム エンドポイント](iot-hub-devguide-endpoints.md#custom-endpoints)を作成することができます。 現在、IoT Hub では、カスタム エンドポイントとして、次のサービスをサポートしています。

### <a name="built-in-endpoint"></a>組み込みのエンドポイント

標準的な [Event Hubs 統合と SDK](iot-hub-devguide-messages-read-builtin.md) を使用して、組み込みのエンドポイント (**messages/events**) から device-to-cloud メッセージを受信することができます。 ルートの作成後、組み込みのエンドポイントへのルートが作成されていないと、そのエンドポイントへのデータ フローは停止します。

### <a name="azure-blob-storage"></a>Azure Blob Storage

IoT Hub では、[Apache Avro](https://avro.apache.org/) 形式と JSON 形式での Azure Blob Storage へのデータの書き込みをサポートしています。 JSON 形式にエンコードする機能は一般に、IoT Hub が提供されているすべてのリージョンで使用できます。 既定値は AVRO です。 エンコード形式は、Blob Storage エンドポイントが構成されている場合にのみ設定できます。 既存のエンドポイントに対して形式を編集することはできません。 JSON エンコードを使用する場合は、メッセージの[システム プロパティ](iot-hub-devguide-routing-query-syntax.md#system-properties)で contentType を **application/json** に設定し、contentEncoding を **UTF-8** に設定する必要があります。 これらのどちらの値でも大文字と小文字は区別されません。 コンテンツのエンコードが設定されていない場合、IoT Hub では Base 64 エンコード形式でメッセージが書き込まれます。 エンコード形式は、IoT Hub の作成または更新 REST API (具体的には [RoutingStorageContainerProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties))、Azure portal、[Azure CLI](https://docs.microsoft.com/cli/azure/iot/hub/routing-endpoint?view=azure-cli-latest)、または [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.iothub/add-aziothubroutingendpoint?view=azps-1.3.0) を使用して選択できます。 次の図は、Azure portal でエンコード形式を選択する方法を示しています。

![Blob Storage エンドポイントのエンコード](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub は、ADLS Gen2 アカウントへのメッセージのルーティングもサポートしています。これは、BLOB ストレージ上に構築された[階層的な名前空間](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-namespace)対応のストレージ アカウントです。 この機能はパブリック プレビュー段階であり、米国西部 2 と米国中西部で新しい ADLS Gen2 アカウントに使用できます。 この機能は、近日中にすべてのクラウド リージョンにデプロイする予定です。

IoT Hub は、バッチが特定のサイズに達するか、一定の時間が経過した時点で、メッセージを一括処理して BLOB にデータを書き込みます。 IoT Hub の既定のファイル名前付け規則は次のとおりです。

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

任意のファイル名前付け規則を使用可能ですが、一覧で示されているすべてのトークンを使う必要があります。 書き込むデータがない場合、IoT ハブは空の BLOB に書き込みます。

Blob Storage にルーティングするときは、パーティションを想定せずにすべてのコンテナーを確実に読み取るために、BLOB を確保したうえでそれらを反復処理することをお勧めします。 [Microsoft が開始するフェールオーバー](iot-hub-ha-dr.md#microsoft-initiated-failover)中や IoT Hub の[手動フェールオーバー](iot-hub-ha-dr.md#manual-failover)中にパーティションの範囲が変わる可能性があります。 [List Blobs API](https://docs.microsoft.com/rest/api/storageservices/list-blobs) を使用して、BLOB のリストを列挙できます。 ガイダンスとして次のサンプルを参照してください。

   ```csharp
        public void ListBlobsInContainer(string containerName, string iothub)
        {
            var storageAccount = CloudStorageAccount.Parse(this.blobConnectionString);
            var cloudBlobContainer = storageAccount.CreateCloudBlobClient().GetContainerReference(containerName);
            if (cloudBlobContainer.Exists())
            {
                var results = cloudBlobContainer.ListBlobs(prefix: $"{iothub}/");
                foreach (IListBlobItem item in results)
                {
                    Console.WriteLine(item.Uri);
                }
            }
        }
   ```

### <a name="service-bus-queues-and-service-bus-topics"></a>Service Bus キューと Service Bus トピック

IoT Hub エンドポイントとして使用される Service Bus のキューおよびトピックでは、**セッション**も**重複データ検出**も有効にしないでください。 これらのオプションのいずれかが有効になっている場合、エンドポイントは Azure Portal に**到達不能**として表示されます。

### <a name="event-hubs"></a>Event Hubs

組み込みの Event Hubs 互換エンドポイントとは別に、Event Hubs タイプのカスタム エンドポイントにデータをルーティングすることもできます。 

## <a name="reading-data-that-has-been-routed"></a>ルーティングされたデータの読み取り

この[チュートリアル](tutorial-routing.md)に従って、ルートを構成することができます。

エンドポイントからメッセージを読み取る方法については、以下のチュートリアルを参照してください。

* [組み込みのエンドポイント](quickstart-send-telemetry-node.md)からの読み取り

* [BLOB ストレージ](../storage/blobs/storage-blob-event-quickstart.md)からの読み取り

* [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) からの読み取り

* [Service Bus キュー](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)からの読み取り

* [Service Bus トピック](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)からの読み取り

## <a name="fallback-route"></a>フォールバック ルート

フォールバック ルートを通じて、既存のいずれのルートのクエリ条件も満たさないすべてのメッセージが、[Event Hubs](/azure/event-hubs/) との互換性がある組み込みのイベント ハブ (**messages/events**) に送信されます。 メッセージ ルーティングが有効になっている場合は、フォールバック ルート機能を有効にすることができます。 ルートの作成後、組み込みのエンドポイントへのルートが作成されていないと、そのエンドポイントへのデータ フローは停止します。 組み込みのエンドポイントへのルートがなく、フォールバック ルートが有効になっている場合は、ルートのどのクエリ条件とも一致しないメッセージのみが組み込みのエンドポイントに送信されます。 また、既存のすべてのルートを削除する場合は、組み込みのエンドポイントですべてのデータを受信するために、フォールバック ルートを有効にする必要があります。

Azure portal の [メッセージ ルーティング] ブレードで、フォールバック ルートを有効または無効にすることができます。 また、Azure Resource Manager で [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) を使用して、フォールバック ルート用にカスタム エンドポイントが使用されるようにすることもできます。

## <a name="non-telemetry-events"></a>非テレメトリ イベント

メッセージ ルーティングでは、デバイス テレメトリのほかに、デバイス ツイン変更イベント、デバイス ライフサイクル イベント、およびデバイス ツイン変更イベントの送信を有効にすることもできます (パブリック プレビューの場合)。 たとえば、データ ソースを**デバイス ツイン変更イベント**に設定してルートを作成した場合、IoT Hub は、デバイス ツインの変更が含まれているメッセージをエンドポイントに送信します。 同様に、データ ソースを**デバイス ライフサイクル イベント**に設定してルートを作成した場合、IoT Hub は、デバイスが削除または作成されたかどうかを示すメッセージを送信します。 最後に、[IoT プラグ アンド プレイ パブリック プレビュー](../iot-pnp/overview-iot-plug-and-play.md)の一部として、開発者は、データ ソースを**デジタル ツイン変更イベント**に設定したルートを作成できます。IoT Hub は、デジタル ツインの[プロパティ](../iot-pnp/iot-plug-and-play-glossary.md)が設定または変更されたとき、[デジタル ツイン](../iot-pnp/iot-plug-and-play-glossary.md)が置き換えられたとき、または基になるデバイス ツインで変更イベントが発生したときに、メッセージを送信します。

[IoT Hub は Azure Event Grid とも統合](iot-hub-event-grid.md)されているため、デバイス イベントを発行して、それらのイベントに基づくワークフローのリアルタイムの統合と自動化をサポートできます。 ご自分のシナリオにどれが最適かについては、[メッセージ ルーティングと Event Grid の主な違い](iot-hub-event-grid-routing-comparison.md)に関するページを参照してください。

## <a name="testing-routes"></a>ルートのテスト

新しいルートを作成したり、既存のルートを編集したりする場合は、サンプル メッセージを使用してルート クエリをテストする必要があります。 個々のルートをテストすることも、すべてのルートを一度にテストすることも可能で、テスト中にメッセージがエンドポイントにルーティングされることはありません。 テストには、Azure portal、Azure Resource Manager、Azure PowerShell、および Azure CLI を使用することができます。 結果は、サンプル メッセージがクエリに一致したか、メッセージがクエリに一致しなかったか、サンプル メッセージまたはクエリ構文が正しくないためにテストを実行できなかったかを識別するのに役立ちます。 詳細については、[ルートのテスト](/rest/api/iothub/iothubresource/testroute)に関するページと[すべてのルートのテスト](/rest/api/iothub/iothubresource/testallroutes)に関するページを参照してください。

## <a name="latency"></a>Latency

組み込みのエンドポイントを使用して device-to-cloud テレメトリ メッセージをルーティングすると、最初のルーティングの作成後にエンド ツー エンドの待機時間が若干上昇します。

ほとんどの場合、待機時間の増加は平均で 500 ミリ秒未満です。 待ち時間は、**Routing: message latency for messages/events (ルーティング: メッセージ/イベントのメッセージ待機時間)** または **d2c.endpoints.latency.builtIn.events** という IoT Hub メトリックを使用して監視することができます。 最初のルーティング後にルーティングを作成または削除してもエンド ツー エンドの待機時間には影響しません。

## <a name="monitoring-and-troubleshooting"></a>監視とトラブルシューティング

IoT Hub には、ハブの正常性と送信されたメッセージの概要を示す、ルーティングおよびエンドポイント関連のメトリックがいくつか用意されています。 複数のメトリックから得た情報を組み合わせることで、問題の根本原因を特定することができます。 たとえば、メトリック **Routing: telemetry messages dropped (ルーティング: 破棄されたテレメトリ メッセージ)** または **d2c.telemetry.egress.dropped** を使用すると、どのルートのクエリにも一致せず、フォールバック ルートが無効になっていたときに破棄されたメッセージの数を特定できます。 [IoT Hub メトリック](iot-hub-metrics.md)に関するページには、IoT ハブで既定で有効になっているすべてのメトリックが一覧で示されています。

REST API の [GetEndpointHealth](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) を使用して、エンドポイントの[正常性状態](iot-hub-devguide-endpoints.md#custom-endpoints)を取得できます。 ルーティングによるメッセージの待機時間に関する [IoT Hub メトリック](iot-hub-metrics.md)を使用して、エンドポイントの正常性が停止または異常の場合にエラーを特定し、デバッグすることをお勧めします。 たとえば、エンドポイントの種類が Event Hubs の場合は、**d2c.endpoints.latency.eventHubs** を監視できます。 IoT Hub が最終的に一貫して正常状態を確立すると、異常なエンドポイントの状態が "正常" に更新されます。

Azure Monitor の[診断設定](../iot-hub/iot-hub-monitor-resource-health.md)の**ルート**診断ログを使用すると、IoT Hub に認識されたエンドポイントの正常性とルーティング クエリの評価中に発生したエラーを追跡することができます (エンドポイントが停止した場合など)。 これらの診断ログは、カスタム処理を実行するために、Azure Monitor ログ、Event Hubs、または Azure Storage に送信できます。

## <a name="next-steps"></a>次の手順

* メッセージ ルートの作成方法については、[ルートを使用した IoT Hub の device-to-cloud メッセージの処理](tutorial-routing.md)に関するページをご覧ください。

* [device-to-cloud メッセージを送信する方法](quickstart-send-telemetry-node.md)

* device-to-cloud メッセージの送信に使用できる SDK については、[Azure IoT SDK](iot-hub-devguide-sdks.md) に関するページを参照してください。
