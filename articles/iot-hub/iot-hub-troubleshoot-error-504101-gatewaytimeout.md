---
title: Azure IoT Hub のエラー 504101 GatewayTimeout のトラブルシューティング
description: エラー 504101 GatewayTimeout を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: amqp
ms.openlocfilehash: 373acc30ed652a7f540e840dfad5eeeda65ca179
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "81759558"
---
# <a name="504101-gatewaytimeout"></a>504101 GatewayTimeout

この記事では、**504101 GatewayTimeout** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

IoT Hub からデバイスへダイレクト メソッドを呼び出そうとすると、要求がエラー **504101 GatewayTimeout** で失敗します。

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

IoT Hub でエラーが発生したため、タイムアウト前にダイレクト メソッドが完了したかどうかを確認できませんでした。

### <a name="cause-2"></a>原因 2

Azure IoT C# SDK の以前のバージョン (1.19.0 より前) を使用している場合、バグが原因でデバイスと IoT Hub 間の AMQP リンクが警告なしで削除されることがあります。

## <a name="solution"></a>解決策

### <a name="solution-1"></a>解決策 1

再試行を発行します。

### <a name="solution-2"></a>解決策 2

最新バージョンの Azure IOT C# SDK にアップグレードします。