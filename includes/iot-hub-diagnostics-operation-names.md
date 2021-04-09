---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 04/28/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 7d6718fb25b3743a50c52f11c8e19d80839b485c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95560129"
---
<!-- operation names for the diag logs for IoT Hub -->

|操作の名前|Level|説明|
|------------- |-----|-----------|
|UndefinedRouteEvaluation|Information|指定された条件ではメッセージを評価できません。 たとえば、ルート クエリ条件のプロパティがメッセージに存在しない場合です。 [こちら](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md)にルーティング クエリの構文の詳細があります。|
|RouteEvaluationError|エラー|メッセージ形式に問題があるため、メッセージの評価中にエラーが発生しました。 たとえば、メッセージにコンテンツ エンコードが指定されていないか、コンテンツの種類が有効でない場合、このエラーがログに記録されます。 これらは[システム プロパティ](../articles/iot-hub/iot-hub-devguide-routing-query-syntax.md#system-properties)で設定する必要があります。|
|DroppedMessage|エラー|メッセージが削除され、送信されませんでした。 この理由としては、メッセージがどのルーティング クエリにも一致しなかったか、エンドポイントが停止しており、数回再試行してもメッセージを配信できなかったなどが考えられます。 REST API の[エンドポイント正常性取得](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)に関するページを利用し、エンドポイントに関する詳細を取得することをお勧めします。|
|EndpointUnhealthy|エラー|エンドポイントで IoT Hub からのメッセージが受信されておらず、IoT Hub ではメッセージの再送信を試行しています。 REST API の[エンドポイント正常性取得](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)に関するページを利用し、最後に判明したエラーを確認することをお勧めします。|
|EndpointDead|エラー|エンドポイントでは、IoT Hub からのメッセージが 1 時間以上受信されていません。 REST API の[エンドポイント正常性取得](/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth)に関するページを利用し、最後に判明したエラーを確認することをお勧めします。|
|EndpointHealthy|Information|エンドポイントは正常であり、IoT Hub からメッセージを受信しています。 このメッセージは継続的に記録されず、エンドポイントが再び正常になったときにのみ、ログに記録されます。 このメッセージは、IoT Hub でメッセージをエンドポイントに送信できなかったが、現在、エンドポイントは正常であることを意味します。|
|OrphanedMessage|Information|メッセージはいずれのルートとも一致しません。|
|InvalidMessage|エラー|エンドポイントとの互換性がないため、メッセージは無効です。 エンドポイントの構成を確認することをお勧めします。|


*UndefinedRouteEvaluation*、*RouteEvaluationError*、*OrphanedMessage* 操作は、IoT Hub あたりせいぜい 1 分に 1 回の頻度で調整され、ログに記録されます。