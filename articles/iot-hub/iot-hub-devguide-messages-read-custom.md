---
title: Azure IoT Hub のカスタム エンドポイントについて | Microsoft Docs
description: 開発者ガイド - ルーティング クエリを使用して、カスタム エンドポイントに device-to-cloud メッセージのルーティングします。
author: wesmc7777
manager: philmea
ms.author: wesmc
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/09/2018
ms.openlocfilehash: 8aebb5b6f6a3ac53bc49fd1d2f75de88667865fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92147641"
---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>device-to-cloud メッセージにメッセージ ルートとカスタム エンドポイントを使用する

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

IoT Hub の[メッセージ ルーティング](iot-hub-devguide-routing-query-syntax.md)を使用すると、サービスに接続するエンドポイントに device-to-cloud メッセージをルーティングすることができます。 また、ルーティングでは、エンドポイントにルーティングする前にデータをフィルター処理するクエリ機能も提供されています。 各ルーティング クエリには、次のプロパティがあります。

| プロパティ      | 説明 |
| ------------- | ----------- |
| **名前**      | クエリを識別する一意の名前。 |
| **ソース**    | 処理するデータ ストリームの元データ。 たとえば、デバイス テレメトリです。 |
| **Condition** | エンドポイントと一致するかどうかを確認するために、メッセージ アプリケーションのプロパティ、システムのプロパティ、メッセージ本文、デバイス ツインのタグ、デバイス ツインのプロパティに対して実行されるルーティング クエリのクエリ式。 クエリの作成方法について詳しくは、[メッセージ ルーティング クエリの構文](iot-hub-devguide-routing-query-syntax.md)に関するページをご覧ください。 |
| **エンドポイント**  | クエリに一致するメッセージを IoT Hub が送信するエンドポイントの名前。 お使いの IoT ハブと同じリージョンのエンドポイントを選択することをお勧めします。 |

1 つのメッセージが複数のルーティング クエリの条件と一致することがあり、その場合、IoT Hub は一致する各クエリに関連するエンドポイントにメッセージを送信します。 IoT Hub はメッセージ配信の重複を自動的に排除するため、あるメッセージが複数のクエリに一致し、それらのルールの配信先が同じであった場合は、配信先には 1 度のみメッセージが書き込まれます。

## <a name="endpoints-and-routing"></a>エンドポイントとルーティング

IoT ハブは、既定の[組み込みのエンドポイント](iot-hub-devguide-messages-read-builtin.md)を持ちます。 サブスクリプション内の他のサービスをハブにリンクして、メッセージをルーティングするカスタム エンドポイントを作成できます。 IoT Hub は現在、カスタム エンドポイントとして、Azure Storage コンテナー、Event Hubs、Service Bus キュー、Service Bus トピックをサポートします。

ルーティングとカスタム エンドポイントを使用すると、メッセージは、クエリに一致しない場合、組み込みのエンドポイントにのみ配信されます。 メッセージを組み込みのエンドポイントとカスタム エンドポイントに配信するには、組み込み **イベント** エンドポイントにメッセージを送信するルートを追加します。

> [!NOTE]
> * IoT Hub は、Azure Storage コンテナーに BLOB としてデータを書き込む処理のみをサポートしています。
> * **セッション** または **重複データ検出** が有効になっている Service Bus のキューおよびトピックは、カスタム エンドポイントとしてはサポートされていません。

IoT Hub でのカスタム エンドポイントの作成の詳細については、[IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)に関するページを参照してください。

カスタム エンドポイントからの読み取りの詳細については、以下をご覧ください。

* [Azure Storage コンテナー](../storage/blobs/storage-blobs-introduction.md)からの読み取り。

* [Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md) からの読み取り。

* [Service Bus キュー](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md)からの読み取り。

* [Service Bus トピック](../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md)からの読み取り。

## <a name="next-steps"></a>次のステップ

* IoT Hub のエンドポイントの詳細については、[IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)に関するページをご覧ください。

* ルーティング クエリの定義に使用するクエリ言語について詳しくは、[メッセージ ルーティング クエリの構文](iot-hub-devguide-routing-query-syntax.md)に関するページをご覧ください。

* [ルートを使用した IoT Hub の device-to-cloud メッセージの処理](tutorial-routing.md)に関するチュートリアルでは、ルーティング クエリおよびカスタム エンドポイントを使用する方法が説明されています。