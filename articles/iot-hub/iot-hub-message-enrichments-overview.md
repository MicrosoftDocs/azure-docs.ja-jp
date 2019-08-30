---
title: Azure IoT Hub メッセージ エンリッチメントの概要
description: Azure IoT Hub のメッセージ エンリッチメントの概要
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/10/2019
ms.author: robinsh
ms.openlocfilehash: 9e3df59af33d6e5fdd1024d42c34dc2b4ed3184e
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2019
ms.locfileid: "69873193"
---
# <a name="message-enrichments-for-device-to-cloud-iot-hub-messages-preview"></a>device-to-cloud IoT Hub のメッセージ エンリッチメント (プレビュー)

"*メッセージ エンリッチメント*" は、指定エンドポイントに送信される前のメッセージに対し、追加情報を含んだ "*スタンプ*" を適用する IoT Hub の機能です。 メッセージ エンリッチメントを使用する理由の 1 つは、ダウンストリームの処理を単純化するために用いることのできるデータを追加することです。 たとえば、デバイス ツイン タグを使用してデバイスのテレメトリ メッセージのエンリッチメントを行えば、この情報のために顧客側でデバイス ツイン API を呼び出す負担を軽減することができます。

![メッセージ エンリッチメント フロー](./media/iot-hub-message-enrichments-overview/message-enrichments-flow.png)

メッセージ エンリッチメントには、重要な要素が 3 つあります。

* エンリッチメントの名前またはキー

* 値

* エンリッチメントを適用する[エンドポイント](iot-hub-devguide-endpoints.md) (複数可)

**キー**は文字列です。 キーに含めることができるのは、英数字と特殊文字 (ハイフン (`-`)、アンダースコア (`_`)、ピリオド (`.`)) のみです。

**値**には、次の例のいずれかを使用できます。

* 任意の静的文字列。 条件、ロジック、演算、関数などの動的な値は使用できません。 たとえば、複数の顧客によって使用される SaaS アプリケーションを開発する場合、それぞれの顧客に識別子を割り当て、その識別子をアプリケーション内で利用可能にすることができます。 アプリケーションを実行すると、IoT Hub によってデバイス テレメトリ メッセージに顧客の識別子のスタンプが適用され、顧客ごとに異なる処理をメッセージに適用することができます。

* メッセージを送信する IoT ハブの名前。 この値は *$iothubname* です。

* デバイス ツインの情報 (そのパスなど)。 *$twin.tags.field* や *$twin.tags.latitude* などが考えられます。

   > [!NOTE]
   > 現時点では、メッセージ エンリッチメントのためにサポートされている変数は、$iothubname、$twin.tags、$twin.properties.desired、および $twin.properties.reported のみです。

メッセージ エンリッチメントは、選択したエンドポイントに送信されるメッセージにアプリケーション プロパティとして追加されます。  

## <a name="applying-enrichments"></a>エンリッチメントを適用する

メッセージのソースには、[IoT Hub のメッセージ ルーティング](iot-hub-devguide-messages-d2c.md)でサポートされるあらゆるデータ ソースを使用できます。その例を次に示します。

* デバイス テレメトリ (温度、気圧など)
* デバイス ツインの変更通知 (デバイス ツインにおける変更)
* デバイスのライフサイクル イベント (デバイスが作成または削除された日時など)

エンリッチメントは、IoT ハブの組み込みのエンドポイントに向かうメッセージのほか、カスタム エンドポイントにルーティングされるメッセージ (Azure Blob Storage、Service Bus キュー、Service Bus トピックなど) に追加できます。

また、エンドポイントを Event Grid として選択することにより、Event Grid にパブリッシュされるメッセージにエンリッチメントを追加することもできます。 詳細については、[IoT Hub と Event Grid](iot-hub-event-grid.md) に関するページを参照してください。

エンリッチメントは、エンドポイントごとに適用されます。 特定のエンドポイントに関して適用される 5 つのエンリッチメントを指定した場合、そのエンドポイントに向かうすべてのメッセージに同じ 5 つのエンリッチメントを含んだスタンプが適用されます。

メッセージ エンリッチメントを試す方法については、[メッセージ エンリッチメントのチュートリアル](tutorial-message-enrichments.md)を参照してください。

## <a name="limitations"></a>制限事項

* Standard レベルまたは Basic レベルの IoT ハブの場合、追加できるエンリッチメントは、ハブごとに 10 個までです。 Free レベルの IoT ハブの場合、追加できるエンリッチメントは 2 個までとなります。

* デバイス ツインのタグまたはプロパティに設定された値でエンリッチメントを適用する場合、その値が、文字列値のスタンプとして適用されることがあります。 たとえば、エンリッチメントの値が $twin.tags.field に設定された場合、ツインから得られる当該フィールドの値ではなく、"$twin.tags.field" という文字列を含んだスタンプがメッセージに適用されます。 該当するのは次のケースです。

   * ご利用の IoT ハブが Basic レベルである。 Basic レベルの IoT ハブでは、デバイス ツインがサポートされません。

   * IoT ハブは Standard レベルだが、メッセージを送信するデバイスにデバイス ツインが存在しない。

   * IoT ハブは Standard レベルだが、エンリッチメントの値に使用されるデバイス ツイン パスが存在しない。 たとえば、エンリッチメントの値が $twin.tags.location に設定されているにもかかわらず、デバイス ツインの tags に location プロパティが存在しない場合、メッセージには、"$twin.tags.location" という文字列を含んだスタンプが適用されます。 

* デバイス ツインに対する更新が、対応するエンリッチメント値に反映されるまでに最大 5 分程度かかります。

* エンリッチメントを含むメッセージ サイズの合計が 256 KB を超えることは許容されません。 メッセージ サイズが 256 KB を超えた場合、IoT ハブでメッセージがドロップされます。 メッセージがドロップされたときのエラーは、[IoT Hub メトリック](iot-hub-metrics.md)を使用して識別し、デバッグすることができます。 たとえば、d2c.telemetry.egress.invalid を監視することが考えられます。

* メッセージ エンリッチメントは、デジタル ツイン変更イベント ([IoT プラグ アンド プレイ パブリック プレビューの一部](../iot-pnp/overview-iot-plug-and-play.md)) には適用されません。

## <a name="pricing"></a>価格

メッセージ エンリッチメントは、追加料金なしで利用できます。 現在は、IoT ハブにメッセージを送信したときに料金が発生します。 メッセージが複数のエンドポイントに向かう場合でも、そのメッセージに関して料金が課されるのは 1 回だけです。

## <a name="availability"></a>可用性

この機能はプレビューとして提供され、米国東部、米国西部、西ヨーロッパ、[Azure Government](/azure/azure-government/documentation-government-welcome)、[Azure China 21Vianet](/azure/china)、[Azure Germany](https://azure.microsoft.com/global-infrastructure/germany/) を除くすべてのリージョンで利用できます。

## <a name="next-steps"></a>次の手順

IoT Hub へのメッセージのルーティングの詳細については、次の記事を参照してください。

* [メッセージ拡張のチュートリアル](tutorial-message-enrichments.md)

* [IoT Hub メッセージ ルーティングを使用して device-to-cloud メッセージを別のエンドポイントに送信する](iot-hub-devguide-messages-d2c.md)

* [チュートリアル:IoT Hub のルーティング](tutorial-routing.md)
