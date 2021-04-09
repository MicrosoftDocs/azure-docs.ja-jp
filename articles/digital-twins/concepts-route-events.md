---
title: イベント ルート
titleSuffix: Azure Digital Twins
description: Azure Digital Twins とその他の Azure サービス内でイベントをルーティングする方法について説明します。
author: baanders
ms.author: baanders
ms.date: 10/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: ea412b695c12f3ff7fdfa6250e2a474b618b8032
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102430923"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Azure Digital Twins の内外でイベントをルーティングする

Azure Digital Twins は、**イベント ルート** を使用して、サービスの外部のコンシューマーにデータを送信します。 

Azure Digital Twins データを送信する主なケースが 2 つあります。
* Azure Digital Twins グラフの 1 つのツインから別のツインにデータを送信する。 たとえば、あるデジタル ツインのプロパティが変更された際、それに応じて別のデジタル ツインに通知し、更新する必要がある場合があります。
* データを下流のデータ サービスに送信して、追加のストレージまたは処理 (*データ エグレス* とも呼ばれます) を行います。 たとえば、
  - 病院は、Azure Digital Twins イベント データを [Time Series Insights (TSI)](../time-series-insights/overview-what-is-tsi.md) に送信して、一括分析に関連するイベントの時系列データを記録することができます。
  - [Azure Maps](../azure-maps/about-azure-maps.md) を既に使用している企業は、Azure Digital Twins を使用してソリューションを強化することが必要になる場合があります。 Azure Digital Twins を設定した後、Azure Map を迅速に有効にしたり、ツイン グラフの[デジタル ツイン](concepts-twins-graph.md)として Azure Map エンティティを Azure Digital Twins に配置したり、Azure Maps と Azure Digital Twins データを組み合わせて活用して強力なクエリを実行したりすることができます。

イベント ルートは、これらの両方のシナリオで使用されます。

## <a name="about-event-routes"></a>イベント ルートについて

イベント ルートを使用すると、Azure Digital Twins のデジタル ツインから、サブスクリプション内のカスタム定義のエンドポイントにイベント データを送信できます。 現在、3 つの Azure サービスがエンドポイントでサポートされています。[Event Hub](../event-hubs/event-hubs-about.md)、[Event Grid](../event-grid/overview.md)、[Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) です。 これらの Azure サービスは、他のサービスに接続して仲介者として機能し、必要な処理を行うために TSI や Azure Maps などの最終的な宛先にデータを送信できます。

次の図は、Azure Digital Twins の特徴を持つ大規模な IoT ソリューションを使用したイベント データのフローを示しています。

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Azure Digital Twins は、エンドポイントを介して複数のダウンストリーム サービスにデータをルーティングします" border="false":::

イベント ルートの一般的なダウンストリーム ターゲットは、TSI、Azure Maps、ストレージ、分析ソリューションなどのリソースです。

### <a name="event-routes-for-internal-digital-twin-events"></a>内部デジタル ツイン イベントのイベント ルート

イベント ルートはツイン グラフ内のイベントを処理し、デジタル ツインからデジタル ツインにデータを送信するためにも使用されます。 これは、Event Grid 経由でイベント ルートを [Azure Functions](../azure-functions/functions-overview.md) などのコンピューティング リソースに接続して行われます。 これらの関数は、ツインがイベントを受信して応答する方法を定義します。 

イベント ルート経由で受信したイベントに基づいて、コンピューティング リソースがツイン グラフを変更する場合は、事前に変更するツインを把握しておくことをお勧めします。 

また、イベント メッセージには、メッセージを送信したソース ツインの ID も含まれているので、コンピューティング リソースはクエリまたはリレーションシップをスキャンして目的の操作のターゲット ツインを見つけることができます。 

コンピューティング リソースは、セキュリティとアクセス許可を個別に確立する必要もあります。

デジタル ツイン イベントを処理するように Azure 関数を設定する手順については、[*方法:データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)に関するページを参照してください。

## <a name="create-an-endpoint"></a>エンドポイントの作成

イベント ルートを定義するには、開発者が最初にエンドポイントを定義する必要があります。 **エンドポイント** は、ルート接続をサポートする Azure Digital Twins の外部の宛先です。 サポートされる宛先:
* Event Grid カスタム トピック
* イベント ハブ
* Service Bus

