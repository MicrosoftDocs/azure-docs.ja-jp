---
title: Azure IoT Hub のエラー 401003 IoTHubUnauthorized のトラブルシューティング
description: エラー 401003 IoTHubUnauthorized を修正する方法の概要
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 8fb891d5a47203c9905a7def9d04199d24327f70
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94357251"
---
# <a name="401003-iothubunauthorized"></a>401003 IoTHubUnauthorized

この記事では、**401003 IoTHubUnauthorized** エラーの原因と解決策について説明します。

## <a name="symptoms"></a>現象

### <a name="symptom-1"></a>現象 1

ログを見ると、**401003 IoTHubUnauthorized** で切断されたデバイスがあり、それに **404104 DeviceConnectionClosedRemotely** が続き、そのすぐ後に接続が成功するパターンがあります。

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

IoT Hub では、認証ヘッダー、規則、またはキーを認証できませんでした。 これには、現象に示されたいずれかの理由が考えられます。

## <a name="solution"></a>解決策

### <a name="solution-1"></a>解決策 1

デバイス接続文字列を使用した接続に IoT SDK を使用する場合、操作は不要です。 SAS トークンの有効期限が切れると、IoT SDK によって新しいトークンが再生成され、再接続されます。

既定のトークンの有効期間は、どの SDK でも 60 分です。ただし、一部の SDK では、トークンの有効期間とトークンの更新のしきい値を構成できます。 さらに、デバイスが切断され、トークンが更新されて再接続したときに生成されるエラーは、各 SDK によって異なります。 詳細、およびデバイスのログで使用されている SDK を判別する方法については、「[Azure IoT SDK での MQTT デバイスの切断動作](iot-hub-troubleshoot-connectivity.md#mqtt-device-disconnect-behavior-with-azure-iot-sdks)」を参照してください。

デバイス開発者の場合、エラーの量が問題になるときは、C SDK に切り替えて、有効期限が切れる前に SAS トークンを更新します。 AMQP の場合は、切断せずに SAS トークンを更新できます。

### <a name="solution-2"></a>解決策 2

一般に、エラーを修正する方法を説明するエラー メッセージが表示されます。 何らかの理由でエラー メッセージの詳細にアクセスできない場合は、次のことを確認してください。

- 使用する SAS またはその他のセキュリティ トークンの有効期限が切れていないこと。
- X.509 証明書認証の場合、デバイス証明書またはデバイスに関連付けられている CA 証明書の有効期限が切れていません。 IoT Hub での X.509 CA 証明書の登録方法については、「[Azure IoT Hub での X.509 セキュリティの設定](iot-hub-security-x509-get-started.md)」を参照してください。
- X.509 証明書の拇印認証の場合、デバイス証明書の拇印は IoT Hub に登録されます。
- 承認の資格情報が使用するプロトコルに適した形式であること。 詳細については、「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」を参照してください。
- 使用される承認規則に、要求された操作に対するアクセス許可があること。

## <a name="next-steps"></a>次のステップ

- IoT Hub の認証を簡単に受けられるようにするには、[Azure IoT SDK](iot-hub-devguide-sdks.md) を使用することをお勧めします。
- IoT Hub での認証の詳細については、「[IoT Hub へのアクセスの制御](iot-hub-devguide-security.md)」を参照してください。
