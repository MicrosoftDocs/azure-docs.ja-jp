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
ms.openlocfilehash: 3a5747912a18b8406cabd03c9823e3f6fe6898a6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "72808839"
---
<!-- description of message routing used in the Azure CLI, PowerShell, and RM routing articles.-->

シミュレートされたデバイスによってメッセージに添付されたプロパティに基づいて、メッセージを異なるリソースにルーティングします。 カスタム ルーティングされないメッセージは、既定のエンドポイント (メッセージ/イベント) に送信されます。 次のチュートリアルでは、メッセージを IoT Hub に送信して、それらがさまざまな宛先にルーティングされるのを確認します。

|値 |結果|
|------|------|
|level="storage" |Azure Storage に書き込みます。|
|level="critical" |Service Bus キューに書き込みます。 ロジック アプリがキューからメッセージを取得し、Office 365 を使ってメールでメッセージを送信します。|
|既定値 (default) |Power BI を使ってこのデータを表示します。|

最初の手順では、データのルーティング先のエンドポイントを設定します。 2 番目の手順では、そのエンドポイントを使用するメッセージ ルートを設定します。 ルーティングの設定後、ポータルでエンドポイントとメッセージ ルートを表示できます。