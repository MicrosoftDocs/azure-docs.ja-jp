---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d03579f704879bd8d012bb0bb326659d1f778dee
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "84793293"
---
REST API の [Get Endpoint Health](https://docs.microsoft.com/rest/api/iothub/iothubresource/getendpointhealth#iothubresource_getendpointhealth) を使用すると、エンドポイントの正常性状態と、最後の既知のエラーが取得され、エンドポイントが正常でない理由を特定できます。 次の表に、最も一般的なエラーを示します。

|最後の既知のエラー|説明/発生する状況|考えられる軽減策|
|-----|-----|-----|
|一時的|一時的なエラーが発生したため、IoT Hub によって操作が再試行されます。|[診断ログ](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes)のルートを確認します。|
|InternalError|エンドポイントにメッセージを配信中にエラーが発生しました。|これは内部例外ですが、[診断ログ](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health#routes)のルートも確認します。|
|権限がありません|IoT Hub には指定されたエンドポイントにメッセージを送信する権限がありません。|エンドポイントの接続文字列が最新のものであることを確認します。 変更されている場合は、IoT Hub の更新を検討してください。 エンドポイントでマネージド ID が使用されている場合は、IoT Hub プリンシパルがターゲットに対して必要な権限を持っていることを確認します。|
|Throttled|エンドポイントへのメッセージの書き込み中に IoT Hub が調整されています。|影響を受けるエンドポイントのスロットル制限を確認します。 必要に応じてスケールアップするようにエンドポイントの構成を変更します。|
|タイムアウト|操作がタイムアウトしました。|操作を再試行してください。|
|見つかりません|ターゲット リソースが存在しません。|ターゲット リソースが存在することを確認してください。|
|コンテナーが見つかりません|ストレージ コンテナーが存在しません。|ストレージ コンテナーが存在することを確認してください。|
|Container disabled|ストレージ コンテナーが無効です。|ストレージ コンテナーが有効であることを確認してください。|
|MaxMessageSizeExceeded|メッセージ ルーティングのメッセージ サイズの上限は 256 Kb です。ルーティングされているメッセージ サイズがこの制限を超えています。|アプリケーション プロパティを少なくするか、メッセージの強化を少なくすることで、メッセージ サイズを減らすことができるかどうかを確認します。|
|PartitioningAndDuplicateDetectionNotSupported|Service Bus で重複検出が有効になっていない可能性があります。|Service Bus の重複検出を無効にするか、重複検出のないエンティティを使用することを検討してください。|
|SessionfulEntityNotSupported|Service Bus でセッションが有効になっていない可能性があります。|Service Bus のセッションを無効にするか、セッションのないエンティティを使用することを検討してください。|
|NoMatchingSubscriptionsForMessage|Service Bus トピックにメッセージを書き込むためのサブスクリプションがありません。|IoT Hub メッセージのルーティング先となるサブスクリプションを作成します。|
|EndpointExternallyDisabled|エンドポイントは、IoT Hub がそこにメッセージを送信できるようなアクティブな状態ではありません。|エンドポイントを有効にして、アクティブな状態に戻します。|
|DeviceMaximumQueueDepthExceeded|Service Bus サイズの制限に達しました。|ターゲットのイベント ハブからメッセージを削除して、イベント ハブに新しいメッセージを取り込めるようにすることを検討してください。|