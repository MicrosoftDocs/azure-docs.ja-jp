---
title: イベント ルート
titleSuffix: Azure Digital Twins
description: Azure Digital Twins とその他の Azure サービス内でイベントをルーティングする方法について説明します。
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 394752792d143a3712d0bb9c50189936f23062f1
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/05/2020
ms.locfileid: "87800468"
---
# <a name="route-events-within-and-outside-of-azure-digital-twins"></a>Azure Digital Twins の内外でイベントをルーティングする

Azure Digital Twins は、**イベント ルート**を使用して、サービスの外部のコンシューマーにデータを送信します。 

プレビュー期間中は、Azure Digital Twins データを送信する主なケースが 2 つあります。
* Azure Digital Twins グラフの 1 つのツインから別のツインにデータを送信する。 たとえば、あるデジタル ツインのプロパティが変更された際、それに応じて別のデジタル ツインに通知し、更新する必要がある場合があります。
* データを下流のデータ サービスに送信して、追加のストレージまたは処理 (*データ エグレス*とも呼ばれます) を行います。 たとえば、
  - 病院は、Azure Digital Twins イベント データを [Time Series Insights (TSI)](../time-series-insights/time-series-insights-update-overview.md) に送信して、一括分析に関連するイベントの時系列データを記録することができます。
  - [Azure Maps](../azure-maps/about-azure-maps.md) を既に使用している企業は、Azure Digital Twins を使用してソリューションを強化することが必要になる場合があります。 Azure Digital Twins を設定した後、Azure Map を迅速に有効にしたり、ツイン グラフの[デジタル ツイン](concepts-twins-graph.md)として Azure Map エンティティを Azure Digital Twins に配置したり、Azure Maps と Azure Digital Twins データを組み合わせて活用して強力なクエリを実行したりすることができます。

イベント ルートは、これらの両方のシナリオで使用されます。

## <a name="about-event-routes"></a>イベント ルートについて

イベント ルートを使用すると、Azure Digital Twins のデジタル ツインから、サブスクリプション内のカスタム定義のエンドポイントにイベント データを送信できます。 現在、3 つの Azure サービスがエンドポイントでサポートされています。[Event Hub](../event-hubs/event-hubs-about.md)、[Event Grid](../event-grid/overview.md)、[Service Bus](../service-bus-messaging/service-bus-messaging-overview.md) です。 これらの Azure サービスは、他のサービスに接続して仲介者として機能し、必要な処理を行うために TSI や Azure Maps などの最終的な宛先にデータを送信できます。

次の図は、Azure Digital Twins の特徴を持つ大規模な IoT ソリューションを使用したイベント データのフローを示しています。

:::image type="content" source="media/concepts-route-events/routing-workflow.png" alt-text="Azure Digital Twins は、エンドポイントを介して複数のダウンストリーム サービスにデータをルーティングします" border="false":::

イベント ルートの一般的なダウンストリーム ターゲットは、TSI、Azure Maps、ストレージ、分析ソリューションなどのリソースです。

### <a name="event-routes-for-internal-digital-twin-events"></a>内部デジタル ツイン イベントのイベント ルート

現在のプレビュー リリースでは、イベント ルートはツイン グラフ内のイベントを処理し、デジタル ツインからデジタル ツインにデータを送信するためにも使用されます。 これは、Event Grid 経由でイベント ルートを [Azure Functions](../azure-functions/functions-overview.md) などのコンピューティング リソースに接続して行われます。 これらの関数は、ツインがイベントを受信して応答する方法を定義します。 

イベント ルート経由で受信したイベントに基づいて、コンピューティング リソースがツイン グラフを変更する場合は、事前に変更するツインを把握しておくことをお勧めします。 

また、イベント メッセージには、メッセージを送信したソース ツインの ID も含まれているので、コンピューティング リソースはクエリまたはリレーションシップをスキャンして目的の操作のターゲット ツインを見つけることができます。 

コンピューティング リソースは、セキュリティとアクセス許可を個別に確立する必要もあります。

デジタル ツイン イベントを処理するように Azure 関数を設定する手順については、[*方法:データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)に関するページを参照してください。

## <a name="create-an-endpoint"></a>エンドポイントの作成

イベント ルートを定義するには、開発者が最初にエンドポイントを定義する必要があります。 **エンドポイント**は、ルート接続をサポートする Azure Digital Twins の外部の宛先です。 現在のプレビュー リリースでサポートされている宛先は次のとおりです。
* Event Grid カスタム トピック
* イベント ハブ
* Service Bus

エンドポイントは、コントロール プレーン API ([Azure Digital Twins CLI](how-to-use-cli.md)でサポートされている)、または Azure portal 経由で設定されます。 エンドポイント定義は次を示します。
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
 
イベント ルートは、次の [.NET (C#) SDK](how-to-use-apis-sdks.md) 呼び出しを使用してクライアント アプリケーションで作成されます。 

```csharp
await client.EventRoutes.AddAsync("<name-for-the-new-route>", new EventRoute("<endpoint-name>"));
```

* `endpoint-name` は、イベント ハブ、Event Grid、Service Bus などのエンドポイントを識別します。 この登録呼び出しを行う前に、これらのエンドポイントをサブスクリプションに作成し、コントロール プレーン API を使用して Azure Digital Twins にアタッチする必要があります。

`EventRoutes.Add` に渡されるイベント ルート オブジェクトは、[**filter** パラメーター](./how-to-manage-routes-apis-cli.md#filter-events)を使用して、このルートに続くイベントの種類を制限することもできます。

ルートは、[Azure Digital Twins CLI](how-to-use-cli.md) を使用して作成することもできます。

### <a name="types-of-event-messages"></a>イベントメッセージの種類

次に示すように、IoT Hub と Azure Digital Twins のさまざまな種類のイベントによって、さまざまな種類の通知メッセージが生成されます。

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="next-steps"></a>次のステップ

イベント ルートを設定および管理する方法を見る:
* [*方法:エンドポイントとルートを管理する*](how-to-manage-routes-apis-cli.md)

または、Azure Functions を使用して Azure Digital Twins 内でイベントをルーティングする方法を見る:
* [*方法:データを処理するための Azure 関数の設定*](how-to-create-azure-function.md)