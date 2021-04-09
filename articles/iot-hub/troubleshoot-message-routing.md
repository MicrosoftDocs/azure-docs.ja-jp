---
title: Azure IoT メッセージ ルーティングのトラブルシューティング
description: Azure IoT メッセージ ルーティングのトラブルシューティングを実行する方法
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: asrastog
ms.openlocfilehash: 3abff5645775d724042acba3ee2461c7cad771a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "103149666"
---
# <a name="troubleshooting-message-routing"></a>メッセージ ルーティングのトラブルシューティング

この記事では、IoT Hub [メッセージ ルーティング](iot-hub-devguide-messages-d2c.md)の一般的な問題と解決策に対する監視とトラブルシューティングのガイダンスを提供します。

## <a name="monitoring-message-routing"></a>メッセージ ルーティングの監視

[メッセージ ルーティングとエンドポイントに関連する IoT Hub メトリック](monitor-iot-hub-reference.md#routing-metrics)を監視して、送信されたメッセージの概要を確認することをお勧めします。 また、[IoT Hub リソース ログの **ルート**](monitor-iot-hub-reference.md#routes)に対する操作を、カスタム処理を実行するために、Azure Monitor ログ、Event Hubs、Azure Storage に送信するための診断設定を作成することもできます。 メトリック、リソース ログ、診断設定の使用の詳細については、[IoT Hub の監視](monitor-iot-hub.md)に関する記事を参照してください。 チュートリアルについては、[IoT Hub でのメトリックとリソース ログの設定と使用](tutorial-use-metrics-and-diags.md)に関するページを参照してください。

また、どのルートでもクエリに一致しないメッセージを維持する場合は、[フォールバック ルート](iot-hub-devguide-messages-d2c.md#fallback-route)を有効にすることをお勧めします。 これらは、構成されている保持日数の間、[組み込みエンドポイント](iot-hub-devguide-messages-read-builtin.md)に保持できます。

## <a name="top-issues"></a>代表的な問題

メッセージ ルーティングで最も一般的な問題を次に示します。 トラブルシューティングを開始するには、問題をクリックして詳細な手順を確認します。

* [デバイスからのメッセージが想定どおりにルーティングされていません](#messages-from-my-devices-are-not-being-routed-as-expected)
* [組み込みの Event Hubs エンドポイントでのメッセージの受信が突然停止しました](#i-suddenly-stopped-getting-messages-at-the-built-in-endpoint)

### <a name="messages-from-my-devices-are-not-being-routed-as-expected"></a>デバイスからのメッセージが想定どおりにルーティングされていません

この問題のトラブルシューティングを行うには、次の分析を行います。

#### <a name="the-routing-metrics-for-this-endpoint"></a>このエンドポイントのルーティング メトリック

[ルーティングに関連するすべての IoT Hub メトリック](monitor-iot-hub-reference.md#routing-metrics)には、*Routing* がプレフィックスとして付けられます。 複数のメトリックから得た情報を組み合わせることで、問題の根本原因を特定することができます。 たとえば、メトリック **Routing Delivery Attempts** (ルーティングの配信試行) を使用すると、エンドポイントに配信されたメッセージの数、またはどのルートのクエリにも一致せず、フォールバック ルートが無効になっていたときに破棄されたメッセージの数を特定できます。 **Routing Latency** (ルーティングの待機時間) メトリックを調べて、メッセージ配信の待機時間が一定または増加しているかどうかを確認します。 待機時間が長くなると、特定のエンドポイントに問題があることを示している可能性があります。[エンドポイントの正常性](#the-health-of-the-endpoint)を確認することをお勧めします。 これらのルーティング メトリックには、エンドポイントの種類、特定のエンドポイント名、メッセージが配信されなかった理由など、メトリックに関する詳細情報を提供する[ディメンション](monitor-iot-hub-reference.md#metric-dimensions)もあります。

#### <a name="the-resource-logs-for-any-operational-issues"></a>運用上の問題のリソース ログ

[**ルート** リソース ログ](monitor-iot-hub-reference.md#routes)を確認して、ルーティングとエンドポイントの [操作](#operation-names)に関する詳細情報を取得するか、エラーおよび関連する [エラー コード](#common-error-codes)を特定して問題をより深く理解します。 たとえば、ログ内の操作名 **RouteEvaluationError** は、メッセージ形式に問題があるため、ルートを評価できなかったことを示します。 この問題を軽減するには、特定の[操作名](#operation-names)に関するヒントを参考にしてください。 イベントがエラーとしてログに記録される場合、そのログにより、評価が失敗した理由に関する詳細情報も提供されます。 たとえば、操作名が **EndpointUnhealthy** の場合、[エラー コード](#common-error-codes) 403004 は、エンドポイントの領域が不足していることを示します。

#### <a name="the-health-of-the-endpoint"></a>エンドポイントの正常性

エンドポイントの[正常性状態](iot-hub-devguide-endpoints.md#custom-endpoints)を取得するには、REST API の [Get Endpoint Health](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) を使用します。 *Get Endpoint Health* API によって、最後にメッセージがエンドポイントに正常に送信された時刻、[最新の既知のエラー](#last-known-errors-for-iot-hub-routing-endpoints)、最新の既知のエラーの時刻、およびこのエンドポイントに対して送信が試行された最後の時刻に関する情報も提供されます。 特定の[最新の既知のエラー](#last-known-errors-for-iot-hub-routing-endpoints)に対して提供される軽減策を使用します。

### <a name="i-suddenly-stopped-getting-messages-at-the-built-in-endpoint"></a>組み込みのエンドポイントでのメッセージの受信が突然停止しました

この問題のトラブルシューティングを行うには、次の分析を行います。

#### <a name="was-a-new-route-created"></a>新しいルートは作成されましたか?

ルートの作成後、組み込みのエンドポイントへのルートが作成されていないと、そのエンドポイントへのデータ フローは停止します。 新しいルートが追加された場合に、確実にメッセージが組み込みのエンドポイントに引き続き流れるようにするには、"*イベント*" エンドポイントへのルートを構成します。 

#### <a name="was-the-fallback-route-disabled"></a>フォールバック ルートは無効になっていましたか?

フォールバック ルートを通じて、既存のいずれのルートのクエリ条件も満たさないすべてのメッセージが、[Event Hubs](../event-hubs/index.yml) との互換性がある[組み込みのイベント ハブ](iot-hub-devguide-messages-read-builtin.md) (messages/events) に送信されます。 メッセージ ルーティングが有効になっている場合は、フォールバック ルート機能を有効にすることができます。 組み込みのエンドポイントへのルートがなく、フォールバック ルートが有効になっている場合は、ルートのどのクエリ条件とも一致しないメッセージのみが組み込みのエンドポイントに送信されます。 また、既存のすべてのルートを削除する場合は、組み込みのエンドポイントですべてのデータを受信するために、フォールバック ルートを有効にする必要があります。

Azure portal の [メッセージ ルーティング] ブレードで、フォールバック ルートを有効または無効にすることができます。 また、Azure Resource Manager で [FallbackRouteProperties](/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) を使用して、フォールバック ルート用にカスタム エンドポイントが使用されるようにすることもできます。

## <a name="last-known-errors-for-iot-hub-routing-endpoints"></a>IoT Hub ルーティング エンドポイント対する最新の既知のエラー

<a id="last-known-errors"></a>
[!INCLUDE [iot-hub-include-last-known-errors](../../includes/iot-hub-include-last-known-errors.md)]

## <a name="routes-resource-logs"></a>ルート リソース ログ

[ルート リソース ログ](monitor-iot-hub-reference.md#routes)に記録される操作名とエラー コードを次に示します。

<a id="diagnostics-operation-names"></a>
### <a name="operation-names"></a>操作名

[!INCLUDE [iot-hub-diagnostics-operation-names](../../includes/iot-hub-diagnostics-operation-names.md)]

<a id="diagnostics-error-codes"></a>
### <a name="common-error-codes"></a>一般的なエラー コード

[!INCLUDE [iot-hub-diagnostics-error-codes](../../includes/iot-hub-diagnostics-error-codes.md)]

## <a name="next-steps"></a>次のステップ

さらにサポートが必要な場合は、[Microsoft Q&A と Stack Overflow フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートにお問い合わせください。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。
