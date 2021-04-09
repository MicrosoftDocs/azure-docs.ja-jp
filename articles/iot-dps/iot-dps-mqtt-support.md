---
title: Azure IoT Device Provisioning Service MQTT のサポートについて | Microsoft Docs
description: 開発者ガイド - MQTT プロトコルを使用して Azure IoT デバイス プロビジョニング サービス (DPS) デバイスに接続されたエンドポイントに接続するデバイスのサポート。
author: rajeevmv
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: ravokkar
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 0a7ec2f4f8fdf631a6bc5096296275291ec41751
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94967127"
---
# <a name="communicate-with-your-dps-using-the-mqtt-protocol"></a>MQTT プロトコルを使用した DPS との通信

DPS により、デバイスは、以下を使用して DPS デバイス エンドポイントと通信できます。

* ポート 8883 上の [MQTT v3.1.1](https://mqtt.org/)
* ポート 443 で WebSocket を介して [MQTT v3.1.1](http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/os/mqtt-v3.1.1-os.html#_Toc398718127)

DPS はフル機能の MQTT ブローカーではないため、MQTT v3.1.1 標準で指定されているすべての動作がサポートされているわけではありません。 この記事では、サポートされている MQTT 動作を使用して、デバイスと DPS の通信を行う方法について説明します。

DPS とのすべてのデバイス通信は、TLS/SSL を使用してセキュリティで保護する必要があります。 そのため、DPS では、ポート 1883 経由のセキュリティで保護されていない接続をサポートしていません。

 > [!NOTE] 
 > DPS では現在、MQTT プロトコル経由で TPM [構成証明メカニズム](./concepts-service.md#attestation-mechanism)を使用しているデバイスをサポートしていません。

## <a name="connecting-to-dps"></a>DPS の接続

デバイスでは、MQTT プロトコルを使用し、次のいずれかのオプションを利用して DPS に接続できます。

* [Azure IoT Provisioning SDK](../iot-hub/iot-hub-devguide-sdks.md#microsoft-azure-provisioning-sdks) のライブラリ。
* 直接的な MQTT プロトコル。

## <a name="using-the-mqtt-protocol-directly-as-a-device"></a>MQTT プロトコルの直接使用 (デバイスとして)

デバイスでデバイス SDK を使用できない場合でも、MQTT プロトコルをポート 8883 で使用して、デバイスをパブリックのデバイス エンドポイントに接続できます。 **CONNECT** パケットの場合、デバイスでは次の値を使用する必要があります。

* **[ClientId]** フィールドには、**registrationId** を使用します。

* **[Usename]** フィールドには、`{idScope}/registrations/{registration_id}/api-version=2019-03-31` を使用します。`{idScope}` は DPS の [idScope](./concepts-service.md#id-scope) です。

* **[Password]** フィールドには、SAS トークンを使用します。 SAS トークンの形式は、HTTPS プロトコルや AMQP プロトコルの場合と同じです。

  `SharedAccessSignature sr={URL-encoded-resourceURI}&sig={signature-string}&se={expiry}&skn=registration` ResourceURI は `{idScope}/registrations/{registration_id}` の形式にする必要があります。 ポリシー名は `registration` にする必要があります。

  > [!NOTE]
  > X.509 証明書の認証を使用する場合は、SAS トークン パスワードは不要です。

  SAS トークンの生成方法について詳しくは、[DPS へのアクセス制御](how-to-control-access.md#security-tokens)に関するページにあるセキュリティ トークンのセクションをご覧ください。

DPS 実装固有のビヘイビアーのリストを以下に示します。

 * DPS では、**0** に設定されている **CleanSession** フラグの機能はサポートされていません。

 * デバイス アプリが **QoS 2** を使用してトピックをサブスクライブしている場合、DPS は **SUBACK** パケットに最大の QoS レベル 1 を許可します。 その後、DPS は QoS 1 を使用してデバイスにメッセージを配信します。

## <a name="tlsssl-configuration"></a>TLS または SSL の構成

MQTT プロトコルを直接使用するには、クライアントは TLS 1.2 経由で接続する *必要があります*。 この手順をスキップすると、接続エラーで失敗します。


## <a name="registering-a-device"></a>デバイスの登録

DPS 経由でデバイスを登録するには、デバイスで `$dps/registrations/res/#` を **トピック フィルター** として使用してサブスクライブする必要があります。 トピック フィルター内の複数レベルのワイルドカード `#` は、デバイスがトピック名の追加プロパティを受信できるようにするためにのみ使用されます。 DPS では、`#` または `?` ワイルドカードを使用してサブトピックをフィルター処理することはできません。 DPS はパブリッシャーとサブスクライバー間の汎用メッセージング ブローカーではないため、ドキュメント化されたトピック名とトピック フィルターのみがサポートされます。

デバイスでは、`$dps/registrations/PUT/iotdps-register/?$rid={request_id}` を **トピック名** として使用して、DPS に登録メッセージを発行する必要があります。 ペイロードには、JSON 形式の[デバイス登録](/rest/api/iot-dps/runtimeregistration/registerdevice#deviceregistration)オブジェクトを含める必要があります。
成功するシナリオでは、`$dps/registrations/res/202/?$rid={request_id}&retry-after=x` トピック名に対する応答がデバイスで受信されます。ここで、x は再試行後の値 (秒) です。 応答のペイロードには、[RegistrationOperationStatus](/rest/api/iot-dps/runtimeregistration/registerdevice#registrationoperationstatus) オブジェクトが JSON 形式で含まれます。

## <a name="polling-for-registration-operation-status"></a>登録操作の状態のポーリング

デバイス登録操作の結果を受け取るため、デバイスで定期的にサービスをポーリングする必要があります。 前述のように、デバイスが `$dps/registrations/res/#` のトピックを既にサブスクライブしている場合、get operationstatus メッセージを `$dps/registrations/GET/iotdps-get-operationstatus/?$rid={request_id}&operationId={operationId}` トピック名に発行できます。 このメッセージの操作 ID は、前の手順で RegistrationOperationStatus 応答メッセージで受信した値である必要があります。 成功した場合、サービスが `$dps/registrations/res/200/?$rid={request_id}` のトピックで応答するようになります。 応答のペイロードには、RegistrationOperationStatus オブジェクトが含まれます。 retry-after 期間と同じ遅延の後に応答コードが 202 の場合、デバイスでサービスのポーリングを続行する必要があります。 サービスで状態コード 200 が返された場合は、デバイス登録操作は正常に実行されています。

## <a name="connecting-over-websocket"></a>Websocket 経由の接続
Websocket 経由で接続する場合は、`mqtt` としてサブプロトコルを指定します。 [RFC 6455](https://tools.ietf.org/html/rfc6455) に従います。

## <a name="next-steps"></a>次のステップ

MQTT プロトコルについて詳しくは、[MQTT のドキュメント](https://mqtt.org/)をご覧ください。

DPS の機能を詳しく調べるには、次のリンクを使用してください。

* [IoT DPS について](about-iot-dps.md)