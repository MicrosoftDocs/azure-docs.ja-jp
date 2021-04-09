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
ms.custom:
- 'Role: Cloud Development'
- devx-track-csharp
ms.openlocfilehash: d2fe8f32ec854e1e6db644a039e6a249cfbddcaa
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012889"
---
# <a name="use-iot-hub-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

メッセージ ルーティングを使用すると、自動化された、スケーラブルで信頼性の高い方法で、デバイスからクラウド サービスにメッセージを送信することができます。 メッセージ ルーティングは、次の目的で使用できます。 

* **デバイスのテレメトリ メッセージだけでなくイベント** (デバイス ライフサイクル イベント、デバイス ツイン変更イベント、デジタル ツイン変更イベント) を組み込みのエンドポイントとカスタム エンドポイントに送信する。 [ルーティング エンドポイント](#routing-endpoints)について確認してください。 IoT プラグ アンド プレイ デバイスから送信されるイベントの詳細については、「[IoT プラグ アンド プレイのデジタル ツインを理解する](../iot-pnp/concepts-digital-twin.md)」を参照してください。

* リッチ クエリを適用して、**さまざまなエンドポイントにルーティングする前にデータをフィルター処理** する。 メッセージ ルーティングでは、メッセージ プロパティとメッセージ本文に基づいてクエリを実行できるほか、デバイス ツインのタグとプロパティに基づいてクエリを実行することもできます。 [メッセージ ルーティングでクエリ](iot-hub-devguide-routing-query-syntax.md)を使用する方法の詳細について確認してください。

IoT Hub でメッセージのルーティングを機能させるには、これらのサービス エンドポイントへの書き込みアクセス許可が必要です。 Azure Portal を使用してエンドポイントを構成する場合、必要なアクセス許可は自動的に追加されます。 必要なスループットをサポートするようにサービスを確実に構成してください。 たとえば、Event Hubs をカスタム エンドポイントとして使用している場合は、そのイベント ハブに対して **スループット ユニット** を構成して、IoT Hub メッセージ ルーティングを介して送信する予定のイベントのイングレスを処理できるようにする必要があります。 同様に、Service Bus キューをエンドポイントとして使用する場合は、コンシューマーによって送信されるまで、キューがすべてのデータ イングレスを保持できるように、**最大サイズ** を構成する必要があります。 IoT ソリューションを初めて構成する場合は、追加したエンドポイントを監視し、実際の負荷の調整を行う必要があります。

IoT ハブでは、各種プロトコルにおける相互運用性を確保するためにすべての device-to-cloud メッセージ用の[共通形式](iot-hub-devguide-messages-construct.md)が定義されています。 メッセージが、同じエンドポイントを指している複数のルートと一致する場合、IoT Hub はそのエンドポイントにメッセージを 1 回だけ送信します。 そのため、Service Bus キューまたはトピックで重複除去を構成する必要はありません。 パーティション分割されたキューでは、パーティションのアフィニティによってメッセージの順序が保証されます。 このチュートリアルを使用して、[メッセージ ルーティングを構成する](tutorial-routing.md)方法について学習してください。

## <a name="routing-endpoints"></a>ルーティング エンドポイント

IoT ハブには、Event Hubs との互換性がある、既定の組み込みのエンドポイント (**messages/events**) があります。 サブスクリプション内の他のサービスを IoT ハブにリンクして、メッセージのルーティング先となる[カスタム エンドポイント](iot-hub-devguide-endpoints.md#custom-endpoints)を作成することができます。 

各メッセージは、一致するルーティング クエリを持つすべてのエンドポイントにルーティングされます。 つまり、メッセージは複数のエンドポイントにルーティングできます。

カスタム エンドポイントにファイアウォール構成がある場合、特定のエンドポイント ([Azure Storage](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing)、[Azure Event Hubs](./virtual-network-support.md#egress-connectivity-to-event-hubs-endpoints-for-routing)、[Azure Service Bus](./virtual-network-support.md#egress-connectivity-to-service-bus-endpoints-for-routing)) に IoT Hub アクセスを与えるため、Microsoft が信頼を置くファースト パーティの例外の使用を検討してください。 [マネージド サービス ID](./virtual-network-support.md) が与えられている IoT Hub の場合、これは一部のリージョンで利用できます。

IoT Hub では現在、次のエンドポイントがサポートされています。

 - 組み込みのエンドポイント
 - Azure Storage
 - Service Bus キューと Service Bus トピック
 - Event Hubs

## <a name="built-in-endpoint-as-a-routing-endpoint"></a>ルーティング エンドポイントとしての組み込みのエンドポイント

標準的な [Event Hubs 統合と SDK](iot-hub-devguide-messages-read-builtin.md) を使用して、組み込みのエンドポイント (**messages/events**) から device-to-cloud メッセージを受信することができます。 ルートの作成後、組み込みのエンドポイントへのルートが作成されていないと、そのエンドポイントへのデータ フローは停止します。

## <a name="azure-storage-as-a-routing-endpoint"></a>ルーティング エンドポイントとしての Azure Storage

IoT Hub がメッセージをルーティングできるストレージ サービスとして、[Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) アカウントと [Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md) (ADLS Gen2) アカウントの 2 つがあります。 Azure Data Lake Storage アカウントは、BLOB ストレージ上に構築された、[階層型名前空間](../storage/blobs/data-lake-storage-namespace.md)に対応したストレージ アカウントです。 これらはどちらも、そのストレージとして BLOB を使用します。

IoT Hub は JSON 形式だけでなく、[Apache Avro](https://avro.apache.org/) 形式での Azure Storage へのデータの書き込みをサポートしています。 既定値は AVRO です。 JSON エンコードを使用する場合は、メッセージの [システム プロパティ](iot-hub-devguide-routing-query-syntax.md#system-properties)で contentType を **application/json** に設定し、contentEncoding を **UTF-8** に設定する必要があります。 これらのどちらの値でも大文字と小文字は区別されません。 コンテンツのエンコードが設定されていない場合、IoT Hub では Base 64 エンコード形式でメッセージが書き込まれます。

このエンコード形式は、Blob Storage エンドポイントが構成されている場合にのみ設定できます。既存のエンドポイントに応じて編集することはできません。 既存のエンドポイントのエンコード形式を切り替えるには、カスタム エンドポイントを削除してから必要な形式で再作成する必要があります。 便利な方法の 1 つとして、目的のエンコード形式で新しいカスタム エンドポイントを作成し、そのエンドポイントに並列ルートを追加する方法があります。 これにより、既存のエンドポイントを削除する前にデータを確認できます。

エンコード形式は、IoT Hub の作成または更新 REST API (具体的には [RoutingStorageContainerProperties](/rest/api/iothub/iothubresource/createorupdate#routingstoragecontainerproperties))、Azure portal、[Azure CLI](/cli/azure/iot/hub/routing-endpoint)、または [Azure PowerShell](/powershell/module/az.iothub/add-aziothubroutingendpoint) を使用して選択できます。 次の画像は、Azure portal でエンコード形式を選択する方法を示しています。

![Blob Storage エンドポイントのエンコード](./media/iot-hub-devguide-messages-d2c/blobencoding.png)

IoT Hub は、バッチが特定のサイズに達するか、または一定の時間が経過すると常に、メッセージを一括処理してストレージにデータを書き込みます。 IoT Hub の既定のファイル名前付け規則は次のとおりです。

```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

任意のファイル名前付け規則を使用可能ですが、一覧で示されているすべてのトークンを使う必要があります。 書き込むデータがない場合、IoT ハブは空の BLOB に書き込みます。

パーティションを前提にすることなくすべての BLOB またはファイルが確実に読み取られるようにするために、BLOB またはファイルは一覧表示してから反復処理することをお勧めします。 [Microsoft が開始するフェールオーバー](iot-hub-ha-dr.md#microsoft-initiated-failover)中や IoT Hub の[手動フェールオーバー](iot-hub-ha-dr.md#manual-failover)中にパーティションの範囲が変わる可能性があります。 [List Blobs API](/rest/api/storageservices/list-blobs) を使用して BLOB の一覧を、または [List ADLS Gen2 API](/rest/api/storageservices/datalakestoragegen2/path/list) を使用してファイルの一覧を列挙できます。 ガイダンスとして次のサンプルを参照してください。

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

Azure Data Lake Gen2 と互換性のあるストレージ アカウントを作成するには、次の図に示すように、新しい V2 ストレージ アカウントを作成し、 **[詳細]** タブで *[階層型名前空間]* フィールドの *[有効]* を選択します。

![Azure Data Lake Storage Gen2 を選択する](./media/iot-hub-devguide-messages-d2c/selectadls2storage.png)

## <a name="service-bus-queues-and-service-bus-topics-as-a-routing-endpoint"></a>ルーティング エンドポイントとしての Service Bus キューと Service Bus トピック

IoT Hub エンドポイントとして使用される Service Bus のキューおよびトピックでは、**セッション** も **重複データ検出** も有効にしないでください。 これらのオプションのいずれかが有効になっている場合、エンドポイントは Azure Portal に **到達不能** として表示されます。

## <a name="event-hubs-as-a-routing-endpoint"></a>ルーティング エンドポイントとしての Event Hubs

組み込みの Event Hubs 互換エンドポイントとは別に、Event Hubs タイプのカスタム エンドポイントにデータをルーティングすることもできます。 

## <a name="reading-data-that-has-been-routed"></a>ルーティングされたデータの読み取り

この[チュートリアル](tutorial-routing.md)に従って、ルートを構成することができます。

エンドポイントからメッセージを読み取る方法については、以下のチュートリアルを参照してください。

* [組み込みのエンドポイント](quickstart-send-telemetry-node.md)からの読み取り

* [BLOB ストレージ](../storage/blobs/storage-blob-event-quickstart.md)からの読み取り

* [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) からの読み取り

* [Service Bus キュー](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)からの読み取り

* [Service Bus トピック](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)からの読み取り


## <a name="fallback-route"></a>フォールバック ルート

フォールバック ルートを通じて、既存のいずれのルートのクエリ条件も満たさないすべてのメッセージが、[Event Hubs](../event-hubs/index.yml) との互換性がある組み込みのイベント ハブ (**messages/events**) に送信されます。 メッセージ ルーティングが有効になっている場合は、フォールバック ルート機能を有効にすることができます。 ルートの作成後、組み込みのエンドポイントへのルートが作成されていないと、そのエンドポイントへのデータ フローは停止します。 組み込みのエンドポイントへのルートがなく、フォールバック ルートが有効になっている場合は、ルートのどのクエリ条件とも一致しないメッセージのみが組み込みのエンドポイントに送信されます。 また、既存のすべてのルートを削除する場合は、組み込みのエンドポイントですべてのデータを受信するために、フォールバック ルートを有効にする必要があります。

Azure portal の [メッセージ ルーティング] ブレードで、フォールバック ルートを有効または無効にすることができます。 また、Azure Resource Manager で [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) を使用して、フォールバック ルート用にカスタム エンドポイントが使用されるようにすることもできます。

## <a name="non-telemetry-events"></a>非テレメトリ イベント

メッセージ ルーティングでは、デバイス テレメトリのほかに、デバイス ツイン変更イベント、デバイス ライフサイクル イベント、およびデバイス ツイン変更イベントの送信を有効にすることもできます。 たとえば、データ ソースを **デバイス ツイン変更イベント** に設定してルートを作成した場合、IoT Hub は、デバイス ツインの変更が含まれているメッセージをエンドポイントに送信します。 同様に、データ ソースを **デバイス ライフサイクル イベント** に設定してルートを作成した場合、IoT Hub は、デバイスが削除または作成されたかどうかを示すメッセージを送信します。 最後に、[Azure IoT プラグ アンド プレイ](../iot-pnp/overview-iot-plug-and-play.md)の一部として、開発者は、データ ソースを **デジタル ツイン変更イベント** に設定したルートを作成できます。IoT Hub では、デジタル ツインのプロパティが設定または変更されたとき、デジタル ツインが置き換えられたとき、または基になるデバイス ツインで変更イベントが発生したときに、メッセージを送信します。

[IoT Hub は Azure Event Grid とも統合](iot-hub-event-grid.md)されているため、デバイス イベントを発行して、それらのイベントに基づくワークフローのリアルタイムの統合と自動化をサポートできます。 ご自分のシナリオにどれが最適かについては、[メッセージ ルーティングと Event Grid の主な違い](iot-hub-event-grid-routing-comparison.md)に関するページを参照してください。

## <a name="testing-routes"></a>ルートのテスト

新しいルートを作成したり、既存のルートを編集したりする場合は、サンプル メッセージを使用してルート クエリをテストする必要があります。 個々のルートをテストすることも、すべてのルートを一度にテストすることも可能で、テスト中にメッセージがエンドポイントにルーティングされることはありません。 テストには、Azure portal、Azure Resource Manager、Azure PowerShell、および Azure CLI を使用することができます。 結果は、サンプル メッセージがクエリに一致したか、メッセージがクエリに一致しなかったか、サンプル メッセージまたはクエリ構文が正しくないためにテストを実行できなかったかを識別するのに役立ちます。 詳細については、[ルートのテスト](/rest/api/iothub/iothubresource/testroute)に関するページと[すべてのルートのテスト](/rest/api/iothub/iothubresource/testallroutes)に関するページを参照してください。

## <a name="ordering-guarantees-with-at-least-once-delivery"></a>順序では最低 1 回の配信が保証される

IoT Hub メッセージ ルーティングでは、エンドポイントへのメッセージの配信順序と少なくとも 1 回の配信が保証されます。 これは、重複するメッセージが存在する可能性があり、元のメッセージの順序に従って一連のメッセージを再送信できることを意味します。 たとえば、元のメッセージの順序が [1,2,3,4] の場合に、[1,2,1,2,3,1,2,3,4] のようなメッセージ シーケンスを受け取ることがあります。 順序保証では、メッセージ [1] を受信した場合、常に [2, 3, 4] が続きます。

メッセージの重複を処理するには、発生ポイントでメッセージのアプリケーション プロパティに一意識別子をスタンプすることをお勧めします。これは通常、デバイスまたはモジュールです。 メッセージを消費するサービスでは、この識別子を使用して重複するメッセージを処理できます。

## <a name="latency"></a>Latency

組み込みのエンドポイントを使用して device-to-cloud テレメトリ メッセージをルーティングすると、最初のルーティングの作成後にエンド ツー エンドの待機時間が若干上昇します。

ほとんどの場合、待機時間の増加は平均で 500 ミリ秒未満です。 待ち時間は、**Routing: message latency for messages/events (ルーティング: メッセージ/イベントのメッセージ待機時間)** または **d2c.endpoints.latency.builtIn.events** という IoT Hub メトリックを使用して監視することができます。 最初のルーティング後にルーティングを作成または削除してもエンド ツー エンドの待機時間には影響しません。

## <a name="monitoring-and-troubleshooting"></a>監視とトラブルシューティング

IoT Hub には、ハブの正常性と送信されたメッセージの概要を示す、ルーティングおよびエンドポイント関連のメトリックがいくつか用意されています。 機能カテゴリ別に分類されたすべての IoT Hub メトリックの一覧については、[監視データ リファレンスのメトリック](monitor-iot-hub-reference.md#metrics)に関するページを参照してください。 IoT Hub リソース ログの [**ルート** カテゴリ](monitor-iot-hub-reference.md#routes)で、IoT Hub に認識されたルーティング クエリとエンドポイントの正常性の評価中に発生したエラーを追跡することができます。 IoT Hub でのメトリック ログとリソース ログの使用の詳細については、[IoT Hub の監視](monitor-iot-hub.md)に関するページを参照してください。

REST API の [GetEndpointHealth](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) を使用して、エンドポイントの[正常性状態](iot-hub-devguide-endpoints.md#custom-endpoints)を取得できます。

ルーティングのトラブルシューティングに関する詳細とサポートが必要な場合、[ルーティングのトラブルシューティング ガイド](troubleshoot-message-routing.md) ページを参照してください。

## <a name="next-steps"></a>次のステップ

* メッセージ ルートの作成方法については、[ルートを使用した IoT Hub の device-to-cloud メッセージの処理](tutorial-routing.md)に関するページをご覧ください。

* [device-to-cloud メッセージを送信する方法](quickstart-send-telemetry-node.md)

* device-to-cloud メッセージの送信に使用できる SDK については、[Azure IoT SDK](iot-hub-devguide-sdks.md) に関するページを参照してください。