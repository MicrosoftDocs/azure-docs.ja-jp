---
title: イベントとメッセージのルーティング - Azure Digital Twins | Microsoft Docs
description: Azure Digital Twins でのサービス エンドポイントへのイベントとメッセージのルーティングの概要を説明します
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 217a1d94a4a5235fc5886f34986ffcb3aef60873
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949264"
---
# <a name="routing-events-and-messages"></a>ルーティング イベントおよびメッセージ

IoT ソリューションでは、多くの場合、ストレージ、分析、その他の複数の強力なサービスが結合されます。 この記事では、Azure Digital Twins アプリを Azure 分析、AI、およびストレージ サービスに接続して、より深い分析情報と機能を提供する方法について説明します。

## <a name="route-types"></a>ルートの種類  

Azure Digital Twins には、IoT イベントを他の Azure サービスやビジネス アプリケーションと接続するための 2 つの方法が用意されています。

* **Azure Digital Twins イベントのルーティング**:Azure Digital Twins イベントは、空間グラフ内のオブジェクトが変化したとき、テレメトリ データが受信されたとき、または事前定義された条件に基づいてユーザー定義関数が通知を作成したときにトリガーされます。 ユーザーは、さらに処理するために、これらのイベントを [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/)、[Azure Service Bus トピック](https://azure.microsoft.com/services/service-bus/)、または [Azure Event Grid](https://azure.microsoft.com/services/event-grid/) に送信できます。

* **デバイス テレメトリのルーティング**:イベントのルーティングだけでなく、Azure Digital Twins では生デバイス テレメトリ メッセージを Event Hubs にルーティングして、さらに解析や分析を行うことができます。 この種のメッセージは、Azure Digital Twins によって処理されません。 これらはイベント ハブにのみ転送されます。

ユーザーは、イベントの送信またはメッセージの転送のためのエグレス エンドポイントを 1 つ以上指定できます。 イベントとメッセージは、これらの定義済みのルーティング設定に従って、エンドポイントに送信されます。 つまり、ユーザーは、グラフ操作イベントの受信用に特定のエンドポイントを指定し、デバイスのテレメトリ イベントの受信用に別のエンドポイントを指定できます。

[![Azure Digital Twins のイベントのルーティング](media/concepts/digital-twins-events-routing.png)](media/concepts/digital-twins-events-routing.png#lightbox)

Event Hubs へのルーティングでは、テレメトリ メッセージの送信順序が保持されます。 そのため、メッセージは最初の受信時と同じ順序でエンドポイントに到達します。 Event Grid と Service Bus では、エンドポイントがその発生順にイベントを取得することは保証されません。 ただし、イベント スキーマにはタイムスタンプが含まれており、これを使用してイベントがエンドポイントに到着した後に順序を特定できます。

## <a name="route-implementation"></a>ルートの実装

Azure Digital Twins サービスでは、現在、以下の **EndpointTypes** がサポートされています。

* **EventHub** は、Event Hubs の接続文字列エンドポイントです。
* **ServiceBus** は、Service Bus の接続文字列エンドポイントです。
* **EventGrid** は、Event Grid の接続文字列エンドポイントです。

現在、Azure Digital Twins では、選択したエンドポイントに送信される以下の **EventTypes** がサポートされています。

* **DeviceMessages** は、ユーザーのデバイスから送信され、システムによって転送される、テレメトリ メッセージです。
* **TopologyOperation** は、グラフまたはグラフのメタデータを変更する操作です。 たとえば、スペースなどのエンティティの追加や削除などです。
* **SpaceChange** は、デバイス テレメトリ メッセージの結果として生じた、スペースの計算値の変化です。
* **SensorChange** は、デバイス テレメトリ メッセージの結果として生じた、センサーの計算値の変化です。
* **UdfCustom** は、ユーザー定義関数からのカスタム通知です。

> [!IMPORTANT]  
> すべての **EndpointTypes** がすべての **EventTypes** をサポートしているわけではありません。
> 各 **EndpointType** で許容される **EventTypes** については、次の表をご覧ください。

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>エンドポイントの作成方法と、イベントのスキーマの例については、[エグレスとエンドポイント](how-to-egress-endpoints.md)に関するページをご覧ください。

## <a name="next-steps"></a>次の手順

- Azure Digital Twins のプレビューの制限については、「[パブリック プレビューのサービスの制限](concepts-service-limits.md)」をご覧ください。

- Azure Digital Twins のサンプルを試すには、[使用可能な部屋を検索するクイック スタート](quickstart-view-occupancy-dotnet.md)のページをご覧ください。