---
title: "Azure IoT Hub のカスタム エンドポイントについて | Microsoft Docs"
description: "開発者ガイド - カスタム エンドポイントへの device-to-cloud メッセージのルーティングにルーティング ルールを使用します。"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/25/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 5edc47e03ca9319ba2e3285600703d759963e1f3
ms.openlocfilehash: a21f1c61f344f96e2e03422e41fd8c5f7f841a0c
ms.contentlocale: ja-jp
ms.lasthandoff: 06/01/2017


---
# <a name="use-message-routes-and-custom-endpoints-for-device-to-cloud-messages"></a>device-to-cloud メッセージにメッセージ ルートとカスタム エンドポイントを使用する

IoT Hub では、メッセージ プロパティに基づいて、IoT Hub サービス向けのエンドポイントに [device-to-cloud メッセージ][lnk-device-to-cloud]をルーティングできます。 ルーティング ルールによってメッセージ送信を柔軟に実行できるため、メッセージを処理するためにサービスを追加したり、追加のコードを記述したりする必要はありません。 各ルーティング ルールには、次のプロパティがあります。

| プロパティ      | Description |
| ------------- | ----------- |
| **名前**      | ルールを識別する一意の名前。 |
| **ソース**    | 処理するデータ ストリームの元データ。 たとえば、デバイス テレメトリです。 |
| **Condition** | メッセージのヘッダーと本文に対して実行され、エンドポイントと一致するかどうかの確認のために使用されるルーティング ルールのクエリ式。 ルート条件の構築の詳細については、[リファレンス - デバイス ツインとジョブのクエリ言語][lnk-devguide-query-language]に関する記事を参照してください。 |
| **エンドポイント**  | IoT Hub が条件に一致するメッセージを送信するエンドポイントの名前。 エンドポイントは、IoT Hub と同じリージョン内に存在する必要があり、そうでない場合は、リージョン間の書き込みに対して課金されるおそれがあります。 |

1 つのメッセージが複数のルーティング ルールの条件と一致することがあり、その場合、IoT Hub は一致する各ルールに関連するエンドポイントにメッセージを送信します。 IoT Hub はメッセージ配信の重複を自動的に排除するため、あるメッセージが複数のルールに一致し、それらのルールの配信先がすべて同じであった場合は、配信先には 1 度のみメッセージが配信されます。

IoT hub は、既定の[組み込みのエンドポイント][lnk-built-in]を持ちます。 サブスクリプション内の他のサービスをハブにリンクして、メッセージをルーティングするカスタム エンドポイントを作成できます。 IoT Hub は現在、カスタム エンドポイントとして Event Hubs、Service Bus キュー、Service Bus トピックをサポートします。

> [!WARNING]
> **セッション**または**重複データ検出**が有効になっている Service Bus のキューおよびトピックは、カスタム エンドポイントとしてはサポートされていません。

IoT Hub でのカスタム エンドポイントの作成の詳細については、[IoT Hub エンドポイント][lnk-devguide-endpoints]に関するページを参照してください。

カスタム エンドポイントからの読み取りの詳細については、以下をご覧ください。

* [Event Hubs][lnk-getstarted-eh] から読み取ります。
* [Service Bus キュー][lnk-getstarted-queue]から読み取ります。
* [Service Bus トピック][lnk-getstarted-topic]から読み取ります。

### <a name="next-steps"></a>次のステップ

IoT Hub のエンドポイントの詳細については、[IoT Hub エンドポイント][lnk-devguide-endpoints]に関するページをご覧ください。

ルーティング ルールの定義に使用するクエリ言語の詳細については、[デバイス ツイン、ジョブ、およびメッセージ ルーティングの IoT Hub クエリ言語][lnk-devguide-query-language]に関するページをご覧ください。

[ルートを使用した IoT Hub の device-to-cloud メッセージの処理][lnk-d2c-tutorial]に関するチュートリアルでは、ルーティング ルールおよびカスタム エンドポイントを使用する方法を説明します。

[lnk-built-in]: iot-hub-devguide-messages-read-builtin.md
[lnk-device-to-cloud]: iot-hub-devguide-messages-d2c.md
[lnk-devguide-query-language]: iot-hub-devguide-query-language.md
[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
[lnk-getstarted-eh]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-getstarted-queue]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md
[lnk-getstarted-topic]: ../service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions.md

