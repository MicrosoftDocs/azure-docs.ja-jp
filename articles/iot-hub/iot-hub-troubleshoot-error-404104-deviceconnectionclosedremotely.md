---
title: Azure IoT Hub のエラー 404104 DeviceConnectionClosedRemotely のトラブルシューティング
description: エラー 404104 DeviceConnectionClosedRemotely を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom: mqtt
ms.openlocfilehash: 673a76417739fa59a91979cca7c6807a584868f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92538257"
---
# <a name="404104-deviceconnectionclosedremotely"></a>404104 DeviceConnectionClosedRemotely

この記事では、**404104 DeviceConnectionClosedRemotely** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

### <a name="symptom-1"></a>現象 1

定期的な間隔 (たとえば 65 分間隔) でデバイスが切断され、IoT Hub リソース ログに **404104 DeviceConnectionClosedRemotely** が記録されます。 場合によっては、**401003 IoTHubUnauthorized** が発生し、1 分経たないうちにデバイス接続イベントが成功することもあります。

### <a name="symptom-2"></a>現象 2

デバイスがランダムに切断され、IoT Hub リソース ログに **404104 DeviceConnectionClosedRemotely** が記録されます。

### <a name="symptom-3"></a>現象 3

一度に多くのデバイスが切断され、["接続されているデバイス (connectedDeviceCount) メトリック"](monitor-iot-hub-reference.md) が低下し、Azure Monitor ログには通常よりも多く **404104 DeviceConnectionClosedRemotely** と [500xxx 内部エラー](iot-hub-troubleshoot-error-500xxx-internal-errors.md)が記録されています。

## <a name="causes"></a>原因

### <a name="cause-1"></a>原因 1

[IoT Hub に接続するために使用される SAS トークン](iot-hub-devguide-security.md#security-tokens)が期限切れになりました。その結果、IoT Hub によってデバイスが切断されました。 デバイスによってトークンが更新されると、接続は再確立されます。 たとえば、[C SDK の場合、SAS トークンは既定で 1 時間ごとに期限切れになります](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/connection_and_messaging_reliability.md#connection-authentication)。その結果、定期的に切断される可能性があります。

詳細については、[401003 IoTHubUnauthorized の原因](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#cause-1)に関する記事を参照してください。

### <a name="cause-2"></a>原因 2

次のような可能性があります。

- デバイスが基となるネットワーク接続を [MQTT キープアライブ](iot-hub-mqtt-support.md#default-keep-alive-timeout)よりも長く切断したため、リモート アイドル タイムアウトになりました。 MQTT キープ アライブの設定は、デバイスごとに異なる場合があります。

- デバイスからは TCP/IP レベルのリセットが送信されましたが、アプリケーションレベルの `MQTT DISCONNECT` は送信されませんでした。 つまり、デバイスによって基となるソケット接続が突然閉じられました。 この問題は、以前のバージョンの Azure IoT SDK のバグが原因で発生する場合があります。

- デバイス側のアプリケーションがクラッシュしました。

### <a name="cause-3"></a>原因 3

IoT Hub に一時的な問題が発生している可能性があります。 [IoT Hub 内部サーバー エラーの原因](iot-hub-troubleshoot-error-500xxx-internal-errors.md#cause)に関する記事を参照してください。

## <a name="solutions"></a>ソリューション

### <a name="solution-1"></a>解決策 1

[401003 IoTHubUnauthorized の解決策 1](iot-hub-troubleshoot-error-401003-iothubunauthorized.md#solution-1) を参照してください。

### <a name="solution-2"></a>解決策 2

- [接続をテスト](tutorial-connectivity.md)して、デバイスが IoT Hub に正常に接続できることを確認します。 ネットワークが信頼できない場合や断続的である場合、キープアライブ値を増やすことはお勧めしません。これは、(たとえば Azure Monitor アラートなどで) 検出に時間がかかる可能性があるためです。 

- [IoT SDK](iot-hub-devguide-sdks.md) の最新バージョンを使用します。

### <a name="solution-3"></a>解決策 3

[IoT Hub の内部サーバー エラーの解決策](iot-hub-troubleshoot-error-500xxx-internal-errors.md#solution)に関する記事を参照してください。

## <a name="next-steps"></a>次のステップ

Azure IoT device SDK を使用して接続を確実に管理することをお勧めします。 詳細については、「[Azure IoT Hub device SDK を使用して、接続と信頼できるメッセージングを管理する方法](iot-hub-reliability-features-in-sdks.md)」を参照してください。