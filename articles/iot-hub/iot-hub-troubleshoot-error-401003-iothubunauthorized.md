---
title: Azure IoT Hub のエラー 401003 IoTHubUnauthorized のトラブルシューティング
description: エラー 401003 IoTHubUnauthorized を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 01/30/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: f46d41c8287d03cbe9582ed560244cbd85cdeeaa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "81759599"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

この記事では、**401003 IoTHubUnauthorized** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

### <a name="symptom-1"></a>現象 1

診断ログを見ると、**401003 IoTHubUnauthorized** で切断されたデバイスがあり、それに **404104 DeviceConnectionClosedRemotely** が続き、そのすぐ後に接続が成功するパターンがあります。

### <a name="symptom-2"></a>現象 2

IoT Hub への要求が、次のいずれかのエラー メッセージで失敗します。

* Authorization header missing (Authorization ヘッダーがありません)
* IotHub '\*' does not contain the specified device '\*' (IotHub '*' に指定したデバイス '*' が含まれていません)
* Authorization rule '\*' does not allow access for '\*' (承認規則 '*' では '*' へのアクセスが許可されていません)
* Authentication failed for this device, renew token or certificate and reconnect (このデバイスの認証に失敗しました。トークンまたは証明書を更新して再接続してください)
* Thumbprint does not match configuration:Thumbprint:SHA1Hash=\*, SHA2Hash=\*; Configuration:PrimaryThumbprint=\*, SecondaryThumbprint=\* (拇印が構成と一致しません: 拇印: SHA1Hash=*、SHA2Hash=*、構成: PrimaryThumbprint=*、SecondaryThumbprint=*)

## <a name="cause"></a>原因

### <a name="cause-1"></a>原因 1

MQTT の場合、一部の SDK では、SAS トークンが期限切れになったときに切断を発行するために IoT Hub を利用し、更新するタイミングを把握しています。 そのため、 

1. SAS トークンが期限切れになります
1. IoT Hub によって有効期限が通知され、**401003 IoTHubUnauthorized** でデバイスが切断されます
1. デバイスでは **404104 DeviceConnectionClosedRemotely** で切断が完了します
1. IoT SDK により、新しい SAS トークンが生成されます
1. デバイスが IoT Hub に正常に再接続されます

### <a name="cause-2"></a>原因 2

IoT Hub では、認証ヘッダー、規則、またはキーを認証できませんでした。

## <a name="solution"></a>解決策

### <a name="solution-1"></a>解決策 1

デバイス接続文字列を使用した接続に IoT SDK を使用する場合、操作は不要です。 SAS トークンの有効期限が切れると、IoT SDK によって新しいトークンが再生成され、再接続されます。 

エラーの量が問題になる場合は、C SDK に切り替えて、有効期限が切れる前に SAS トークンを更新します。 また、AMQP の場合は、SAS トークンを切断せずに更新できます。

### <a name="solution-2"></a>解決策 2

一般に、エラーを修正する方法を説明するエラー メッセージが表示されます。 何らかの理由でエラー メッセージの詳細にアクセスできない場合は、次のことを確認してください。

- 使用する SAS またはその他のセキュリティ トークンの有効期限が切れていないこと。 
- 承認の資格情報が使用するプロトコルに適した形式であること。 詳細については、「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」を参照してください。
- 使用される承認規則に、要求された操作に対するアクセス許可があること。

## <a name="next-steps"></a>次のステップ

IoT Hub の認証を簡単に受けられるようにするには、[Azure IoT SDK](iot-hub-devguide-sdks.md) を使用することをお勧めします。