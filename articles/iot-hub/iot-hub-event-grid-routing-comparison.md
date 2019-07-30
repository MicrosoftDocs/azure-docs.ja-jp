---
title: IoT Hub における Event Grid とルーティングの比較 | Microsoft Docs
description: IoT Hub には独自のメッセージ ルーティング サービスが用意されていますが、イベントの発行のために Event Grid とも統合します。 2 つの機能を比較します。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: kgremban
ms.openlocfilehash: c6d395d878d38ab48b95ca82d94d2a222428bae1
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274942"
---
# <a name="compare-message-routing-and-event-grid-for-iot-hub"></a>IoT Hub のメッセージ ルーティングと Event Grid の比較

Azure IoT Hub には、接続されたデバイスからデータをストリーミングし、そのデータをビジネス アプリケーションに統合する機能があります。 IoT Hub には、IoT イベントを他の Azure サービスやビジネス アプリケーションに統合するための 2 つの方法が用意されています。 この記事では、実際のシナリオに最適なオプションを選択できるように、この機能を提供する 2 つの方法について説明します。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

**[IoT Hub メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)** :この IoT Hub の機能を使用して、Azure Storage コンテナー、Event Hubs、Service Bus キュー、Service Bus トピックなどのサービス エンドポイントに、device-to-cloud メッセージをルーティングすることができます。 また、ルーティングでは、エンドポイントにルーティングする前にデータをフィルター処理するクエリ機能も提供されています。 デバイスのテレメトリ データに加えて、アクションのトリガーに使用できる[非テレメトリ イベント](iot-hub-devguide-messages-d2c.md#non-telemetry-events)も送信できます。 

**IoT Hub の Event Grid との統合**:Azure Event Grid は、パブリッシュ - サブスクライブ モデルを使用する、フル マネージドのイベント ルーティング サービスです。 IoT Hub と Event Grid は、[Azure サービスと Azure 以外のサービスに IoT Hub イベントを統合する](iot-hub-event-grid.md)ために、ほぼリアルタイムで連携します。 IoT Hub では、一般公開されている[デバイス イベント](iot-hub-event-grid.md#event-types)を発行し、パブリック プレビュー段階である利用統計情報も発行するようになりました。

## <a name="differences"></a>相違点

メッセージ ルーティングと Event Grid は共にアラートの構成を可能にしますが、両者の間には主な違いがいくつかあります。 詳細については、以下の表を参照してください。

| 機能 | IoT Hub メッセージ ルーティング | IoT Hub の Event Grid との統合 |
| ------- | --------------- | ---------- |
| **デバイスのメッセージとイベント** | はい、利用統計情報にメッセージ ルーティングを使用することができ、ツイン変更とデバイス ライフサイクル イベントをレポートできます。 | はい、利用統計情報に Event Grid を使用することはできますが、IoT Hub のデバイスが作成、削除、接続、切断されたときにレポートすることもできます。 |
| **順序付け** | はい、イベントの順序付けは維持されます。  | いいえ、イベントの順序付けは保証されません。 | 
| **Filtering** | メッセージ アプリケーション プロパティ、メッセージ システム プロパティ、メッセージ本文、デバイス ツインのタグおよびプロパティに基づく豊富なフィルター。 例については、[メッセージ ルーティングのクエリ構文](iot-hub-devguide-routing-query-syntax.md)に関するページを参照してください。 | 各イベントのイベントの種類、サブジェクトの種類、属性に基づくフィルター処理。 たとえば、[Event Grid サブスクリプションでのフィルター処理イベントの理解](../event-grid/event-filtering.md)に関するページを参照してください。 利用統計情報をサブスクライブしている場合、Event Grid に発行する前に、データに追加のフィルターを適用し、ご利用の IoT Hub のメッセージ プロパティ、メッセージ本文、デバイス ツインにフィルターを適用することができます。 [イベントのフィルター方法](../iot-hub/iot-hub-event-grid.md#filter-events)に関するページを参照してください。 |
| **Endpoints** | <ul><li>Event Hubs</li> <li>Azure Blob Storage</li> <li>Service Bus キュー</li> <li>Service Bus トピック</li></ul><br>有料の IoT Hub SKU (S1、S2、S3) は、10 のカスタム エンドポイントに制限されます。 IoT Hub あたり 100 のルートを作成できます。 | <ul><li>Azure Functions</li> <li>Azure Automation</li> <li>Event Hubs</li> <li>Logic Apps</li> <li>ストレージ BLOB</li> <li>カスタム トピック</li> <li>Queue Storage</li> <li>Microsoft Flow</li> <li>WebHook を通じたサード パーティのサービス</li></ul><br>IoT Hub ごとに 500 個のエンドポイントがサポートされます。 最新のエンドポイントの一覧については、[Event Grid のイベント ハンドラー](../event-grid/overview.md#event-handlers)に関する記事を参照してください。 |
| **コスト** | メッセージ ルーティングについては、別料金はかかりません。 IoT Hub へのテレメトリのイングレスのみが課金されます。 たとえば、3 つの異なるエンドポイントにメッセージをルーティングする場合、1 つのメッセージに対してのみ課金されます。 | IoT Hub からの課金はありません。 Event Grid では、毎月最初の 100,000 操作が無料で提供され、それ以降は 100 万操作あたり 0.60 ドルが課金されます。 |

## <a name="similarities"></a>類似点

IoT Hub メッセージ ルーティングと Event Grid には類似点もあります。そのいくつかについて以下の表で詳しく説明します。

| 機能 | IoT Hub メッセージ ルーティング | IoT Hub の Event Grid との統合 |
| ------- | --------------- | ---------- |
| **最大メッセージ サイズ** | 256 KB、device-to-cloud | 256 KB、device-to-cloud |
| **信頼性** | [高]:ルートごとに少なくとも 1 回はエンドポイントに各メッセージを配信します。 1 時間内に配信されないメッセージはすべて有効期限切れになります。 | [高]:サブスクリプションごとに少なくとも 1 回は webhook に各メッセージを配信します。 24 時間内に配信されないイベントはすべて有効期限切れになります。 | 
| **拡張性** | [高]:何十億ものメッセージを送信する数百万の同時接続されたデバイスをサポートするように最適化されます。 | [高]:リージョンあたり 10,000,000 イベント/秒をルーティングすることができます。 |
| **待機時間** | [低]:ほぼリアルタイムです。 | [低]:ほぼリアルタイムです。 |
| **複数のエンドポイントへの送信** | はい、1 つのメッセージを複数のエンドポイントに送信します。 | はい、1 つのメッセージを複数のエンドポイントに送信します。  
| **セキュリティ** | IoT Hub では、デバイスごとの ID と取り消し可能なアクセス制御が用意されます。 詳細については、[IoT Hub のアクセス制御](iot-hub-devguide-security.md)に関する記事を参照してください。 | Event Grid には 3 点での検証が用意されています。イベント サブスクリプション、イベントの発行、webhook のイベント配信です。 詳細については、「[Event Grid security and authentication](../event-grid/security-authentication.md)」(Event Grid のセキュリティと認証) を参照してください。 |

## <a name="how-to-choose"></a>選択する方法

IoT Hub メッセージ ルーティングと IoT Hub の Event Grid との統合は、同様の結果を得るために異なるアクションを実行します。 両者は共に IoT Hub ソリューションから情報を取得し、他のサービスが対応できるように情報を渡します。 どのようにして、どちらを使用するか決めればよいのでしょうか。 判断に役立つ次の質問を検討します。 

* **どのような種類のデータをエンドポイントに送信しますか。**

   他のサービスにテレメトリ データを送信する必要がある場合は、IoT Hub メッセージ ルーティングを使用します。 メッセージ ルーティングでは、メッセージ アプリケーションとメッセージ システムのプロパティ、メッセージ本文、デバイス ツインのタグとプロパティのクエリも可能です。

   IoT Hub の Event Grid との統合は、IoT Hub サービスで発生するイベントに対応します。 これらの IoT Hub イベントには、利用統計情報、デバイスの作成、削除、接続、切断が含まれます。 利用統計情報をサブスクライブしている場合、Event Grid に発行する前に、データに追加のフィルターを適用し、ご利用の IoT Hub のメッセージ プロパティ、メッセージ本文、デバイス ツインにフィルターを適用することができます。 [イベントのフィルター方法](../iot-hub/iot-hub-event-grid.md#filter-events)に関するページを参照してください。

* **どのようなエンドポイントがこの情報を受信する必要がありますか。**

   IoT Hub メッセージ ルーティングでは、固有のエンドポイント数とエンドポイントの種類が制限されますが、コネクタを構築してデータとイベントを追加のエンドポイントに経路変更することができます。 サポートされるエンドポイントの完全な一覧については、前のセクションの表を参照してください。 

   IoT Hub と Event Grid の統合では、IoT Hub ごとに 500 個のエンドポイントをサポートし、より多様なエンドポイントの種類をサポートします。 これはネイティブに Azure Functions、Logic Apps、Storage と Service Bus キューと統合され、Azure サービスのエコシステム外のデータ送信に拡張し、サードパーティのビジネス アプリケーションに対して Webhook も使用します。

* **データが到着する順序は重要ですか。**

   IoT Hub メッセージ ルーティングでは、メッセージが送信される順序が保持されるため、同じ順序で配信されます。

   Event Grid では、エンドポイントがその発生順にイベントを取得することは保証されません。 メッセージの絶対的な順序が重要なケースや、コンシューマーが信頼できるメッセージの一意識別子を必要とするケースでは、メッセージ ルーティングを使用することをお勧めします。 

## <a name="next-steps"></a>次の手順

* [IoT Hub メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)および [IoT Hub エンドポイント](iot-hub-devguide-endpoints.md)について学習します。
* [Azure Event Grid](../event-grid/overview.md) について学習します。
* メッセージ ルートの作成方法については、[ルートを使用した IoT Hub の device-to-cloud メッセージの処理](../iot-hub/tutorial-routing.md)に関するチュートリアルを参照してください。
* [Logic Apps を使用して Azure IoT Hub のイベントに関する電子メール通知を送信する](../event-grid/publish-iot-hub-events-to-logic-apps.md)手順に従って Event Grid の統合を試します。