エンドポイントを作成するには、Azure Digital Twins [REST API、CLI コマンド](how-to-manage-routes-apis-cli.md#create-an-endpoint-for-azure-digital-twins)、または [Azure portal](how-to-manage-routes-portal.md#create-an-endpoint-for-azure-digital-twins) を使用できます。

エンドポイントを定義するときは、次を指定する必要があります。
* エンドポイントの名前
* エンドポイントの種類 (Event Grid、イベント ハブ、または Service Bus)
* 認証するプライマリ接続文字列およびセカンダリ接続文字列 
* エンドポイントのトピック パス (*your-topic.westus2.eventgrid.azure.net など*)

コントロール プレーンで使用できるエンドポイント API は次のとおりです。
* エンドポイントを作成する
* エンドポイント一覧を取得する
* 名前によりエンドポイントを取得する
* 名前によりエンドポイントを削除する

## <a name="create-an-event-route"></a>イベント ルートを作成する
 
イベント ルートを作成するには、Azure Digital Twins [REST API、CLI コマンド](how-to-manage-routes-apis-cli.md#create-an-event-route)、または [Azure portal](how-to-manage-routes-portal.md#create-an-event-route) を使用できます。

次に示すのは、`CreateOrReplaceEventRouteAsync` [.NET (C#) SDK](/dotnet/api/overview/azure/digitaltwins/client) 呼び出しを使用して、クライアント アプリケーション内でイベント ルートを作成する例です。 

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/eventRoute_operations.cs" id="CreateEventRoute":::

1. まず、`DigitalTwinsEventRoute` オブジェクトが作成され、コンストラクターはエンドポイントの名前を受け取ります。 この `endpointName` フィールドは、Event Hub、Event Grid、または Service Bus などのエンドポイントを識別します。 この登録呼び出しを行う前に、これらのエンドポイントをサブスクリプションに作成し、コントロール プレーン API を使用して Azure Digital Twins にアタッチする必要があります。

2. また、イベント ルート オブジェクトには [**Filter**](how-to-manage-routes-apis-cli.md#filter-events)フィールドがあります。このルートの後に続くイベントの種類を制限するために使用できます。 `true` のフィルターを使用すると、追加のフィルター処理を適用せずにルートが有効になります (`false` のフィルターによって、ルートは無効になります)。 

3. このイベント ルート オブジェクトは、ルートの名前と共に `CreateOrReplaceEventRouteAsync` に渡されます。

> [!TIP]
> すべての SDK 関数に同期バージョンと非同期バージョンがあります。

## <a name="dead-letter-events"></a>配信不能イベント

エンドポイントでは、一定の時間内にイベントを配信できない場合、あるいはイベントの配信を一定回数試行したが配信できない場合、未配信イベントをストレージ アカウントに送信できます。 このプロセスは **配信不能処理** と呼ばれます。 Azure Digital Twins では、**次のいずれかの** 条件に一致したとき、イベントが配信不能処理されます。 

* イベントが Time-To-Live 期間内に配信されない
* イベント配信試行回数が上限を超えている

いずれかの条件が満たされた場合、イベントは削除されるか、配信不能になります。 既定では、各エンドポイントは配信不能を有効に **しません**。 この処理を有効にするには、エンドポイントの作成時に、配信不能イベントを保持するようにストレージ アカウントを指定する必要があります。 その後で、このストレージ アカウントからイベントをプルして配信を解決できます。

配信不能の場所を設定するには、コンテナーを含むストレージ アカウントが必要です。 エンドポイントの作成時に、このコンテナーの URL を指定します。 配信不能メッセージは、コンテナーの URL として、SAS トークンで提供されます。 このトークンには、ストレージ アカウント内の送信先コンテナーに対する `write` アクセス許可のみが必要です。 正しい形式の URL は次の形式になります。`https://<storageAccountname>.blob.core.windows.net/<containerName>?<SASToken>`

SAS トークンの詳細については、以下を参照してください。[*Shared Access Signatures (SAS) を使用して Azure Storage リソースへの制限付きアクセスを許可する*](../storage/common/storage-sas-overview.md)

配信不能処理を構成してエンドポイントを設定する方法については、操作方法ガイドの「[*Azure Digital Twins のエンドポイントとルートを管理する (API と CLI)*](how-to-manage-routes-apis-cli.md#create-an-endpoint-with-dead-lettering)」を参照してください。

### <a name="types-of-event-messages"></a>イベントメッセージの種類

次に示すように、IoT Hub と Azure Digital Twins のさまざまな種類のイベントによって、さまざまな種類の通知メッセージが生成されます。

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>次のステップ

イベント ルートを設定および管理する方法を見る:
* [*方法:エンドポイントとルートを管理する*](how-to-manage-routes-apis-cli.md)

または、Azure Functions を使用して Azure Digital Twins 内でイベントをルーティングする方法を見る:
* [*方法:データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)