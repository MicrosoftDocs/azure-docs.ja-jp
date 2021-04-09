---
title: 他のサービスとの統合
titleSuffix: Azure Digital Twins
description: Azure Digital Twins をデプロイするときのイングレスとエグレスの要件について説明します。
author: baanders
ms.author: baanders
ms.date: 3/16/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 118b02ab694d27dbe4e13cbfa1a617a56b052772
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "92043070"
---
# <a name="integrate-azure-digital-twins-with-other-services"></a>Azure Digital Twins を他のサービスと統合する

通常、Azure Digital Twins は他のサービスと共に使用し、さまざまな方法でデータを使用する柔軟な接続済みソリューションを作成します。

Azure Digital Twins では、[**イベント ルート**](concepts-route-events.md)を使用して、テレメトリと通知を配信するために使用される [IoT Hub](../iot-hub/about-iot-hub.md) や [Logic Apps](../logic-apps/logic-apps-overview.md) などのアップストリーム サービスからデータを受信できます。 

Azure Digital Twins では、ストレージ、ワークフロー統合、分析などのためにデータを [Azure Maps](../azure-maps/about-azure-maps.md) や [Time Series Insights](../time-series-insights/overview-what-is-tsi.md) などのダウンストリーム サービスにルーティングすることもできます。 

## <a name="data-ingress"></a>データのイングレス

Azure Digital Twins は、[IoT Hub](../iot-hub/about-iot-hub.md)、[Logic Apps](../logic-apps/logic-apps-overview.md)、独自のカスタム サービスなど、任意のサービスからのデータとイベント使用して動作できます。 これにより、環境内の物理デバイスからテレメトリを収集し、クラウドの Azure Digital Twins グラフを使用してこのデータを処理することができます。

組み込みの IoT Hub をバックグラウンドで使用する代わりに、Azure Digital Twins を使用すると、サービスで使用するための "独自の" IoT Hub を実現できます。 現在運用中の既存の IoT Hub を使用することも、この目的で使用する新しい IoT Hub をデプロイすることもできます。 これにより、IoT Hub のすべてのデバイス管理機能にフル アクセスできます。

任意のソースから Azure Digital Twins にデータを取り込むには、[**Azure 関数**](../azure-functions/functions-overview.md)を使用します。 このパターンの詳細については、[*IoT Hub からテレメトリを取り込む方法*](how-to-ingest-iot-hub-data.md)に関するページを参照するか、Azure Digital Twins [*チュートリアルのエンド ツー エンドのソリューションの接続*](tutorial-end-to-end.md)に関するページを参照してください。 

また、Azure Digital Twins を Logic Apps トリガーに接続する方法については、[*Logic Apps と統合する方法*](how-to-integrate-logic-apps.md)に関するページを参照してください。

## <a name="data-egress-services"></a>データ エグレス サービス

Azure Digital Twins は、接続されている **エンドポイント** にデータを送信できます。 サポートされるエンドポイントは次のとおりです。
* [イベント ハブ](../event-hubs/event-hubs-about.md)
* [Event Grid](../event-grid/overview.md)
* [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)

エンドポイントは、管理 API または Azure portal を使用して Azure Digital Twins にアタッチされています。 エンドポイントを Azure Digital Twins に接続する方法の詳細については、[*エンドポイントとルートを管理する方法*](how-to-manage-routes-apis-cli.md)に関するページを参照してください。

他にも、[Azure Storage](../storage/common/storage-introduction.md)、[Azure Maps](../azure-maps/about-azure-maps.md)、[Time Series Insights](../time-series-insights/overview-what-is-tsi.md) など、データの最終的な転送先となるサービスが多数あります。 このようなサービスにデータを送信するには、宛先サービスをエンドポイントにアタッチします。

たとえば、Azure Maps も使用していて、Azure Digital Twins の[ツイン グラフ](concepts-twins-graph.md)と場所を関連付ける必要がある場合は、Azure Functions で Event Grid を使用して、デプロイ内のすべてのサービス間の通信を確立できます。 これについて詳しくは、[*Azure Digital Twins を使用して Azure Maps の屋内マップを更新する方法*](how-to-integrate-maps.md)に関するページを参照してください

また、Time Series Insights と同様の方法でデータをルーティングする方法については、[*Time Series Insights と統合する方法*](how-to-integrate-time-series-insights.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

エンドポイントの詳細と外部サービスに対するルーティング イベントについては、次を参照してください。
* "[*概念: Azure Digital Twins イベントのルーティング*](concepts-route-events.md)

IoT Hub からデータを取り込むように Azure Digital Twins を設定する方法をご覧ください。
* [*方法: IoT Hub からテレメトリを取り込む*](how-to-ingest-iot-hub-data.md)