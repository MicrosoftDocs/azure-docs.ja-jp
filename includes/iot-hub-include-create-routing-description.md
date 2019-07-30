---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: b640a3cb9382ad72bb48e06c6a7074c96409e2e4
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181796"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

シミュレートされたデバイスによってメッセージに添付されたプロパティに基づいて、メッセージを異なるリソースにルーティングします。 カスタム ルーティングされないメッセージは、既定のエンドポイント (メッセージ/イベント) に送信されます。 次のチュートリアルでは、メッセージを IoT Hub に送信して、それらがさまざまな宛先にルーティングされるのを確認します。

|value |結果|
|------|------|
|level="storage" |Azure Storage に書き込みます。|
|level="critical" |Service Bus キューに書き込みます。 ロジック アプリがキューからメッセージを取得し、Office 365 を使ってメールでメッセージを送信します。|
|default |Power BI を使ってこのデータを表示します。|

最初の手順では、データのルーティング先のエンドポイントを設定します。 2 番目の手順では、そのエンドポイントを使用するメッセージ ルートを設定します。 ルーティングの設定後、ポータルでエンドポイントとメッセージ ルートを表示できます。